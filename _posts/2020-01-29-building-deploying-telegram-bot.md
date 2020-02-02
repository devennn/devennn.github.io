---
layout: post
title: How I build and deploy Telegram conversation bot
---

I want to build something "futuristic" while learning something new. So, I build a Telegram conversation bot. View [full code here](https://github.com/devennn/tonduyutung-bot)

I called it Tonduyutung. Don't mind the name. It is a [dusun language for slow loris](https://glosbe.com/dtp/en/tonduyutung). 

_Disclaimer: You can find the bot on telegram. But, the server is not online because I'm low on budget._

I develop the bot using Anaconda on Windows 10 with Python 3.7. Start by installing required dependencies

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
from telegram.ext import (MessageHandler, Filters, ConversationHandler,
                            CommandHandler)
from chatterbot import ChatBot
from chatterbot.trainers import ChatterBotCorpusTrainer

chatbot = ChatBot('Ron Obvious')
# Create a new trainer for the chatbot
trainer = ChatterBotCorpusTrainer(chatbot)
# Train the chatbot based on the english corpus
trainer.train("chatterbot.corpus.english")

RESPONSE = range(1)

def start_chat_v1(update, context):
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
    update.message.reply_text('Goodbye! Talk later')
    return ConversationHandler.END

def get_bot_response(update, context):
    text = update.message.text
    response = str(chatbot.get_response(text))
    chat_id = update.message.chat_id
    context.bot.send_message(chat_id=chat_id, text=response)

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

## Test the bot

Run...
> python main.py

Then go to Telegram, search the bot's name and start the conversation by sending
> /start_v1

Example output:
```
Me: hello
Tonduyutung: How are you doing?
Me: Fine, how are you?
Tonduyutung: well, from what i can recall it is the study of cells.
```
I don't think that is the right response for my questions. Nevermind, the bot is working.

For now, the bot is hosted on my local machine. To scale it, the program need to be hosted on a server. 

## Choosing and deploying to Server

I am using AWS EC2 Linux 16.04. At first, i tried the t2.micro instance Free trial. It has 1Gb ram with 1vCpu. However, I encounter out of memory problem when installing the requirements.txt. So, i choose the t2.small with 4Gb ram with 2vCpu. To upload local files to virtual machine, I used WinSCP.

Reference:
- [Logging into EC2 Linux Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)
- [Transferring file to EC2 Server](https://winscp.net/eng/docs/guide_amazon_ec2)

Before proceeding, make sure to install python 3.7 and program requirements.

To let the process running after logging out, install ```screen``` . It allows user detachment from the SSH session without exiting the remote job.
> sudo apt-get install screen

Start screen session
> screen

Then run program
> python3 main.py

To detach the session, press ```ctrl + a``` followed by ```d```.
Then output such as below will appear
> [detached from 1365.pts-0.server]

To reattach, login to the instances and:
> screen -r

or view the process by
> screen -ls

If there are lots of session running, reattach to specific session using
> screen -r 1365

## Final Thoughts

Now, I have a conversation bot which is not hosted on my computer. What's next? Maybe improving the conversation technique?
