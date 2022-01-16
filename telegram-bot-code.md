# Telegram-Bot-Binance-python
# this bot can  show you top 10 of gainers and lossers in crypto

import pandas as pd

from binance.client import Client

from pprint import pprint


def get_data(param):

    api_key = 'gcJLtOs6tZYTOBEyIYY0JZBDagmFMkc5SY5T8R792cCUgBn7YCLfG7pOJZG3J36x'
    
    api_secret = 'EYyaqoqRyPxozdDQQmlL7xQiHaqDIxgGAav68UoYAznTAOI6darEG132kavhI3Vh'

    client = Client(api_key, api_secret)

    dt = client.get_ticker()

    dt = pd.DataFrame(dt)

    dt["priceChangePercent"] = dt["priceChangePercent"].astype(str).astype(float)

    dt.fillna(0, inplace=True)

    # dt = dt[dt['priceChangePercent'].between(0, 10)]

    dt = dt.sort_values(by=["priceChangePercent"], ascending=False, ignore_index=True, )
    
    if param == 'gainer':
    
        gainer = dt.head(10)
        
        return gainer
        
    loser = dt.tail(10)
    
    return loser


import telegram

from telegram import (replykeyboardmarkup, replykeyboardremove, ReplyKeyboardMarkup, update)

from telegram.ext.updater import Updater

from telegram.update import Update

from telegram.ext.callbackcontext import CallbackContext

from telegram.ext.commandhandler import CommandHandler

from telegram.ext.messagehandler import MessageHandler

from telegram.ext.filters import Filters


updater = Updater("PASTE HEAR YOUR BOT TOKEN", use_context=True)

#You Can Change Reply Text and Replace Your Words.

def start(update: Update, context: CallbackContext):

    update.message.reply_text(
        "سلام"
        "\n"
        "برای دیدن لیست ۱۰ ارزی که امروز بیشترین سود رو دادند روی گزینه زیر کلیک کنید:"
        "\n"
        "/gainer"
        '\n'
        "برای دیدن لیست ۱۰ ارزی که بیشترین ضرر رو دادند روی کلیک کنید:"
        "\n"
        "/loser")


def gainer(update: Update, context: CallbackContext):

    update.message.reply_text("کمی صبر کنید...")
    
    gainer = get_data('gainer')
    
    gainer = gainer[["symbol", "priceChange", "priceChangePercent"]]
    
    for index, row in gainer.iterrows():
    
        update.message.reply_text(row.to_string())

        print(gainer)
    update.message.reply_text("برای دیدن لیست ۱۰ ارزی که بیشرین ضرر رو دادند روی /loser کلیک کنید.")


def loser(update: Update, context: CallbackContext):

    update.message.reply_text("کمی صبر کنید...")
    loser = get_data('')
    loser = loser[["symbol", "priceChange", "priceChangePercent"]]

    for index, row in loser.iterrows():
    
        update.message.reply_text(row.to_string())

        print(loser)
        
    update.message.reply_text("برای برگشتن به منو روی /text کلیک کنید.")


updater.dispatcher.add_handler(CommandHandler('start', start))

updater.dispatcher.add_handler(CommandHandler('gainer', gainer))

updater.dispatcher.add_handler(CommandHandler('loser', loser))

updater.dispatcher.add_handler(CommandHandler('text', text))

updater.start_polling()

#this bot will work when your online and your code is running so for work better you can add your api, you shoud buy it
