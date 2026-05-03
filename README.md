import discord
import asyncio
import yt_dlp
import os

# ==========================================
BOT_TOKEN = "Token_ของคุณ"
# ==========================================

intents = discord.Intents.default()
intents.message_content = True
client = discord.Client(intents=intents)

def download_audio(url):
    ydl_opts = {
        "format":         "bestaudio/best",
        "outtmpl":        "downloads/%(title)s.%(ext)s",
        "postprocessors": [{
            "key":              "FFmpegExtractAudio",
            "preferredcodec":   "mp3",
            "preferredquality": "192",
        }],
        "quiet": True,
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

def fmt_duration(secs):
    m, s = divmod(int(secs), 60)
    return f"{m}:{s:02d}"

def fmt_size(path):
    mb = os.path.getsize(path) / (1024 * 1024)
    return f"{mb:.1f} MB"

@client.event
async def on_message(message):
    if message.author.bot:
        return

    if message.content.lower().startswith("!dl "):
        url = message.content[4:].strip()
        if not url:
            await message.channel.send("❌ ใส่ลิ้ง YouTube ด้วยนะครับ\nเช่น `!dl https://youtube.com/watch?v=xxxxx`")
            return

        # สร้างโฟลเดอร์ downloads ถ้าไม่มี
        os.makedirs("downloads", exist_ok=True)

        # Embed กำลังดาวน์โหลด
        embed = discord.Embed(
            description="⏳ กำลังดาวน์โหลด...",
            color=0xffffff
        )
        embed.set_footer(text="DL BOT • Audio Downloader")
        msg = await message.channel.send(embed=embed)

        try:
            loop   = asyncio.get_event_loop()
            result = await loop.run_in_executor(None, download_audio, url)

            mp3_file = result["file"]
            title    = result["title"]
            channel  = result["channel"]
            duration = fmt_duration(result["duration"])

            # เช็คขนาดไฟล์
            size_mb = os.path.getsize(mp3_file) / (1024 * 1024)
            if size_mb > 25:
                embed = discord.Embed(
                    description=f"❌ ไฟล์ใหญ่เกิน 25MB ({size_mb:.1f}MB) ส่งไม่ได้ครับ",
                    color=0xffffff
                )
                await msg.edit(embed=embed)
                return

            # Embed สำเร็จ
            embed = discord.Embed(color=0xffffff)
            embed.set_author(name="▶ YouTube Audio")
            embed.title       = title
            embed.description = f"by **{channel}**"
            embed.add_field(name="🎵 Format",   value="MP3",         inline=True)
            embed.add_field(name="📻 Quality",  value="192 kbps",    inline=True)
            embed.add_field(name="⏱ Duration", value=duration,       inline=True)
            embed.add_field(name="💾 Size",     value=fmt_size(mp3_file), inline=True)
            embed.set_footer(text="DL BOT • ดาวน์โหลดสำเร็จ ✅")
            embed.timestamp = discord.utils.utcnow()

            await msg.edit(embed=embed)
            await message.channel.send(
                file=discord.File(mp3_file)
            )

        except Exception as e:
            embed = discord.Embed(
                description=f"❌ เกิดข้อผิดพลาด: {e}",
                color=0xffffff
            )
            await msg.edit(embed=embed)

@client.event
async def on_ready():
    print(f"[BOT] ออนไลน์: {client.user}")

client.run(BOT_TOKEN)
