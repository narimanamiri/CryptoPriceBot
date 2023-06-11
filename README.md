# CryptoPriceBot
this bot sends top 5 crypto currency's price every 5 minutes.
this Python script that creates a Telegram bot using the `python-telegram-bot` library to output the top 5 cryptocurrency prices and changes based on market cap every 5 minutes:

```python
import requests
from bs4 import BeautifulSoup
import telegram
import time

# Set up Telegram bot
bot_token = 'YOUR_BOT_TOKEN_HERE'
bot_chatID = 'YOUR_CHAT_ID_HERE'
bot = telegram.Bot(token=bot_token)

def get_top_5():
    # Get top 100 cryptocurrencies by market cap from CoinMarketCap
    url = 'https://coinmarketcap.com/'
    response = requests.get(url)
    soup = BeautifulSoup(response.content, 'html.parser')
    table = soup.find_all('table')[2]
    rows = table.find_all('tr')[1:101]

    # Extract name, symbol, price, and change of top 100 cryptocurrencies
    cryptos = []
    for row in rows:
        name = row.find_all('td')[2].text.strip()
        symbol = row.find_all('td')[3].text.strip()
        price = row.find_all('td')[4].text.strip()
        change = row.find_all('td')[7].text.strip()
        cryptos.append({'name': name, 'symbol': symbol, 'price': price, 'change': change})

    # Sort list of cryptocurrencies by market cap
    cryptos = sorted(cryptos, key=lambda k: float(k['price'].replace(',', '')), reverse=True)

    # Get top 5 cryptocurrencies by market cap
    top_5 = cryptos[:5]

    # Format message with top 5 cryptocurrencies, prices, and changes
    message = 'Top 5 cryptocurrencies by market cap:\n'
    for crypto in top_5:
        message += f'{crypto["name"]} ({crypto["symbol"]}): ${crypto["price"]} ({crypto["change"]}%)\n'

    return message

# Loop to send message every 5 minutes
while True:
    message = get_top_5()
    bot.send_message(chat_id=bot_chatID, text=message)
    time.sleep(300)
```

Here's how the script works:

1. The script sets up a Telegram bot using the `telegram.Bot` class from the `python-telegram-bot` library, and sets the bot token and chat ID using the `bot_token` and `bot_chatID` variables.
2. The script defines a function `get_top_5` that gets the top 5 cryptocurrencies by market cap from the CoinMarketCap website, formats a message string with the top 5 cryptocurrencies, prices, and changes, and returns the message string.
3. The script enters a `while` loop that calls the `get_top_5` function, sends the message to the Telegram chat using the `send_message` method of the `Bot` object, and waits for 5 minutes using the `time.sleep` function.
4. The loop continues indefinitely, sending a message every 5 minutes.

Note that this script assumes that you have created a Telegram bot and obtained a bot token and chat ID. You can create a bot and obtain a token using the BotFather bot in Telegram, and obtain your chat ID by sending a message to your bot and querying the `chat.id` value in the JSON response. Additionally, note that the script may not work correctly if the CoinMarketCap website changes its HTML structure or if the Telegram API changes. In those cases, you may need to modify the script to adapt to the changes.
