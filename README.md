import discord
import requests
import time
import asyncio

# ตั้งค่า
BOT_TOKEN = "TOKEN_ของคุณ"
CHANNEL_ID = 123456789  # ID ช่องที่จะส่งข้อมูล
CHECK_INTERVAL = 60     # เช็คทุก 60 วินาที

intents = discord.Intents.default()
client = discord.Client(intents=intents)
seen_ids = set()

def get_new_audio():
    url = "https://catalog.roblox.com/v1/search/items"
    params = {
        "category": "Audio",
        "sortType": "3",
        "limit": 30,
        "salesTypeFilter": "1"
    }
    try:
        res = requests.get(url, params=params, timeout=10)
        res.raise_for_status()
        return res.json().get("data", [])
    except Exception as e:
        print(f"[Error] {e}")
        return []

async def check_loop():
    await client.wait_until_ready()
    channel = client.get_channel(CHANNEL_ID)

    # โหลดครั้งแรก
    first_batch = get_new_audio()
    for item in first_batch:
        seen_ids.add(item["id"])
    print(f"✅ โหลดข้อมูลเริ่มต้น {len(seen_ids)} รายการ")

    while not client.is_closed():
        await asyncio.sleep(CHECK_INTERVAL)
        items = get_new_audio()

        for item in items:
            if item["id"] not in seen_ids:
                seen_ids.add(item["id"])

                audio_id = item["id"]
                name = item.get("name", "ไม่มีชื่อ")
                creator = item.get("creatorName", "?")

                # ดึงข้อมูลเพิ่มเติม
                detail_url = f"https://apis.roblox.com/toolbox-service/v1/items/details?assetIds={audio_id}"
                duration = "N/A"
                try:
                    detail = requests.get(detail_url, timeout=10).json()
                    duration = detail["data"][0].get("asset", {}).get("duration", "N/A")
                    if duration != "N/A":
                        m, s = divmod(int(duration), 60)
                        duration = f"{m}:{s:02d}"
                except:
                    pass

                # สร้าง Embed สวยๆ
                embed = discord.Embed(
                    title=f"🎵 New Audio from {creator}",
                    color=0x9B59B6
                )
                embed.add_field(name="📛 ชื่อเพลง", value=name, inline=False)
                embed.add_field(name="🆔 Audio ID", value=str(audio_id), inline=True)
                embed.add_field(name="⏱ Duration", value=duration, inline=True)
                embed.add_field(name="🔗 Links", value=f"[View on Roblox](https://www.roblox.com/catalog/{audio_id})", inline=False)
                embed.set_footer(text=f"Roblox Audio Monitor")

                await channel.send(embed=embed)
                print(f"[NEW] {name} | ID: {audio_id}")

@client.event
async def on_ready():
    print(f"✅ บอทออนไลน์: {client.user}")
    client.loop.create_task(check_loop())

client.run(BOT_TOKEN)
