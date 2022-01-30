# MongoDB - The Complete Developer Guide <!-- omit in toc -->

![monogodb-logo](assets/mongodb-logosvg.svg)

- [1. An Intro](#1-an-intro)
  - [1.1. What is Mongoose?](#11-what-is-mongoose)
  - [1.2. MongoDB installation & setup](#12-mongodb-installation--setup)
  - [1.3. Connecting to MongoDB](#13-connecting-to-mongodb)
  - [1.4. Defining a Schema](#14-defining-a-schema)
  - [1.5. Storing and Querying Documents](#15-storing-and-querying-documents)
    - [1.5.1. Create/store document to MongoDB](#151-createstore-document-to-mongodb)
    - [1.5.2. Retrieve documents from MongoDB](#152-retrieve-documents-from-mongodb)
- [2. Mongoose Core](#2-mongoose-core)
  - [2.1. Models, Schemas, Connections, Documents, Queries](#21-models-schemas-connections-documents-queries)
  - [2.2. Documents: Casting, Validation, and Change Tracking](#22-documents-casting-validation-and-change-tracking)
    - [2.2.1. Casting](#221-casting)
    - [2.2.2. Validation](#222-validation)
    - [2.2.3. Change Tracking](#223-change-tracking)
  - [2.3. Schemas and SchemaTypes](#23-schemas-and-schematypes)
  - [2.4. Getters and Setters](#24-getters-and-setters)
    - [2.4.1. Getters](#241-getters)
    - [2.4.2. Setters](#242-setters)
    - [2.4.3. Advanced Getters/Setters](#243-advanced-getterssetters)
    - [2.4.4. Computed Properties using Setters](#244-computed-properties-using-setters)
  - [2.5. Virtuals](#25-virtuals)
    - [2.5.1. Virtuals and JSON.stringify()](#251-virtuals-and-jsonstringify)
  - [2.6. Queries](#26-queries)
  - [2.7. The Mongoose Global and Multiple Connections](#27-the-mongoose-global-and-multiple-connections)
- [3. Queries](#3-queries)
  - [3.1. Query Execution](#31-query-execution)
  - [3.2. Query Operations](#32-query-operations)
    - [3.2.1. Read](#321-read)
    - [3.2.2. Delete](#322-delete)
    - [3.2.3. Update](#323-update)
    - [3.2.4. Find and Modify](#324-find-and-modify)
    - [3.2.5. Other operation](#325-other-operation)
  - [3.3. Query Operators](#33-query-operators)
    - [3.3.1. Comparison](#331-comparison)
    - [3.3.2. Element/Type](#332-elementtype)
    - [3.3.3. Array](#333-array)
  - [3.4. Update Operators](#34-update-operators)
    - [3.4.1. Field Update Operators](#341-field-update-operators)
    - [3.4.2. Numeric Update Operators](#342-numeric-update-operators)
    - [3.4.3. Array Update Operators](#343-array-update-operators)
  - [3.5. Sort Order](#35-sort-order)
    - [3.5.1. Sorting Types Other than Strings and Numbers](#351-sorting-types-other-than-strings-and-numbers)
    - [3.5.2. With Query Selectors and Update Operators](#352-with-query-selectors-and-update-operators)
  - [3.6. Limit, Skip, Project](#36-limit-skip-project)
    - [3.6.1. Projections](#361-projections)
    - [3.6.2. Schema-Level Projections](#362-schema-level-projections)
  - [3.7. Query Casting and Validators](#37-query-casting-and-validators)
    - [3.7.1. Query Validation](#371-query-validation)
    - [3.7.2. Query Validators with Custom Validators](#372-query-validators-with-custom-validators)
  - [3.8. Corollary](#38-corollary)
- [4. Middleware](#4-middleware)
  - [4.1. `pre()` and `post()`](#41-pre-and-post)
  - [4.2. Async Middleware Functions](#42-async-middleware-functions)

## 1. An Intro

### 1.1. What is Mongoose?

Mongoose is a **object-document mapping (ODM)** framework for Node.js and MongoDB.

[Mongoose official site](https://mongoosejs.com/)

Overview of the core benefits of using Mongoose.

- The MongoDB Driver (In general, any DB driver) is responsible for maintaining a connection to MongoDB and serializing commands (queries) into MongoDB's wire protocol.
- It is entirely possible to build an application without Mongoose.
- Mongoose is built on top of the official *MongoDB Node.js Driver*.

The major benefits of using Mongoose over using the driver directly are:

1. **Application-level schema validation**: Mongoose can validate that untrusted data coming in over the network conforms to a declarative schema.
2. **Models**: Model-View-Controller (MVC) architecture enjoys widespread adoption. Mongoose models make it easy to write MongoDB code using standard MVC practices.
3. **Change tracking:** Mongoose converts vanilla JavaScript assignments into MongoDB atomic operations.
4. **Middleware**: Mongoose middleware helps you handle cross cutting concerns. Instead of adding a log statement every time you call `save()`, you can put a single log statement in `pre('save')`.
5. **Plugins**: Mongoose has a robust plugin architecture and an active community. [Mongoose plugin search](https://plugins.mongoosejs.io/)

Corollary:

**Mongoose is an ODM for Node.js and MongoDB. It provides schema validation, change tracking, middleware, and plugins. Mongoose also makes it easy to build apps using MVC patterns.**

### 1.2. MongoDB installation & setup

1. **MongoDB server and MongoDB Compass**:  
    Download and install MongoDB server `mongod` from here `https://www.mongodb.com/try/download/community`

    Notes:

    - Select **Complete** installation type & select the checkbox, that says, install MongoDB Compass
    - MongoDB Compass - The GUI for MongoDB to easily explore and manipulate the database.

2. **MongoDB Shell** - The CLI for MongoDB to easily explore and manipulate the database.  
    Download and install MongoDB `mongosh` from here `https://www.mongodb.com/try/download/shell`

Add `C:\Program Files\MongoDB\Server\5.0\bin` to System `Path` environment variable.

### 1.3. Connecting to MongoDB

```console
mkdir try-mongodb
cd try-mongodb
echo "" > index.js
npm init -y
npm install mongoose
```

package.json

```json
{
  .
  .
  "type": "module",
  .
  .
}
```

index.js

```js
import mongoose from "mongoose";
mongoose.connect("mongodb://localhost:27017/mydb");
```

- The first parameter to `mongoose.connect()` is known as the connection string.
- The connection string defines which MongoDB server(s) you're connecting to and the name of the database you want to use.
- More sophisticated connection strings can also include authentication information and configuration options.
- The `mydb` section of the connection string tells Mongoose which database to use.
- MongoDB stores data in the form of **documents**.
- A document is essentially a Node.js object, and analogous to a row in SQL databases.
- Every document belongs to a collection, and every collection belongs to a database.
- Think of a database as a set of collections.
  
We can, and generally should, `await` on `mongoose.connect()` to ensure Mongoose connects successfully. like `await mongoose.connect("mongodb://localhost:27017/mydb");`

> Many Mongoose apps do not wait for Mongoose to connect because it isn't strictly necessary. Don't be surprised if you see `mongoose.connect()` with no `await` or `then()`.

### 1.4. Defining a Schema

To store and query MongoDB documents using Mongoose, you need to define a model.
Mongoose models are the primary tool you'll use for creating and loading documents. To define a
model, you first need to define a schema.

In general, Mongoose schemas are objects that configure models. In particular, schemas are responsible for defining what properties your documents can have and what types those
properties must be.

Ex, let's say, we are creating a model to represent a `product`. A minimal product should
have a `string` property `name` and a `number` property `price` as shown below.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const productSchema = new mongoose.Schema({
  // A product has two properties: `name` and `price`
  name: String,
  price: Number,
});

// The `mongoose.model()` function has 2 required parameters:
// The 1st param is the model's name, a string
// The 2nd param is the schema
const Product = mongoose.model("Product", productSchema);

const product = new Product({
  name: "iPhone",
  price: "800", // Note that this is a string, not a number
  notInSchema: "foo",
});

console.log(product);
// {
//   name: 'iPhone',
//   price: 800, // 800, Mongoose converted this to a number
//   _id: new ObjectId("61f3b447c88121a8b454ae83")
// }

console.log(product.notInSchema);
// undefined, Mongoose removes props that aren't in the schema
```

`node index.js` to run the app and see it also created db with name `mydb`

```console
> mongosh
test> show dbs
admin       41 kB
config     111 kB
local     73.7 kB
mern-app  73.7 kB
mydb      8.19 kB
```

The `mongoose.model()` function takes the model's name and schema as parameters, and returns a `class`. That class is configured to cast, validate, and track changes on the paths defined in the schema.

Schemas have numerous features beyond just type checking. For example, you can make Mongoose lowercase the product's name as shown below.

```js
const productSchema = new mongoose.Schema({
  // The below means the `name` property should be a string
  // with an option `lowercase` set to `true`.
  name: { type: String, lowercase: true },
  price: Number,
});
const Product = mongoose.model("Product", productSchema);
const product = new Product({ name: "iPhone", price: 800 });
console.log(product.name); // 'iphone', lowercased
```

How does the model class looks like?

Internally, when you instantiate a Mongoose model, Mongoose defines native JavaScript getters and setters for every path (prop) in your schema on every instance of that model.

Ex,

```js
class Product {
  constructor(obj) {
    // `_doc` stores the raw data, bypassing getters and setters
    // Otherwise `doc.name = 'foo'` causes infinite recursion
    this._doc = {};
    Object.assign(this, { name: obj.name, price: obj.price });
  }
  get name() {
    return this._doc.name;
  }
  set name(v) {
    this._doc.name = v == null ? v : "" + v;
  }
  get price() {
    return this._doc.price;
  }
  set price(v) {
    this._doc.price = v == null ? v : +v;
  }
}
const p = new Product({ name: "iPhone", price: "800", foo: "bar" });
p.name; // 'iPhone'
p.price; // 800
p.foo; // undefined
```

### 1.5. Storing and Querying Documents

- In Mongoose, a model is a class, and an instance of a model is called a `document`.
- Also, a `document` as an object stored in a MongoDB collection.

These two definitions are equivalent for practical purposes, because a Mongoose document in Node.js maps one-to-one to a document stored on the MongoDB server.

#### 1.5.1. Create/store document to MongoDB

- To create a new document, you can use your model as a constructor. This creates a new document that has **not** been stored in MongoDB yet.
- Documents have a `save()` function that you use to persist changes to MongoDB.
- When you create a new document, the `save()` function sends an `insertOne()` operation to the MongoDB server.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const productSchema = new mongoose.Schema({
  name: String,
  price: Number,
});
const Product = mongoose.model("Product", productSchema);

// `doc` is just in Node.js, you haven't persisted it to MongoDB yet
const doc = new Product({
  name: "iPhone",
  price: "800",
});

// Store `doc` in MongoDB
await doc.save();
```

```console
> mongosh
> use mydb
mydb> db.products.find()
[
  {
    _id: ObjectId("61f3c00230b0c08b816c6eae"),
    name: 'iPhone',
    price: 800,
    __v: 0
  }
]
```

#### 1.5.2. Retrieve documents from MongoDB

- To load an existing document from MongoDB, you can use the `Model.find()` or `Model.findOne()` functions.

```js
// `Product.findOne()` returns a Mongoose _query_. Queries are
// thenable, which means you can `await` on them.
let product = await Product.findOne();
product.name; // "iPhone"
product.price; // 800
const products = await Product.find();
product = products[0];
product.name; // "iPhone"
product.price; // 800
```

Connecting to MongoDB, defining a schema, creating a model, creating documents, and loading documents are the basic patterns that you'll see in almost every Mongoose app.

## 2. Mongoose Core

### 2.1. Models, Schemas, Connections, Documents, Queries

Mongoose has 5 core concepts that every other concept in the framework builds on

1. A **Model** is a class representing an object stored in MongoDB. Models are the primary way you interact with MongoDB using Mongoose. To create a model, you need a
schema and a connection.
2. A **Schema** is class representing a configuration object for a model.
3. A **Connection** is a class representing a pool of sockets that connect to one or more MongoDB server processes. A connection provides an interface for models to communicate with MongoDB.
4. A **Document** is an instance of a model. You can `save()` a document to MongoDB.
5. A **Query** is class representing an operation that Mongoose will send to MongoDB server.

Here's an example of the 5 core concepts in action:

```js
const mongoose = require("mongoose");

// Here's how you create a connection:
const conn = mongoose.createConnection();
conn instanceof mongoose.Connection; // true

// Here's how you create a schema
const schema = new mongoose.Schema({ name: String });

// Calling `conn.model()` creates a new model. In this book
// a "model" is a class that extends from `mongoose.Model`
const MyModel = conn.model("ModelName", schema);
Object.getPrototypeOf(MyModel) === mongoose.Model; // true

// You shouldn't instantiate the `Document` class directly.
// You should use a model instead.
const document = new MyModel();
document instanceof MyModel; // true
document instanceof mongoose.Document; // true
document instanceof mongoose.Model; // true

// Query
const query = MyModel.find();
query instanceof mongoose.Query; // true
```

Models are the primary concept you will use to interact with MongoDB using Mongoose. In order to create a model you need a connection and a schema. A schema configures your model, and a connection provides your model a low-level database interface.

Under the hood, Mongoose's `Model` class inherits from the `Document` class. When you create a new model using `mongoose.model()`, you create a new class that inherits from `Model`. However, for convenience, Mongoose developers typically use the term `model` to refer to classes that inherit from `Model`, and the term `document` to refer to instances of models.

### 2.2. Documents: Casting, Validation, and Change Tracking

Two ways to create a model in Mongoose,

1. `mongoose.model()` function, which takes 2 parameters: a model name and a schema

    ```js
    const mongoose = require("mongoose");
    mongoose.model("MyModel", new mongoose.Schema({}));
    ```

2. create a new connection and call the connection's `model()` function

    ```js
    const conn = mongoose.createConnection();
    conn.model("MyModel", new mongoose.Schema({}));
    ```

Remember that a model needs both a connection and a schema. Mongoose has a default connection `mongoose.connection`. That is the connection `mongoose.model()` uses.

Ex,

```js
// `mongoose.model()` uses the default connection
mongoose.model("MyModel", schema);
// So the below function call does the same thing
mongoose.connection.model("MyModel", schema);
```

Note that the return value of `mongoose.model()` is not an instance of the Model class. Rather, `mongoose.model()` returns a class that extends from `Model`.

Let's prove it?

```js
const MyModel = mongoose.model("MyModel", new mongoose.Schema({}));
// `MyModel` is a class that extends from `mongoose.Model`, _not_
// an instance of `mongoose.Model`.
console.log(MyModel instanceof mongoose.Model); // false
console.log(Object.getPrototypeOf(MyModel) === mongoose.Model); // true
```

Because `Document` and `Model` are distinct classes, some functions are documented under the `Document` class and some under the `Model` class. For example, `save()` is defined on the `Model` class, not the `Document` class.

#### 2.2.1. Casting

*Casting* means converting values to the types defined in your schema.

Mongoose converts arbitrary values to the correct type, or tracks a `CastError` if it failed to cast the specified value. Calling `save()` will fail if there was a `CastError`.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const MyModel = mongoose.model("MyModel", schema);

const doc = new MyModel({ name: "avinash", age: 30 });

doc.age = "not a number";
await doc.save();

// Throws CastError: Cast to Number failed for value "not a number" (type string) at path "age"
```

If casting fails, Mongoose will not overwrite the current value of the path. In the above example, `doc.age` will still be 30 after trying to assign 'not a number'.

You can clear the cast error by setting age to a value Mongoose can cast to a number. In the below example, Mongoose will successfully save the document, because setting age to a number clears the cast error,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const MyModel = mongoose.model("MyModel", schema);

const doc = new MyModel({ name: "avinash", age: 30 });

doc.age = "not a number";
doc.age; // 59

doc.age = "12";
doc.age; // 12 as a number

// Saving succeeds, because Mongoose successfully converted the
// string '12' to a number and cleared the previous CastError
await doc.save();
```

By default, Mongoose casting considers `null` and `undefined` (collectively known as `nullish` values) as valid values for **any** type.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const MyModel = mongoose.model("MyModel", schema);

const doc = new MyModel();

doc.age = null;
doc.name = undefined;

// Succeeds, because Mongoose casting lets `null` and `undefined`.
// `name` wont saved to db at all in the database.
// `age` will set to be `null` in the database.
await doc.save();

/**
 * mydb> db.mymodels.find()
 * [ { _id: ObjectId("61f3d12e38ff1132714c7d67"), age: null, __v: 0 } ]
 */
```

If you want to disallow `null` values for a path, you need to use validation.

#### 2.2.2. Validation

- Casting converts a value to the correct type. After Mongoose casts the value successfully, validation lets you further restrict what values are valid.
- However, validation can't transform the value being validated, whereas casting may change the value.
- When you call `save()`, Mongoose runs validation on successfully casted values, and reports a `ValidatorError` if validation fails.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = new mongoose.Schema({
  name: {
    type: String,
    // `enum` adds a validator to `name` path(prop). The built-in `enum`
    // validator ensures `name` will be one of the below values.
    enum: ["Dharmaray", "Bhim", "Arjun", "Nakul", "Sahadev"],
  },
});
const Person = mongoose.model("Person", schema);
const doc = new Person({ name: "Karna" });

await doc.save().catch((error) => {
  console.log(error); // Error: Person validation failed: name: `Karna` is not a valid enum value for path `name`.
  console.log(error.errors["name"]); // ValidatorError: `Karna` is not a valid enum value for path `name`.
  Object.keys(error.errors); // ['name']
});
```

If `validate()` fails, Mongoose rejects the promise with a `ValidationError`. The `ValidationError` has an `errors` property that contains a hash mapping errored paths to the corresponding `ValidatorError` or `CastError`. In other words, a `ValidationError` can contain multiple `ValidatorError` objects and/or `CastError` objects.

Mongoose has numerous built-in validators. Here's a list of all the built-in validators Mongoose supports for different types:

- Strings: enum, match, minlength, maxlength
- Numbers: min, max
- Dates: min, max

There is one built-in validator that applies to all schema types: the `required` validator. **The required validator disallows nullish values**.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = new mongoose.Schema({
  name: {
    type: String,
    // Adds the `required` validator to the `name` path
    required: true,
  },
});
const Person = mongoose.model("Person", schema);
const doc = new Person({ name: null });
await doc.validate().catch((error) => {
  console.log(error); // Error: Person validation failed: name: Path `name` is required.
  console.log(error.errors["name"]); // ValidatorError: Path `name` is required.
});
```

Mongoose documents have a `validate()` function that you can use to manually run validation. The `validate()` function is asynchronous, so you need to `await` on it.

#### 2.2.3. Change Tracking

Mongoose documents have *change tracking*.

That means Mongoose tracks vanilla JavaScript assignments, like `doc.name = 'foo'` or `Object.assign(doc, { name: 'foo' })`, and converts them into MongoDB update operators. When you call `save()`, Mongoose sends your changes to MongoDB.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const MyModel = mongoose.model(
  "MyModel",
  mongoose.Schema({
    name: String,
    age: Number,
  })
);
const doc = new MyModel({});
doc.name = "avinash";
doc.age = 27;
await doc.save(); // Persist `doc` to MongoDB
```

When you load a document from the database using a query, change tracking means Mongoose
can determine the minimal update to send to MongoDB and avoid wasting network bandwidth.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const MyModel = mongoose.model(
  "MyModel",
  mongoose.Schema({
    name: String,
    age: Number,
  })
);

// Mongoose loads the document from MongoDB and then _hydrates_ it into a full Mongoose document.
const doc = await MyModel.findOne();
doc.name; // "avinash"
doc.name = "avinash c";
doc.modifiedPaths(); // ['name']
// `save()` only sends updated paths to MongoDB. Mongoose doesn't send `age`.
await doc.save();
```

A Mongoose document is an instance of a Mongoose model. Documents track changes, so you can modify documents using vanilla JavaScript operations and rely on Mongoose to send a minimal update to MongoDB when you `save()`.

Mongoose does more with change tracking than just structuring MongoDB updates: it also makes sure your JavaScript assignments conform to a schema. Let's take a closer look at schemas.

### 2.3. Schemas and SchemaTypes

In Mongoose, a schema is a configuration object for a model. A schema's primary responsibility is to define the paths that the model will *cast*, *validate*, and *track changes* on.

Ex,

This schema has two explicitly defined paths: `name` and `age`. Mongoose also adds an `_id` path to schemas by default.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

Object.keys(schema.paths); // ['name', 'age', '_id']

schema.path("name"); // SchemaString { path: 'name', ... }

schema.path("name") instanceof mongoose.SchemaType; // true

schema.path("age"); // SchemaNumber { path: 'age', ... }

schema.path("age") instanceof mongoose.SchemaType; // true

// The `SchemaString` class inherits from `SchemaType`.
schema.path("name") instanceof mongoose.Schema.Types.String; // true
```

- A Mongoose SchemaType, like the `SchemaString` class, does not contain an actual value.
- There is no string value associated with an instance of `Schema.Types.String`
- A `SchemaType` instance is a configuration object for an individual path: it describes what casting, validation, and other Mongoose features should apply to a given path.

> Don't use the terms "schema" and "model" interchangeably. Don't do this! A schema **configures** a model, but a schema instance can't write to the database.  
> Similarly, a document is not an instance of a schema. Documents are instances of models, and models have an associated schema.  
> In object-oriented programming terminology, a model **has a** schema, and a model **is a** document.

### 2.4. Getters and Setters

- In Mongoose, getters/setters allow you to execute custom logic when getting or setting a property on a document.
- Getters are useful for converting data that's stored in MongoDB into a more human-friendly form.
- Setters are useful for converting user data into a standardized format for storage in MongoDB.

#### 2.4.1. Getters

Mongoose executes your custom getter when you access a property. In the below example, the string path `email` has a custom getter that replaces `@` with ` [at] `.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = new mongoose.Schema({ email: String });

// `v` is the underlying value stored in the database
schema.path("email").get((v) => v.replace("@", " [at] "));

const Model = mongoose.model("User", schema);
const doc = new Model({ email: "test@gmail.com" });

console.log(doc.email); // 'test [at] gmail.com'
console.log(doc.get("email")); // 'test [at] gmail.com'
```

- Getters do not impact the underlying data stored in MongoDB. If you save `doc`, the `email` property will be `test@gmail.com` in the database.
- Mongoose applies the getter every time you access the property. In the above example, Mongoose runs the getter function **twice**, once for `doc.email` and once for `doc.get('email')`.
- Another useful feature of getters is that Mongoose applies getters when converting a document to JSON (JSON.parse(), in express res.json() etc).

You can also skip running getters on a one-off basis using the `Document#get()` function's getters option.

```js
// Make sure you don't forget to pass `null` as the 2nd parameter.
doc.get('email', null, { getters: false }); // 'test@gmail.com'
```

#### 2.4.2. Setters

Mongoose executes custom setters when you assign a value to a property.

Suppose you want to ensure all user emails are lowercased, so users don't have to worry about case when logging in.

Ex, The below userSchema adds a custom setter that lowercase the emails.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const userSchema = mongoose.Schema({ email: String });
userSchema.path("email").set((v) => v.toLowerCase());

const User = mongoose.model("User", userSchema);
const user = new User({ email: "TEST@gmail.com" });

console.log(user.email); // 'test@gmail.com'
```

Mongoose will call the custom setter regardless of which syntax you use to set the email
property.

```js
doc.email = 'TEST@gmail.com'
doc.set('email', 'TEST@gmail.com')
Object.assign(doc, { email: 'TEST@gmail.com' })
```

Mongoose also runs setters on update operations:

```js
updateOne()
updateMany()
findOneAndUpdate()
findOneAndReplace()
```

Ex, Mongoose will run the `toLowerCase()` setter in the below example, because the below example calls `updateOne()`.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const userSchema = mongoose.Schema({ email: String });
userSchema.path("email").set((v) => v.toLowerCase());

const User = mongoose.model("User", userSchema);

const { _id } = await User.create({ email: "test@gmail.com" });

// Mongoose will run the `toLowerCase()` setter on `email`
await User.updateOne({ _id }, { email: "NEW@gmail.com" });

const doc = await User.findOne({ _id });
console.log(doc.email); // 'new@gmail.com'
```

#### 2.4.3. Advanced Getters/Setters

Getters and setters are good for more than just manipulating strings. You can also use getters and setters to transform values to and from different types.

Ex, MongoDB ObjectIds are objects that are commonly represented as strings that look like `5d124083fc741d44eca250fd`.

> However, you cannot compare ObjectIds using JavaScript's `===` operator, or even the `==` operator.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const str = "5d124083fc741d44eca250fd";

const schema = mongoose.Schema({ objectid: mongoose.ObjectId });

const Model = mongoose.model("ObjectIdTest", schema);

const doc1 = new Model({ objectid: str });
const doc2 = new Model({ objectid: str });

// Mongoose casted the string `str` to an mongoose.ObjectId
typeof doc1.objectid; // 'object'
doc1.objectid instanceof mongoose.Types.ObjectId; // true
doc1.objectid === doc2.objectid; // false
doc1.objectid == doc2.objectid; // false
```

So, How we solve above problem?

Write a custom getter that converts the `objectid` property into a `string` for comparison purposes.

Ex,

```js
import assert from "assert";
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const str = "5d124083fc741d44eca250fd";

const schema = mongoose.Schema({ objectid: mongoose.ObjectId });
// Add a custom getter that converts ObjectId values to strings
schema.path("objectid").get((v) => v.toString());

const Model = mongoose.model("ObjectIdTest", schema);
const doc1 = new Model({ objectid: str });
const doc2 = new Model({ objectid: str });

// Mongoose now converts `objectid` to a string for you
typeof doc1.objectid; // 'string'
// The raw value stored in MongoDB is still an ObjectId
typeof doc1.get("objectid", null, { getters: false }); // 'object'

doc1.objectid === doc2.objectid; // true
doc1.objectid == doc2.objectid; // true

assert.deepEqual(doc1.toObject(), doc2.toObject()); // passes
```

Mongoose casting converts strings to ObjectIds for you, so in the above example you don't need to write a custom setter.

#### 2.4.4. Computed Properties using Setters

Many codebase use setters to handle computed properties, properties that are computed from other properties in the document.

Ex, suppose you want to store a user's email domain (for example, "gmail.com") in addition to the user's email.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const userSchema = mongoose.Schema({ email: String, domain: String });
// Whenever you set `email`, make sure you update `domain`.
userSchema.path("email").set(function (v) {
  // In a setter function, `this` may refer to a document or a query.
  // If `this` is a document, you can modify other properties.
  this.domain = v.slice(v.indexOf("@") + 1);
  return v;
});

const User = mongoose.model("User", userSchema);

const user = new User({ email: "test@gmail.com" });

console.log(user.email); // 'test@gmail.com'
console.log(user.domain); // 'gmail.com'
```

Using setters to modify other properties within the document is viable, but you should avoid doing so unless there's no other option. **However, it's recommend using virtual or middleware instead.**

Why computed properties using setters not recommended?

```js
// Since `domain` is a real property, you can `set()` it and
// overwrite the computed value.
let user = new User({ email: 'test@gmail.com', domain: 'oops' });
user.domain; // 'oops'
```

This means using setters for computed properties is unpredictable.

### 2.5. Virtuals

- You can handle computed properties with getters and setters, but virtuals are typically the better choice.
- **In Mongoose, a virtual is a property that is not stored in MongoDB.**
- Virtuals can have getters and setters just like normal properties.

Consider the previous example of a `domain` property that stores everything after the `@` in the user's email. Instead of a custom setter that updates the `domain` property every time the `email` property is modified, you can create a `virtual` property `domain`.

The virtual property `domain` will have a getter that recomputes the user's email domain every time the property is accessed.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const userSchema = mongoose.Schema({ email: String });
userSchema.virtual("domain").get(function () {
  return this.email.slice(this.email.indexOf("@") + 1);
});

const User = mongoose.model("User", userSchema);

let doc = await User.create({ email: "test@gmail.com" });

doc.domain; // 'gmail.com'
// Mongoose ignores setting virtuals that don't have a setter
doc.set({ email: "test@test.com", domain: "foo" });
doc.domain; // 'test.com
```

Note that, even though the above code tries to `set()` the domain property to an incorrect value, `domain` is still correct.

> Setting a virtual property without a setter is a no-op.

This virtual-based `userSchema` can safely accept untrusted data without risk of corrupting `domain`.

The downside of virtuals is that, since they are **not** stored in MongoDB, you cannot query documents by virtual properties.

If you try to query by a virtual property like `domain`, you won't get any results. That's because MongoDB doesn't know about `domain`, `domain` is a property that Mongoose computes in Node.js.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const userSchema = mongoose.Schema({ email: String });
userSchema.virtual("domain").get(function () {
  return this.email.slice(this.email.indexOf("@") + 1);
});

const User = mongoose.model("User", userSchema);

await User.create({ email: "test@gmail.com" });
// `doc` will be null, because the document in the database
// does **not** have a `domain` property
const doc = await User.findOne({ domain: "gmail.com" }, null, {
  strictQuery: false,
});
console.log(doc); // null
```

> Mongoose supports a separate `strictQuery` option to avoid strict mode for query filters. This is because empty query filters cause Mongoose to return all documents in the model, which can cause issues. it means turn off strict mode for query filters.
> [Click here for official doc](https://mongoosejs.com/docs/guide.html#strictQuery)

#### 2.5.1. Virtuals and JSON.stringify()

By default, Mongoose does **not** include virtuals when you convert a document to JSON.  
For example, if you pass a document to the Express web framework's `res.json()` function, virtuals will not be included by default.

You need to set the `toJSON` schema option to `{ virtuals: true }` to opt in to including virtuals in your document's JSON representation.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

// Opt in to Mongoose putting virtuals in `toJSON()` output for `JSON.stringify()`, and in `toObject()` output.
const opts = { toJSON: { virtuals: true } };

const userSchema = mongoose.Schema({ email: String }, opts);
userSchema.virtual("domain").get(function () {
  return this.email.slice(this.email.indexOf("@") + 1);
});

const User = mongoose.model("User", userSchema);

const doc = await User.create({ email: "test@gmail.com" });

console.log(doc);
// {
//   email: 'test@gmail.com',
//   _id: new ObjectId("61f3f7b0ea82ae6ba62d24fc"),
//   __v: 0
// }

console.log(doc.toJSON());
// {
//   email: 'test@gmail.com',
//   _id: new ObjectId("61f3f7b0ea82ae6ba62d24fc"),
//   __v: 0,
//   domain: 'gmail.com',
//   id: '61f3f7b0ea82ae6ba62d24fc'
// }

console.log(JSON.stringify(doc));
// {"email":"test@gmail.com","_id":"61f3f7d929209332d2b82ff7","__v":0,"domain":"gmail.com","id":"61f3f7d929209332d2b82ff7"}
```

JavaScript's native `JSON.stringify()` function looks for `toJSON()` functions, and replaces the object being stringified with the result of `toJSON()`.

Look at this simple example, its a default behavior of `JSON.stringify()` in JavaScript.

```js
const myObject = {
  toJSON: function () {
    // Will print once when you call `JSON.stringify()`.
    console.log("Called!");
    return 42;
  },
};
// `{"prop":42}`. That is because `JSON.stringify()` uses the result
// of the `toJSON()` function.
console.log(JSON.stringify({ prop: myObject }));
```

You can also configure the `toJSON` schema option globally:

```js
mongoose.set('toJSON', { virtuals: true });
```

### 2.6. Queries

So far, we had only used `Model.findOne()` and `Model.updateOne()` as promises using the `await` keyword.

```js
const doc = await MyModel.findOne();
```

When you call `Model.findOne()`, Mongoose does not return a promise. It returns an instance of the **Mongoose Query class**, which can be used as a promise.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const userSchema = mongoose.Schema({ email: String });

const User = mongoose.model("User", userSchema);

const query = User.findOne();
query instanceof mongoose.Query; // true

// Each model has its own subclass of the `mongoose.Query` class
query instanceof User.Query; // true

const doc = await query; // Executes the query

console.log(doc);

// {
//   _id: new ObjectId("61f3e8126d74d8c7dd608633"),
//   email: 'new@gmail.com',
//   __v: 0
// }
```

The reason why Mongoose has a special Query class rather than just returning a promise from `Model.findOne()` is for chaining. You can construct a query by chaining query helper methods like `where()` and `equals()`.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const userSchema = mongoose.Schema({ email: String });

const User = mongoose.model("User", userSchema);

// Equivalent to `await Model.findOne({ email: 'new@gmail.com' })`
const doc = await User.findOne().where("email").equals("new@gmail.com");
console.log(doc);

// {
//   _id: new ObjectId("61f3e8126d74d8c7dd608633"),
//   email: 'new@gmail.com',
//   __v: 0
// }
```

Each query has a `op` property, which contains the operation Mongoose will send to the MongoDB server.

For example, when you call `Model.findOne()`, Mongoose sets op to `findOne`.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const userSchema = mongoose.Schema({ email: String });

const User = mongoose.model("User", userSchema);

const query = User.findOne();
console.log(query.op); // findOne

const query1 = User.deleteMany();
console.log(query1.op); // deleteMany
```

Below are the possible values for `op`. There's a corresponding method on the `Query` class for each of the below values of `op`.

```md
find
findOne
findOneAndDelete
findOneAndUpdate
findOneAndReplace
deleteOne
deleteMany
replaceOne
updateOne
updateMany
```

You can modify the `op` property before executing the query. Until you execute the query, you can change the `op` by calling any of the above methods.

For example, some projects chain `.find().updateOne()` as shown below, because separating out the query filter and the update operation may make your code more readable.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const userSchema = mongoose.Schema({ email: String, age: Number });

const User = mongoose.model("User", userSchema);

// Equivalent to `Model.updateOne({ name: 'avi' },{ $set: { age: 59 } })`
const query = User.find({ name: "avi" }).updateOne({}, { $set: { age: 59 } });
```

Mongoose does **not** automatically execute a query, you need to explicitly execute the query.

There's two methods that let you execute a query: `exec()` and `then()`. When you `await` on a query, the JavaScript runtime calls then() under the hood.

```js
// The `exec()` function executes the query and returns a promise.
const promise1 = User.findOne().exec();

// The `then()` function calls `exec()` and returns a promise
const promise2 = User.findOne().then((doc) => doc);
// Equivalently, `await` calls `then()` under the hood
const doc = await User.findOne();
```

### 2.7. The Mongoose Global and Multiple Connections

When you `require('mongoose')`, you get an instance of the Mongoose class.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

// Mongoose exports an instance of the `Mongoose` class
mongoose instanceof mongoose.Mongoose; // true
```

When someone refers to the "Mongoose global" or "Mongoose singleton", they're referring to the object you get when you `require('mongoose')`.

However, you can also instantiate an entirely separate Mongoose instance with its own options, models, and connections.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const mongoose1 = new mongoose.Mongoose();
const mongoose2 = new mongoose.Mongoose();
mongoose1.set("toJSON", { virtuals: true });
console.log(mongoose1.get("toJSON")); // { virtuals: true }
console.log(mongoose2.get("toJSON")); // undefined
```

In particular, the Mongoose global has a default `connection` that is an instance of the `mongoose.Connection` class. When you call `mongoose.connect()`, that is equivalent to calling `mongoose.connection.openUri()`.

Ex,

```js
import mongoose from "mongoose";

const mongoose1 = new mongoose.Mongoose();
const mongoose2 = new mongoose.Mongoose();

mongoose1.connection instanceof mongoose.Connection; // true
mongoose2.connection instanceof mongoose.Connection; // true

mongoose1.connections.length; // 1
mongoose1.connections[0] === mongoose1.connection; // true

console.log(mongoose1.connection.readyState); // 0, 'disconnected'
mongoose1.connect("mongodb://localhost:27017/mydb", { useNewUrlParser: true });
console.log(mongoose1.connection.readyState); // 2, 'connecting'
```

When you call `mongoose.createConnection()`, you create a new connection object that Mongoose tracks in the `mongoose.connections` property.

```js
import mongoose from "mongoose";

const mongoose1 = new mongoose.Mongoose();

const conn = mongoose1.createConnection("mongodb://localhost:27017/test", {
  useNewUrlParser: true,
});

console.log(mongoose1.connections.length); // 2
mongoose1.connections[1] === conn; // true
```

Why do you need multiple connections? For most apps, you only need one connection. Here's a couple examples when you would need to create multiple connections:

- Your app needs to access data stored in multiple databases. A Mongoose connection is scoped to exactly one database - if you need to create models on different databases, you need a separate connection.

```js
import mongoose from "mongoose";

const conn1 = mongoose.createConnection("mongodb://localhost:27017/db1", {
  useNewUrlParser: true,
});
const conn2 = mongoose.createConnection("mongodb://localhost:27017/db2", {
  useNewUrlParser: true,
});

// Will store data in the 'db1' database's 'tests' collection
const Model1 = conn1.model("Test", mongoose.Schema({ name: String }));

// Will store data in the 'db2' database's 'tests' collection
const Model2 = conn2.model("Test", mongoose.Schema({ name: String }));
```

- Your app has certain operations that are slow, and you don't want the slow operations to cause performance issues on fast queries. Will discuss how multiple connections can help speed up your app.

In simple words, when you `require('mongoose')`, you get back an instance of the Mongoose class. An instance of the `Mongoose` class tracks a list of connections, and stores some global options. Advanced users may want to create multiple Mongoose instances, but most apps will only use the singleton `Mongoose` instance you get when you `require('mongoose')`.

## 3. Queries

Queries and aggregations are how you fetch data from MongoDB into Mongoose.

You can also use queries to update data in MongoDB without needing to fetch the documents.

Mongoose models have several static functions, like `find()` and `updateOne()`, that return Mongoose `Query` objects.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const Model = mongoose.model("Model", schema);

const query = Model.find({ age: { $lte: 30 } });
query instanceof mongoose.Query; // true
```

Mongoose queries provide a chainable API for creating and executing CRUD (create, ead, update, delete) operations. Mongoose queries let you build up a CRUD operation in Node.js, and then send the operation to the MongoDB server.

### 3.1. Query Execution

Mongoose queries are just objects in Node.js memory until you actually execute them. There are 3 ways to execute a query:

1. `await` on the query
2. Call `Query#then()`
3. Call `Query#exec()`

Under the hood, all 3 ways are equivalent. The `await` keyword calls `Query#then()`, and
Mongoose's `Query#then()` calls `Query#exec()`, so (1) and (2) are just syntactic sugar for (3).

Here are the 3 ways to execute a query in code form:

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const Model = mongoose.model("Model", schema);

const query = Model.find({ age: { $lte: 30 } });

// Execute the query 3 times, in 3 different ways
await query; // 1
query.then((res) => {}); // 2
await query.exec(); // 3
```

Note that a query can be executed multiple times. The above example executes 3 separate
`findOne()` queries.

The actual operation that Mongoose will execute is stored as a string in the `Query#op` property. Below is a list of all valid query ops, grouped by CRUD verb.

**Read:**

- `count`: return the number of documents that match `filter` (deprecated)
- `countDocuments`: return the number of documents that match `filter`
- `distinct`: return a list of the distinct values of a given field
- `estimatedDocumentCount`: return the number of documents in the collection
- `find`: return a list of documents that match `filter`
- `findOne`: return the first document that matches `filter`, or `null`

**Update:**

- `findOneAndReplace`: same as `replaceOne()` + returns the replaced document
- `findOneAndUpdate`: same as `updateOne()` + returns the replaced document
- `replaceOne`: replace the first document that matches `filter` with replacement
- `update`: update the first document that matches `filter` (deprecated)
- `updateMany`: update all documents that match `filter`
- `updateOne`: update the first document that matches `filter`

**Delete:**

- `deleteOne`: delete the first document that matches `filter`
- `deleteMany`: delete all documents that match `filter`
- `findOneAndDelete`: same as `deleteOne()` + returns the deleted document
- `findOneAndRemove`: same as `remove()` + returns the deleted document (deprecated)
- `remove`: delete all documents that match `filter` (deprecated)

You can modify the query op before executing. Many developers use `.find().updateOne()` in order to separate out the query `filter` from the update object for readability.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const Model = mongoose.model("Model", schema);

// Chaining makes it easier to visually break up complex updates
await Model.find({ name: "avinash" }).updateOne({}, { age: 29 });

// Equivalent, without using chaining syntax
await Model.updateOne({ name: "avinash" }, { age: 29 });
```

### 3.2. Query Operations

There are 17 distinct query operations in Mongoose. 4 are deprecated: `count()`, `update()`, `findOneAndRemove()`, and `remove()`. The remaining 13 can be broken up into 5 classes: reads, updates, deletes, find and modify ops, and other.

#### 3.2.1. Read

`find()`, `findOne()`, and `countDocuments()` all take in a filter parameter and find
document(s) that match the `filter`.

- `find()` returns a list of all documents that match `filter`. If none are found, `find()` returns an empty array.
- `findOne()` returns the first document that matches `filter`. If none are found, `findOne()` returns `null`.
- `countDocuments()` returns the number of documents that match `filter`. If none are found, `countDocuments()` returns 0.

Ex, below code snippet above 3 functions in action.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const Model = mongoose.model("Model", schema);

await Model.insertMany([
  { name: "Raam", age: 59 },
  { name: "Lakshman", age: 29 },
  { name: "Hanuman", age: 29 },
]);

const filter = { age: { $lt: 30 } };

let res = await Model.find(filter);
console.log(res[0].name); // Lakshman
console.log(res[1].name); // Hanuman

res = await Model.findOne(filter);
console.log(res.name); // Lakshman

res = await Model.countDocuments(filter);
console.log(res); // 2
```

#### 3.2.2. Delete

`deleteOne()`, `deleteMany()` , and `remove()` all take a `filter` parameter and delete
document(s) from the collection that match the given `filter`.

- `deleteOne()` deletes the first document that matches `filter`
- `deleteMany()` deletes all documents that match `filter`
- `remove()` deletes all documents that match `filter`. `remove()` is deprecated, you should use `deleteOne()` or `deleteMany()` instead.

Ex, Let's see all of above delete operation in action

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const Model = mongoose.model("Model", schema);

await Model.insertMany([
  { name: "Raam", age: 59 },
  { name: "Lakshman", age: 29 },
  { name: "Hanuman", age: 29 },
]);

const filter = { age: { $lt: 30 } };

let res = await Model.deleteOne(filter);
console.log(res.deletedCount); // 1

await Model.create({ name: "Lakshman", age: 29 });

res = await Model.deleteMany(filter);
console.log(res.deletedCount); // 2

await Model.insertMany([
  { name: "Lakshman", age: 29 },
  { name: "Hanuman", age: 29 },
]);

// `remove()` deletes all docs that match `filter` by default
res = await Model.remove(filter);
console.log(res.deletedCount); // 2
```

The return value of await Model.deleteOne() is not the deleted document or documents. The return value is a result object that has a deletedCount property, which tells you the number of documents MongoDB deleted. To get the deleted document, you'll need to use
`findOneAndDelete()`.

#### 3.2.3. Update

`updateOne()`, `updateMany()`, `replaceOne()`, and `update()` all take in a `filter` parameter and an update parameter, and modify documents that match filter based on `update`.

- `updateOne()` updates the first document that matches `filter`
- `updateMany()` updates all documents that match `filter`
- `replaceOne()` replaces the first document that matches `filter` with the update parameter
- `update()` updates the first document that matches `filter`. `update()` is deprecated, use `updateOne()` instead.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const Model = mongoose.model("Model", schema);

await Model.insertMany([
  { name: "Raam", age: 59 },
  { name: "Lakshman", age: 29 },
  { name: "Hanuman", age: 29 },
]);

const filter = { age: { $lt: 30 } };
const update = { age: 39 };

// `updateOne()`
let res = await Model.updateOne(filter, update);
console.log(res.modifiedCount); // 1

let docs = await Model.find(filter);
console.log(docs[0].name); // Hanuman
console.log(docs[0].age); // 29

await Model.updateOne({ name: "Lakshman" }, { age: 29 });

// `updateMany()`
res = await Model.updateMany(filter, update);
console.log(res.modifiedCount); // 2

docs = await Model.find({ age: { $lt: 40 } });
console.log(docs[0].age); // 39
console.log(docs[1].age); // 39

// `update()` behaves like `updateOne()` by default
res = await Model.update(filter, update);
console.log(res.modifiedCount); // 0
```

The `replaceOne()` function is slightly different from `updateOne()` because it replaces the matched document, meaning that it deletes all keys that aren't in the update other than `_id`.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const Model = mongoose.model("Model", schema);

await Model.insertMany([
  { name: "Raam", age: 59 },
  { name: "Lakshman", age: 29 },
  { name: "Hanuman", age: 29 },
]);

const filter = { age: { $lt: 30 } };
const replacement = { name: "Lakshman" };

// Sets `rank`, unsets `age`
let res = await Model.replaceOne(filter, replacement);
res.modifiedCount; // 1

let docs = await Model.find({ name: "Lakshman" });
console.log(!!docs[0]._id); // true
console.log(docs[0].name); // 'Lakshman'
console.log(docs[0].age); // undefined
```

Like deletes, updates do **not** return the updated document or documents. They return a result object with `modifiedCount` property that contains the number of documents the MongoDB server updated. To get the updated document, you should use `findOneAndUpdate()`.

#### 3.2.4. Find and Modify

`findOneAndUpdate()`, `findOneAndDelete()`, `findOneAndReplace()`, and `findOneAndRemove()` are operations that update or remove a single document. Unlike `updateOne()`, `deleteOne()`, and `replaceOne()`, these functions return the updated document rather than simply the number of updated documents.

- `findOneAndUpdate()` works like `updateOne()`, but also returns the document
- `findOneAndDelete()` works like `deleteOne()`, but also returns the document
- `findOneAndReplace()` works like `replaceOne()`, but also returns the document
- `findOneAndRemove()` works like `deleteOne()`. It is deprecated in favor of `findOneAndDelete()`.

Remember that `findOneAndUpdate()` is different than calling `findOne()` followed by `updateOne()`. `findOneAndUpdate()` is a single operation, and it is atomic.

For example, if you call `findOne()` followed by an `updateOne()`, another update may come in and change the document between when you called `findOne()` and when you called
`updateOne()`. With `findOneAndUpdate()`, that cannot happen.

By default, `findOneAndUpdate()`, `findOneAndDelete()`, and `findOneAndReplace()` return the document as it was **before** the MongoDB server applied the update.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Model = mongoose.model("Model", schema);

await Model.insertMany([
  { name: "Jean-Luc Picard", age: 59 },
  { name: "Will Riker", age: 29 },
  { name: "Deanna Troi", age: 29 },
]);

const filter = { name: "Will Riker" };
const update = { rank: "Commander" };

// MongoDB will return the matched doc as it was **before** applying `update`
let doc = await Model.findOneAndUpdate(filter, update);
console.log(doc.name); // 'Will Riker'
console.log(doc.rank); // undefined

const replacement = { name: "Will Riker", rank: "Commander" };
doc = await Model.findOneAndReplace(filter, replacement);
// `doc` still has an `age` key, because `findOneAndReplace()` returns the document as it was before it was replaced.
console.log(doc.rank); // 'Commander'
console.log(doc.age); // 29

// Delete the doc and return the doc as it was before the MongoDB server deleted it.
doc = await Model.findOneAndDelete(filter);
console.log(doc.name); // 'Will Riker'
console.log(doc.rank); // 'Commander'
console.log(doc.age); // undefined
```

To return the document as it is **after** the MongoDB server applied the update, use the `new` option.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Model = mongoose.model("Model", schema);

await Model.insertMany([
  { name: "Jean-Luc Picard", age: 59 },
  { name: "Will Riker", age: 29 },
  { name: "Deanna Troi", age: 29 },
]);

const filter = { name: "Will Riker" };
const update = { rank: "Commander" };
const options = { new: true };

// MongoDB will return the matched doc **after** the update was applied if you set the `new` option
let doc = await Model.findOneAndUpdate(filter, update, options);
console.log(doc.name); // 'Will Riker'
console.log(doc.rank); // 'Commander'

const replacement = { name: "Will Riker", rank: "Commander" };
doc = await Model.findOneAndReplace(filter, replacement, options);
console.log(doc.rank); // 'Commander'
console.log(doc.age); // undefined
```

#### 3.2.5. Other operation

The `distinct()` and `estimatedDocumentCount()` operations don't quite fit in any of the other classes. Their semantics are sufficiently different that they belong in their own class.

- `distinct(key, filter)` returns an array of distinct values of key among documents that match `filter`.
- `estimatedDocumentCount()` returns the number of documents in the collection based on MongoDB's collection metadata. It doesn't take any parameters.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Model = mongoose.model("Model", schema);

await Model.insertMany([
  { name: "Jean-Luc Picard", age: 59 },
  { name: "Will Riker", age: 29 },
  { name: "Deanna Troi", age: 29 },
]);

// Return an array containing the distinct values of the `age` property. The values in the array can be in any order.
let values = await Model.distinct("age");
console.log(values.sort()); // [29, 59]

const filter = { age: { $lte: 29 } };
values = await Model.distinct("name", filter);
console.log(values.sort()); // ['Deanna Troi', 'Will Riker']

// Unlike `countDocuments()`, `estimatedDocumentCount()` does **not** take a `filter` parameter. It only returns the number of documents in the collection.
const count = await Model.estimatedDocumentCount();
console.log(count); // 3
```

In most cases, calling `estimatedDocumentCount()` will give the same result as calling `countDocuments()` with no parameters. `estimatedDocumentCount()` is generally faster than `countDocuments()` because it doesn't actually go through and count the documents in the collection. However, `estimatedDocumentCount()` may be incorrect for one of two reasons:

1. Orphaned documents, which only happen on sharded clusters.
2. There was an unclean shutdown of the database

`estimatedDocumentCount()` is rarely useful when building apps because it doesn't accept a `filter` parameter. You can use `estimatedDocumentCount()` if you want to count the total number of documents in a massive collection, but `countDocuments()` is usually fast enough.

### 3.3. Query Operators

In the previous section, several examples used the `$lte` **query operator (also known as a query selector)** to `filter` for Star Trek characters whose age was less than `30`:

Ex,

```js
// `$lte` is an example of a query selector
const filter = { age: { $lte: 30 } };
const docs = await Model.find(filter);
```

If an object has multiple query selectors, MongoDB treats it as an **and**.

For example, the below query selector will match documents whose `age` is greater than 50 and less than 60.

```js
const querySelectors = { $gt: 50, $lt: 60 };
const filter = { age: querySelectors };
```

The most commonly used query selectors fall into one of 4 classes:

#### 3.3.1. Comparison

- `$eq`: Matches values that strictly equal the specified value
- `$gt`: Matches values that are greater than the specified value
- `$gte`: Matches values that are greater than or equal to the specified value
- `$in`: Matches values that are strictly equal to a value in the specified array
- `$lt`: Matches values that are less than a given value
- `$lte`: Matches values that are less than or equal to the specified value
- `$ne`: Matches values that are not strictly equal to the specified value
- `$nin`: Matches values that are not strictly equal to any of the specified values
- `$regex`: Matches values that are strings which match the specified regular expression

The right hand side of a comparison query selector is the value or values to compare against.

```js
// Matches if the `age` property is exactly equal to 42
let filter = { age: { $eq: 42 } };

// Matches if the `age` property is a number between 30 and 40
filter = { age: { $gte: 30, $lt: 40 } };

// Matches if `name` is 'Jean-Luc Picard' or 'Will Riker'
filter = { name: { $in: ["Jean-Luc Picard", "Will Riker"] } };

// Matches if `name` is a string containing 'picard', ignoring case
filter = { name: { $regex: /picard/i } };
```

#### 3.3.2. Element/Type

There are two 'element' query selectors. These query selectors help you filter documents based on the type of the value, like whether a value is a string or a number.

- `$exists`: Matches either documents that have a certain property, or don't have the specified property, based on whether the given value is `true` or `false`
- `$type`: Matches documents where the property has the specified type.

Here's how `$exists` works:

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Model = mongoose.model("Model", schema);

await Model.create({ name: "Will Riker", age: 29 });

// Finds the doc, because there's an `age` property
let doc = await Model.findOne({ age: { $exists: true } });

// Does **not** find the doc, no `rank` property
doc = await Model.findOne({ rank: { $exists: true } });

// Finds the doc, because there's no `rank` property
doc = await Model.findOne({ rank: { $exists: false } });

// Finds the doc, because `$exists: true` matches `null` values.
// `$exists` is analogous to a JavaScript `in` or `hasOwnProperty()`
await Model.updateOne({}, { rank: null });
doc = await Model.findOne({ rank: { $exists: true } });
```

`$exists` is useful, but it also matches `null` values.

The `$type` query selector is helpful for cases where you want to match documents that are the wrong type.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Model = mongoose.model("Model", schema);

await Model.create({ name: "Will Riker", age: 29 });

// Finds the doc, because `age` is a number
let doc = await Model.findOne({ age: { $type: "number" } });

// Does **not** find the doc, because the doc doesn't have a `rank` property.
doc = await Model.findOne({ rank: { $type: "string" } });
```

Below is a list of valid types for the `$type` query selector:

- `double`
- `string`
- `object`
- `array`
- `binData`
- `objectId`
- `bool`
- `null`
- `regex`
- `int`
- `timestamp`
- `long`
- `decimal`
- `number`: Alias that matches `double`, `int`, `long`, and `decimal`

There is a small gotcha when using `$type` with Mongoose numbers: to save space, the underlying MongoDB driver stores JavaScript numbers as 'int' where possible, and 'double' otherwise. So if `age` is an integer, Mongoose will store it as an 'int', otherwise Mongoose will store it as a 'double'.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Model = mongoose.model("Model", schema);

await Model.create({ name: "Will Riker", age: 29 });

// Finds the doc. Mongoose stores `age` as an int
let doc = await Model.findOne({ age: { $type: "int" } });
doc.age = 29.5;
await doc.save();

// Does **not** find the doc: `age` is no longer an int
doc = await Model.findOne({ age: { $type: "int" } });

// Finds the doc, `age` is now a double
doc = await Model.findOne({ age: { $type: "double" } });

// Finds the doc, 'number' matches both ints and doubles.
doc = await Model.findOne({ age: { $type: "number" } });
```

You can also invert the `$type` query selector using the `$not` query selector. `$not: { $type: 'string' }` will match any value that is not a string, including documents that do not have the property.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Model = mongoose.model("Model", schema);

await Model.create({ name: "Will Riker", age: 29 });

// Finds the doc, because Mongoose stores `age` as an int
const querySelector = { $not: { $type: "string" } };
let doc = await Model.findOne({ age: querySelector });

// Finds the doc, because `doc` doesn't have a `rank` property
doc = await Model.findOne({ rank: querySelector });
```

#### 3.3.3. Array

Array query selectors help you filter documents based on array properties.

- `$all`: matches arrays that contain all of the values in the given array
- `$size`: matches arrays whose length is equal to the given number
- `$elemMatch`: matches arrays which contain a document that matches the given filter

The need for array query selectors is limited because MongoDB is smart enough to drill into arrays.

For example, suppose you have a `BlogPost` model with an array of embedded comments. Each comment has a `user` property. You can find all blog posts that a given user commented on simply by querying by `comments.user`.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ comments: [{ user: String, text: String }] });

const BlogPost = mongoose.model("BlogPost", schema);

await BlogPost.create([
  { comments: [{ user: "jpicard", text: "Make it so!" }] },
  { comments: [{ user: "wriker", text: "One, or both?" }] },
]);

const docs = await BlogPost.find({ "comments.user": "jpicard" });
console.log(docs.length); // 1
```

However, what if you want to find all blog posts that both 'wriker' and 'jpicard' commented on? That's where the `$all` query selector comes in. The `$all` query selector matches arrays that contain all elements in the given array.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ comments: [{ user: String, text: String }] });

const BlogPost = mongoose.model("BlogPost", schema);

await BlogPost.create([
  { comments: [{ user: "jpicard", text: "Make it so!" }] },
  { comments: [{ user: "wriker", text: "One, or both?" }] },
  { comments: [{ user: "wriker" }, { user: "jpicard" }] },
]);

// Find all blog posts that both 'wriker' and 'jpicard' commented on.
const all = ["wriker", "jpicard"];
let docs = await BlogPost.find({ "comments.user": { $all: all } });
console.log(docs.length); // 1

// Find all blog posts that 'jpicard' commented on.
docs = await BlogPost.find({ "comments.user": "jpicard" });
console.log(docs.length); // 2

// Find all blog posts that have exactly 2 comments
const comments = { $size: 2 };
let docs = await BlogPost.find({ comments });
docs.length; // 1
```

The `$size` operator lets you filter documents based on array length. For example, here's how you find all blog posts that have 2 comments:

The `$elemMatch` query selector more subtle. For example, suppose you wanted to find all blog posts where the user 'jpicard' commented 'Make it so!'.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ comments: [{ user: String, text: String }] });

const BlogPost = mongoose.model("BlogPost", schema);

await BlogPost.create([
  { comments: [{ user: "jpicard", text: "Make it so!" }] },
  { comments: [{ user: "wriker", text: "One, or both?" }] },
  {
    comments: [
      { user: "wriker", text: "Make it so!" },
      { user: "jpicard", text: "That's my line!" },
    ],
  },
]);

// `$elemMatch` is like a nested filter for array elements.
const $elemMatch = { user: "jpicard", text: "Make it so!" };
let docs = await BlogPost.find({ comments: { $elemMatch } });
console.log(docs.length); // 1
```

### 3.4. Update Operators

MongoDB also provides several update operators to help you build up sophisticated update parameters to `updateOne()`, `updateMany()`, and `findOneAndUpdate()`. Each update has at least one update operator.

In MongoDB, update operators start with `$`. So far, we have not explicitly used any update operators. That's because, when you provide an update parameter that doesn't have any update operators, Mongoose wraps your update in the `$set` update operator.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Character = mongoose.model("Character", schema);

await Character.create({ name: "Will Riker", age: 29 });

const filter = { name: "Will Riker" };
let update = { rank: "Commander" };
const opts = { new: true };

let doc = await Character.findOneAndUpdate(filter, update, opts);
console.log(doc.name); // Will Riker
console.log(doc.rank); // 'Commander'

// By default, Mongoose wraps your update in `$set`, so the below update is equivalent to the previous update.
// update = { $set: { rank: "Captain" } };
// doc = await Character.findOneAndUpdate(filter, update, opts);
// console.log(doc.name); // Will Riker
// console.log(doc.rank); // 'Captain'
```

The `$set` operator sets the value of the given field to the given value. If you provide multiple fields, `$set` sets them all. The `$set` operator also supports dotted paths within nested objects.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const Character = mongoose.model(
  "Character",
  mongoose.Schema({
    name: { first: String, last: String },
    age: Number,
    rank: String,
  })
);

const name = { first: "Will", last: "Riker" };

await Character.create({ name, age: 29, rank: "Commander" });

// Update `name.first` without touching `name.last`
const $set = { "name.first": "Thomas", rank: "Lieutenant" };

let doc = await Character.findOneAndUpdate({}, { $set }, { new: true });
console.log(doc.name.first); // 'Thomas'
console.log(doc.name.last); // 'Riker'
console.log(doc.rank); // 'Lieutenant'
```

The `$set` operator is an example of a **field update operator**.

#### 3.4.1. Field Update Operators

Field update operators operate on fields of any type, as opposed to array update operators or numeric update operators. Below is a list of the most common field update operators:

- `$set`: set the value of the given fields to the given values
- `$unset`: delete the given fields
- `$setOnInsert`: set the value of the given fields to the given values if a new document was inserted in an upsert. Ignored if MongoDB updated an existing document.
- `$min`: set the value of the given fields to the specified values, if the specified value is less than the current value of the field.
- `$max`: set the value of the given fields to the specified values, if the specified value is greater than the current value of the field.

`$unset` operator deletes all properties in the given object.  
For example, you an use it to unset a character's `age`:

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Character = mongoose.model("Character", schema);

await Character.create({ name: "Will Riker", age: 29 });

const filter = { name: "Will Riker" };

// Delete the `age` property
const update = { $unset: { age: 1 } };
const opts = { new: true };
let doc = await Character.findOneAndUpdate(filter, update, opts);
console.log(doc.name); // Will Riker
console.log(doc.age); // undefined
```

The `$setOnInsert` operator behaves like a conditional `$set`. It only sets the values if a new document was inserted because of the `upsert` option. If `upsert` is `false`, or the `upsert` modified an existing document rather than inserting a new one, `$setOnInsert` does nothing.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Character = mongoose.model("Character", schema);

await Character.create({ name: "Will Riker", age: 29 });

let filter = { name: "Will Riker" };

// Set `rank` if inserting a new document
const update = { $setOnInsert: { rank: "Captain" } };

// If `upsert` option isn't set, `$setOnInsert` does nothing
const opts = { new: true, upsert: true };

let doc = await Character.findOneAndUpdate(filter, update, opts);
doc.rank; // undefined

filter = { name: "Jean-Luc Picard" };

doc = await Character.findOne(filter);
doc; // null, so upsert will insert a new document

doc = await Character.findOneAndUpdate(filter, update, opts);
doc.name; // 'Jean-Luc Picard'
doc.rank; // 'Captain'
```

`$min` and `$max` set values based on the current value of the field. They're so named because if you use `$min`, the updated value will be the minimum of the given value and the current value.

Similarly, if you use `$max`, the updated value will be the maximum of the given value and the current value.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Character = mongoose.model("Character", schema);

await Character.create({ name: "Will Riker", age: 29 });

const filter = { name: "Will Riker" };
const update = { $min: { age: 30 } };
const opts = { new: true, upsert: true };

let doc = await Character.findOneAndUpdate(filter, update, opts);
console.log(doc.age); // 29

update.$min.age = 28;
doc = await Character.findOneAndUpdate(filter, update, opts);
console.log(doc.age); // 28
```

#### 3.4.2. Numeric Update Operators

Numeric update operators can only be used with numeric values. Both the value in the database and the given value must be numbers.

- `$inc`: increments the given fields by the given values. The given values may be positive (for incrementing) or negative (for decrementing)
- `$mul`: multiplies the given fields by the given values.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number, rank: String });

const Character = mongoose.model("Character", schema);

await Character.create({ name: "Will Riker", age: 29 });

// Increment `age` by 1 using `$inc`
const filter = { name: "Will Riker" };
let update = { $inc: { age: 1 } };
const opts = { new: true };

let doc = await Character.findOneAndUpdate(filter, update, opts);
console.log(doc.age); // 30

// Decrement `age` by 1
update.$inc.age = -1;
doc = await Character.findOneAndUpdate(filter, update, opts);
console.log(doc.age); // 29

// Multiply `age` by 2
update = { $mul: { age: 2 } };
doc = await Character.findOneAndUpdate(filter, update, opts);
console.log(doc.age); // 58
```

#### 3.4.3. Array Update Operators

Array update operators can only be used when the value in the database is an array They let you add or remove elements from arrays.

- `$push`: add the given value to the end of the array
- `$addToSet`: add the given value to the end of the array, unless there's already an element in the array that is exactly equal to the given value
- `$pull`: remove all elements from the array that match the specified condition
- `$pullAll`: remove all elements that are exactly equal to one of the values in the given array
- `$pop`: remove the first element of the array if the given value is -1, or the last element if the given value is `1`

The `$push` operator lets you add an element to the end of the array.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ title: String, tags: [String] });

const Post = mongoose.model("BlogPost", schema);

const title = "Introduction to Mongoose";
await Post.create({ title, tags: ["Node.js"] });

// Add 'MongoDB' to the blog post's list of tags
const update = { $push: { tags: "MongoDB" } };
const opts = { new: true };

let doc = await Post.findOneAndUpdate({ title }, update, opts);
console.log(doc.tags); // ['Node.js', 'MongoDB']
```

The `$addToSet` operator is similar to `$push`, except it skips adding duplicates.  

For example, if `BlogPost.tags` contains 'MongoDB', `$addToSet` will skip adding 'MongoDB'.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ title: String, tags: [String] });

const Post = mongoose.model("BlogPost", schema);

const title = "Introduction to Mongoose";
await Post.create({ title, tags: ["Node.js"] });

// 'MongoDB' isn't in `tags`, so `$addToSet` behaves like `$push`
const update = { $addToSet: { tags: "MongoDB" } };
const opts = { new: true };

let doc = await Post.findOneAndUpdate({ title }, update, opts);
console.log(doc.tags); // ['Node.js', 'MongoDB']
// Since 'MongoDB' is in `tags`, `$addToSet` will be a no-op.
doc = await Post.findOneAndUpdate({ title }, update, opts);
console.log(doc.tags); // ['Node.js', 'MongoDB']
```

### 3.5. Sort Order

Operations like `findOne()` and `updateOne()` find the first document in the collection that matches a given filter. But how does MongoDB determine what the first document is?

By default, MongoDB iterates through documents in what MongoDB calls *natural order*. Natural order means whatever order MongoDB decides to store the documents in internally, which isn't guaranteed.

When you execute a `findOne()`, by default MongoDB gives you the first document that matches the given filter in natural order. So if there are multiple documents in the collection that match the given filter, there's no guarantee which one MongoDB will return.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const Character = mongoose.model("Character", schema);

await Character.create({ name: "Jean-Luc Picard", age: 59 });
await Character.create({ name: "Will Riker", age: 29 });

// `doc` may be either of the 2 documents inserted above.
// Natural order means MongoDB may return whichever one.
const doc = await Character.findOne();
console.log(doc.name); // Jean-Luc Picard
```

MongoDB has a `sort` option for operations like `findOne()` that let you tell MongoDB what fields to sort on. Mongoose queries also have a `sort()` helper that sets the sort option for you.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const Character = mongoose.model("Character", schema);

await Character.create({ name: "Jean-Luc Picard", age: 59 });
await Character.create({ name: "Will Riker", age: 29 });

// The 3rd parameter to `findOne()` is an `options` parameter.
// `options.sort` lets you specify what order MongoDB should
// use when checking which documents match `filter`
const options = { sort: { name: 1 } };

// `doc` will always be the doc with name 'Jean-Luc Picard'
let doc = await Character.findOne({}, null, options);
console.log(doc.name); // Jean-Luc Picard

// You can also set the `sort` option using `Query#sort()`.
doc = await Character.findOne({}).sort({ name: 1 });
console.log(doc.name); // Jean-Luc Picard

doc = await Character.findOne({}, null, { sort: { age: 1 } });
console.log(doc.name); // Will Riker

doc = await Character.findOne({}).sort({ age: 1 });
console.log(doc.name); // Will Riker
```

The `Query#sort()` function in Mongoose works with the following operations:

- `find()`
- `updateOne()`
- `replaceOne()`
- `findOneAndUpdate()`
- `findOneAndReplace()`
- `deleteOne()`
- `findOneAndDelete()`

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });

const Character = mongoose.model("Character", schema);

await Character.create({ name: "Jean-Luc Picard", age: 59 });
await Character.create({ name: "Will Riker", age: 29 });

// Update the character with the highest `age`. The `sort` option
// can be a string property name, or an object.
await Character.updateOne({}, { rank: "Captain" }).sort("-age");

// Find all characters, sorted by `age`
const docs = await Character.find().sort({ age: 1 });

// Delete the character whose name comes first alphabetically
// out of all the characters whose age is greater than 25.
await Character.deleteOne({ age: { $gt: 25 } }).sort("name");
```

Sorting is useful for more than just ordering by numeric values like age. MongoDB can sort any other type.

For example, by default MongoDB sorts strings in lexicographic order. Here's how
lexicographic ordering compares two strings:

- Compare the first character of both strings based on their ASCII character code, so `'' < 'A' < 'Z' < 'a' < 'z'`.
- If the first two characters are equal, compare the 2nd character of each string.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ value: String });

const TestString = mongoose.model("TestString", schema);

await TestString.create([
  { value: "A" },
  { value: "a" },
  { value: "Z" },
  { value: "z" },
  { value: "" },
  { value: "aa" },
]);

let docs = await TestString.find().sort({ value: 1 });
console.log(docs.map((v) => v.value)); // ['', 'A', 'Z', 'a', 'aa', 'z']
```

#### 3.5.1. Sorting Types Other than Strings and Numbers

MongoDB can also compare values of different types. When sorting values of different types, MongoDB first compares the type, and only compares values if both values are the same type.

For example, in MongoDB, a number is always less than a string.

Here's the order of types in MongoDB, from lowest to highest:

```md
Null
Numbers
Strings
Objects
Arrays
BinData: the type Mongoose uses to store Node.js buffers
ObjectId
Boolean
Date
Timestamp
Regular Expression
```

Here's an example of sorting values with different types using Mongoose's `Query#sort`() function.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

// `value: {}` means Mongoose skips casting the `value` property
const schema = new mongoose.Schema({ value: {} });

const Test = mongoose.model("Test", schema);

await Test.create([
  { value: 42 },
  { value: "test string" },
  { value: true },
  { value: null },
]);
const docs = await Test.find().sort({ value: 1 });

console.log(docs.map((v) => v.value)); // [null, 42, 'test string', true]
```

#### 3.5.2. With Query Selectors and Update Operators

The update operators `$min` and `$max` rely on comparing two values. `$min` and `$max` depend on the same sort order as the `Query#sort()` function.

For example, since `null` is the lowest value in MongoDB's sort order, updating a field with `$min: { field: null }` will always set that field to `null`.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

// `value: {}` means Mongoose skips casting the `value` property, so `value` can be of any type.
const schema = new mongoose.Schema({ value: {} });

const Test = mongoose.model("Test", schema);

await Test.create([{ value: 42 }]);
const opts = { new: true };

// Does nothing because 'a' > 42 in MongoDB's sort order
let doc = await Test.findOneAndUpdate({}, { $min: { value: "a" } }, opts);
console.log(doc.value); // 42

// Sets `value` to `null` because `null` is smaller than
// any other value in MongoDB's sort order.
doc = await Test.findOneAndUpdate({}, { $min: { value: null } }, opts);
console.log(doc.value); // null
```

On the other hand, the comparison query selectors `$gte`, `$lte`, `$gt`, and `$lt` implicitly filter for elements that have the same type as the given value.

For example, `field: { $gte: null }` will only match documents whose field property is exactly equal to `null`, because `$gte` implicitly filters for values that have the same type as `null`.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = new mongoose.Schema({ value: {} });
const Test = mongoose.model("Test", schema);

await Test.create([{ value: 42 }]);

// Does **not** find the doc. 42 is greater than null in MongoDB
// sort order, but `$gte` only compares values with the same type
let doc = await Test.findOne({ value: { $gte: null } });

// Also doesn't find the doc. `$lte` will only compare docs
// whose `value` is the same type as the given value (string).
doc = await Test.findOne({ value: { $lte: "42" } });
```

### 3.6. Limit, Skip, Project

Sometimes, the result set from `find()` is too big to fit in memory. Mongoose queries have a `limit()` function that sets a limit on the maximum amount of documents that a query can return.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = new mongoose.Schema({ name: String, age: Number });
const Character = mongoose.model("Character", schema);

await Character.create({ name: "Jean-Luc Picard", age: 59 });
await Character.create({ name: "Will Riker", age: 29 });
await Character.create({ name: "Deanna Troi", age: 29 });

// Sort by `age` ascending, and return at most 2 documents.
const docs = await Character.find().sort({ age: 1 }).limit(2);

console.log(docs[0].name); // Deanna Troi
console.log(docs[1].name); // Will Riker
```

Note that `limit()` takes effect after the sort order. Even if you put `.limit()` before `.sort()`, MongoDB will internally sort the documents and then apply the `limit`.

The `skip()` function is commonly used with `limit()`. `skip(num)` tells MongoDB to skip the first num documents.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = new mongoose.Schema({ name: String, age: Number });
const Character = mongoose.model("Character", schema);

await Character.create({ name: "Jean-Luc Picard", age: 59 });
await Character.create({ name: "Beverly Crusher", age: 40 });
await Character.create({ name: "Will Riker", age: 29 });
await Character.create({ name: "Deanna Troi", age: 29 });

const docs = await Character.find()
  .sort({ age: 1 }) // Sort by `age` ascending
  .skip(2) // Skip the 2 documents
  .limit(2); // And return at most 2 documents

console.log(docs.map((doc) => doc.name));
// ['Bevery Crusher', 'Jean-Luc Picard']
```

Like `limit()`, MongoDB applies `skip()` after applying the `sort` order. MongoDB applies `skip()` before applying `limit()`. So even if you change the order of `skip()`, `limit()`, and `sort()`, MongoDB will still apply them in the following order:

1. Sort the documents to match the given sort order
2. Skip the first num docs
3. Collect docs until it reaches the given limit

`skip()` and `limit()` are most commonly used for pagination.

Here's how you might implement a `getPage()` function that finds the search results for a given page.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const resultSchema = mongoose.Schema({ title: String, order: Number });

const SearchResult = mongoose.model("SearchResult", resultSchema);

for (let i = 1; i <= 25; ++i) {
  await SearchResult.create({ order: i, title: "test" + i });
}

function getResults(page, resultsPerPage) {
  return SearchResult.find()
    .sort({ order: 1 })
    .skip(page * resultsPerPage)
    .limit(resultsPerPage);
}

// Returns results 1-10
const docs = await getResults(0, 10);
console.log(docs.map((v) => v.title));
// [
//   'test1', 'test2',
//   'test3', 'test4',
//   'test5', 'test6',
//   'test7', 'test8',
//   'test9', 'test10'
// ]

// Returns results 11-20
const docs1 = await getResults(1, 10);
console.log(docs1.map((v) => v.title));

// [
//   'test11', 'test12',
//   'test13', 'test14',
//   'test15', 'test16',
//   'test17', 'test18',
//   'test19', 'test20'
// ]
```

#### 3.6.1. Projections

Sometimes you want to omit certain fields from a query result. MongoDB supports *projections*, which let you select which fields to include or exclude from the result documents.

Mongoose queries have a `select()` function that let you add a projection to your query.

Projections are objects that have the following form:

```js
{ field1: <value1>, field2: <value2> }
```

`value1` and `value2` can either be:

- Truthy (`1`, `true`, etc.) to include `field1` and `field2`, and exclude all other fields
- Falsy (`0`, `false`, etc.) to exclude `field1` and `field2`, and include all other fields.

For example, here's an example of using `Query#select()` to include or exclude fields.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

let schema = mongoose.Schema({ name: String, age: Number, rank: String });
const Character = mongoose.model("Character", schema);

await Character.create({ name: "Will Riker", age: 29, rank: "Commander" });

// Include `name` and `age`, exclude `rank`
let projection = { name: 1, age: 1 };
let doc = await Character.findOne().select(projection);
console.log(doc.name); // 'Will Riker'
console.log(doc.rank); // undefined

// Exclude `name` and `age`, include `rank`
projection = { name: false, age: false };
doc = await Character.findOne().select(projection);
console.log(doc.name); // undefined
console.log(doc.rank); // 'Commander'
```

**A projection is either inclusive or exclusive**. That means either all the values in the object must be truthy, or they must all be falsy. If you try to mix and max projections, MongoDB will throw an `MongoError`.

Ex,

```js
const projection = { name: 1, age: 0 };
const err = await Character.findOne().select(projection).
catch(err => err);
// 'Projection cannot have a mix of inclusion and exclusion.'
err.message;
```

Projections only work with query operations that return a document or an array of documents.

For example, you can use projections with `findOneAndUpdate()`:

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

let schema = mongoose.Schema({ name: String, age: Number, rank: String });
const Character = mongoose.model("Character", schema);

await Character.create({ name: "Will Riker", age: 29, rank: "Commander" });

const update = { age: 44, rank: "Captain" };
const opts = { new: true };
const projection = { name: 1, age: 1 };

// Updates `rank`, but excludes it from the result document
const doc = await Character.findOneAndUpdate({}, update, opts).select(
  projection
);

console.log(doc.age); // 44
console.log(doc.name); // Will Riker
console.log(doc.rank); // undefined
```

But projections have no impact on `updateOne()`.

For example, you can't exclude `modifiedCount` from the result of `updateOne( )` using projections:

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

let schema = mongoose.Schema({ name: String, age: Number, rank: String });
const Character = mongoose.model("Character", schema);

await Character.create({ name: "Will Riker", age: 29, rank: "Commander" });

const update = { age: 44, rank: "Captain" };
const fields = { modifiedCount: false };
const res = await Character.updateOne({}, update).select(fields);
console.log(res.modifiedCount); // 1
```

#### 3.6.2. Schema-Level Projections

You can define default projections on your schema.

For example, you may want to exclude a user's `email` from query results by default.

Here's how you can accomplish that using the `select` option for schema types:

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const User = mongoose.model(
  "User",
  mongoose.Schema({
    name: String,
    // Exclude `email` by default
    email: { type: String, select: false },
  })
);

await User.create({ name: "John", email: "john@gmail.com" });
await User.create({ name: "Bill", email: "bill@startup.co" });

const docs = await User.find().sort({ name: 1 });

console.log(docs[0].name); // 'Bill'
console.log(docs[1].name); // 'John'
console.log(docs[0].email); // undefined
console.log(docs[1].email); // undefined
```

You need to be careful when including paths that are excluded in the schema by default. If you use `.select('email')`, you'll include the `email` field but exclude all other fields.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const User = mongoose.model(
  "User",
  mongoose.Schema({
    name: String,
    // Exclude `email` by default
    email: { type: String, select: false },
  })
);

await User.create({ name: "John", email: "john@gmail.com" });
await User.create({ name: "Bill", email: "bill@startup.co" });

const docs = await User.find().sort({ name: 1 }).select("email");

console.log(docs[0].name); // undefined
console.log(docs[1].name); // undefined
console.log(docs[0].email); // 'bill@startup.co'
console.log(docs[1].email); // 'john@gmail.com'
```

If you want to include `email` without excluding all other fields, use `.select('+email')`. When you prefix a field with `+`, that tells Mongoose to skip adding a schema-level projection that excludes `email`, as opposed to adding a projection that excludes everything except `email`.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const User = mongoose.model(
  "User",
  mongoose.Schema({
    name: String,
    // Exclude `email` by default
    email: { type: String, select: false },
  })
);

await User.create({ name: "John", email: "john@gmail.com" });
await User.create({ name: "Bill", email: "bill@startup.co" });

const docs = await User.find().sort({ name: 1 }).select("+email"); // Note the `+` here

console.log(docs[0].name); // Bill Johnson
console.log(docs[1].name); // John Smith
console.log(docs[0].email); // 'bill@startup.co'
console.log(docs[1].email); // 'john.smith@gmail.com'
```

### 3.7. Query Casting and Validators

Mongoose casts fields query filters and updates to the path specified in your schema.

For example, Mongoose casts strings in the filter to `ObjectIds` if you query by `_id`.

Ex,

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String });
const Character = mongoose.model("Character", schema);

const ObjectId = mongoose.Types.ObjectId;

const _id = "5dd57639649ce0bd87750caa";

await Character.create([{ _id: ObjectId(_id), name: "Will Riker" }]);

// Works even though `_id` is a string
const doc = await Character.findOne({ _id });
console.log(doc._id instanceof ObjectId); // true
console.log(doc._id === _id); // false
```

Mongoose also casts updates to the type specified in your schema.

For example, if you call `updateOne()` with age as a string as opposed to a number, Mongoose will cast it for you.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });
const Character = mongoose.model("Character", schema);

await Character.create([{ name: "Will Riker", age: 29 }]);

const filter = { name: "Will Riker" };

// Even though `age` is a string, Mongoose will cast `age` to a
// number because that's the type in `schema`.
const update = { age: "30" };
const opts = { new: true };
const doc = await Character.findOneAndUpdate(filter, update, opts);
doc.age; // 30, as a number
```

If Mongoose can't cast a value in `filter` or `update` to the correct type, Mongoose will reject the query with a `CastError`:

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });
const Character = mongoose.model("Character", schema);

await Character.create([{ name: "Will Riker", age: 29 }]);

// This `findOneAndUpdate()` will error out because Mongoose can't cast the string 'not a number' to a number.
const filter = { name: "Will Riker" };
const update = { age: "not a number" };
const opts = { new: true };

const err = await Character.findOneAndUpdate(filter, update, opts).catch(
  (err) => err
);

console.log(err.message); // Cast to Number failed for value "not a number" (type string) at path "age"
console.log(err.name); // 'CastError'
```

Mongoose runs casting on both filters and updates. So if you try to use a query selector like `$gte` on a number path, and the value isn't something Mongoose can cast to a number, Mongoose will also reject the query with a `CastError`:

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String, age: Number });
const Character = mongoose.model("Character", schema);

await Character.create([{ name: "Will Riker", age: 29 }]);

const filter = { age: { $gte: "fail" } };

const err = await Character.findOne(filter).catch((err) => err);

console.log(err.message); // Cast to Number failed for value "fail" (type string) at path "age" for model "Character"
console.log(err.name); // 'CastError'
```

#### 3.7.1. Query Validation

Mongoose does not validate queries by default, but it can if you enable the `runValidators` option on your query.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({
  name: String,
  rank: { type: String, enum: ["Captain", "Commander"] },
});

const Character = mongoose.model("Character", schema);
await Character.create([{ name: "Will Riker", rank: "Commander" }]);

// By default, Mongoose will let the below update go through, even
// though 'Lollipop' is not in the `enum` of allowed values.
const update = { rank: "Lollipop" };
let opts = { new: true };
const doc = await Character.findOneAndUpdate({}, update, opts);
console.log(doc.rank); // 'Lollipop'

// But if you set `runValidators`, Mongoose will run the `enum`
// validator and reject because of an invalid `rank`.
opts = { runValidators: true };
const err = await Character.findOneAndUpdate({}, update, opts).catch(
  (err) => err
);

console.log(err.message); // Validation failed: rank: `Lollipop` is not a valid enum value for path `rank`.
console.log(err.name); // ValidationError
```

Mongoose only runs query validation on updates.

For example, the below query goes through even though the `filter` option contains an invalid `rank`. That's because the below query operation doesn't write to the database, so there's no risk of storing an invalid `rank`.

```js
let opts = { runValidators: true };
// Mongoose executes the below query without error
await Character.findOne({ rank: 'Lollipop' }).setOptions(opts);
```

However, if the query `op` is `findOneAndUpdate()` or `updateOne()`, and the `upsert` option is set, it is possible that filter may end up getting stored in the database.

Be careful when using query validators with upserts!

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({
  name: String,
  rank: { type: String, enum: ["Captain", "Commander"] },
});

const Character = mongoose.model("Character", schema);
await Character.create([{ name: "Will Riker", rank: "Commander" }]);

// Mongoose will let the below upsert through, which will store
// an invalid `rank` in the database.
const filter = { rank: "Lollipop" };
const update = { name: "test" };
const opts = { runValidators: true, upsert: true, new: true };
const doc = await Character.findOneAndUpdate(filter, update, opts);
console.log(doc.name); // test
console.log(doc.rank); // 'Lollipop'
```

Update validators only run on paths specified in the `update` parameter. So if `rank` isn't in the update, Mongoose won't run query validators on it. Even if `rank` is invalid in the database, Mongoose will still allow the below update to go through.

```js
// Insert an invalid doc in the database
const doc = { _id: new mongoose.Types.ObjectId(), rank: 'Lollipop' };
await Character.collection.insertOne(doc);
// Below update succeeds, even though the document in the database
// has an invalid `rank`
await Character.updateOne({ _id: doc._id }, { name: 'Test' });
```

#### 3.7.2. Query Validators with Custom Validators

Query validators are hidden behind a `runValidators` option because query validators have some caveats with custom validators.

Conceptually, there are two types of validators:

- Simple validators are validators that only rely on the value being validated
- Complex validators are validators that rely on other values in the document

Most built-in validators, like `enum` and `required`, are simple validators. But, in practice, developers often write complex validators to express constraints that involve multiple properties.

For example, suppose that, in order to have `rank = 'Captain'`, a character's `age` must be at least `30`.

Ex, Here's how you would write a custom validator that handles this for `save()`:

```js
import mongoose from "mongoose";
import assert from "assert";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({
  name: String,
  age: Number,
  rank: { type: String, validate: ageRankValidator },
});

function ageRankValidator(v) {
  const message = "Captains must be at least 30";
  assert(v !== "Captain" || this.age >= 30, message);
}

const Character = mongoose.model("Character", schema);
const rank = "Captain";
const doc = new Character({ name: "Will Riker", age: 29, rank });
const err = await doc.save().catch((err) => err);

console.log(err.message); // Character validation failed: rank: Captains must be at least 30
```

But `ageRankValidator` assumes that `this` is the document being validated. Unfortunately, with a query validator, the document being updated might not be in memory. If you're updating the document's `age`, you might not know the document's `rank`.

When Mongoose runs a custom validator as part of query validation, `this` will be the query object. For complex custom validators, you need to check whether `this` is a `Query` or a `Document`, and handle each case appropriately.

Ex,

```js
import mongoose from "mongoose";
import assert from "assert";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({
  name: String,
  age: Number,
  rank: { type: String, validate: ageRankValidator },
});

const Character = mongoose.model("Character", schema);

function ageRankValidator(v) {
  const message = "Captains must be at least 30";
  if (this instanceof mongoose.Query) {
    const update = this.getUpdate();
    assert(v !== "Captain" || update.$set.age >= 30, message);
  } else {
    assert(v !== "Captain" || this.age >= 30, message);
  }
}
const update = { age: 29, rank: "Captain" };

// The `context` option tells Mongoose to set the value of `this`
// in the validator to the query. Otherwise `this` will be `null`
const opts = { runValidators: true, context: "query" };

const err = await Character.findOneAndUpdate({}, update, opts).catch(
  (err) => err
);
console.log(err.message); // Validation failed: rank: Captains must be at least 30
```

### 3.8. Corollary

In Mongoose, queries let you load documents from the database as well as execute CRUD operations. Because of limitations with validation, you should generally use `save()` to update Mongoose documents, rather than `updateOne()` or `findOneAndUpdate()`.

However, if you need atomic updates, you should use `findOneAndUpdate()`. And if you need to update multiple documents quickly, `updateMany()` is helpful.

Each Mongoose query has a distinct `op` that determines what the actual operation Mongoose will send to MongoDB is. The `op` can be a create operation like `findOne()`, a replace operation like `replaceOne()`, an update operation like `updateMany()`, a delete operation like `deleteOne()`, or an "other" operation like `distinct()`.

Each query also stores `filter`, `update`, and `options` properties. Depending on the `op`, Mongoose may or may not send these properties to MongoDB. But filter defines which documents MongoDB should look for, `update` defines how MongoDB should change those documents, and `options` defines other tuneable parameters.

## 4. Middleware

In Mongoose, middleware lets you attach your own custom logic to built-in Mongoose functions. Middleware functions are sometimes called hooks. That's because middleware lets you "hook" into existing functions.

For example, suppose you wanted to log every time you saved a document. You can define a `pre('save')` middleware function that Mongoose will call every time you call `save()`.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String });

// `middleware` is a function that Mongoose will call for you when you call `save()`
schema.pre("save", function middleware() {
  console.log("Saving", this.name);
});
const Model = mongoose.model("Test", schema);

const doc = new Model({ name: "test" });

// Prints "Saving test"
await doc.save();
```

You can define middleware for many built-in Mongoose functions. `save()` middleware is most common, but you can also define middleware for other document methods, like `validate()` and `remove()`. You can also define middleware for query functions and aggregation framework calls.

Middleware is defined on a schema. To define middleware, you need to use the `Schema#pre()` and `Schema#post()` functions.

### 4.1. `pre()` and `post()`

The `Schema#pre()` and `Schema#post()` functions each take 2 parameters:

- A string `name`.
- A middleware function fn.

`name` tells Mongoose which function you want to attach middleware to, and fn is the middleware function you want Mongoose to execute.

The function you attach middleware to is called the wrapped function. `Schema#pre()` tells Mongoose to execute `fn` **before** the wrapped function, and `Schema#post()` tells Mongoose to execute `fn` **after** the wrapped function.

For example, Mongoose calls `pre('save')` middleware before actually sending the updated fields to the database. Then Mongoose calls `post('save')` middleware after it sends the updated fields to the database and receives a response from MongoDB.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String });

// Mongoose will execute `preFn()` before saving the document...
schema.pre("save", function preFn() {
  console.log("Saving", this.name);
});

// And `postFn()` after saving the document
schema.post("save", function postFn() {
  console.log("Saved", this.name);
});
const Model = mongoose.model("Test", schema);
const doc = new Model({ name: "test" });

// Prints,
// Saving test
// Saved test
await doc.save();
```

You can call de􀂦ne any number of pre and post hooks for a given function. Mongoose executes middleware functions in series in the order of your `pre('save')` and `post('save')` calls.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String });
schema.pre("save", () => console.log("pre save 1"));
schema.pre("save", () => console.log("pre save 2"));
const Model = mongoose.model("Test", schema);

const doc = new Model({ name: "test" });

// Prints "pre save 1" followed by "pre save 2"
await doc.save();
```

Mongoose passes the result res of the operation as the 􀂦rst parameter to post() middleware.

For example, since `await doc.save()` returns a document, Mongoose passes the saved document as the 􀂦rst parameter to `post('save')` middleware functions.

```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/mydb");

const schema = mongoose.Schema({ name: String });
schema.post("save", function (doc) {
  console.log("Saved:", doc.name);
});

const Model = mongoose.model("Test", schema);
const doc = new Model({ name: "test" });

// Prints "Saved: test"
await doc.save();
```

### 4.2. Async Middleware Functions
