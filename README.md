# Generators: Iterable ES6 Functions

## I. What is a generator?

```
function thingsICallMyCat() {
  console.log("Edgar")
  console.log("Boo-boo")
  console.log("Sweet Munchkin")
}

thingsICallMyCat()
```

```
function thingsICallMyCat() {
  console.log("Edgar")
  console.log("Boo-boo")
  chaseCat()
  console.log("Sweet Munchkin")
}

function chaseCat() {
  console.log("Phew, got him!")
}

thingsICallMyCat()
```

What is the expected output?

When a function is called, it runs to completion. This is because JS is _single threaded_. What do we mean by this?


```
function thingsICallMyCat() {
  console.log("Edgar")
  console.log("Boo-boo")
  chaseCat()
  console.log("Sweet Munchkin")
}

function chaseCat() {
  setTimeout(() => console.log("HEY GET BACK HERE YOU LITTLE #$@#$!"), 1000)
}

thingsICallMyCat()
```

How was this output different from before? We expect to see the strings printed in order, but the `chaseCat()` string prints last. This is due to JS event loop and call stack. JS executes each function in the order that arrives in the `message queue`, but `setTimeout` is a `web api` that waits for a specified time _AND_ for the `message queue` to be clear before executing the callback within.

A great video on the event loop: https://youtu.be/8aGhZQkoFbQ

This is where generators come into play. Generators are complicated, misunderstood and incredibly useful advanced iterators introduced in ES6.

## II. Generator Syntax

Pretty simple. `*` and `yield`. The hardest part about this is remembering `i before e except after c`. Note that the `*` can come either after the word function or before the generator method name. 

Generators are functions `()` that return objects `{}` that are easily iterated over `[]`.

What is an iterable? What else is iterable in JS?

Although iterable objects aren't arrays, they are still collections of key value pairs that can be iterated over with a `for...in` loop. 

Whats the syntax of a `for...in` loop?

```
function* simpleGenerator() {
  yield 1
  yield 2
  yield 3
}

const generatorObject = simpleGenerator()
console.log(generatorObject)
```

We can see the first time we run our generator, an object is returned. Look at the `proto` and note what methods are available on this object. Which one will help us `iterate`?

```
const nextObject = generatorObject.next()
console.log(nextObject)
```
We should note that the result of `.next()` always results in an object with `value` and `done` keys. 

```
function* simpleGenerator() {
  console.log("before 1")
  yield 1
  console.log("after 1")
  console.log("before 2")
  yield 2
  console.log("after 2")
  console.log("before 3")
  yield 3
  console.log("after 3")
}

const generatorObject = simpleGenerator()

console.log(generatorObject.next())
console.log(generatorObject.next())
console.log(generatorObject.next())
console.log(generatorObject.next())
```

See how the code executes only up the the `yield`, then stops until the next `.next()` is invoked. 
The first time the generator is called, the generator object is returned. Calling next executes the code and returns an object with `value` and `done`.

We can also have a generator within a generator. Note the syntax here. 

```
function* simpleGenerator() {
  yield 1
  yield 2
  yield* inceptionGenerator()
  yield 6
}

function* inceptionGenerator() {
  yield 3
  yield 4
  yield 5
}

const generatorObject = simpleGenerator()

for (let num in simpleGenerator){
  console.log(num)
}
```

## III. Use Case

What is a UUID/GUID? Has your app every crashed from an infinite loop? Lets see how generators solve both of those problems. 

```
function *generateId() {
  let id = 1

  while (true) {
    yield id
    id++
  }
}

const idGenerator = generateId()

console.log(idGenerator.next())
console.log(idGenerator.next())
console.log(idGenerator.next())
console.log(idGenerator.next())
console.log(idGenerator.next())
```

## IV. Advanced features

```
function *generateId() {
  let id = 1

  while (true) {
    const increment = yield id
    if (increment != null) {
      id += increment
    } else {
      id++
    }
  }
}

const idGenerator = generateId()

console.log(idGenerator.next())
console.log(idGenerator.next())
console.log(idGenerator.next(12))
console.log(idGenerator.next(2))
console.log(idGenerator.next())
```

Remember those other two methods on the prototype? Lets try those out to see what happens. 

```
function *generateId() {
  let id = 1

  while (true) {
    const increment = yield id
    if (increment != null) {
      id += increment
    } else {
      id++
    }
  }
}

const idGenerator = generateId()

console.log(idGenerator.next())
console.log(idGenerator.next())
console.log(idGenerator.next(12))
console.log(idGenerator.next(2))
console.log(idGenerator.next())
console.log(idGenerator.throw(new Error("Whoops!")))
console.log(idGenerator.return())
console.log(idGenerator.next())

```

## V. Why we do this

Avoid callback hell, too many promises. 
