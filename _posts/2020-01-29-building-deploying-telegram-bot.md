---
layout: post
title: How I build and deploy Telegram conversation bot
---

I want to build something "futuristic" while learning something new. So, I build a Telegram conversation bot. View [full code here](https://github.com/devennn/tonduyutung-bot)

I called it Tonduyutung. Don't mind the name. It is a [dusun language for slow loris](https://glosbe.com/dtp/en/tonduyutung). 

I develop the bot using Anaconda on Windows 10. Start by installing required dependencies

- [python-telegram-bot](https://python-telegram-bot.readthedocs.io/en/stable/)

```
pip install python-telegram-bot
```

- [chatterbot](https://chatterbot.readthedocs.io/en/stable/). 

```
pip install chatterbot
pip install chatterbot-corpus
```

## Create Telegram bot

To create telegram bot, start by registering it from @botfather from Telegram app itself. Some important things need to be set up are:

- Username
- Name

After setting them up, botfather will give a Telegram token. For me, i save it in a file called token.txt


## Chatbot code

In main.py

```python
from telegram.ext import Updater, CommandHandler
from features import *

def get_token():
    with open('token.txt', 'r') as f:
        token = f.read()
        token = token.strip('\n')
    return token

def main():
    # Get token and start program
    token = get_token()
    updater = Updater(token, use_context=True)
    dp = updater.dispatcher

    # Add Feature handler
    conv_handler_chat1 = set_chat_v1()
    dp.add_handler(conv_handler_chat1)

    # Polling and Idle
    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
```

In features folder, I have 2 files for now:
- \_\_init\_\_.py
- chat_v1.py
I turn the features folder to package so that I can add features in the future easily.

In \_\_init\_\_.py
```python
from .chat_v1 import set_chat_v1
```

In chat_v1.py
```python
import re
from telegram.ext import (MessageHandler, Filters, ConversationHandler,
                            CommandHandler)
from chatterbot import ChatBot
from chatterbot.trainers import ChatterBotCorpusTrainer

from logs.set_logger import set_logger
logger = set_logger(name=__name__, level='Info')

chatbot = ChatBot('Ron Obvious')
# Create a new trainer for the chatbot
trainer = ChatterBotCorpusTrainer(chatbot)
# Train the chatbot based on the english corpus
trainer.train("chatterbot.corpus.english")

RESPONSE = range(1)

def start_chat_v1(update, context):
    logger.info('Sending start message')
    update.message.reply_text(
        'You are now chatting with an AI version 1\n\n'
        'INFORMATION:\n'
        '- Build based on https://chatterbot.readthedocs.io/ backend\n'
        # English corpus https://github.com/gunthercox/chatterbot-corpus/
        '- Trained using English corpus\n\n'
        'You can chat with me as usual. '
        'Send /quit_chat to stop me.\n\n'
    )
    return RESPONSE

def cancel_chat_v1(update, context):
    logger.info("User {} quitting the conversation.".format(
        update.message.from_user.first_name
    ))
    update.message.reply_text('Goodbye! Talk later')
    return ConversationHandler.END

def get_bot_response(update, context):
    logger.info('Start /chat_v1')
    text = update.message.text
    logger.info('Input: {}'.format(text))
    response = str(chatbot.get_response(text))
    chat_id = update.message.chat_id
    logger.info('Response: {} , type: {}'.format(response, type(response)))
    context.bot.send_message(chat_id=chat_id, text=response)
    logger.info( 'Reply sent to {}'.format(
        update.message.from_user.first_name
    ))


def set_chat_v1():
    conv_handler = ConversationHandler(
        entry_points=[CommandHandler('chat_v1', start_chat_v1)],
        states={
            RESPONSE: [MessageHandler(Filters.text, get_bot_response)]
        },
        fallbacks=[CommandHandler('quit_chat', cancel_chat_v1)]
    )
    return conv_handler
```
