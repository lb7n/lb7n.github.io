---
layout: post
title: "Rock, Paper, Scissors"
date:   2020-01-05 15:07:19
categories: [Python]
comments: true
---

I created a simple rock, paper, scissors game that is played on the terminal using python. It was a fun exercise to practice accessing the random library.

I began by importing the system and random libraries.

{% highlight python %}
import sys
import random
{% endhighlight %}

next I had to build out the logic for what choices won against which choices.


{% highlight python %}
print("Welcome to Rock, Paper, Scissors!")

running = False
# rock wins scissors
# paper wins rock
# scissors wins paper
def did_win(computer_choice, user_choice):
    if user_choice == "rock" and computer_choice == "scissors":
        return True
    elif user_choice == "scissors" and computer_choice == "paper":
        return True
    elif user_choice == "paper" and computer_choice == "rock":
        return True
    return False

{% endhighlight %}

I then built out the actual game play logic in a separate function, which would use the logic from the did_win function above.

{% highlight python %}
def gameplay():
    # play options :
    options = ["rock", "paper", "scissors"]
    # make computer choose randomly
    computer_choice = random.choice(options)
    print("**************************" + computer_choice)

    user_choice = input("Type your choice: ROCK, PAPER or SCISSORS")
    user_choice = user_choice.lower()

    if computer_choice == user_choice:
        print("ITS A TIE!")
        replay()
    elif did_win(computer_choice, user_choice):
        print("YOU WON")
        print("Computer Choice: " + computer_choice)
        print("Your Choice: " + user_choice)
        replay()
    else:
        print("YOU LOST!")
        print("Computer Choice: " + computer_choice)
        print("Your Choice: " + user_choice)
        replay()

{% endhighlight %}

I then checked whether the user wanted to continue to play or not.

{% highlight python %}
def yesno():
    global running
    if response == "y":
        running = True
    elif response == "n":
        print("GOODBYE")
        sys.exit()
    else:
        print("Enter either Y or N")

def replay():
    global response
    response = input("Do you want to play again? (Y/N)?")
    yesno()
{% endhighlight %}


initialize the game:
{% highlight python %}
def initial():
    global running
    global response
    response = input("Would you like to play a game? (Y/N)")
    response = response.lower()
    yesno()
{% endhighlight %}

Start/Stop Gameplay: 

{% highlight python %}
while True:
    if running:
        gameplay()
    else:
        initial()
{% endhighlight %}