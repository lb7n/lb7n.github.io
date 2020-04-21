---
layout: post
title:  "URL Shortener"
date:   2020-01-05 15:07:19
categories: [Python]
comments: true
---
On brand with the python groove I have been in, I thought it would be neat to have a simple user log in/registration with validations in my arsenal. 

I put the log in/validation in a simple post/message board application I built using python/django and used bcrypt to encrypt user passwords.

First I created my models.

(models.py)
{% highlight python %}

class User(models.Model):
    first_name = models.CharField(max_length=45)
    last_name = models.CharField(max_length=45)
    email = models.CharField(max_length=255)
    password = models.CharField(max_length=45)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    objects = UserManager()


class Message(models.Model):
    user = models.ForeignKey(User, related_name="messages", default=None, on_delete=models.CASCADE)
    message_text = models.CharField(max_length=255, default=None)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)


class Comment(models.Model):
    comment_text = models.CharField(max_length=255, default=None)
    messages = models.ForeignKey(Message, related_name="comments", default=None, on_delete=models.CASCADE)
    users = models.ForeignKey(User, related_name="comments", default=None, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
{% endhighlight %}

after that, I created my UserManager which housed my validators
(models.py)
{% highlight python %}
class UserManager(models.Manager):
    def basic_validator(self, postData):
        EMAIL_REGEX = re.compile(r'^[a-zA-Z0-9.+_-]+@[a-zA-Z0-9._-]+\.[a-zA-Z]+$')
        errors = {}
        if len(postData['first_name']) < 2:
            errors['first_name'] = "First name should be at least 2 characters long!"
        if len(postData['last_name']) < 2:
            errors['last_name'] = "Last name should be at least 2 characters long!"
        if User.objects.filter(email=postData['email']).exists():
            errors['email'] = "Email should be unique!"
        if not EMAIL_REGEX.match(postData['email']):  # test whether a field matches the pattern
            errors['email_makes_sense'] = "Invalid email address!"
        if len(postData['password']) < 8:
            errors['pw_length'] = "Passwords should be at least 8 characters long!"
        if postData['password'] != postData['password_confirm']:
            errors['pw_confirm'] = "Your passwords need to match!"
        return errors

    def basic_validator2(self, postData):
        EMAIL_REGEX = re.compile(r'^[a-zA-Z0-9.+_-]+@[a-zA-Z0-9._-]+\.[a-zA-Z]+$')
        errors = {}
        if not User.objects.filter(email=postData['email']).exists():
            errors['email'] = "Email not in db!"
        if not EMAIL_REGEX.match(postData['email']):  # test whether a field matches the pattern
            errors['email_makes_sense'] = "Invalid email address!"
        this_user = User.objects.get(email=postData['email'])
        check_it = bcrypt.checkpw(postData['password'].encode(), this_user.password.encode())
        if check_it is False:
            errors['password'] = "you typed the wrong password!"
        return errors
{% end highlight %}

Made sure to create some simple and descriptive url paths to use on the front end
(urls.py)
{% highlight python %}
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index),
    path('register_user', views.register_user),
    path('login', views.login),
    path('wall', views.wall),
    path('logout', views.logout),
    path('post_message', views.post_message),
    path('post_comment', views.post_comment),
    path('delete_message', views.delete_message),
]
{% end highlight %}

Finally, I hooked up my urls to their views
(views.py)
{% highlight python %}
def index(request):
    return render(request, 'index.html')


def register_user(request):
    errors = User.objects.basic_validator(request.POST)
    if len(errors) > 0:
        for key, value in errors.items():
            messages.error(request, value)
        return redirect('/')
    else:
        hash1 = bcrypt.hashpw(request.POST['password'].encode(), bcrypt.gensalt()).decode()
        print(hash1)
        c = User.objects.create(first_name=request.POST['first_name'], last_name=request.POST['last_name'],
                                email=request.POST['email'], password=hash1)
        c.save()
        request.session['this_user'] = c.id
        request.session['right_message'] = "Successfully created a new account!"
        return redirect('/wall')


def login(request):
    errors = User.objects.basic_validator2(request.POST)
    if len(errors) > 0:
        for key, value in errors.items():
            messages.error(request, value)
        return redirect('/')
    else:
        getting_something = User.objects.get(email=request.POST['email'])
        request.session['this_user'] = getting_something.id
        request.session['right_message'] = "Successfully logged in!"
        return redirect('/wall')


def wall(request):
    this_message = request.session['right_message']

    context = {
        'users': User.objects.all(),
        'user': User.objects.get(id=request.session['this_user']),
        'message': this_message,
        'message_list': reversed(Message.objects.all())
    }
    return render(request, 'wall.html', context)


def logout(request):
    request.session.flush()
    return redirect('/')


def post_message(request):
    c = Message.objects.create(user_id=request.session['this_user'], message_text=request.POST['message_content'])
    c.save()
    return redirect('/wall')

def post_comment(request):
    c = Comment.objects.create(messages_id=request.POST['post_it'], users_id=request.session['this_user'], comment_text=request.POST['comment_content'])
    c.save()
    return redirect('/wall')

def delete_message(request):
    this_message = request.POST['delete_it']
    c = Message.objects.get(id=this_message)
    c.delete()
    return redirect('/wall')
{% end python %}