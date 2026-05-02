import discord
from discord.ext import commands
import json
import os

# การตั้งค่าพื้นฐาน
TOKEN = 'ใส่_Discord_Bot_Token_ของคุณตรงนี้'
DB_FILE = 'songs.json'

# โหลดข้อมูลจากไฟล์ JSON
def load_songs():
    if not os.path.exists(DB_FILE):
        return {}
    with open(DB_FILE, 'r', encoding='utf-8') as f:
        return json.load(f)

# บันทึกข้อมูลลงไฟล์ JSON
def save_songs(data):
    with open(DB_FILE, 'w', encoding='utf-8') as f:
        json.dump(data, f, indent=4, ensure_ascii=False)

intents = discord.Intents.default()
intents.message_content = True
bot = commands.Bot(command_prefix='!', intents=intents)

@bot.event
async def on_ready():
    print(f'✅ บอท {bot.user} ออนไลน์แล้ว!')

# คำสั่งเพิ่มเพลง: !add ชื่อเพลง IDเพลง
@bot.command()
async def add(ctx, name: str, song_id: str):
    data = load_songs()
    data[name.lower()] = song_id
    save_songs(data)
    await ctx.send(f'💾 บันทึกเพลง **{name}** (ID: `{song_id}`) เรียบร้อยแล้ว!')

# คำสั่งค้นหา: !search ชื่อเพลง
@bot.command()
async def search(ctx, name: str):
    data = load_songs()
    song_id = data.get(name.lower())
    if song_id:
        await ctx.send(f'🎵 เจอเพลงที่คุณหา!\n**ชื่อ:** {name}\n**ID:** `{song_id}`')
    else:
        await ctx.send('❌ ไม่พบเพลงนี้ในคลังครับ')

# คำสั่งดูรายการทั้งหมด: !list
@bot.command()
async def list(ctx):
    data = load_songs()
    if not data:
        await ctx.send('📭 ยังไม่มีเพลงในคลังเลย!')
        return
    
    msg = "**📚 รายชื่อเพลงทั้งหมด:**\n"
    for name, song_id in data.items():
        msg += f"- {name.capitalize()}: `{song_id}`\n"
    await ctx.send(msg)

# คำสั่งลบเพลง: !remove ชื่อเพลง
@bot.command()
async def remove(ctx, name: str):
    data = load_songs()
    if name.lower() in data:
        del data[name.lower()]
        save_songs(data)
        await ctx.send(f'🗑️ ลบเพลง {name} ออกจากคลังแล้ว!')
    else:
        await ctx.send('❌ ไม่พบเพลงนี้ในรายการ')

bot.run(TOKEN)

