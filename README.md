import discord
import asyncio
import subprocess
import os

# ==========================================
BOT_TOKEN = "Token_ของคุณ"
# ==========================================

intents = discord.Intents.default()
intents.message_content = True
client = discord.Client(intents=intents)

async def process_audio(intro_path, song_path, output_path):
    # รวม intro + song แล้วปรับ speed x1.12
    cmd = [
        "ffmpeg", "-y",
        "-i", intro_path,
        "-i", song_path,
        "-filter_complex",
        "[0:a][1:a]concat=n=2:v=0:a=1[a];[a]atempo=1.12[out]",
        "-map", "[out]",
        "-c:a", "libvorbis",
        output_path
    ]
    proc = await asyncio.create_subprocess_exec(
        *cmd,
        stdout=asyncio.subprocess.DEVNULL,
        stderr=asyncio.subprocess.DEVNULL
    )
    await proc.wait()
    return proc.returncode == 0

@client.event
async def on_message(message):
    if message.author.bot:
        return

    # !dl ดาวน์โหลดเสียงจาก YouTube
    if message.content.lower().startswith("!dl "):
        url = message.content[4:].strip()
        if not url:
            await message.channel.send("❌ ใส่ลิ้ง YouTube ด้วยนะครับ")
            return

        import yt_dlp
        os.makedirs("downloads", exist_ok=True)

        embed = discord.Embed(description="⏳ กำลังดาวน์โหลด...", color=0xffffff)
        msg = await message.channel.send(embed=embed)

        def download():
            ydl_opts = {
                "format":         "bestaudio/best",
                "outtmpl":        "downloads/%(title)s.%(ext)s",
                "postprocessors": [{
                    "key":              "FFmpegExtractAudio",
                    "preferredcodec":   "mp3",
                    "preferredquality": "192",
                }],
                "quiet":       True,
                "no_warnings": True,
            }
            with yt_dlp.YoutubeDL(ydl_opts) as ydl:
                info     = ydl.extract_info(url, download=True)
                filename = ydl.prepare_filename(info)
                mp3_file = os.path.splitext(filename)[0] + ".mp3"
                return {
                    "file":     mp3_file,
                    "title":    info.get("title", "audio"),
                    "channel":  info.get("uploader", "?"),
                    "duration": info.get("duration", 0),
                }

        try:
            loop   = asyncio.get_event_loop()
            result = await loop.run_in_executor(None, download)
            mp3    = result["file"]
            secs   = int(result["duration"])
            dur    = f"{secs//60}:{secs%60:02d}"
            size   = f"{os.path.getsize(mp3)/(1024*1024):.1f} MB"

            if os.path.getsize(mp3) / (1024*1024) > 25:
                await msg.edit(embed=discord.Embed(description="❌ ไฟล์ใหญ่เกิน 25MB", color=0xffffff))
                return

            embed = discord.Embed(color=0xffffff)
            embed.set_author(name="▶ YouTube Audio")
            embed.title       = result["title"]
            embed.description = f"by **{result['channel']}**"
            embed.add_field(name="🎵 Format",   value="MP3",     inline=True)
            embed.add_field(name="⏱ Duration", value=dur,       inline=True)
            embed.add_field(name="💾 Size",     value=size,      inline=True)
            embed.set_footer(text="ดาวน์โหลดสำเร็จ ✅")
            embed.timestamp = discord.utils.utcnow()

            await msg.edit(embed=embed)
            await message.channel.send(file=discord.File(mp3))

        except Exception as e:
            await msg.edit(embed=discord.Embed(description=f"❌ Error: {e}", color=0xffffff))

    # !kiku รวม intro + song แล้วปรับ speed x1.12
    elif message.content.lower() == "!kiku":
        if len(message.attachments) != 2:
            await message.channel.send("❌ แนบไฟล์ `.ogg` 2 ไฟล์ด้วยนะครับ\nไฟล์แรก = Intro / ไฟล์สอง = เพลง")
            return

        # เช็คนามสกุล
        for att in message.attachments:
            if not att.filename.lower().endswith(".ogg"):
                await message.channel.send(f"❌ ไฟล์ `{att.filename}` ต้องเป็น `.ogg` เท่านั้นครับ")
                return

        embed = discord.Embed(description="⏳ กำลังรวมไฟล์และปรับ Speed x1.12...", color=0xffffff)
        msg = await message.channel.send(embed=embed)

        os.makedirs("downloads", exist_ok=True)

        try:
            # ดาวน์โหลดไฟล์ที่แนบมา
            intro_path  = f"downloads/intro_{message.id}.ogg"
            song_path   = f"downloads/song_{message.id}.ogg"
            output_path = f"downloads/output_{message.id}.ogg"

            await message.attachments[0].save(intro_path)
            await message.attachments[1].save(song_path)

            # ประมวลผล
            success = await process_audio(intro_path, song_path, output_path)

            if not success or not os.path.exists(output_path):
                await msg.edit(embed=discord.Embed(description="❌ รวมไฟล์ไม่สำเร็จ ลองใหม่ครับ", color=0xffffff))
                return

            size = f"{os.path.getsize(output_path)/(1024*1024):.1f} MB"

            embed = discord.Embed(color=0xffffff)
            embed.set_author(name="🎵 Kiku Processor")
            embed.title       = "รวมไฟล์สำเร็จ!"
            embed.description = "Intro + เพลง • Speed x1.12"
            embed.add_field(name="💾 Size",   value=size,   inline=True)
            embed.add_field(name="🎵 Format", value="OGG",  inline=True)
            embed.add_field(name="⚡ Speed",  value="x1.12", inline=True)
            embed.set_footer(text="ดำเนินการสำเร็จ ✅")
            embed.timestamp = discord.utils.utcnow()

            await msg.edit(embed=embed)
            await message.channel.send(file=discord.File(output_path))

        except Exception as e:
            await msg.edit(embed=discord.Embed(description=f"❌ Error: {e}", color=0xffffff))

@client.event
async def on_ready():
    print(f"[BOT] ออนไลน์: {client.user}")

client.run(BOT_TOKEN)
