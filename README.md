import logging
import wikipediaapi
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, filters, CallbackContext

# Bot tokenini shu yerga kiriting
TOKEN = "7893860405:AAFq_Pl2vwwvbpib27tXEjgIWHnVuJEhUGU"

# Logging sozlamalari
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)


async def start(update: Update, context: CallbackContext) -> None:
    await update.message.reply_text("Salom! Men Wikipedia botman. Savolingizni yozing!")


async def chatbot_response(update: Update, context: CallbackContext) -> None:
    user_input = update.message.text.lower()
    responses = {
        "salom": "Salom! Qanday yordam bera olaman?",
        "qalesiz": "Yaxshi, rahmat! Sizda yaxshilikmi?",
        "isming nima": "Men Telegram botman!",
        "xayr": "Xayr! Yana ko'rishguncha!",
    }

    if user_input in responses:
        await update.message.reply_text(responses[user_input])
    else:
        wiki = wikipediaapi.Wikipedia(user_agent="MyTelegramBot/1.0", language="uz")
        page = wiki.page(user_input)

        if page.exists():
            await update.message.reply_text(page.summary[:500])
        else:
            await update.message.reply_text("Kechirasiz, bu mavzuda ma'lumot topa olmadim. Boshqa savol bering!")


async def main():
    app = ApplicationBuilder().token(TOKEN).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, chatbot_response))

    print("Bot ishga tushdi...")
    await app.run_polling()


if __name__ == "__main__":
    import asyncio

    asyncio.run(main())

