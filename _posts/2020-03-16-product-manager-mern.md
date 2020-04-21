---
layout: post
title:  "Product Manager - MERN"
date:   2020-03-26 01:31:28
categories: [JavaScript]
comments: true
---
I created a basic MERN application for handling product inventory.

First step is to install and set up react and mongoose inside of my project. 
Inside of my terminal I ran the following commands for my front end dependencies inside of my project folder: 
>npx create-react-app client
>cd my-app
>npm start
>npm install @reach/router

For my back end dependencies I ran the following inside of a new server folder inside of my project folder:
>npm install express
>npm init -y
>npm install mongoose express

Inside of the server folder, before doing anything I set up my express server. 
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
const AllMyProductRoutes = require("../server/routes/products.routes");
AllMyProductRoutes(app);

app.listen(8000, () => console.log("The server is all fired up on port 8000"));
{%endhighlight%}

For the server I had to create a models, controllers, routes, and config and folder. Inside of each of these folders lives the appropriate models/controllers/routes/config settings.

Because this was a MERN application, I used mongoose in collaboration with my MongoDB
>(mongoose.config.js)
{% highlight javaScript %}
  
const mongoose = require("mongoose");

mongoose.connect("mongodb://localhost/products", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
})
    .then(() => console.log("Established a connection to the database"))
    .catch(err => console.log("Something went wrong when connecting to the database", err));
{% endhighlight %}

Next it was time to create the model for my future product objects. This was a really simple MERN application, so I only used a title, price, and description in my schema.

>(products.model.js)
{% highlight javascript %}
const mongoose = require("mongoose");

const ProductSchema = new mongoose.Schema({
    title: "",
    price: "",
    description: ""
});

const Product = mongoose.model("Product", ProductSchema);

module.exports = Product;
{% endhighlight %}

I used the product controller to handle all CRUD communications with the database

>(products.controller.js)
{% highlight javascript %}
const Product = require("../models/products.model");


module.exports.createNewProduct = (req, res) => {
    Product.create(req.body)
        .then(newlyCreatedProduct => res.json({ product : newlyCreatedProduct }))
        .catch(err => res.json({ message: "Something went wrong", error: err }));
};

module.exports.findAllProducts = (req, res) => {
    Product.find()
        .then(allProducts => res.json({ products: allProducts }))
        .catch(err => res.json({ message: "Something went wrong", error: err }));
};

module.exports.findOneSingleProduct = (req, res) => {
    Product.findOne({ _id: req.params.id })
        .then(oneSingleProduct => res.json({ product: oneSingleProduct }))
        .catch(err => res.json({ message: "Something went wrong", error: err }));
};
module.exports.updateExistingProduct = (req, res) => {
    Product.findOneAndUpdate({ _id: req.params.id }, req.body, { new: true })
        .then(updatedProduct => res.json({ product: updatedProduct }))
        .catch(err => res.json({ message: "Something went wrong", error: err }));
};
module.exports.deleteAnExistingProduct = (req, res) => {
    Product.deleteOne({ _id: req.params.id })
        .then(result => res.json({ result: result }))
        .catch(err => res.json({ message: "Something went wrong", error: err }));
};
{% endhighlight %}

And connected these to methods to routes to create my api endpoints.
>(products.routes.js)
{% highlight javascript %}
const ProductController = require("../controllers/products.controller");

module.exports = app => {
    app.get("/api/products/", ProductController.findAllProducts);
    app.post("/api/products/new", ProductController.createNewProduct);
    app.get("/api/products/:id", ProductController.findOneSingleProduct);
    app.put("/api/products/update/:id", ProductController.updateExistingProduct);
    app.delete("/api/products/delete/:id", ProductController.deleteAnExistingProduct);
};
{% endhighlight %}

Finally it is time to look at the front end of the application, which I will go into detail about in another post.
