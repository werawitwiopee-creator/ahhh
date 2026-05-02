import discord
import requests
import asyncio
import time

# ==========================================
#              ตั้งค่าตรงนี้
# ==========================================
BOT_TOKEN      = "Discord_Bot_Token_ของคุณ"
CHANNEL_ID     = 123456789012345678
CHECK_INTERVAL = 60
# ==========================================

intents = discord.Intents.default()
client  = discord.Client(intents=intents)
seen_ids: set = set()

# ---------- ดึงรายการเพลงใหม่ ----------
def fetch_audio_list() -> list:
    url = "https://catalog.roblox.com/v1/search/items"
    params = {
        "category":        "Audio",
        "sortType":        "3",
        "limit":           "30",
        "salesTypeFilter": "1",
    }
    try:
        r = requests.get(url, params=params, timeout=10)
        r.raise_for_status()
        return r.json().get("data", [])
    except Exception as e:
        print(f"[ERROR] fetch_audio_list: {e}")
        return []

# ---------- ดึง Thumbnail ----------
def fetch_thumbnail(asset_id: int) -> str:
    url = "https://thumbnails.roblox.com/v1/assets"
    params = {"assetIds": asset_id, "size": "150x150", "format": "Png"}
    try:
        r = requests.get(url, params=params, timeout=10)
        r.raise_for_status()
        data = r.json().get("data", [])
        if data:
            return data[0].get("imageUrl", "")
    except Exception as e:
        print(f"[ERROR] fetch_thumbnail: {e}")
    return ""

# ---------- สร้าง Embed ----------
def build_embed(item: dict, thumb_url: str) -> discord.Embed:
    asset_id     = item["id"]
    name         = item.get("name", "ไม่มีชื่อ")
    creator_name = item.get("creatorName", "?")

    embed = discord.Embed(color=0xffffff)
    embed.set_author(name=f"👑  New Audio from {creator_name}")
    embed.title       = name
    embed.description = f"by **{creator_name}**"
    embed.url         = f"https://www.roblox.com/catalog/{asset_id}"

    if thumb_url:
        embed.set_thumbnail(url=thumb_url)

    embed.add_field(name="🆔  Audio ID", value=f"`{asset_id}`", inline=True)
    embed.add_field(name="🔗  Link",
                    value=f"[View on Roblox](https://www.roblox.com/catalog/{asset_id})",
                    inline=True)

    embed.set_footer(text=f"@{creator_name}  •  Audio Monitor",
                     icon_url="https://www.roblox.com/favicon.ico")
    embed.timestamp = discord.utils.utcnow()
    return embed

# ---------- Loop หลัก ----------
async def monitor_loop():
    await client.wait_until_ready()
    channel = client.get_channel(CHANNEL_ID)

    if channel is None:
        print(f"[ERROR] ไม่เจอ Channel ID: {CHANNEL_ID}")
        return

    # โหลดเพลงเก่าก่อน
    for item in fetch_audio_list():
        seen_ids.add(item["id"])
    print(f"[READY] โหลดเพลงเก่า {len(seen_ids)} รายการแล้ว")

    while not client.is_closed():
        await asyncio.sleep(CHECK_INTERVAL)
        print(f"[CHECK] {time.strftime('%H:%M:%S')} กำลังเช็ค...")

        for item in fetch_audio_list():
            aid = item["id"]
            if aid in seen_ids:
                continue

            seen_ids.add(aid)
            thumb_url = fetch_thumbnail(aid)
            embed     = build_embed(item, thumb_url)

            try:
                await channel.send(embed=embed)
                print(f"[NEW] {item.get('name')} | ID: {aid}")
            except discord.HTTPException as e:
                print(f"[SEND ERROR] {e}")

# ---------- Event ----------
@client.event
async def on_ready():
    print(f"[BOT] ออนไลน์: {client.user}")
    client.loop.create_task(monitor_loop())

# ---------- Run ----------
if __name__ == "__main__":
    client.run(BOT_TOKEN)
