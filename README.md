import discord
import requests
import asyncio
import time

# ==========================================
BOT_TOKEN      = "Token_ของคุณ"
CHANNEL_ID     = 123456789012345678
CHECK_INTERVAL = 60
# ==========================================

intents = discord.Intents.default()
intents.message_content = True
client  = discord.Client(intents=intents)
seen_ids: set = set()

def fetch_audio_list():
    try:
        r = requests.get(
            "https://catalog.roblox.com/v1/search/items",
            params={
                "category":        "Audio",
                "sortType":        "3",
                "limit":           "30",
                "salesTypeFilter": "1",
                "includeNotForSale": "true",
                "keyword":         "",
                "creatorType":     "2",  # Distrokid = uploaded by group
            },
            timeout=10
        )
        r.raise_for_status()
        data = r.json().get("data", [])
        # กรองเฉพาะ Distrokid
        return [i for i in data if "distrokid" in i.get("name", "").lower()
                or i.get("creatorName", "").lower() in ["distrokid", "roblox"]]
    except Exception as e:
        print(f"[ERROR] {e}")
        return []

def fetch_distrokid_audio():
    try:
        # ดึงจาก Toolbox API ตรงๆ
        r = requests.get(
            "https://apis.roblox.com/toolbox-service/v1/catalog",
            params={
                "category":  "Audio",
                "sortType":  "Recently Added",
                "limit":     "30",
                "creatorId": "1",  # Roblox official
            },
            timeout=10
        )
        r.raise_for_status()
        return r.json().get("data", [])
    except Exception as e:
        print(f"[ERROR] {e}")
        return []

def fetch_new_distrokid():
    try:
        r = requests.get(
            "https://catalog.roblox.com/v1/search/items",
            params={
                "category":    "Audio",
                "sortType":    "3",
                "limit":       "30",
                "creatorName": "Distrokid",
            },
            timeout=10
        )
        r.raise_for_status()
        return r.json().get("data", [])
    except Exception as e:
        print(f"[ERROR] {e}")
        return []

def fetch_thumbnail(asset_id):
    try:
        r = requests.get(
            "https://thumbnails.roblox.com/v1/assets",
            params={"assetIds": asset_id, "size": "150x150", "format": "Png"},
            timeout=10
        )
        data = r.json().get("data", [])
        if data:
            return data[0].get("imageUrl", "")
    except:
        pass
    return ""

def build_embed(item, thumb_url):
    asset_id     = item["id"]
    name         = item.get("name", "ไม่มีชื่อ")
    creator_name = item.get("creatorName", "?")

    embed = discord.Embed(color=0xffffff)
    embed.set_author(name=f"👑 New Audio from {creator_name}")
    embed.title       = name
    embed.description = f"by **{creator_name}**"
    embed.url         = f"https://www.roblox.com/catalog/{asset_id}"
    embed.add_field(name="🆔 Audio ID", value=f"`{asset_id}`", inline=True)
    embed.add_field(name="🔗 Link", value=f"[View on Roblox](https://www.roblox.com/catalog/{asset_id})", inline=True)
    embed.timestamp = discord.utils.utcnow()

    if thumb_url:
        embed.set_thumbnail(url=thumb_url)

    return embed

@client.event
async def on_message(message):
    if message.author.bot:
        return
    if message.content.lower() == "/check":
        await message.channel.send("🔍 กำลังเช็คเพลง Distrokid ใหม่...")
        items = fetch_new_distrokid()
        if not items:
            await message.channel.send("❌ ดึงข้อมูลไม่ได้")
            return
        for item in items:
            thumb = fetch_thumbnail(item["id"])
            await message.channel.send(embed=build_embed(item, thumb))
            await asyncio.sleep(0.5)
        await message.channel.send(f"✅ เจอทั้งหมด **{len(items)}** เพลง!")

async def monitor_loop():
    await client.wait_until_ready()
    channel = client.get_channel(CHANNEL_ID)
    if not channel:
        print(f"[ERROR] ไม่เจอ Channel")
        return

    for item in fetch_new_distrokid():
        seen_ids.add(item["id"])
    print(f"[READY] โหลดเพลงเก่า {len(seen_ids)} รายการ")

    while not client.is_closed():
        await asyncio.sleep(CHECK_INTERVAL)
        print(f"[CHECK] {time.strftime('%H:%M:%S')}")
        for item in fetch_new_distrokid():
            aid = item["id"]
            if aid in seen_ids:
                continue
            seen_ids.add(aid)
            thumb = fetch_thumbnail(aid)
            await channel.send(embed=build_embed(item, thumb))
            print(f"[NEW] {item.get('name')} | ID: {aid}")

@client.event
async def on_ready():
    print(f"[BOT] ออนไลน์: {client.user}")
    client.loop.create_task(monitor_loop())

client.run(BOT_TOKEN)
