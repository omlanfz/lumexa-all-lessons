# 🚀 Lesson 4: Events and Broadcasting — Sprites That React!

## Hi, coder! 👋

Remember when your sprite could move, look different, and make sounds? Today, your sprite is going to **REACT** to YOU — pressing keys, clicking, and even listening to messages from other sprites!

## 🧠 Think Like a Coder: Hat Blocks Are Doorbells

Blocks with a rounded top, like "when green flag clicked," are called **hat blocks**. They're like a doorbell — nothing happens until someone rings it (the event happens). Then the whole stack underneath springs into action!

Try these events:
- **when green flag clicked** — starts when you press the green flag.
- **when [space] key pressed** — starts when you press a certain key.
- **when this sprite clicked** — starts when you click right on the sprite.

## 🤖 Robot Tip: Broadcasting Is Like a Walkie-Talkie

Imagine you're on a playground with walkie-talkies. If you shout "GO!" into yours, everyone tuned to that channel hears it, even from far away. In Scratch:
- **broadcast [message]** shouts a message.
- **when I receive [message]** listens for that message and reacts.

The sprite that shouts doesn't have to know exactly who's listening — it just sends the message out!

## Try This: Make a Sprite React

1. Add **"when [space] key pressed"** → **"move 20 steps."** Press spacebar and watch it move!
2. Add **"when this sprite clicked"** → **"say [You clicked me!] for 2 seconds."** Click your sprite directly!

## Try This: Two Sprites Talking With Broadcasts

1. On your FIRST sprite: **"when green flag clicked"** → **"broadcast [wave hello]."**
2. On your SECOND sprite: **"when I receive [wave hello]"** → **"say [Hi there!] for 2 seconds."**
3. Click the green flag. Even though only sprite 1 has the broadcast block, sprite 2 reacts too!

## ⭐ Bonus Challenge

Can you build a broadcast relay? Sprite A broadcasts "scene 2." Sprite B receives it, reacts, AND broadcasts "scene 3." Sprite C receives THAT and reacts too! It's like passing a note down a row of friends.

## 🔍 Debug Detective

Uh-oh! A coder built "when I receive [go]" on one sprite, but "broadcast [Go]" (with a capital G!) on another, typed separately instead of picked from the list. When they click the green flag, nothing happens! Why do you think that is? (Hint: always pick the SAME broadcast from the dropdown menu — never type a new one by accident, or Scratch thinks it's a totally different message.)

## 🏆 Victory Moment

You just made sprites react to you AND talk to each other using secret messages! Next time, we'll learn how to make things repeat with loops. See you in Lesson 5! 🚀
