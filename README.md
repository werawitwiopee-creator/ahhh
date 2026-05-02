# discord_ytdl_bot.py
# ติดตั้ง dependencies ก่อน:
#   pip install discord.py yt-dlp

import discord
import yt_dlp
import os
import asyncio
import re
from pathlib import Path

# ================== ตั้งค่า ==================
BOT_TOKEN = "YOUR_BOT_TOKEN_HERE"   # ใส่ Token ของบอทตรงนี้
MAX_FILE_MB = 25                     # Discord free tier จำกัด 25MB (Nitro = 500MB)
DOWNLOAD_DIR = "./downloads"
# ============================================

intents = discord.Intents.default()
intents.message_content = True
client = discord.Client(intents=intents)

os.makedirs(DOWNLOAD_DIR, exist_ok=True)


def is_youtube_url(url: str) -> bool:
    pattern = r"(https?://)?(www\.)?(youtube\.com|youtu\.be)/.+"
    return bool(re.match(pattern, url))


async def download_audio(url: str) -> dict:
    """ดาวน์โหลดเสียงจาก YouTube แล้วคืน path + ข้อมูลเพลง"""
    ydl_opts = {
        "format": "bestaudio/best",
        "outtmpl": f"{DOWNLOAD_DIR}/%(id)s.%(ext)s",
        "postprocessors": [{
            "key": "FFmpegExtractAudio",
            "preferredcodec": "mp3",
            "preferredquality": "192",
        }],
        "quiet": True,
        "no_warnings": True,
    }

    loop = asyncio.get_event_loop()

    def _download():
        with yt_dlp.YoutubeDL(ydl_opts) as ydl:
            info = ydl.extract_info(url, download=True)
            video_id = info["id"]
            title = info.get("title", "Unknown")
            duration = info.get("duration", 0)
            uploader = info.get("uploader", "Unknown")
            mp3_path = f"{DOWNLOAD_DIR}/{video_id}.mp3"
            return {
                "path": mp3_path,
                "title": title,
                "duration": duration,
                "uploader": uploader,
                "video_id": video_id,
            }

    return await loop.run_in_executor(None, _download)


def format_duration(seconds: int) -> str:
    m, s = divmod(seconds, 60)
    h, m = divmod(m, 60)
    return f"{h:02d}:{m:02d}:{s:02d}" if h else f"{m:02d}:{s:02d}"


@client.event
async def on_ready():
    print(f"✅ บอท {client.user} พร้อมใช้งานแล้ว!")


@client.event
async def on_message(message: discord.Message):
    # ไม่ตอบสนองต่อบอทตัวเอง
    if message.author.bot:
        return

    content = message.content.strip()

    # ตรวจสอบคำสั่ง !dl
    if not content.lower().startswith("!dl"):
        return

    parts = content.split(maxsplit=1)
    if len(parts) < 2:
        await message.reply("❌ ใช้งาน: `!dl <ลิงก์ YouTube>`\nตัวอย่าง: `!dl https://youtu.be/xxxxx`")
        return

    url = parts[1].strip()

    if not is_youtube_url(url):
        await message.reply("❌ ลิงก์ไม่ถูกต้อง รองรับเฉพาะ YouTube เท่านั้น")
        return

    # แจ้งสถานะกำลังดาวน์โหลด
    status_msg = await message.reply("⏳ กำลังดาวน์โหลด...")

    mp3_path = None
    try:
        info = await download_audio(url)
        mp3_path = info["path"]

        if not os.path.exists(mp3_path):
            raise FileNotFoundError("ไม่พบไฟล์ MP3 หลังดาวน์โหลด")

        file_size_mb = os.path.getsize(mp3_path) / (1024 * 1024)

        if file_size_mb > MAX_FILE_MB:
            await status_msg.edit(content=(
                f"❌ ไฟล์ใหญ่เกินไป ({file_size_mb:.1f} MB)\n"
                f"Discord รองรับสูงสุด {MAX_FILE_MB} MB"
            ))
            return

        # สร้าง Embed ข้อมูลเพลง
        embed = discord.Embed(
            title="🎵 ดาวน์โหลดสำเร็จ!",
            color=discord.Color.green()
        )
        embed.add_field(name="ชื่อเพลง", value=info["title"], inline=False)
        embed.add_field(name="ช่อง", value=info["uploader"], inline=True)
        embed.add_field(name="ความยาว", value=format_duration(info["duration"]), inline=True)
        embed.add_field(name="ขนาดไฟล์", value=f"{file_size_mb:.2f} MB", inline=True)
        embed.set_footer(text=f"ขอโดย {message.author.display_name}")

        # อัปโหลดไฟล์ไปดิสคอร์ด
        discord_file = discord.File(mp3_path, filename=f"{info['title'][:80]}.mp3")
        await status_msg.delete()
        await message.reply(embed=embed, file=discord_file)

    except yt_dlp.utils.DownloadError as e:
        await status_msg.edit(content=f"❌ ดาวน์โหลดไม่สำเร็จ: `{e}`")

    except Exception as e:
        await status_msg.edit(content=f"❌ เกิดข้อผิดพลาด: `{e}`")

    finally:
        # ลบไฟล์ชั่วคราวหลังส่งแล้ว
        if mp3_path and os.path.exists(mp3_path):
            os.remove(mp3_path)


client.run(BOT_TOKEN)
