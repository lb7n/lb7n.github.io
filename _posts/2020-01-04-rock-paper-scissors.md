---
layout: post
title: "Simple Rock, Paper, Scissors"
date:   2020-01-04 15:07:19
categories: [Python]
comments: true
---

This Rock, Paper, Scissors tutorial was made using the  'random' module package from the python standard library and is played using the terminal.

* Working out the logic of who wins what is a good first step, since everything about this games logic will use it.
    1. rock wins scissors
    2. paper wins rock
    3. scissors wins paper
* It's also a good idea to create a boolean where we keep track of the running state of the game, to easily replay the game without relaunching. 

{% highlight python %}
print("Welcome to Rock, Paper, Scissors!")

running = False

def did_win(computer_choice, user_choice):
    if user_choice == "rock" and computer_choice == "scissors":
        return True
    elif user_choice == "scissors" and computer_choice == "paper":
        return True
    elif user_choice == "paper" and computer_choice == "rock":
        return True
    return False
{% endhighlight %}

Now that our game knows the underlying logic, we can build out the rest.

* keeping an array that stores all of the possible options is a great way to save space, we know which word is at which index so we

{% highlight python %}
def gameplay():
    # play options :
    options = ["rock", "paper", "scissors"]
    # make computer choose randomly
    computer_choice = random.choice(options)

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

At the end of all of this, we can make use of our running boolean and choose whether or not to replay the game.

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

Finally, with all of the logic in place and working we can initialize a game. Because we created a different response on startup than on replay we should also let the program know which one to call first. Time to wrap up our program!

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

Run your python script in your terminal, and play until you are satisfied with the results!