import discord
from discord.ext import commands, tasks
import requests

# 1. ตั้งค่าพื้นฐาน
# นำ Token ใหม่ที่ได้จากการ Reset มาใส่ตรงนี้
TOKEN = 'ใส่_TOKEN_ใหม่ของคุณ_ที่นี่' 
# ใส่ ID ห้องแชท (เปิด Developer Mode ใน Discord แล้วคลิกขวาที่ห้อง > Copy ID)
CHANNEL_ID = 123456789012345678 

intents = discord.Intents.default()
intents.message_content = True
bot = commands.Bot(command_prefix='!', intents=intents)

last_seen_ids = set()

# 2. ฟังก์ชันดึงข้อมูลจาก Roblox API
def fetch_data():
    url = "https://apis.roblox.com/creator-marketplace-api/v2/items/search"
    params = {"category": "Audio", "keyword": "distrokid", "limit": 30}
    try:
        response = requests.get(url, params=params, timeout=10)
        if response.status_code == 200:
            return response.json().get("items", [])
    except Exception as e:
        print(f"เกิดข้อผิดพลาดในการเชื่อมต่อ: {e}")
    return []

# 3. ลูปตรวจจับเพลงใหม่ทุกๆ 60 วินาที
@tasks.loop(seconds=60)
async def check_songs():
    global last_seen_ids
    songs = fetch_data()
    
    channel = bot.get_channel(CHANNEL_ID)
    if not channel: return

    for song in songs:
        audio_id = str(song['id'])
        if audio_id not in last_seen_ids:
            last_seen_ids.add(audio_id)
            # ถ้าไม่ใช่การรันครั้งแรก ให้ส่งแจ้งเตือน
            if len(last_seen_ids) > 30: # ป้องกันแจ้งเตือนรัวตอนเปิดบอทใหม่
                await channel.send(f"🚨 **เพลงใหม่เข้าหน้าร้าน!**\n🎵 ชื่อ: {song['name']}\n🆔 ID: `{audio_id}`")
                print(f"พบเพลงใหม่: {song['name']}")

@bot.event
async def on_ready():
    print(f'✅ บอท {bot.user} เริ่มทำงานแล้ว! กำลังเฝ้าร้าน...')
    # โหลดค่าเริ่มต้นป้องกันแจ้งเตือนของเก่า
    initial = fetch_data()
    for s in initial:
        last_seen_ids.add(str(s['id']))
    check_songs.start()

bot.run(TOKEN)

