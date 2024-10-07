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
# Telethon: A library used to interact with the Telegram API, capable of handling messages and sending messages
pip install telethon
```
* Write code to list the chat_id of channels and groups.
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
## Step 2 : Get your okx API
* Visit https://www.okx.com/zh-hant/account/my-api.
* After logging into your OKX account and applying for the API, you will receive a set of API_KEY and SECRET_KEY. The PASSPHRASE is the password you created when setting up the API.
```
API_KEY = 'API_KEY'
SECRET_KEY = 'SECRET_KEY'
PASSPHRASE = 'PASSPHRASE'
```
## Step 3: Use the TG_Channel_OKX
### Library Imports
```
import ccxt
import re
from telethon import TelegramClient, events
```
* ccxt: A library that provides a unified API for various cryptocurrency exchanges, allowing access to market data and order placement.
```
pip install ccxt
```
### Initialize OKX Exchange
```
okx = ccxt.okx({
    'apiKey': API_KEY,
    'secret': SECRET_KEY,
    'password': PASSPHRASE,
    'options': {'defaultType': 'swap'},  # Set to 'future' or 'spot' as needed
})
```
* This initializes an instance of the OKX exchange for later market operations.
### Initialize Telegram Client
```
client = TelegramClient('session_name', api_id, api_hash)
```
* Creates an instance of the Telegram client to connect to the Telegram API.
### Get Market Precision
```
def get_market_precision(symbol):
    markets = okx.fetch_markets()
    for market in markets:
        if market['symbol'] == symbol:
            return market['precision']
    return None
```
* This function retrieves the market precision for a specific trading pair, helping ensure that orders are placed with the correct precision required by the exchange.
### Parse Telegram Message Format
```
def parse_message(text):
    pattern = r"coin：(\w+)\s*direction：(\w+（accont(\d+(?:\.\d{1,5})?)%SL）)\s*entryprice：(\d+(?:\.\d{1,5})?)-(\d+(?:\.\d{1,5})?)\s*TP：\s*1\)：(\d+(?:\.\d{1,5})?)\s*2\)：(\d+(?:\.\d{1,5})?)\s*3\)：(\d+(?:\.\d{1,5})?)\s*SL：(\d+(?:\.\d{1,5})?)\s*SL_ratio：(\d+(?:\.\d{1,5})?)"
    match = re.match(pattern, text, re.DOTALL)
    if match:
        coin, direction, percenttake, entry_low, entry_high, take_profit1, take_profit2, take_profit3, stop_loss_price, stop_loss_ratio = match.groups()
        return coin, direction, float(percenttake), float(entry_low), float(entry_high), float(take_profit1), float(take_profit2), float(take_profit3), float(stop_loss_price), float(stop_loss_ratio)
    return None
```
* This function uses regular expressions to parse the received message from Telegram, extracting trading-related information.
### Place Order Function
```
def place_order(symbol, entry_price, direction, take_profit1, take_profit2, take_profit3, stop_loss_price, stop_loss_ratio, percenttake):
    # Logic for calculating risk and position size...
    # Order placement logic...
```
* This function handles placing the order, calculating position size based on the extracted information, and setting take profit and stop loss orders based on the trade direction (long or short).
### Handle Telegram Messages
```
async def main():
    async with TelegramClient('session_name', api_id, api_hash) as client:
        @client.on(events.NewMessage(chats=channel_id))
        async def handler(event):
            # Handle new incoming messages...
    await client.start()
    await client.run_until_disconnected()
```
* This asynchronous function listens for new messages in the specified Telegram channel. When a new message arrives, it parses the message and calls the place_order function to execute the trade.
### Run the Client
```
if __name__ == '__main__':
    import asyncio
    asyncio.run(main())
```
* This ensures that the main function is called if the script is run directly, starting the Telegram client and beginning to listen for messages.
