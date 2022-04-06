# first1
123
import logging
import random

import telebot

bot = telebot.TeleBot('5079416344:AAEo4QCPSjzJXWsSQN-fZ4WfqpOT_seyH4c')
telebot.logger.setLevel(logging.INFO)

storage = dict()


def storage1(user_id):
    storage[user_id] = dict(attempt=None, random_digit=None)


def storage2(user_id, key, value):
    storage[user_id][key] = value


def storage3(user_id):
    return storage[user_id]


@bot.message_handler(func=lambda message: message.text.lower() == "hi")
def command_text_hi(m):
    bot.send_message(m.chat.id, "Hello,to play on a game enter 'game'")


@bot.message_handler(func=lambda message: message.text.lower() == "game")
def digitgames(message):
    storage1(message.chat.id)
    attempt = 5
    storage2(message.chat.id, "attempt", attempt)
    bot.send_message(message.chat.id,'Game is "find a number"!')
    bot.send_message(message.chat.id,'Number of attemts: {attempt}')
    random_digit = random.randint(1, 10)
    storage2(message.chat.id, "random_digit", random_digit)
    bot.send_message(message.chat.id, 'Ready! The number was hidden from 1 to 10!')
    bot.send_message(message.chat.id, 'Enter your num:')
    bot.register_next_step_handler(message, process_digit_step)


def process_digit_step(message):
    user_digit = message.text
    if not user_digit.isdigit():
        message = bot.reply_to(message, 'please enter a number')
        bot.register_next_step_handler(message, process_digit_step)
        return

    if int(user_digit) == random_digit:
        bot.send_message(message.chat.id, f'Congratulate! You find a number correctly! It was: {random_digit}')
        storage1(message.chat.id)
        return
    elif attempt > 1:
        attempt -= 1
        storage2(message.chat.id, "attempt", attempt)
        bot.send_message(message.chat.id, f'Mistake, you have last: {attempt} attempts')
        bot.register_next_step_handler(message, process_digit_step)
    else:
        bot.send_message(message.chat.id, 'You lose!')
        storage1(message.chat.id)
        return
