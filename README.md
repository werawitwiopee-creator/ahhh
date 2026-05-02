# m.py
# pip install discord.py yt-dlp

import discord
import yt_dlp
import os
import asyncio
import re
import subprocess

BOT_TOKEN = "ใส่ TOKEN บอทตรงนี้"
MAX_FILE_MB = 25
DOWNLOAD_DIR = "./downloads"
INTRO_PATH = "อ_พโดย191_2_.ogg"
FFMPEG = "./ffmpeg.exe"

intents = discord.Intents.default()
intents.message_content = True
client = discord.Client(intents=intents)

os.makedirs(DOWNLOAD_DIR, exist_ok=True)


def is_youtube_url(url):
    return bool(re.match(r"(https?://)?(www\.)?(youtube\.com|youtu\.be)/.+", url))


def format_duration(seconds):
    m, s = divmod(seconds, 60)
    h, m = divmod(m, 60)
    return f"{h:02d}:{m:02d}:{s:02d}" if h else f"{m:02d}:{s:02d}"


async def download_audio(url):
    ydl_opts = {
        "format": "bestaudio/best",
        "outtmpl": f"{DOWNLOAD_DIR}/%(id)s.%(ext)s",
        "postprocessors": [{
            "key": "FFmpegExtractAudio",
            "preferredcodec": "mp3",
            "preferredquality": "192",
        }],
        "ffmpeg_location": ".",
        "quiet": True,
        "no_warnings": True,
    }
    loop = asyncio.get_event_loop()
    def _dl():
        with yt_dlp.YoutubeDL(ydl_opts) as ydl:
            info = ydl.extract_info(url, download=True)
            return {
                "path": f"{DOWNLOAD_DIR}/{info['id']}.mp3",
                "title": info.get("title", "Unknown"),
                "duration": info.get("duration", 0),
                "uploader": info.get("uploader", "Unknown"),
            }
    return await loop.run_in_executor(None, _dl)


@client.event
async def on_ready():
    print(f"✅ บอท {client.user} พร้อมใช้งานแล้ว!")
    print("คำสั่ง: !dl <ลิงก์> | !kiku + แนบไฟล์")


@client.event
async def on_message(message):
    if message.author.bot:
        return

    content = message.content.strip()

    # ===== !dl =====
    if content.lower().startswith("!dl"):
        parts = content.split(maxsplit=1)
        if len(parts) < 2:
            await message.reply("❌ ใช้งาน: `!dl <ลิงก์ YouTube>`")
            return

        url = parts[1].strip()
        if not is_youtube_url(url):
            await message.reply("❌ ลิงก์ไม่ถูกต้อง รองรับเฉพาะ YouTube เท่านั้น")
            return

        status = await message.reply("⏳ กำลังดาวน์โหลด...")
        try:
            info = await download_audio(url)
            mp3_path = info["path"]

            if not os.path.exists(mp3_path):
                raise FileNotFoundError("ไม่พบไฟล์")

            size_mb = os.path.getsize(mp3_path) / (1024 * 1024)
            if size_mb > MAX_FILE_MB:
                await status.edit(content=f"❌ ไฟล์ใหญ่เกินไป ({size_mb:.1f} MB)")
                return

            embed = discord.Embed(title="🎵 ดาวน์โหลดสำเร็จ!", color=discord.Color.green())
            embed.add_field(name="ชื่อเพลง", value=info["title"], inline=False)
            embed.add_field(name="ช่อง", value=info["uploader"], inline=True)
            embed.add_field(name="ความยาว", value=format_duration(info["duration"]), inline=True)
            embed.add_field(name="ขนาด", value=f"{size_mb:.2f} MB", inline=True)
            embed.set_footer(text=f"ขอโดย {message.author.display_name}")

            await status.delete()
            await message.reply(embed=embed, file=discord.File(mp3_path, filename=f"{info['title'][:80]}.mp3"))

        except yt_dlp.utils.DownloadError as e:
            await status.edit(content=f"❌ ดาวน์โหลดไม่สำเร็จ: `{e}`")
        except Exception as e:
            await status.edit(content=f"❌ เกิดข้อผิดพลาด: `{e}`")

    # ===== !kiku =====
    elif content.lower().startswith("!kiku"):
        if not message.attachments:
            await message.reply("❌ แนบไฟล์เสียงมาด้วยนะครับ")
            return

        attachment = message.attachments[0]
        if not attachment.filename.lower().endswith((".mp3", ".wav", ".ogg", ".m4a", ".flac")):
            await message.reply("❌ รองรับเฉพาะไฟล์เสียงเท่านั้น")
            return

        status = await message.reply("⏳ กำลังทำ Kiku style...")

        mid = message.id
        input_path  = f"{DOWNLOAD_DIR}/in_{mid}.ogg"
        sped_path   = f"{DOWNLOAD_DIR}/sped_{mid}.ogg"
        output_path = f"{DOWNLOAD_DIR}/out_{mid}.ogg"
        list_path   = f"{DOWNLOAD_DIR}/list_{mid}.txt"

        try:
            await attachment.save(input_path)

            loop = asyncio.get_event_loop()

            def run_ff():
                # ปรับเร็ว x1.12
                r1 = subprocess.run([
                    FFMPEG, "-y", "-i", input_path,
                    "-filter:a", "asetrate=44100*1.12,aresample=44100",
                    "-c:a", "libvorbis", "-q:a", "6",
                    sped_path
                ], capture_output=True)
                if r1.returncode != 0:
                    raise RuntimeError(r1.stderr.decode())

                # เขียน list ต่อ intro + เพลง
                with open(list_path, "w", encoding="utf-8") as f:
                    f.write(f"file '{os.path.abspath(INTRO_PATH)}'\n")
                    f.write(f"file '{os.path.abspath(sped_path)}'\n")

                # concat
                r2 = subprocess.run([
                    FFMPEG, "-y",
                    "-f", "concat", "-safe", "0",
                    "-i", list_path,
                    "-c:a", "libvorbis", "-q:a", "6",
                    output_path
                ], capture_output=True)
                if r2.returncode != 0:
                    raise RuntimeError(r2.stderr.decode())

            await loop.run_in_executor(None, run_ff)

            size_mb = os.path.getsize(output_path) / (1024 * 1024)
            if size_mb > MAX_FILE_MB:
                await status.edit(content=f"❌ ไฟล์ใหญ่เกินไป ({size_mb:.1f} MB)")
                return

            name = os.path.splitext(attachment.filename)[0]
            embed = discord.Embed(title="🎐 Kiku Style!", color=discord.Color.purple())
            embed.add_field(name="ไฟล์", value=attachment.filename, inline=False)
            embed.add_field(name="เอฟเฟกต์", value="Intro + Speed x1.12", inline=True)
            embed.add_field(name="ขนาด", value=f"{size_mb:.2f} MB", inline=True)
            embed.set_footer(text=f"ขอโดย {message.author.display_name}")

            await status.delete()
            await message.reply(embed=embed, file=discord.File(output_path, filename=f"{name}_kiku.ogg"))

        except Exception as e:
            await status.edit(content=f"❌ เกิดข้อผิดพลาด: `{e}`")

        finally:
            for p in [input_path, sped_path, list_path]:
                if os.path.exists(p):
                    os.remove(p)


client.run(BOT_TOKEN)
