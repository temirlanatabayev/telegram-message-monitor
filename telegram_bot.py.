
import logging
import os
import re

from telethon import TelegramClient, events

logging.basicConfig(level=logging.INFO)

API_ID = int(os.environ["TG_API_ID"])
API_HASH = os.environ["TG_API_HASH"]
GROUP_ID = int(os.environ["TG_GROUP_ID"])

client = TelegramClient("bar_session", API_ID, API_HASH)

PROCESSED_FILE = "processed_messages.txt"

if os.path.exists(PROCESSED_FILE):
    with open(PROCESSED_FILE, "r", encoding="utf-8") as f:
        processed_messages = set(f.read().splitlines())
else:
    processed_messages = set()

BARTENDERS_PATTERN = re.compile(r"бармены\s+нужны", re.IGNORECASE)


@client.on(events.NewMessage(chats=GROUP_ID))
async def new_message(event):
    message_id = f"{event.chat_id}:{event.message.id}"

    if message_id in processed_messages:
        return

    text = event.raw_text or ""

    if not BARTENDERS_PATTERN.search(text):
        return

    await event.reply("+")

    processed_messages.add(message_id)

    with open(PROCESSED_FILE, "a", encoding="utf-8") as f:
        f.write(message_id + "\n")

    logging.info("Plus sent | Message ID: %s", message_id)


async def main():
    me = await client.get_me()
    logging.info("Authorized as %s", me.first_name)
    logging.info("Monitoring started")


if __name__ == "__main__":
    with client:
        client.loop.run_until_complete(main())
        client.run_until_disconnected()
