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
