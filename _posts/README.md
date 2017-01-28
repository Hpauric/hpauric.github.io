---
layout: post
title: Using the MongoDB Shell
---



# Using the MongoDB Shell

It’s worth getting to know about [the MongoDB `mongo` shell.](https://docs.mongodb.com/manual/mongo/)

Running shell commands with MongoDB is a really useful way to quickly check what’s in your database. You can review and update your database contents directly from the command line.

## Start MongoDB

Make sure the MongoDB database is running before attempting to start the `mongo` shell. To start up the database, run `./mongod`. You should see something like this:

![](https://github.com/Hpauric/readme-test/blob/master/mongod-cli-screenshot.png)

## Shell Prompt

To start a shell prompt for the above MongoDB, **open a new terminal** and type the command `mongo` to initialize the shell.

With the shell running, I can list the available databases with the command `show databases`. I can see listed the database I’ve been using: `url-shortener-microservice`.

![](https://github.com/Hpauric/readme-test/blob/master/show-databases-screenshot.png)

I can switch to this database with the command: `use url-shortener-microservice`.

### Show Collections

Now I can have a look at the collections in the database with: `show collections`.

![](https://github.com/Hpauric/readme-test/blob/master/show-collections-screenshot.png)

I haven’t been too busy - there are just two collections so far.
I can see exactly what’s in the storedLinks collection by running a `find()` method on it without any arguments. This returns everything:
`db.storedLinks.find()`

![](https://github.com/Hpauric/readme-test/blob/master/storedlinks-find-screenshot.png)

There's a multitude of entries in the collection with the same URL that I entered while testing the interface. This is useful to know!


