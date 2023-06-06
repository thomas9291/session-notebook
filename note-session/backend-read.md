# Backend Read

## Learning Objectives

- [ ] Knowing about ORM and (`mongoose` as) ODM
- [ ] Understanding how to write a `mongoose` Schema
- [ ] Knowing how to connect an application with a (local) database using `mongoose`
- [ ] Knowing how to read data with a `mongoose` model

---

## What and why mongoose

In order to access a MongoDB from your app, we'll need a JavaScript API. This API is sometimes called a database driver (imagine it like your printer drive).

We will use a library called `mongoose`. That's an ODM (Object Document Mapper).

### Difference between ORM and ODM

ORM (_Object Relation Mapping_):

- technique to perform CRUD operations to mainly relational databases (MySQL, PostgreSQL, etc.),
- uses an _object-oriented paradigm_
- like excel spreadsheet with rows and columns => you cannot add a field to one entry that doesn't exist for all
- is mapped to a single object for all entries.

ODM (_Object Document Mapping_):

- like ORM for non-relational databases (MongoDB)
- uses a _document-oriented paradigm_

### Reasons to use `mongoose` as ORM

- It helps building a **schema** and querying the database (it's also our db driver).
- It has to run on the server, because database access is not secure in the browser.
  - Remember: We already have a server (= Next.js API routes).

---

## DB Connect

In order to read data from a database and consume it in our app, we need two things:

- a (local) database with documents (e.g. about jokes)
- a connection between this database and the Next.js app with `mongoose`.

To create the connection, follow these steps:

1. install `mongoose` with `npm install mongoose`
2. create a `.env.local` file at the root of your project with the following content:
   `MONGODB_URI=mongodb://localhost:27017/jokes-database`
   - Files called `.env` contain environment variables: secrets like usernames and passwords **you don't want to share with anybody**.
   - These files should be ignored by git inside of the `.gitignore` file.
   - Note the structure of the content: the variable is called `MONGODB_URI` and has the value `mongodb://localhost:27017/jokes-database`.
   - `jokes-database` is the name of your database: this value can vary.
3. create a `db/connect.js` file and copy the
   [content from the Next.js mongoose example](https://github.com/vercel/next.js/blob/canary/examples/with-mongodb-mongoose/lib/dbConnect.js)
   - Note that this file uses the `MONGODB_URI` we have just set up in `.env.local` to create a connection.

---

## Schema and Models

We need to declare a
[Schema that describes the data type of the documents in a collection](https://mongoosejs.com/docs/guide.html).

We use this Schema to create a Model that we can use to interact with the database.

Note the difference between _Schema_ and _Model_

- the _Schema_ describes the structure of a document
- the _model_ gives us a programming interface for interacting with the database (liking searching the database, updating, etc.)

### Writing a Schema

We write a Schema in the corresponding file placed in the `db/models` folder like this:

- When creating a `new Schema`, we pass an object with the key-value-pairs we want our documents to have, like `joke` which is a `String` and `required`.
- We don't need to define the `id` because `mongoose` will automatically create one.
- Export the Schema to make it available in our application.

```js
// db/models/Joke.js
import mongoose from "mongoose";

const { Schema } = mongoose;

const jokeSchema = new Schema({
  joke: { type: String, required: true },
});

const Joke = mongoose.models.Joke || mongoose.model("Joke", jokeSchema);

export default Joke;
```

Further notes:

- The name of the collection the model works upon is being generated from the models name, in this case "Joke" => "jokes".
- You can call the `mongoose.model` method with a third argument that holds the collection name.
- We have to check whether the model with the name "Joke" has already been compiled and if yes, take the already compiled model. That's why we use the logical OR (`||`) operator.

---

## Using the Model: Querying the DB (.find, .findById)

In our Next.js API route, we can now write a request handler which

- connects to the database with `dbConnect()`,
- uses the Model to search a document, and
- returns the data.

```js
// api/jokes/index.js
import dbConnect from "../../../db/connect";
import Joke from "../../../db/models/Joke";

export default async function handler(request, response) {
  await dbConnect();

  if (request.method === "GET") {
    const jokes = await Joke.find();
    return response.status(200).json(jokes);
  } else {
    return response.status(405).json({ message: "Method not allowed" });
  }
}
```

`mongoose` comes with a `.findById()` method you can use in a dynamic route:

```js
// api/jokes/[id].js
import dbConnect from "../../../db/connect";
import Joke from "../../../db/models/Joke";

export default async function handler(request, response) {
  await dbConnect();
  const { id } = request.query;

  if (request.method === "GET") {
    const joke = await Joke.findById(id);

    if (!joke) {
      return response.status(404).json({ status: "Not Found" });
    }

    response.status(200).json(joke);
  }
}
```

Note that MongoDB returns an `_id` instead of `id`, so you might need to adapt your frontend to get the correct information.

> 📙 You can find a reference to [all methods of a Model in the mongoose documentation](https://mongoosejs.com/docs/api/model.html).

---

## Gathering linked collections with `.populate()`

Imagine your MongoDB has two collections: `jokes` and `comments` on these jokes. They are linked by the `commentIds`.

When reading the `jokes`, you want to get the comments as well. You can easily achieve this by linking both schemas and when you query the database, you simply add `.populate()` with method chaining.
First, link the schemas for `Joke` and `Comment`:

```js
// link the schemas
const jokeSchema = new Schema({
  joke: { type: String, required: true },
  comments: { type: [Schema.Types.ObjectId], ref: "Comment" },
});

const commentSchema = new Schema({
  _id: Schema.Types.ObjectId,
  comment: { type: String, required: true },
  author: { type: String, required: true },
});

const Joke = mongoose.models.Joke || mongoose.model("Joke", jokeSchema);
const Comment =
  mongoose.models.Comment || mongoose.model("Comment", commentSchema);
```

Second, when reading from the database, populate the comments:

```js
const joke = await Joke.findById(id).populate("comments");
```

> 📙 Read more about [populate in the mongoose docs](https://mongoosejs.com/docs/populate.html).

---

## Resources

- [ORM vs. ODM](https://medium.com/spidernitt/orm-and-odm-a-brief-introduction-369046ec57eb)
