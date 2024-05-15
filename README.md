# PensionBee Code Review

#### Notes

- I've tried to break down this code review from the perspective of explaining the code to a more junior member within the team.

```JavaScript
var animalData = [
  {
    id: 1,
    title: 'hippo',
    faveFood: 'carrots',
  },
  {
    id: 2,
    title: 'Cat',
    faveFood: 'carrots',
  },
  {
    id: 3,
    title: 'ducks',
    faveFood: 'breadcrumbs',
  },
];

exports.findAnimal = function () {
  for (x = 0; x < 3; x++) {
    if ((animalData[x].title = args[0])) {
      var answer = animalData[x].faveFood;
    }
  }
  return answer;
};
```

Hi [colleague name], this is looking good and a great start! However, we might run into some issues. I'll try my best to explain what could cause us some hiccups. If anything doesn't quite make sense feel free to ping me some questions back or if it's easier I'm happy to jump on a call to explain.

To start off let’s take a look at the function itself. The approach looks solid and a for loop would work for what we want the outcome to be. However, we are going to run into issues accessing the value of `x` as we haven’t initialised the value. This means that when we access `animalData[x]` we’re going to have an error as `x` is `undefined`. To fix this we can use a `let` to initialise `x`. We can also ensure that we loop through all the values within our `animalData`, we can do this by looping until `x` is less than the length of our `animalData` array.

```JavaScript
for (let x = 0; x < animalData.length; x++) {
    // Our code here
}
```

We have the right approach looking up the `title` and comparing with the `args` value. However, in the code you’re using an `=` rather than a `==` or `===`. In our case we’d probably want to use a `===` which will strictly check our left element against our right element.

```JavaScript
if (animalData[x].title === args[0]) {
    // Our code here
}
```

We will also run into issues when accessing the `args` value as we haven’t passed this into our function definition, therefore `args` will be `undefined`. We’ll want to change the data type of `args` too if possible.

If we can pull out the value of `args` when we call our `findAnimal` function so that we’re only passing a string rather than an array it’ll make our lookup faster and keep this function simple. We can also make the input more descriptive so that our code makes more sense for the next person who will read it.

```JavaScript
exports.findAnimal = function (animalType) {
    if (animalData[x].title === animalType) {
	    // Our code here
    }
};
```

We'll want to consider limiting the use of `var` as this is a global declaration, which means that we could access this variable anywhere in the code base, which is a bit inappropriate for our little function. We have two options here, we either:

- Keep the same structure and store the answer within a `const` and then return the value within our `if` statement. If we do this we can be more descriptive.
- We return the value straight away rather than storing the value first.

```
// Approach 1
    if () {
      const favouriteFood = animalData[x].faveFood;
      return favouriteFood;
    }

// Approach 2
    if () {
      return animalData[x].faveFood;
    }
```

In doing both of the above approach will mean that our final return (`return answer`) will be `undefined` if it is called. We’d still want to return something here and this could be a good opportunity to tell the user that the animal that they are calling our function with does not exist in our lookup table.

```JavaScript
return "Animal not found";
```

Lastly looking back at our `animalData`, if this data is something that isn’t going to change and is going to only be called by our function in the same file we would want to remove the use of `var` and instead use a `const` as we don’t want this data globally available to the code base. Additionally, we want to standardise the data, what I mean by that is there are different casings in some of the data entries. E.g. for title we have lower case entries for 2 of the three entries.

```JavaScript
const animalData = [
  {
    id: 1,
    title: 'hippo',
    faveFood: 'carrots',
  },
  {
    id: 2,
    title: 'cat',
    faveFood: 'carrots',
  },
  {
    id: 3,
    title: 'ducks',
    faveFood: 'breadcrumbs',
  },
];
```

We should have something like this now and it should run as originally intended:

```JavaScript
const animalData = [
  {
    id: 1,
    title: 'hippo',
    faveFood: 'carrots',
  },
  {
    id: 2,
    title: 'cat',
    faveFood: 'carrots',
  },
  {
    id: 3,
    title: 'ducks',
    faveFood: 'breadcrumbs',
  },
];

exports.findAnimal = function (animalType) {
  for (let x = 0; x < 3; x++) {
    if (animalData[x].title === animalType) {
      return animalData[x].faveFood;
    }
  }
	return "Animal not found";
};
```

Here are a few additional suggestions you may want to consider too:

- We may want to consider sanitising the input of `animalType` for example if our function is being passed in an `animalType` that has been inputted by a user it could be in a few different forms: all lowercase, all uppercase, First letter could be uppercase, etc. we’d probably want to convert the `animalType` to all lowercase to match our titles in our data. This could be done like this:

```JavaScript
if (animalData[x].title === animalType.toLowerCase()) {
   return animalData[x].faveFood;
}
```

- We may want to consider moving away from a `for` loop and move towards a `forEach` this will help be a little bit more descriptive for the next person who reads the code. This could be done like this:

```JavaScript
	animalData.forEach((animal) => {
		if(animal.title === animalType) {
			return animal.faveFood;
		}
	})
	return "Animal not found";
};
```

- We could also move away from looping through the array and find the animal directly in the data, we could do this using the `.find()` method

```JavaScript
exports.findAnimal = function (animalType) {
    const animalFaveFood = animalData.find((animal) => animal.title === animalType).faveFood;
    if (animalFaveFood) {
        return animalFaVeFood;
    }
    return 'Animal not found';
};
```

Hopefully this all makes sense, but again feel free to ask any questions for things that you don't understand. I'm happy to jump on a call to explain stuff too.

#### Notes

Additional things that I would consider inside this PR depending on the current best practices and conventions within the repo.

- The use of `i` instead of `x` in a for loop.
- Moving towards more ES6 style function definitions. E.g. arrow functions.
- Move towards ternary operators to shorten code to make the main code more readable.
- The use of TypeScript to define types for variables, inputs and outputs. (Along with any inference).
