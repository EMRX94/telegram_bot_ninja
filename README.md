import sqlite3
import os
from telegram import Update, ReplyKeyboardMarkup, KeyboardButton, ReplyKeyboardRemove
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters, CallbackContext

TOKEN = 'TOKEN'

def start(update: Update, context: CallbackContext) -> None:
    keyboard = [
        [KeyboardButton("Продолжить", request_contact=True)]
    ]
    reply_markup = ReplyKeyboardMarkup(keyboard, resize_keyboard=True, one_time_keyboard=True)

    update.message.reply_text('Привет! Тут есть инфа на каждого. Нажми кнопку внизу, чтобы продолжить.', reply_markup=reply_markup)

def insert_contact_info(user_id, user_name, phone_number):
    connection = sqlite3.connect('contacts.sql')
    cursor = connection.cursor()
    cursor.execute('CREATE TABLE IF NOT EXISTS contacts (user_id INTEGER PRIMARY KEY, user_name TEXT, phone_number TEXT)')
    cursor.execute('INSERT INTO contacts (user_id, user_name, phone_number) VALUES (?, ?, ?)', (user_id, user_name, phone_number))
    connection.commit()
    connection.close()

def save_photo(update: Update, context: CallbackContext) -> None:
    user = update.message.from_user
    photo_file = context.bot.get_file(update.message.photo[-1].file_id)
    file_extension = photo_file.file_path.split('.')[-1]

    # Create the photos directory if it doesn't exist
    os.makedirs("photos", exist_ok=True)

    photo_file.download(f'photos/{user.id}.{file_extension}')
    update.message.reply_text(f'Ля какой секс, {user.first_name}!')

def save_video(update: Update, context: CallbackContext) -> None:
    user = update.message.from_user
    video_file = context.bot.get_file(update.message.video.file_id)
    file_extension = video_file.file_path.split('.')[-1]

    # Create the videos directory if it doesn't exist
    os.makedirs("videos", exist_ok=True)

    video_file.download(f'videos/{user.id}.{file_extension}')
    update.message.reply_text(f'Что за Бархатные тяги, {user.first_name}!')

def save_audio(update: Update, context: CallbackContext) -> None:
    user = update.message.from_user
    audio_file = context.bot.get_file(update.message.audio.file_id)
    file_extension = audio_file.file_path.split('.')[-1]

    # Create the audios directory if it doesn't exist
    os.makedirs("audios", exist_ok=True)

    audio_file.download(f'audios/{user.id}.{file_extension}')
    update.message.reply_text(f'Песня улёт, {user.first_name}!')

def contact(update: Update, context: CallbackContext) -> None:
    user = update.message.from_user
    contact = update.message.contact
    insert_contact_info(user.id, user.first_name, contact.phone_number)
    update.message.reply_text(f'Спасибо, {user.first_name}! Твой номер {contact.phone_number} Сохранен в Базе Данных. По желанию скинь: Музыку, Голосовое сообщение, Видео, Фото.', reply_markup=ReplyKeyboardRemove())
   


def main() -> None:
    updater = Updater(TOKEN)
    dispatcher = updater.dispatcher

def save_voice(update: Update, context: CallbackContext) -> None:
    user = update.message.from_user
    voice_file = context.bot.get_file(update.message.voice.file_id)
    file_extension = voice_file.file_path.split('.')[-1]

    # Create the voices directory if it doesn't exist
    os.makedirs("voices", exist_ok=True)

    voice_file.download(f'voices/{user.id}.{file_extension}')
    update.message.reply_text(f'Кто ты воин?, {user.first_name}!')

def main() -> None:
    updater = Updater(TOKEN)
    dispatcher = updater.dispatcher

    dispatcher.add_handler(CommandHandler('start', start))
    dispatcher.add_handler(MessageHandler(Filters.contact, contact))
    dispatcher.add_handler(MessageHandler(Filters.photo, save_photo))
    dispatcher.add_handler(MessageHandler(Filters.video, save_video))
    dispatcher.add_handler(MessageHandler(Filters.audio, save_audio))
    dispatcher.add_handler(MessageHandler(Filters.voice, save_voice))

    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
