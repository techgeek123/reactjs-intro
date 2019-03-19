# Learning Competencies
  - Quickly revisit Mongo
  - Learn SQL!!
  - Compare and contrast between Nosql and Mysql
  - Should be able to build a CRUD app with references between models
  - CRUD operations using Mysql
  - SQL joins and how to write complex SQL queries

# NoSQL

## Intro to concepts
So far we have been using an array to store our data. Since this array is being stored in memory on the server, it only lasts as long as our server is running. The second our server goes down, we lose all our data! This simply will not work for any practical application. At some point, we will need to be able to save, or persist, our data. To do that, we'll need to use a database.

### Install
Depending on your OS - here are instructions to install MongoDB.

Installing MongoDB on Mac OSX(http://treehouse.github.io/installation-guides/mac/mongo-mac.html)

For Windows, you can read how to install it here and here

## what wikipedia says??
## why's and how's ??
### CRUD
To perform CRUD operations in mongo, start up a mongo server by typing mongod in the terminal. Then open up a new tab and open up a mongo shell by typing mongo. We can see our databases by using show dbs and our collections in that database by using show collections. To connect to database simply type use ``NAME_OF_DATABASE``

Read more about the commands [here](https://docs.mongodb.com/manual/crud/).

### Create

To create in mongo we use the ``insert`` function, which belongs to a collection we are operating on. ``insert`` accepts an object of keys and values.

You can also insert an array of objects using the ``insertMany`` function.
```js
db.users.insert({
    first: 'Elie'
    last: 'Schoppik',
    isInstructor: true,
    favoriteColors: ['Red', 'Blue', 'Purple'],
    favoriteFood: 'sushi'
});

db.users.insert({
    first: 'Mary',
    last: 'Malarkin',
    isInstructor: false,
    favoriteColors: ['Green', 'Yellow'],
    hometown: 'Omaha'
});

db.users.insertMany( [t branc
     { first: "tim", fun_fact: 'owns a boat!' },
     { first: "matt", fun_fact: 'has a pet dog!' },
  ] );
  ```
### Read

To read in mongo, we use the find command to find multiple objects and findOne for a single one
```js
db.users.find()
/*
{
  "_id": ObjectId("58aa39cb62a53c60f58471c7"),
  "first": "Mary",
  "last": "Malarkin",
  "isInstructor": false,
  "favoriteColors": [
    "Green",
    "Yellow"
  ],
  "hometown": "Omaha"
}
{
  "_id": ObjectId("58aa39cc62a53c60f58471c8"),
  "first": "tim",
  "fun_fact": "owns a boat!"
}
{
  "_id": ObjectId("58aa39cc62a53c60f58471c9"),
  "first": "matt",
  "fun_fact": "has a pet dog!"
}
*/

db.users.findOne({first:'Mary'})
/*{
  "_id": ObjectId("58aa39cb62a53c60f58471c7"),
  "first": "Mary",
  "last": "Malarkin",
  "isInstructor": false,
  "favoriteColors": [
    "Green",
    "Yellow"
  ],
  "hometown": "Omaha"
}
*/
```
### Update

To update documents in mongo, we use the update method and pass in different key-value pairs for the values we want to update. We can both update existing keys create new key-value pairs using this function.
```js
db.users.update(
  // what to find the user by
   { first: "tim" },
   // what data to update
   {
      first: "Bob",
      moreInfo: "Is a new instructor",
      favorite_numbers: [11,12,13]
   }
)

/*
WriteResult({
  "nMatched": 1,
  "nUpserted": 0,
  "nModified": 1
})
*/
```
We can also use update to add a new document, if we pass it the upsert option! upsert is a [portmanteau](https://en.wikipedia.org/wiki/Portmanteau) of update and insert. When you upsert into a collection, a document will be updated if it is found, and inserted if no matching document is found.
```js
db.users.update(
   { name: "fjlsadkdfjsdaklfjdklsajfklds" },
   {
      name: "Upsert will insert if it can not find!",
      moreInfo: "How cool is that?",
      favorite_numbers: [11,12,13]
   },
   // if it is not found, insert it! (upsert = update or insert)
   { upsert: true }
)

/*
WriteResult({
  "nMatched": 0,
  "nUpserted": 1,
  "nModified": 0,
  "_id": ObjectId("58aa3a859c4f01de70dcd04b")
})
*/
```
Finally, if you want to update multiple documents, you can set multi equal to true.
```js
// update every single one that is found using multi:true
db.users.update(
  // find all where the name is not equal to 'nope'
  { name: { $ne: 'nope' } },
  // set a new key of isHilarious to true
  {$set: { isHilarious: true }},
  // for more than 1 record
  { multi: true }
)

/*
WriteResult({
  "nMatched": 4,
  "nUpserted": 0,
  "nModified": 4
})
*/

```
For more on update, check out the [docs](https://docs.mongodb.com/manual/reference/method/db.collection.update/#db.collection.update).

### Delete

To delete documents in mongo, we use the remove method.
```js
// remove a single user
db.users.remove({name:'Bob'})
/*
Removed 0 record(s) in 1ms
WriteResult({
  "nRemoved": 1
})
*/

// remove all the users
db.users.remove({})
/*
Removed 1 record(s) in 2ms
WriteResult({
  "nRemoved": 3
})
*/
```
### Additional Find Methods
Since the process of deleting or updating involves first finding a record, we can use some built-in methods to perform both operations at once.
```js
db.users.findAndModify({
    // find someone with a name of elie and a score greater than 10
    query: { name: "Elie", score: { $gt: 10 } },
    // increment their score by 1
    update: { $inc: { score: 1 } }
})

db.users.findOneAndUpdate(
   { name: "Elie" },
   { $inc: { "points" : 5 } }
)

db.users.findOneAndDelete(
   { "name" : "Elie" }
)
```
You can read more about the difference between modify and update [here](http://stackoverflow.com/questions/10778493/whats-the-difference-between-findandmodify-and-update-in-mongodb). And for documentation on all of these methods (and others we haven't discussed), go [here](https://docs.mongodb.com/manual/reference/method/js-collection/).

### Mongoose
So far we have seen how to connect to a database with mongo and perform CRUD and query operations on a mongo collection. While we can connect mongo with our node applications, there is a useful tool called mongoose, which gives us access to additional methods to help with querying and building larger applications.

Mongoose also introduces two concepts which we will examine:

- **schema** - a schema is a structure for a collection. Even though mongo is "schemaless", mongoose allows us to create a structure for our collection and add validations, class methods, instance methods and associates (which we will examine later).
- **model** - a model is an object which is created from the schema and it contains methods for creating, finding, updating and deleting documents.

        touch app.js
        npm init -y
        npm install --save mongoose
To get started with mongoose, let's require it and connect to our database. We will be placing all of our models inside of a folder called models, so let's create a file called index.js and write the following code inside of it.
```js
var mongoose = require('mongoose');
mongoose.set('debug',true); // this will log the mongo queries to the terminal
mongoose.connect('mongodb://localhost/first_mongoose_app'); // connect to the DB

mongoose.Promise = global.Promise // let's use ES2015 promises for mongoose! No callbacks necessary!
```
### Schema
Now let's add our first Schema. You can read more about the different data types [here](http://mongoosejs.com/docs/guide.html). For now, let's create a file called instructor.js and place the following inside of the models folder.
```js
// create our schema which describes what each document will look like
var instructorSchema = new mongoose.Schema({
    firstName: String,
    lastName: String,
    isHilarious: {type: Boolean, default: true},
    favoriteColors: [String],
    createdAt: { type: Date, default: Date.now }
});

// create our model from the schema to perform CRUD actions on our documents (which are objects created from the model constructor)
var Instructor = mongoose.model('Instructor', instructorSchema);

// Now it would be nice if we could aggregate our models into one single file and export them out to be used in our routes and other files, so let's export this model out to another file!

module.exports = Instructor;
```
Now let's add to our models/index.js file
```js
var mongoose = require('mongoose');
mongoose.set('debug',true); // this will log the mongo queries to the terminal
mongoose.connect('mongodb://localhost/first_mongoose_app'); // connect to the DB

mongoose.Promise = global.Promise // let's use ES2015 promises for mongoose! No callbacks necessary!

// let's export out an object and attach to it a property called Instructor
module.exports.Instructor = require('./instructor') // the value of this property will be the Instructor model that we exported out in our instructor.js file!
```
We will be using this index.js file in our routes files so it's important to isolate our model logic from our routing (or controller) logic.


## Intro to concepts(Mongoose)

Mongoose in many ways "completes" Mongo in ways that most SQL people demand - that is:

- It creates nested, typed schemas
    - Schema field specs can be as loose or specific as you like.
    - They can include type requirements (String, Number, Date... YourCustomType)
    - They can insist a record has a field value (Required)
    - They can be restricted to a set of values (a.k.a. Enum)
    - They can be set to a single or array of another Schema allowing arbitrarily nested documents.
- It gives you transactional integrity
    - If the "non dirty" values for a record have changed since you retrieve a record, mongoose assumes that there is a transactional violation and rejects your changes when you save.
- It employs the ActiveRecord pattern
- It gives you Joins, foreign keys, and effortless map/reduce.
    - Well, not really. Sorry. But nobody else does either so there you go.
- It gives you through and complete documentation.
    - Well -- the SCHEMA documentation is complete. But apparently there is nothing else important you will ever do with Mongo other than create schemas so that's pretty much all you get.

## why's and how's??
### CRUD
Now that we have created a schema and a corresponding model, let's see what kinds of methods exist on our model to perform CRUD operations! Remember, CRUD stands for Create, Read, Update, and Delete.

### Create

In mongoose, documents are instances of the model. As a result, so there are two ways of creating documents:
```js
// invoke the constructor
var elie = new Instructor({firstName: 'Elie' });
// call .save on the object created from the constructor (which is the model)
elie.save().then(function(){
    console.log('saved!');
}, function(err){
    console.log('Error saving!', err);
})

// OR

// invoke the create method directly on the model
Instructor.create({firstName: 'Elie'}).then(function(instructor){
    console.log('saved!');
}, function(err){
    console.log('Error creating!');
})
```
### Read

There are quite a few ways to query for information in mongoose.
```js
// finding multiple records
Instructor.find({}).then(function(instructors){
    console.log(instructors);
}, function(err){
    console.log('error!', err);
})

// finding a single record
Instructor.findOne({firstName: 'Elie'}).then(function(data){
    console.log(data)
}, function(err){
    console.log('error!', err);
})
// finding by id - this is very useful with req.params!
Instructor.findById(2).then(function(data){
    console.log(data)
}, function(err){
    console.log('error!', err);
})

// Finding in a nested object
var query = Person.findOne({ 'name.first': 'Elie' });

// selecting the `name` and `occupation` fields
query.select('name occupation');

// execute the query at a later time
query.exec().then(function(person) {
    console.log(person);
}, function(err){
    console.log("ERROR!");
})
```
You can read more about querying [here](http://mongoosejs.com/docs/queries.html)

### Update

There are quite a few ways to update with mongoose as well. Here are some examples:
```js
// update multiple records
Instructor.update({firstName:'Elie'}, {firstName:'Bob'}).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});

// update a single record
Instructor.findOneAndUpdate({firstName:'Elie'}, {firstName:'Bob'}).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});

// update a single record and find by id (very useful with req.params!)
Instructor.findByIdAndUpdate(1, {firstName:'Bob'}).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});
```
You can read more about each of these methods here:

- http://mongoosejs.com/docs/api.html#model_Model.update

- http://mongoosejs.com/docs/api.html#model_Model.findOneAndUpdate

- http://mongoosejs.com/docs/api.html#model_Model.findByIdAndUpdate

### Delete

As you might have guessed, there are also quite a few ways as well to remove a document with mongoose. Here are some examples:
```js
// remove multiple records
Instructor.remove({firstName:'Elie'}, {firstName:'Bob'}).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});

// find and remove
Instructor.findOneAndRemove({firstName:'Elie'}).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});

// find by id and remove (very useful with req.params)
Instructor.findByIdAndRemove(1).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});
```
You can read more here about [findOneAndRemove](http://mongoosejs.com/docs/api.html#model_Model.findOneAndRemove) and [findByIdAndRemove](http://mongoosejs.com/docs/api.html#model_Model.findByIdAndRemove).

Now that we have seen how to use mongoose, let's add this logic to a CRUD application. Let's also think about a better folder structure! Let's start with our application in the terminal. We're going to build a simple CRUD application with pets as our resource. Let's start with our folder structure in terminal.

    mkdir pets-app && cd pets-app
    touch app.js
    npm init -y
    npm install express pug body-parser method-override mongoose
    mkdir views routes models
    touch views/{base,index,new,edit,show}.pug
    touch models/{index,pet}.js
    touch routes/pets.js
Let's start with our models/pet.js:
```js
var mongoose = require("mongoose");
var petSchema = new mongoose.Schema({
    name: String
})

var Pet = mongoose.model('Pet',petSchema);

module.exports = Pet;
```
Next, our models/index.js:
```js
var mongoose = require("mongoose");
mongoose.set('debug', true);
mongoose.connect('mongodb://localhost/pets-app');
mongoose.Promise = Promise;

module.exports.Pet = require('./pet');
```
Now let's move to our routes/pets.js:
```js

var express = require("express");
var router = express.Router();
var db = require('../models'); // by default, require will look for a file called index.js so we dont need ../models/index

router.get('/', function(req, res, next){
    db.Pet.find().then(function(pets){
        res.render('index', {pets});
    });
});

router.get('/new', function(req, res, next){
    res.render('new');
});

router.get('/:id', function(req, res, next){
    db.Pet.findById(req.params.id).then(function(pet){
        res.render('show', {pet});
    });
});

router.get('/:id/edit', function(req, res, next){
    db.Pet.findById(req.params.id).then(function(pet){
        res.render('edit', {pet});
    });
});

// instead of app.post...
router.post('/', function(req, res, next){
    db.Pet.create(req.body).then(function(pet){
        res.redirect('/users');
    });
});

// instead of app.patch...
router.patch('/:id', function(req, res, next){
    db.Pet.findByIdAndUpdate(req.params.id, req.body).then(function(pet){
        res.redirect('/users');
    });
});

// instead of app.delete...
router.delete('/:id', function(req, res, next){
    db.Pet.findByIdAndRemove(req.params.id).then(function(pet){
        res.redirect('/users');
    });
});

module.exports = router;
```
Before we move onto our views, let's finish our app.js:
```js
var express = require("express");
var app = express();
var methodOverride = require("method-override");
var morgan = require("morgan");
var bodyParser = require("body-parser");
var petsRoutes = require("../routes/pets");

app.set("view engine", "pug");
app.use(express.static(__dirname + "/public"));
app.use(morgan("tiny"));
app.use(bodyParser.urlencoded({extended:true}));
app.use(methodOverride("_method"));

app.use('/pets', petsRoutes);

app.get("/", function(req, res, next){
  res.redirect("/pets");
});

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// error handlers

// development error handler
// will print stacktrace
if (app.get('env') === 'development') {
  app.use(function(err, req, res, next) {
    res.status(err.status || 500);
    res.render('error', {
      message: err.message,
      error: err
    });
  });
}

// production error handler
// no stacktraces leaked to user
app.use(function(err, req, res, next) {
  res.status(err.status || 500);
  res.render('error', {
    message: err.message,
    error: {}
  });
});

app.listen(3000, function(){
  console.log("Server is listening on port 3000");
});
```

Now let's move onto our views. First, here's views/base.pug:
```html
<!DOCTYPE html>
html(lang="en")
head
    meta(charset="UTF-8")
    title Document
body
    block content
```
Now our views/index.pug:
```html
extends base.pug

block content
    a(href="/pets/new") Create a new pet
    each pet in pets
        p `Name ${pet.name}` | a(href=`/pets/${pet.id}/edit`) Edit

```
Now our views/new.pug
```html
extends base.pug

block content
    h1 Add a new pet!
    form(action="/pets", method="POST")
        input(type="text", name="name")
        input(type="submit", value="Add a Pet!")
```
Now our views/show.pug
```html
extends base.pug

block content
    h1 Welcome to `${pet.name}'s` show page!
```
Finally, our views/edit.pug
```html
extends base.pug

block content
    h1 Edit a pet!
    form(action=`/pets/${pet.id}?_method=PATCH`, method="POST")
        input(type="text", name="name", value=`${pet.name}`)
        input(type="submit", value="Edit a Pet!")
    form(action=`/pets/${pet.id}?_method=DELETE`, method="POST")
        input(type="submit", value="X")
```

## Intro to concepts(ASSOCIATES)

When we start building larger applications, we will need to create additional models. Many times, we will want to connect or associate data between one or more models. While this is a foundational part of relational databases, it is implemented a bit differently with non relational databases like MongoDB. There are two different ways to associate data with MongoDB: referencing and embedding.

## why's and how's??
### Embedding
The idea with embedding is that we place data we want to associate inside of an existing document. Let's imagine we wanted to create an application where we can create authors and books for each author. With embedding, our documents might look like this
```js
{
  id: 1,
  name: 'JK Rowling',
  countryOfOrigin: 'England',
  books: [{
    title: "Harry Potter and the Sorcerer's Stone",
    }, {
    title: "Harry Potter and the Goblet of Fire",
    }]
}
```
This is an example of a one-to-many relationship: one authoer has many books. To read more about embedding in mongo, read through [this](https://docs.mongodb.com/manual/tutorial/model-embedded-one-to-many-relationships-between-documents/) chapter.

### Referencing
When referencing, we use two separate collections for managing our data. In the example above, we would have a collection for authors and a collection for books.
```js
// authors
{
  id: 1,
  name: 'JK Rowling',
  countryOfOrigin: 'England'
}
// books
{
  id: 1,
  author_id: 1,
  title: "Harry Potter and the Sorcerer's Stone",
}

{
  id: 2,
  author_id: 1,
  title: "Harry Potter and the Goblet of Fire",
}
```
You can read more about referencing [here](https://docs.mongodb.com/manual/tutorial/model-referenced-one-to-many-relationships-between-documents/). We'll see a more explicit example of referencing in just a minute.

#### So which one?
In general, it's best to use embedding when you have smaller subdocuments and you want to read information quickly (since you do not need an additional collection query). Ideally you embed when data does not change frequently and when your documents do not grow frequently.

Referencing is useful when you have large subdocuments that change frequently and need faster writes (since you have a single collection and do not need a nested query). Referencing is also useful when you want to exclude parent data (show all the books, but don't worry about the authors of each one).

You can read more about how to think about which to choose [here](http://stackoverflow.com/questions/5373198/mongodb-relationships-embed-or-reference), [here](http://openmymind.net/Multiple-Collections-Versus-Embedded-Documents/), and [here](https://coderwall.com/p/px3c7g/mongodb-schema-design-embedded-vs-references).

### Building off our pets app
With Mongoose, we can easily embed and reference, but what makes Mongoose really helpful is that when we choose to reference, we can easily populate documents with their subdocuments without writing more complex mongo queries. If we correctly set up our schema, we will be able to easily query across multiple collections.

Let's revisit our previous pets application and create a new model called owner. Each owner will have an array of pets and each pet will have a single owner.

Let's look at our models/owners.js
```js
var mongoose = require("mongoose");
var ownerSchema = new mongoose.Schema({
    name: String,
    // every owner should have an array called pets
    pets: [{
      // which consists of a bunch of ids (we will use mongoose to populate the entire pet object, let's just store the id for now)
      type: mongoose.Schema.Types.ObjectId,
      // make sure that we reference the Pet model ('Pet' is defined as the first parameter to the mongoose.model method)
      ref: 'Pet'
    }]
});

var Owner = mongoose.model('Owner',ownerSchema);

module.exports = Owner;
// Let's look at our models/pets.js

var mongoose = require("mongoose");
var petSchema = new mongoose.Schema({
    name: String,
    // let's create a reference to the owner model
    owner: {
      // the type is going to be just an id
      type: mongoose.Schema.Types.ObjectId,
      // but it will refer to the Owner model (the first parameter to the mongoose.model method)
      ref: 'Owner'
    }
});

var Pet = mongoose.model('Pet',petSchema);

module.exports = Pet;
```
### RESTful routing for nested resources
Since we now have two resources, pets and owners and pets are dependent on owners (we can't have a pet without an owner), we need to nest or place our pets routes inside of owners. Here is what the RESTful routes for each resource will look like:

#### Owners

 |Verb  |Path|  Description
|:--:|:--:|:--:|
GET|  /owners|  Show all owners
GET|  /owners/new |Show a form for creating a new owner
GET|  /owners/:id |Show a single owner
GET |/owners/:id/edit |Show a form for editing a owner
POST| /owners |Create a owner when a form is submitted
PATCH |/owners/:id  |Edit a owner when a form is submitted
DELETE  |/owners/:id| Delete a owner when a form is submitted
#### Pets

HTTP Verb|  Path  |Description
|:--:|:--:|:--:|
GET|  /owners/:owner_id/pets  |Show all pets for an owner
GET |/owners/:owner_id/pets/new |Show a form for creating a new pet for an owner
GET|  /owners/:owner_id/pets/:id  |Show a single pet for an owner
GET |/owners/:owner_id/pets/:id/edit  |Show a form for editing an owner's pet
POST| /owners/:owner_id/pets  |Create a pet for an owner when a form is submitted
PATCH|  /owners/:owner_id/pets/:id  |Edit an owner's pet when a form is submitted
DELETE| /owners/:owner_id/pets/:id  |Delete an owner's pet when a form is submitted

As you can see, we will have quite a few more routes so this is where having a file for each resource is quite helpful! If you are dealing with nested routes, in the file you are placing nested routes, it is important to include the following when creating the express router:
```js
var express = require("express");
var router = express.Router({mergeParams: true}); // this is essential when working with nested routes
// CRUD with nested resources

// Let's now examine what it looks like to save a pet:

router.post('/owners/:owner_id/pets', function(req, res, next){
  var fido = new Pet(req.body);
  fido.owner = req.params.owner_id;
  fido.save().then(function(pet) {
    db.Owner.findById(req.params.owner_id).then(function(owner){
      owner.pets.push(fido)
      owner.save().then(function(owner) {
        res.redirect(`/owners/${owner.id}/pets`);
      });
    });
  });
});
```
How about finding all of the pets for an owner? Here we need to make use of a mongoose method called populate, which will populate the entire object for one or more object id's
```js
router.get('/owners/:owner_id/pets', function(req, res, next){
  db.Owner.findById(req.params.owner_id)
    .populate('pets')
    .exec()
    // owner now has a property called pets which is an array of all the populated pet objects!
    .then(function(owner){
      res.render('pets/index', {owner});
  });
});
```


# Intro to concept(MYSQL)
MySQL is the most popular Open Source Relational SQL Database Management System. MySQL is one of the best RDBMS being used for developing various web-based software applications. MySQL is developed, marketed and supported by MySQL AB, which is a Swedish company. This tutorial will give you a quick start to MySQL and make you comfortable with MySQL programming.

### what's a RDBMS?? 
A Relational DataBase Management System (RDBMS) is a software that −

- Enables you to implement a database with tables, columns and indexes.

- Guarantees the Referential Integrity between rows of various tables.

- Updates the indexes automatically.

- Interprets an SQL query and combines information from various tables.

## why's and how's??
### Create sql db
Create a database named "mydb":
```js
var mysql = require('mysql');

var con = mysql.createConnection({
  host: "localhost",
  user: "yourusername",
  password: "yourpassword"
});

con.connect(function(err) {
  if (err) throw err;
  console.log("Connected!");
  con.query("CREATE DATABASE mydb", function (err, result) {
    if (err) throw err;
    console.log("Database created");
  });
});
```
### create table
Create a table named "customers":
```js
var mysql = require('mysql');

var con = mysql.createConnection({
  host: "localhost",
  user: "yourusername",
  password: "yourpassword",
  database: "mydb"
});

con.connect(function(err) {
  if (err) throw err;
  console.log("Connected!");
  var sql = "CREATE TABLE customers (name VARCHAR(255), address VARCHAR(255))";
  con.query(sql, function (err, result) {
    if (err) throw err;
    console.log("Table created");
  });
});
```
### intsert into
Insert a record in the "customers" table:
```js
var mysql = require('mysql');

var con = mysql.createConnection({
  host: "localhost",
  user: "yourusername",
  password: "yourpassword",
  database: "mydb"
});

con.connect(function(err) {
  if (err) throw err;
  console.log("Connected!");
  var sql = "INSERT INTO customers (name, address) VALUES ('Company Inc', 'Highway 37')";
  con.query(sql, function (err, result) {
    if (err) throw err;
    console.log("1 record inserted");
  });
}); 
```
### select from
Select all records from the "customers" table, and display the return object:
```js
var mysql = require('mysql');

var con = mysql.createConnection({
  host: "localhost",
  user: "yourusername",
  password: "yourpassword",
  database: "mydb"
});

con.connect(function(err) {
  if (err) throw err;
  con.query("SELECT * FROM customers", function (err, result, fields) {
    if (err) throw err;
    console.log(result);
  });
});
```
### delete
Delete any record with the address "Mountain 21":
```js
var mysql = require('mysql');

var con = mysql.createConnection({
  host: "localhost",
  user: "yourusername",
  password: "yourpassword",
  database: "mydb"
});

con.connect(function(err) {
  if (err) throw err;
  var sql = "DELETE FROM customers WHERE address = 'Mountain 21'";
  con.query(sql, function (err, result) {
    if (err) throw err;
    console.log("Number of records deleted: " + result.affectedRows);
  });
});
```
### delete table
Delete the table "customers":
```js
var mysql = require('mysql');

var con = mysql.createConnection({
  host: "localhost",
  user: "yourusername",
  password: "yourpassword",
  database: "mydb"
});

con.connect(function(err) {
  if (err) throw err;
  var sql = "DROP TABLE customers";
  con.query(sql, function (err, result) {
    if (err) throw err;
    console.log("Table deleted");
  });
});
```
### update table
Overwrite the address column from "Valley 345" to "Canyon 123":
```js
var mysql = require('mysql');

var con = mysql.createConnection({
  host: "localhost",
  user: "yourusername",
  password: "yourpassword",
  database: "mydb"
});

con.connect(function(err) {
  if (err) throw err;
  var sql = "UPDATE customers SET address = 'Canyon 123' WHERE address = 'Valley 345'";
  con.query(sql, function (err, result) {
    if (err) throw err;
    console.log(result.affectedRows + " record(s) updated");
  });
});
```

## Intro to concept(MySQL Joins) 
MySQL JOINS are used to retrieve data from multiple tables. A MySQL JOIN is performed whenever two or more tables are joined in a SQL statement.
There are different types of MySQL joins:

- MySQL INNER JOIN (or sometimes called simple join)
- MySQL LEFT OUTER JOIN (or sometimes called LEFT JOIN)
- MySQL RIGHT OUTER JOIN (or sometimes called RIGHT JOIN)


## why's and how's??

### INNER JOIN (simple join)

Chances are, you've already written a statement that uses a MySQL INNER JOIN. It is the most common type of join. MySQL INNER JOINS return all rows from multiple tables where the join condition is met.

The syntax for the INNER JOIN in MySQL is:

    SELECT columns
    FROM table1 
    INNER JOIN table2
    ON table1.column = table2.column;

#### Visual Illustration

In this visual diagram, the MySQL INNER JOIN returns the shaded area:
<div align = "center">
<image src = "./imgs/1.jpg">
</div>

The MySQL INNER JOIN would return the records where table1 and table2 intersect.

#### Example

Here is an example of a MySQL INNER JOIN:

    SELECT suppliers.supplier_id, suppliers.supplier_name, orders.order_date
    FROM suppliers 
    INNER JOIN orders
    ON suppliers.supplier_id = orders.supplier_id;
This MySQL INNER JOIN example would return all rows from the suppliers and orders tables where there is a matching supplier_id value in both the suppliers and orders tables.

Let's look at some data to explain how the INNER JOINS work:

We have a table called suppliers with two fields (supplier_id and supplier_name). It contains the following data:

supplier_id |supplier_name
|:--:|:--:|
10000 |IBM
10001|  Hewlett Packard
10002|  Microsoft
10003|  NVIDIA
We have another table called orders with three fields (order_id, supplier_id, and order_date). It contains the following data:

order_id| supplier_id |order_date
|:--:|:--:|:--:|
500125| 10000|  2013/05/12
500126| 10001|  2013/05/13
500127| 10004|  2013/05/14
If we run the MySQL SELECT statement (that contains an INNER JOIN) below:

    SELECT suppliers.supplier_id, suppliers.supplier_name, orders.order_date
    FROM suppliers
    INNER JOIN orders
    ON suppliers.supplier_id = orders.supplier_id;
Our result set would look like this:

supplier_id|  name| order_date
|:--:|:--:|:--:|
10000 |IBM  |2013/05/12
10001|  Hewlett Packard |2013/05/13
The rows for Microsoft and NVIDIA from the supplier table would be omitted, since the supplier_id's 10002 and 10003 do not exist in both tables. The row for 500127 (order_id) from the orders table would be omitted, since the supplier_id 10004 does not exist in the suppliers table.
### LEFT OUTER JOIN

Another type of join is called a MySQL LEFT OUTER JOIN. This type of join returns all rows from the LEFT-hand table specified in the ON condition and only those rows from the other table where the joined fields are equal (join condition is met).

#### Syntax


    SELECT columns
    FROM table1
    LEFT [OUTER] JOIN table2
    ON table1.column = table2.column;
In some databases, the LEFT OUTER JOIN keywords are replaced with LEFT JOIN.

#### Visual Illustration

In this visual diagram, the MySQL LEFT OUTER JOIN returns the shaded area:

<div align = "center">
<image src = "./imgs/2.jpg">
</div>

The MySQL LEFT OUTER JOIN would return the all records from table1 and only those records from table2 that intersect with table1.

#### Example

Here is an example of a MySQL LEFT OUTER JOIN:

    SELECT suppliers.supplier_id, suppliers.supplier_name, orders.order_date
    FROM suppliers
    LEFT JOIN orders
    ON suppliers.supplier_id = orders.supplier_id;
This LEFT OUTER JOIN example would return all rows from the suppliers table and only those rows from the orders table where the joined fields are equal.

If a supplier_id value in the suppliers table does not exist in the orders table, all fields in the orders table will display as \<null> in the result set.

Let's look at some data to explain how LEFT OUTER JOINS work:

We have a table called suppliers with two fields (supplier_id and supplier_name). It contains the following data:

supplier_id |supplier_name
|:--:|:--:|
10000|  IBM
10001|  Hewlett Packard
10002|  Microsoft
10003|  NVIDIA
We have a second table called orders with three fields (order_id, supplier_id, and order_date). It contains the following data:

order_id| supplier_id|  order_date
|:--:|:--:|:--:|
500125| 10000 |2013/05/12
500126| 10001 |2013/05/13
If we run the SELECT statement (that contains a LEFT OUTER JOIN) below:

    SELECT suppliers.supplier_id, suppliers.supplier_name, orders.order_date
    FROM suppliers
    LEFT JOIN orders
    ON suppliers.supplier_id = orders.supplier_id;
Our result set would look like this:

supplier_id |supplier_name| order_date
|:--:|:--:|:--:|
10000|  IBM|  2013/05/12
10001|  Hewlett Packard|  2013/05/13
10002|  Microsoft|  \<null>
10003|  NVIDIA| \<null>
The rows for Microsoft and NVIDIA would be included because a LEFT OUTER JOIN was used. However, you will notice that the order_date field for those records contains a \<null> value.

### RIGHT OUTER JOIN

Another type of join is called a MySQL RIGHT OUTER JOIN. This type of join returns all rows from the RIGHT-hand table specified in the ON condition and only those rows from the other table where the joined fields are equal (join condition is met).

#### Syntax

The syntax for the RIGHT OUTER JOIN in MySQL is:

    SELECT columns
    FROM table1
    RIGHT [OUTER] JOIN table2
    ON table1.column = table2.column;
In some databases, the RIGHT OUTER JOIN keywords are replaced with RIGHT JOIN.

#### Visual Illustration

In this visual diagram, the MySQL RIGHT OUTER JOIN returns the shaded area:

<div align = "center">
<image src= "./imgs/3.jpg">
</div>

The MySQL RIGHT OUTER JOIN would return the all records from table2 and only those records from table1 that intersect with table2.

#### Example

Here is an example of a MySQL RIGHT OUTER JOIN:

    SELECT orders.order_id, orders.order_date, suppliers.supplier_name
    FROM suppliers
    RIGHT JOIN orders
    ON suppliers.supplier_id = orders.supplier_id;
This RIGHT OUTER JOIN example would return all rows from the orders table and only those rows from the suppliers table where the joined fields are equal.

If a supplier_id value in the orders table does not exist in the suppliers table, all fields in the suppliers table will display as \<null> in the result set.

Let's look at some data to explain how RIGHT OUTER JOINS work:

We have a table called suppliers with two fields (supplier_id and supplier_name). It contains the following data:

supplier_id|  supplier_name
|:--:|:--:|
10000|  Apple
10001|  Google
We have a second table called orders with three fields (order_id, supplier_id, and order_date). It contains the following data:

order_id| supplier_id |order_date
|:--:|:--:|:--:|
500125| 10000|  2013/08/12
500126| 10001|  2013/08/13
500127| 10002|  2013/08/14
If we run the SELECT statement (that contains a RIGHT OUTER JOIN) below:

    SELECT orders.order_id, orders.order_date, suppliers.supplier_name
    FROM suppliers
    RIGHT JOIN orders
    ON suppliers.supplier_id = orders.supplier_id;
Our result set would look like this:

order_id| order_date| supplier_name
|:--:|:--:|:--:|
500125| 2013/08/12| Apple
500126| 2013/08/13| Google
500127| 2013/08/14| \<null>
The row for 500127 (order_id) would be included because a RIGHT OUTER JOIN was used. However, you will notice that the supplier_name field for that record contains a \<null> value.


# Exploration: 
- Sample App
You can see how a basic folder structure and model would look like [here](https://github.com/rithmschool/node_curriculum_examples/tree/master/mongoose_intro).
- Wrapping up and Example App :

As you can see above, building these applications is a process that requires repetition to understand and become comfortable with. Take the time to build a few of these applications and see and debug errors as you go along!

You can see another sample CRUD app with Mongoose [here](https://github.com/rithmschool/node_curriculum_examples/tree/master/mongoose_crud).
- You can see a sample CRUD application with associations here.(https://github.com/rithmschool/node_curriculum_examples/tree/master/mongoose_associations)
