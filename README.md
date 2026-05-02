import discord
import requests
import asyncio
import time

# ==========================================
BOT_TOKEN      = "Token_ของคุณ"
CHANNEL_ID     = 123456789012345678
CHECK_INTERVAL = 60
ROBLO_COOKIE   = "_|WARNING:-DO-NOT-SHARE-THIS...ใส่ Cookie ของคุณตรงนี้"
# ==========================================

HEADERS = {
    "Cookie": f".ROBLOSECURITY={ROBLO_COOKIE}",
    "User-Agent": "Mozilla/5.0",
}

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
            },
            headers=HEADERS,
            timeout=10
        )
        r.raise_for_status()
        return r.json().get("data", [])
    except Exception as e:
        print(f"[ERROR] fetch_audio_list: {e}")
        return []

def fetch_audio_detail(asset_id):
    try:
        r = requests.get(
            f"https://apis.roblox.com/toolbox-service/v1/items/details?assetIds={asset_id}",
            headers=HEADERS,
            timeout=10
        )
        data = r.json().get("data", [])
        if data:
            asset = data[0].get("asset", {})
            secs  = int(asset.get("duration", 0))
            return {
                "duration": f"{secs//60}:{secs%60:02d}" if secs else "N/A",
                "format":   asset.get("audioFormat", "N/A"),
            }
    except:
        pass
    return {"duration": "N/A", "format": "N/A"}

def fetch_thumbnail(asset_id):
    try:
        r = requests.get(
            "https://thumbnails.roblox.com/v1/assets",
            params={"assetIds": asset_id, "size": "150x150", "format": "Png"},
            headers=HEADERS,
            timeout=10
        )
        data = r.json().get("data", [])
        if data:
            return data[0].get("imageUrl", "")
    except:
        pass
    return ""

def build_embed(item, detail, thumb_url):
    asset_id     = item["id"]
    name         = item.get("name", "ไม่มีชื่อ")
    creator_name = item.get("creatorName", "?")

    embed = discord.Embed(color=0xffffff)
    embed.set_author(name=f"👑 New Audio from {creator_name}")
    embed.title       = name
    embed.description = f"by **{creator_name}**"
    embed.url         = f"https://www.roblox.com/catalog/{asset_id}"

    if thumb_url:
        embed.set_thumbnail(url=thumb_url)

    embed.add_field(name="🆔 Audio ID",  value=f"`{asset_id}`",          inline=True)
    embed.add_field(name="⏱ Duration",  value=detail["duration"],        inline=True)
    embed.add_field(name="🎵 Format",   value=detail["format"],          inline=True)
    embed.add_field(name="🔗 Link",     value=f"[View on Roblox](https://www.roblox.com/catalog/{asset_id})", inline=False)
    embed.timestamp = discord.utils.utcnow()
    return embed

@client.event
async def on_message(message):
    if message.author.bot:
        return
    if message.content.lower() == "/check":
        await message.channel.send("🔍 กำลังเช็คเพลงใหม่...")
        items = fetch_audio_list()
        if not items:
            await message.channel.send("❌ ดึงข้อมูลไม่ได้")
            return
        for item in items:
            detail = fetch_audio_detail(item["id"])
            thumb  = fetch_thumbnail(item["id"])
            await message.channel.send(embed=build_embed(item, detail, thumb))
            await asyncio.sleep(0.5)
        await message.channel.send(f"✅ เจอทั้งหมด **{len(items)}** เพลง!")

async def monitor_loop():
    await client.wait_until_ready()
    channel = client.get_channel(CHANNEL_ID)
    if not channel:
        print("[ERROR] ไม่เจอ Channel")
        return

    for item in fetch_audio_list():
        seen_ids.add(item["id"])
    print(f"[READY] โหลดเพลงเก่า {len(seen_ids)} รายการ")

    while not client.is_closed():
        await asyncio.sleep(CHECK_INTERVAL)
        print(f"[CHECK] {time.strftime('%H:%M:%S')}")
        for item in fetch_audio_list():
            aid = item["id"]
            if aid in seen_ids:
                continue
            seen_ids.add(aid)
            detail = fetch_audio_detail(aid)
            thumb  = fetch_thumbnail(aid)
            await channel.send(embed=build_embed(item, detail, thumb))
            print(f"[NEW] {item.get('name')} | ID: {aid}")

@client.event
async def on_ready():
    print(f"[BOT] ออนไลน์: {client.user}")
    client.loop.create_task(monitor_loop())

client.run(BOT_TOKEN)
