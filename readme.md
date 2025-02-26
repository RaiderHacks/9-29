Jokes API code `jokes.py` from last meeting 
```python
import requests

URL = 'https://official-joke-api.appspot.com/random_joke'


def check_valid_status_code(request):
    if request.status_code == 200:
        return request.json()

    return False


def get_joke():
    request = requests.get(URL)
    data = check_valid_status_code(request)

    return data
```
Discord bot code `jokes_bot.py` from last meeting 
```python
import discord

client = discord.Client()


@client.event
async def on_ready():
    print('We have logged in as {0.user}'.format(client))


@client.event
async def on_message(message):
    if message.author == client.user:
        return

    if message.content.startswith('$hello'):
        await message.channel.send('Hello!')

client.run('your token here.')
```



### Adding the $joke command

The `$joke` command will allow users to request a joke from jokes API through the discord channel

![image-20200929150334355](notes.assets/image-20200929150334355.png)

Import the `jokes.py` file into your `jokes_bot.py` file by adding `import jokes` below the other import statment 

Add this to the `on_message` function in `jokes_bot.py`

```python
if message.content.startswith('$joke'):
        joke = jokes.get_joke()

        if joke == False:
            await message.channel.send("Couldn't get joke from API. Try again later.")
        else:
            await message.channel.send(joke['setup'] + '\n' + joke['punchline'])
```

What is this doing? 

- Line 1 checks if the string `$joke` was sent in the discord chat 
- Line 2 calls the `get_joke()`  function from line 13 of `jokes.py` and saves it to the `joke ` variable 
- Line 5 makes sure that we got a response from the jokes API before trying to send a joke 
- Line 7 formats the joke by accessing keys of `setup` and `punchline` from the `joke` object which is formatted as a python dictionary 

Final code for `jokes_bot.py`

```python
import discord
import jokes

client = discord.Client()


@client.event
async def on_ready():
    print('We have logged in as {0.user}'.format(client))


@client.event
async def on_message(message):
    if message.author == client.user:
        return

    if message.content.startswith('$hello'):
        await message.channel.send('Hello!')
        
    if message.content.startswith('$joke'):
        joke = jokes.get_joke()

        if joke == False:
            await message.channel.send("Couldn't get joke from API. Try again later.")
        else:
            await message.channel.send(joke['setup'] + '\n' + joke['punchline'])

client.run('your_bot_token')
```

Run `python jokes_bot.py` and see if it works in your server 

