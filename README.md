apt update
apt upgrade 
pkg update
pkg upgrade
pkg install python git
pip install--upgrade pip
python-telegram-bot  version-20.7
pkg install nano-y
nano bot.py
import os
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, ContextTypes, filters
from PIL import Image
import pytesseract
from config import BOT_TOKEN

# /start command
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "📸 OCR Bot Ready!\n\n"
        "send me images,I will extract texts."
    )

# Handle image
async def image_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
    photo = update.message.photo[-1]
    file = await photo.get_file()

    image_path = "image.jpg"
    await file.download_to_drive(image_path)

    try:
        text = pytesseract.image_to_string(Image.open(image_path))
        if text.strip() == "":
            text = "❌ No text detected."
        await update.message.reply_text(f"📝 Extracted Text:\n\n{text}")
    except Exception as e:
        await update.message.reply_text("❌ OCR failed.")
    finally:
        if os.path.exists(image_path):
            os.remove(image_path)

def main():
    app = ApplicationBuilder().token(BOT_TOKEN).build()

    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.PHOTO, image_handler))

    print("🤖 OCR Bot running...")
    app.run_polling()

if __name__ == "__main__":
    main()
    
