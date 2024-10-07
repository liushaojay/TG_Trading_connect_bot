# TG_Trading_connect_bot
This bot can analyze specific messages in Telegram channels and then transmit this data to the exchange for order placement. It can be used in various trading groups to automatically track the orders of different traders.
## Step 1 : Get your TG API
* Visit https://my.telegram.org/auth.
* Log in to your Telegram account.
* Click on "API Development Tools" to create a new application and obtain your api_id and api_hash.
```
api_id = 'api_id'  
api_hash = 'api_hash'  
```
* obtain the chat_id of a Telegram channel or group.
```
pip install telethon
```
Write code to list the chat_id of channels and groups.
```
from telethon import TelegramClient

# Replace with your api_id and api_hash
api_id = 'api_id'  
api_hash = 'api_hash'  

# Initialize Telegram client
client = TelegramClient('session_name', api_id, api_hash)

async def list_channels_and_groups():
    await client.start()
    dialogs = await client.get_dialogs()
    for dialog in dialogs:
        print(f'Name: {dialog.name}, ID: {dialog.id}, Type: {"Channel" if dialog.is_channel else "Group"}')

with client:
    client.loop.run_until_complete(list_channels_and_groups())
```
When you have obtained the chat_id, you can use it in your main program.
## Step 2 : Get your okx API
* Visit https://www.okx.com/zh-hant/account/my-api.
* After logging into your OKX account and applying for the API, you will receive a set of API_KEY and SECRET_KEY. The PASSPHRASE is the password you created when setting up the API.
```
API_KEY = 'API_KEY'
SECRET_KEY = 'SECRET_KEY'
PASSPHRASE = 'PASSPHRASE'
```
