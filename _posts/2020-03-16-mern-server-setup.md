---
layout: post
title:  "How to Basic MERN Setup"
date:   2020-03-26 01:31:28
categories: [JavaScript, MERN]
comments: true
---
This part of the tutorial sets up a beginning file structure skeleton for your entire project, but is focused on the relationship between your Express server and MongoDB.
If after using this tutorial you want to check out my basic React setup tutorial, check out the link at the bottom of this page that will take you to that post.

Before starting any actual programming, create a project folder that will house the server and the client. 
Now it's time to create our client. 
* The client is our React app
* Our server will be the Mongoose/Express portion of the project.

Inside of the terminal we can run the following commands to set up front end dependencies.
Make sure you are inside of the project folder: 
>npx create-react-app client <br/>
>cd my-app<br/>
>npm start<br/>
>npm i @reach/router

For our back end dependencies, we should first create a server folder inside of the project folder at the same level as our client. After navigating into the server folder from the terminal, we should first create our Server.js file that will live at the root of the server folder. While in the server folder, we can run the following commands to set up mongoose and express.:
>npm i mongoose express cors
>npm init -y<br/>


Before going further, it's a good idea to set up the express server.
Using cors allows us to have different domains for our server and client that can still communicate restricted resources to each other. 

Setting the app to have json spaces, makes our json pretty and easily readable without having to use an external json parser.

>server.js
{% highlight javascript%}
const express = require("express");
const cors = require('cors');
const app = express();
app.set('json spaces', 4)
app.use(cors())

// This will fire our mongoose.connect statement to initialize our database connection
require("../server/config/mongoose.config");

app.use(express.json(), express.urlencoded({ extended: true }));

// This is where we import the users routes function from our user.routes.js file
const AllMyFooRoutes = require("../server/routes/Foos.routes");
AllMyFooRoutes(app);

app.listen(8000, () => console.log("The server is all fired up on port 8000"));
{%endhighlight%}

For the server a solid file structure is having a config folder, models, controllers, and routes folder. Inside of each of the appropriate folders will be the config settings/models/controllers/routes files.

Setting up mongoose is easy, and using it gives us access to a lot of really handy ways to interact with our MongoDB using JavaScript.

Here is a sample conection
>(mongoose.config.js)
{% highlight javaScript %}
const mongoose = require("mongoose");

mongoose.connect("mongodb://localhost/foos", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
})
    .then(() => console.log("Established a connection to the database"))
    .catch(err => console.log("Something went wrong when connecting to the database", err));
{% endhighlight %}

Inside of the models folder, we can create any models that we want to keep track of. For the purpose of this exercise, let's just create a model for 'foo'. Let's give 'foo' a title, price, and description in the schema just to have a few different input fields to work with.

>(foo.model.js)
{% highlight javascript %}
const mongoose = require("mongoose");

const FooSchema = new mongoose.Schema({
    title: "",
    price: "",
    description: ""
});

const Foo = mongoose.model("Foo", FooSchema);

module.exports = Foo;
{% endhighlight %}

We can use our controller to handle any CRUD communications we want to perform on the database.

>(foo.controller.js)
{% highlight javascript %}
const Foo = require("../models/foo.model");


module.exports.createNewFoo = (req, res) => {
    Foo.create(req.body)
        .then(newlyCreatedFoo => res.json({ Foo : newlyCreatedFoo }))
        .catch(err => res.json({ message: "Something went wrong", error: err }));
};
module.exports.findAllFoos = (req, res) => {
    Foo.find()
        .then(allFoos => res.json({ Foos: allFoos }))
        .catch(err => res.json({ message: "Something went wrong", error: err }));
};

module.exports.findOneSingleFoo = (req, res) => {
    Foo.findOne({ _id: req.params.id })
        .then(oneSingleFoo => res.json({ Foo: oneSingleFoo }))
        .catch(err => res.json({ message: "Something went wrong", error: err }));
};
module.exports.updateExistingFoo = (req, res) => {
    Foo.findOneAndUpdate({ _id: req.params.id }, req.body, { new: true })
        .then(updatedFoo => res.json({ Foo: updatedFoo }))
        .catch(err => res.json({ message: "Something went wrong", error: err }));
};
module.exports.deleteAnExistingFoo = (req, res) => {
    Foo.deleteOne({ _id: req.params.id })
        .then(result => res.json({ result: result }))
        .catch(err => res.json({ message: "Something went wrong", error: err }));
};
{% endhighlight %}

Finally, we can create our api end points so that we can access our API from the front end or an API development tool like Postman. 
>(Foos.routes.js)
{% highlight javascript %}
const FooController = require("../controllers/Foos.controller");

module.exports = app => {
    app.get("/api/Foos/", FooController.findAllFoos);
    app.post("/api/Foos/new", FooController.createNewFoo);
    app.get("/api/Foos/:id", FooController.findOneSingleFoo);
    app.put("/api/Foos/update/:id", FooController.updateExistingFoo);
    app.delete("/api/Foos/delete/:id", FooController.deleteAnExistingFoo);
};
{% endhighlight %}

That's it for our server! Now we can move onto our front end, and deciding how we want to display our information in our views. I am writing another post which goes into a basic front end set up more in depth. 
