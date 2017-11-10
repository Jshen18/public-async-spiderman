# WORK IN PROGRESS - Review Module - Async Foundations

In this module, we'll explore foundational concepts of writing and using asynchronous (for short, "async") code in JavaScript. At the end of this module, you should feel comfortable demonstrating the following skills:
- explaining why one needs to use async code in JavaScript
- passing functions as values to other functions
- using and understanding "node-style" callbacks
- using callback functions to:
  - perform a simple async action
  - perform a sequence of async actions
  - perform an async action on a collection of items
  - aggregate the results of several different async interactions in one object

## The Amasync Spider-Man
> "With great power there must also come... great responsibility!"

For your friendly neighborhood Spider-Man, the work of saving the world is never done! Spider-Man keeps records of the many villains he has encountered throughout the years as well as detailed summaries of each of his encounters. He's generated lots of data, but he's too busy chasing after the bad guys to write a program that will help him analyze this data. Fortunately, he has hired *you*, dear reader, to help him out. The task won't be easy: you'll need to carefully traverse the tangled web of asynchronous JavaScript, crawling your way up a wall of nested callbacks in order to swing your way to victory.

You've been provided a directory, `data`, which contains all the data you'll be working with in this module, as well as directories in which you'll do your work for each part of the assignment. We will explore writing node-style callbacks in this exercise. Once you finish implementing the exercise using callbacks, you should consider creating another implementation using Promises for more practice!

Inside the `data` directory, you'll find:
  - `villains.txt`, a text file listing out Spider-Man's rogue's gallery of enemies. Each line in this text file corresponds to an entry, formatted thusly:

  `<villain name>, <alias>, <ID number>, <status>`.

  All of the items in a villain's entry must appear in *that exact order*.
  - `/reports`, a directory containing `*.txt` files outlining a crime report. Each of these reports summarize one of Spider-Man's encounters with a villain. Each report file is formatted like so:
  ```txt
  event:: <a short title of the event>
  villain:: <the name of the villain at the event>
  date:: <the date of the event>
  description:: <a longer summary of what happened at the event>
  ```
  Notice the labeling convention in this file using double colons (`::`). In your implementation, you'll be reading these files and parsing the information contained in them.

In this module, you will write a series of functions to interact with the data inside the `data` directory. Notice that there is neither a client nor a server included in this module - you'll be verifying your work by invoking your functions directly.

**Note:** most of the work in this module will involve reading from and writing to the file system. We'll use Node's built-in [`fs` module](https://nodejs.org/api/fs.html) to accomplish this. Understanding file I/O and the `fs` module is not the primary learning goal of this module, but you'll find it useful to be familiar with these concepts as you dig deeper in to Node.js elsewhere in the curriculum. You'll also be working with JavaScript Arrays and Objects quite a bit. Many of the built-in Array and Object methods will help you implement the functions in this exercise.

---
### Before you begin
- [ ] Ensure you have a working installation of [Node.js](https://nodejs.org/en/).
- [ ] Run `npm install` inside this directory to install dependencies.

---
## Part 1: Callbacks
TODO - https://github.com/maxogden/art-of-node#callbacks
- other introductory copy re: callbacks

### 1-1: Taking our First Steps
Let's start with a simple interaction - reading data from a file.

Implement the `getAll` function inside `Villain.js`. `getAll` accepts one parameter: a *callback* function, and doesn't return anything. It has a side effect of invoking the callback with an array of objects, with each object representing a villain from our `data/villains.txt` data file.

The villain objects should be formatted like this:
```javascript
  {
    id: Number,
    name: String,
    alias: String,
    status: String
  }
```

Some questions to guide your implementation:
- At what point in your code are you certain that you have the contents of the `/data/villains.txt` file available to you?
- What is the data type of the data once you load it via `fs.readFile`?
- What steps must you take to transform that data into match the expected object format described above? Which of these steps can be abstracted into their own function?
- What happens if there is a problem opening the file? 

### 1-2: Another Simple Interaction
Let's continue our practice with another straightforward action - adding a new villain to the bottom of our data file.

Implement the `add` function inside `Villain.js`. `add` accepts two parameters: an *object* which represents a villain, and a *callback* function. It doesn't return anything, and has a side effect of adding a new line to the bottom of `data/villains.txt` with information from the provided villain object, and invoking a callback to indicate that the villain was successfully added to the data file.

Some questions to guide your implementation:
- What method(s) of the `fs` module might be useful in this function?
- At what point in your code can you be certain that the villain has been added?
- What happens if there is a problem writing to the file?

### 1-3: Using Your Own Asynchronous Code
Next, we're going to write some code that makes use of one of the functions we've already written.

Implement the `findOne` function inside `Villain.js`. `findOne` accepts two parameters: an *object* containing our search options, and a *callback* function. This function looks for a villain from our data set that has the same key-value pair that is found in  'findOne' doesn't return anything, and has the side effect of invoking the given callback on a single object representing a villain. The search options object will have one key-value pair.

What are the steps we'll need to complete in order to implement this function?
1. Read the list of villains from the data file
2. Convert the raw villain data into an array of javascript objects
3. Locate our target villain within that array

The first two steps are already handled by `getAll`. `getAll` accepts a callback, which gets invoked on the villain data. We can use `getAll` inside `findOne`, and add the logic for locating a specific villain from the dataset inside the callback that gets passed into `getAll`.

A question to guide your implementation:
- What is the difference between the callback we're passing to our invocation of `getAll`, and the callback we're receiving as a parameter to `findOne`?

### 1-4: Handling a Sequence of Asynchronous Operations
By now, you should start to notice a pattern for using asynchronous code. The outer function performs some work, and _when that work is done_, invokes the callback function it was given at invocation time. "Do this thing, then do the callback." Now let's examine what happens when you need to perform several asynchronous operations in sequence.

Implement the `findOneAndUpdate` function inside `Villain.js`. `findOneAndUpdate` accepts three parameters:
  - an *object* containing search options (similar to the search options object from the previous step)
  - a second *object* containing key-value pairs that will be updated on the villain that matches the key-value pair from the search options object
  - a *callback* function, which will be invoked with the entire updated villain object.

A question to guide your implementation:
- Why can't we just reuse our `findOne` function here? What must we do instead?

### 1-5: Iteration and Asynchronous Programming
Now let's turn our attention to the Crime Reports. Every time Spider-Man concludes an interaction with a villain, he summarizes it a text file in the `/data/reports` directory. Take a moment to familiarize yourself with the structure of each Crime Report file.

We need to write a function that will load all of the Crime Report files and store them as objects in an array. The objects will be formatted like this:
```javascript
{
  event: String,
  villain: String,
  date: String,
  description: String
}
```

Implement the `getAll` function inside `crimeReports.js`. `getAll` accepts one parameter: a *callback* function. `getAll` doesn't return anything, and has a side effect of generating the array of crime report objects (as described above) and invoking the given callback function on that array. You'll need to use the [`fs.readdir`](https://nodejs.org/api/fs.html#fs_fs_readdir_path_options_callback) and `fs.readfile` methods in your implementation.

### 1-6: Using our Crime Reports `getAll` Function
Once we have the ability to generate an array of Crime Report objects, we can filter through it to find a subset of reports that match a given search term.

Implement the `find` function inside `crimeReports.js`. `find` accepts an *object* containing search options (similar to the ones in our `Villain` functions), and a *callback* function. It has no return value, and has the side effect of invoking the callback on a filtered array of crime reports (where any crime report in the filtered array matches the key/value pair given in the search options object). You'll need to use the `getAll` function from the previous step in your implementation.

### 1-7: Assembling the Results of Several Asynchronous Operations
We're nearly done with our callback-based implementation! Our final task involves using some of our previously written code to stitch together results from two different async operations.

Our final task is to create a _dossier_ that summarizes all the data available about a Villain and their nefarious activities. A dossier will take the form of an object with the following format:
```javascript
{
  id: Number,
  name: String,
  alias: String,
  status: String,
  crimeReports: Array [
    {
      event: String,
      villain: String,
      date: String,
      description: String,
    }
    //...
  ]
}
```

Implement the `generate` function inside `dossier.js`. `generate` accepts an *object* containing search options, and a *callback* function which will be invoked on an object representing a Villain's dossier. The `Villain` and `crimeReports` modules have been imported at the top of `dossier.js`. Use the `Villain.findOne` and `Report.find` functions in your implementation.

Some questions to guide your implementation:
- Which operation should come first - finding the Villain, or finding the Crime Reports?
- In either case: at what point in your code are you certain that you'll have the information you need to proceed to the next step in solving the problem?

### 1-Wrap-up: What did we learn here?
We've completed our work, and now Spider-Man can use a data-driven approach to stop villains before they have a chance to act!

Let's reflect a bit on what we can observe about using asynchronous callback functions based on the code we just wrote.

- *Using callbacks involves keeping track of mental models for several functions at the same time.* We're thinking about the function we're writing, the callback that it will eventually invoke, plus the functions and callbacks for any other async functions we use in service of our approach (like those in the Node.js `fs` module).

- *Our top-level functions never had return values.* Instead, they invoked a callback function to indicate that their work was complete, usually passing along some new data as an argument to that callback function. This means we have to reach for even more mental models - scoping rules and closures, in particular - to write asynchronous code.

- *Sequential asynchronous operations require nested callbacks.* If I need to perform a chain of async actions, each subsequent action must be nested inside of a callback that is passed to its preceding action until we reach the first action in the sequence. We tend to describe problems as a sequence of steps: "do step 1, _then_ do step 2, _then_ do step 3..."

