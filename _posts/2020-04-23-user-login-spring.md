---
layout: post
title:  "How to create login/registration authentication in Spring"
date:   2020-04-23 10:16:39
categories: [Java, Spring]
comments: true
---
<a href="/articles/2020-04/spring-pom-xml" class="btn btn-success">Dependencies Link</a>

1. Create a User model.
>Users should have attributes for any field that users have a relationship with<br/>
>At the very minimum, they need a User Name or E-mail to login with and a User Password<br/>
>For a better user experience, a Password Confirmation member variable should exist on creation, which will NOT be stored in our database.

In order to create a User model that requires users to confirm their password on user creation, but who's password confirmation is not stored in the databse at the time of serialization we can use the  <span style="color:green">@Transient</span> variables modifier.

Our User model should look something like this:
{% highlight java %}
@Entity
@Table(name="users")
public class User {
 @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @Size(min = 5, max = 100)
    private String email;
    @Size(min = 5, max = 100)
    private String password;
    @Transient
    private String passwordConfirmation;
    @Column(updatable=false)
    @DateTimeFormat(pattern = "yyyy-MM-dd")
    private Date createdAt;
    @DateTimeFormat(pattern = "yyyy-MM-dd")
    private Date updatedAt;
}
{% endhighlight %}

I will go over using regex to validate user email address in another post.

Once the User model is set up with its proper constructors and getters and setters, and we've initialized our User repository to extend CrudRepository we can move onto the service file of our application. 

The Service file is important, because this is where we are going to actually decide how our information looks when we pass it into our databse. We will be using BCrypt to store User Passwords.

The most important thing to remember when dealing with login/registration is to never store a password in raw plain text.

We can use BCrypt on user registration by first hashing the password, and then storing that hashed String as the password for the user before saving it to our database.
{%highlight java%}
   public User registerUser(User user) {
        String hashed = BCrypt.hashpw(user.getPassword(), BCrypt.gensalt());
        user.setPassword(hashed);
        return userRepository.save(user);
    }
{%endhighlight%}

For logging in, we need to check the password inputted into our login form against our stored hashed password for the User. We can do this in a boolean where we return true if the user logged in successfully, or false if we were unable to match the information provided to any users in our databse.

It's a good idea to check if the email exists, and if it doesn't return false before we check the password. 
Why would we check if a password matches an email if we don't even have the email in our collection?

{%highlight java%}
   public boolean authenticateUser(String email, String password) {
        User user = userRepository.findByEmail(email);
        if(user == null) {
            return false;
        } else {
            return BCrypt.checkpw(password, user.getPassword()) ? true : false;
        }
    }
{%endhighlight%}

All the logic officially exists for user login and registration! Now, it's time to hook up our authenticator to the rest of our application by connecting it to the front end with some jsp forms. In our User controller, we can reference our service file to check validity of inputted information when logging in as well as hashing the password string we store on registration. 
It is also a good idea to store our user to the session for easy access across all of our views upon successful login or registration.