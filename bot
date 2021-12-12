import discord
import os
import random
import requests
import json
from replit import db
from server import keep_alive

sadWords = [
  'sad', 'depressed', 'tragic', 'unhappy', 'drama', 'angry',
  'miserable', 'depressing', 'tragedy'
]

encouragements = [
  'Cheer up!',
  'Hang in there.',
  'You are a great person / bot!',
  'Good for you.',
  'I rejoice in the shadow of your pain.',
  'Nice!',
  'Awesome! I am happy.'
]

howAreYouRes = [
  'Fine! What about you?', 
  'I am great!', 
  'I am a robot, you know that right?',
  'How many brain cells have you got to ask a robot such a question?',
  'I was fine, but now I am worried about your mental capacities.',
  'Are you okay?'
]

userGreetings = [
  'hey',
  'hello',
  'hi'
]

botGreetings = [
  'Hello!',
  'Hi there!',
  'Yes sir!',
  'Wassup!',
  'Heyyy!',
  'Ugh! Come on, this is my personal time homie.',
  'Why are you here?',
  'I don\'t like you.',
  'Can\'t you see that I am busy right now?!'
]

def get_quote():
  response = requests.get("https://zenquotes.io/api/random")
  jsonData = json.loads(response.text)
  quote = jsonData[0]['q'] + ' -' + jsonData[0]['a']
  return quote

def get_joke():
  response = requests.get("https://geek-jokes.sameerkumar.website/api?format=json")
  jsonData = json.loads(response.text)
  joke = jsonData['joke']
  return joke

def get_poem():
  response = requests.get("https://www.poemist.com/api/v1/randompoems")
  jsonData = json.loads(response.text)
  title = 'Title: ' + jsonData[0]['title']
  poem = jsonData[0]['content']
  poet = 'Poet - ' + jsonData[0]['poet']['name']
  return [title, poem, poet]

def updateEncouragements(encouragingMsg):
  if "encouragements" in db.keys():
    encouragements = db['encouragements'].value
    encouragements.append(encouragingMsg)
    db['encouragements'].value = encouragements
  else:
    db['encouragements'].value = [encouragingMsg]

def deleteEncouragement(index):
  encouragements = db['encouragements'].value
  if len(encouragements) > 0 and len(encouragements) > index:
    del encouragements[index]
    db['encouragements'].value = encouragements

client = discord.Client()

@client.event
async def on_ready():
  print("Dot came to life as {}!".format(client.user))


@client.event
async def on_message(message):
  if not message.author == client.user:
    msg = message.content.lower()
    if any(word in msg for word in userGreetings):
      await message.channel.send(random.choice(botGreetings))

    if 'how are you' in msg:
      await message.channel.send(random.choice(howAreYouRes))

    if msg.startswith('$inspire'):
      quote = get_quote()
      await message.channel.send(quote)

    if ('joke' or 'jokes') in msg:
      joke = get_joke()
      await message.channel.send(joke)

    if ('poem' or 'poetry') in msg:
      poem = get_poem()
      for i in range(len(poem)):
        await message.channel.send(poem[i])

    if any(word in msg for word in sadWords):
      await message.channel.send(random.choice(encouragements))

    options = encouragements
    if 'encouragements' in db.keys():
      options = options + db['encouragements'].value

    if msg.startswith('$new'):
      enMsg = message.content.split('$new ', 1)[1]
      updateEncouragements(enMsg)
      await message.channel.send(enMsg + " added successfully!")

    if msg.startswith("$del"):
      ens = []
      if 'encouragements' in db.keys():
        index = int(msg.split("$del", 1)[1])
        deleteEncouragement(index)
        ens = db['encouragements'].value
      await message.channel.send(ens)

keep_alive()
client.run(os.getenv('TOKEN'))
