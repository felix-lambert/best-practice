# clean-code-javascript

## Introduction

Not every principle has to be strictly followed, and even fewer will be
universally agreed upon. These are guidelines and nothing more.

Our craft of software engineering is just a bit over 50 years old, and we are
still learning a lot. When software architecture is as old as architecture
itself, maybe then we will have harder rules to follow. For now, let these
guidelines serve as a touchstone by which to assess the quality of the
JavaScript code that you and your team produce.

## **Consistent dev environments**

Set your node version in engines in package.json.

Why:

It lets others know the version of node the project works on.

## **Variables**

### Use meaningful and pronounceable variable names

**Bad:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**Good:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```
**[⬆ back to top](#table-of-contents)**

### Use the same vocabulary for the same type of variable

**Bad:**
```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Good:**
```javascript
getUser();
```
**[⬆ back to top](#table-of-contents)**

### Use searchable names
We will read more code than we will ever write. It's important that the code we
do write is readable and searchable. By *not* naming variables that end up
being meaningful for understanding our program, we hurt our readers.
Make your names searchable. 

**Bad:**
```javascript
// What the heck is 86400000 for?
setTimeout(blastOff, 86400000);

```

**Good:**
```javascript
// Declare them as capitalized named constants.
const MILLISECONDS_IN_A_DAY = 86400000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);

```
**[⬆ back to top](#table-of-contents)**

### Use explanatory variables
**Bad:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(address.match(cityZipCodeRegex)[1], address.match(cityZipCodeRegex)[2]);
```

**Good:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```
**[⬆ back to top](#table-of-contents)**

### Avoid Mental Mapping
Explicit is better than implicit.

**Bad:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Wait, what is `l` for again?
  dispatch(l);
});
```

**Good:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```
**[⬆ back to top](#table-of-contents)**

### Don't add unneeded context
If your class/object name tells you something, don't repeat that in your
variable name.

**Bad:**
```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}
```

**Good:**
```javascript
const Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red';
}
```
**[⬆ back to top](#table-of-contents)**

### Use default arguments instead of short circuiting or conditionals
Default arguments are often cleaner than short circuiting. Be aware that if you
use them, your function will only provide default values for `undefined`
arguments. Other "falsy" values such as `''`, `""`, `false`, `null`, `0`, and
`NaN`, will not be replaced by a default value.

**Bad:**
```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.';
  // ...
}

```

**Good:**
```javascript
function createMicrobrewery(name = 'Hipster Brew Co.') {
  // ...
}

```
**[⬆ back to top](#table-of-contents)**

## **Objects**
### Avoid object literals
If we need to create the same type of object in other places, then we’ll end up copy-pasting the object’s methods, data, and initialization. We need a way to create not just the one object, but a family of objects.

**Bad:**
```javascript
var o = {
  x: 42,
  y: 3.14,
  f: function() {},
  g: function() {}
};
```

**Good:**
```javascript
function thing() {
  return {
    x: 42,
    y: 3.14,
    f: function() {},
    g: function() {}
  };
}

var o = thing();
```

# **Module**

JavaScript modules are self-contained code blocks, that perform specific work.

## **Factories**

### First example

**Bad:**

```javascript
const Animal = function(name) {
  this.name = name;
}

Animal.prototype.walk = function() {
  console.log(this.name + " walks");
}

poo = new Animal("poo")
tommy = new Animal("tommy")
poo.walk()
tommy.walk()

```

**Bad:**

```javascript

const Animal = {
  name: "not_given_yet", //remove this if you like
  walk() {
    console.log(this.name + " walks");
  }
}
const poo = Object.assign({}, Animal, {name: "poo"})
poo.walk()

```

**Bad:**

```javascript

const tommy = Object.create(Animal) //this sets up prototype chain
tommy.name = "tommy"
tommy.walk()

```

**Good:**

```javascript

const Animal = function(name){
    const animal = {};
    animal.name = name;
    animal.walk = function(){
        console.log(this.name + " walks");
    }
    return animal;
};

const poo = Animal("poo");
const tommy = Animal("tommy");
poo.walk() // poo walks
tommy.walk() // tommy walks


```

```javascript
const martialArtist = (state) => ({
  hit: () => console.log('Fly kick')
})

const vulgar = (state) => ({
  taunt: () => console.log('Show is over!!!!')
})

const BadassHero = (name) => {
  const state = {
    name
  };

  return Object.assign({}, martialArtist(state), vulgar(state))
}

const HitGirl = BadassHero('Hit-Girl')
hitGirl.taunt()
hitGirl.hit()
```

```javascript
// composition is designing your types with what they do
// inheritance is designing your types with what they are
const barker = (state) => ({
  bark: () => console.log('Woof, I am ' + state.name)
})

const driver = (state) => ({
  drive: () => state.position = state.position + state.speed
})

barker({name: 'karp'}).bark()
// Woof, I am karo

const murderRobotDog = (name) => {
  let state = {
    name,
    speed: 100,
    position: 0
  }
  return Object.assign(
    {},
    barker(state),
    driver(state)
  )
}

murderRobotDog('sniffles').bark()
```

## **Functionnal pattern**

```javascript
const xs = [1,2,3,4,5];

// impure
const minimum = 21;
const checkAge = age => age >= minimum;

// pure
const checkAge = (age) => {
  const minimum = 21;
  return age >= minimum;
};
```


### Names

Having multiple names for the same concept is a common source of confusion in projects. There is also the issue of generic code. 

**Bad:**
```javascript
// specific to our current blog
const validArticles = articles =>
  articles.filter(article => article !== null && article !== undefined),
```

**Good:**
```javascript
// vastly more relevant for future projects
const compact = xs => xs.filter(x => x !== null && x !== undefined);
```

### this

I avoid using this like a dirty nappy. There's really no need when writing functional code. 

## **Observable pattern**

Objects that notifies other objects when the state changes. The other objects are called observers. It basically describes events. Javascript and the DOM are heavily event. So if we don't have observer pattern, we don't have events.

It enables one-to-many data binding between elements so that when one object changes state, all its dependents are notified and updated automatically. This one-way data binding can be event driven. With this pattern, you can build reusable code that solves for your specific needs.

You dispatch an event, you can then listen to it. There is a state, and depending on what the state looks like, I rerender this.

In javascript, you have a DOM. It's an object that you can mutate. 

## **Currying**

You can do currying for dependency injection instead of class

```javascript
const makeCreateUser = connection => name => 
  connection.table('users').insert({
    is_new: true,
    full_name: name
  }).then(user => user.id)

const makeDeleteUser = connection => id

const makeBanUser = connection => id

const connection = db({
  host: 'database',
  port: 1212
})

const factories = [
  makeCreateUser,
  makeDeleteUser,
  makeUpdateUser,
  makeBanUser
]

// I use destructuring
const [
  createUser,
  deleteUser,
  updateUser,
  banUser
] = factories.map(factory => factory(connection))

// const createUser = makeCreateUser(connection)
// const deleteUser = makeDeleteUser(connection)
// const updateUser = makeUpdateUser(connection)
// const banUser = makeBanUser(connection)

const app = new App(createUser)
app.start()

```
**Bad:**
```javascript
const createUser = makeCreateUser(connection)
const deleteUser = makeDeleteUser(connection)
const updateUser = makeUpdateUser(connection)
const banUser = makeBanUser(connection)
```

**Good:**
```javascript
const factories = [
  makeCreateUser,
  makeDeleteUser,
  makeUpdateUser,
  makeBanUser
]

// I use destructuring
const [
  createUser,
  deleteUser,
  updateUser,
  banUser
] = factories.map(factory => factory(connection))
```

**Best:**
```javascript

const makeUserUtils = ({ connection, mailService }) => ({
  createUser: connection.table('users').insert({
    is_new: true,
    full_name: name
  }).then(user => user.id),
  deleteUser: connection => id => connection.table('users').delete(id),
  banUser: id => connection.table('users').update({
    $set: {
      banned: true,
    }
  }).then(() => mailService('jos@gmail.com', 'i banned you'))
})

// I use destructuring
const {
  makeCreateUser,
  makeDeleteUser,
  makeUpdateUser,
  makeBanUser
} = makeUserUtils({ connection, emailService })

```

## **Functions**
### Function arguments (2 or fewer ideally)
Limiting the amount of function parameters is incredibly important because it
makes testing your function easier. Having more than three leads to a
combinatorial explosion where you have to test tons of different cases with
each separate argument.

One or two arguments is the ideal case, and three should be avoided if possible.
Anything more than that should be consolidated. Usually, if you have
more than two arguments then your function is trying to do too much. In cases
where it's not, most of the time a higher-level object will suffice as an
argument.

Since JavaScript allows you to make objects on the fly, without a lot of class
boilerplate, you can use an object if you are finding yourself needing a
lot of arguments.

**Bad:**
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Good:**
```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```
**[⬆ back to top](#table-of-contents)**


### Functions should do one thing
This is by far the most important rule in software engineering. When functions
do more than one thing, they are harder to compose, test, and reason about.
When you can isolate a function to just one action, they can be refactored
easily and your code will read much cleaner. If you take nothing else away from
this guide other than this, you'll be ahead of many developers.

**Bad:**
```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Good:**
```javascript
function emailActiveClients(clients) {
  clients
    .filter(isActiveClient)
    .forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```
**[⬆ back to top](#table-of-contents)**

### Function names should say what they do

**Bad:**
```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to tell from the function name what is added
addToDate(date, 1);
```

**Good:**
```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```
**[⬆ back to top](#table-of-contents)**

### Functions should only be one level of abstraction
When you have more than one level of abstraction your function is usually
doing too much. Splitting up functions leads to reusability and easier
testing.

**Bad:**
```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}
```

**Good:**
```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const ast = lexer(tokens);
  ast.forEach((node) => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function lexer(tokens) {
  const ast = [];
  tokens.forEach((token) => {
    ast.push( /* ... */ );
  });

  return ast;
}
```
**[⬆ back to top](#table-of-contents)**

### Remove duplicate code
Do your absolute best to avoid duplicate code. Duplicate code is bad because it
means that there's more than one place to alter something if you need to change
some logic.

Imagine if you run a restaurant and you keep track of your inventory: all your
tomatoes, onions, garlic, spices, etc. If you have multiple lists that
you keep this on, then all have to be updated when you serve a dish with
tomatoes in them. If you only have one list, there's only one place to update!

Oftentimes you have duplicate code because you have two or more slightly
different things, that share a lot in common, but their differences force you
to have two or more separate functions that do much of the same things. Removing
duplicate code means creating an abstraction that can handle this set of
different things with just one function/module/class.

Getting the abstraction right is critical. Bad abstractions can be
worse than duplicate code, so be careful! Having said this, if you can make
a good abstraction, do it! Don't repeat yourself, otherwise you'll find yourself
updating multiple places anytime you want to change one thing.

**Bad:**
```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Good:**
```javascript
function showEmployeeList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case 'manager':
        data.portfolio = employee.getMBAProjects();
        break;
      case 'developer':
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```
**[⬆ back to top](#table-of-contents)**

### Set default objects with Object.assign

**Bad:**
```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Good:**
```javascript
const menuConfig = {
  title: 'Order',
  // User did not include 'body' key
  buttonText: 'Send',
  cancellable: true
};

function createMenu(config) {
  config = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```
**[⬆ back to top](#table-of-contents)**


### Don't use flags as function parameters
Flags tell your user that this function does more than one thing. Functions should do one thing. Split out your functions if they are following different code paths based on a boolean.

**Bad:**
```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Good:**
```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```
**[⬆ back to top](#table-of-contents)**

### Avoid Side Effects (part 1)


**Bad:**
```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Good:**
```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```
**[⬆ back to top](#table-of-contents)**

### Avoid Side Effects (part 2)

**Bad:**
```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Good:**
```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ back to top](#table-of-contents)**

### Don't write to global functions

**Bad:**
```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Good:**
```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```
**[⬆ back to top](#table-of-contents)**

### Favor functional programming over imperative programming
JavaScript isn't a functional language in the way that Haskell is, but it has
a functional flavor to it. Functional languages can be cleaner and easier to test.
Favor this style of programming when you can.

**Bad:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Good:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput
  .map(output => output.linesOfCode)
  .reduce((totalLines, lines) => totalLines + lines);
```
**[⬆ back to top](#table-of-contents)**

### Encapsulate conditionals

**Bad:**
```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**Good:**
```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```
**[⬆ back to top](#table-of-contents)**

### Avoid negative conditionals

**Bad:**
```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Good:**
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```
**[⬆ back to top](#table-of-contents)**

### Avoid conditionals
This seems like an impossible task. Upon first hearing this, most people say,
"how am I supposed to do anything without an `if` statement?" The answer is that
you can use polymorphism to achieve the same task in many cases. The second
question is usually, "well that's great but why would I want to do that?" The
answer is a previous clean code concept we learned: a function should only do
one thing. When you have classes and functions that have `if` statements, you
are telling your user that your function does more than one thing. Remember,
just do one thing.

**Bad:**
```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Good:**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}


```


**[⬆ back to top](#table-of-contents)**

### Don't over-optimize
Modern browsers do a lot of optimization under-the-hood at runtime. A lot of
times, if you are optimizing then you are just wasting your time. 

**Bad:**
```javascript

// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Good:**
```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```
**[⬆ back to top](#table-of-contents)**

### Use getters and setters

**Bad:**
```javascript

function makeBankAccount() {
  // ...

  return {
    balance: 0,
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Good:**
```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance,
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

### Make objects have private members

This can be accomplished through closures (for ES5 and below).

**Bad:**
```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Good:**
```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    },
  };
}

const employee = makeEmployee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

### Use method chaining

This pattern is very useful in JavaScript and you see it in many libraries such as jQuery and Lodash. It allows your code to be expressive, and less verbose. For that reason, I say, use method chaining and take a look at how clean your code will be. In your class functions, simply return this at the end of every function, and you can chain further class methods onto it.

**Bad:**
```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car('Ford','F-150','red');
car.setColor('pink');
car.save();
```

**Good:**
```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car('Ford','F-150','red')
  .setColor('pink')
  .save();

```

**Bad:**
```javascript
const person = {
  name: 'John',
  age: 28
}
const newPerson = person
newPerson.age = 30
console.log(newPerson === person) // true
console.log(person) // { name: 'John', age: 30 }

```

Object.assign is an ES6 feature that takes objects as parameters. It will merge all objects you pass it into the first one. You are probably wondering why the first parameter is an empty object {}. If the first parameter would be ‘person’ we would still mutate person. If it would be { age: 30 }, we’d overwrite 30 with 28 again because that would be coming after. This solution works, we kept person intact, we treated it as immutable!

**Good:**
```javascript
const person = {
  name: 'John',
  age: 28
}
const newPerson = Object.assign({}, person, {
  age: 30
})

console.log(newPerson === person) // false
console.log(person) // { name: 'John', age: 28 }
console.log(newPerson) // { name: 'John', age: 30 }

```

**Even better:**
```javascript
const person = {
  name: 'John',
  age: 28
}
const newPerson = {
  ...person,
  age: 30
}
console.log(newPerson === person) // false
console.log(newPerson)
```

This time, even cleaner code. First, the ‘spread’ operator (...), copies all the properties from person to the new object. Then we define a new ‘age’ property that overrides the old one. Note that order matters, if age: 30 would be defined above ...person, it would be overridden by age: 28.

## Delete item in object


**Good**:
```javascript
const person = {
  name: 'John',
  password: '123',
  age: 28
}
const newPerson = Object.keys(person).reduce((obj, key) => {
  if (key !== property) {
    return { ...obj, [key]: person[key] }
  }
  return obj
}, {})
```

### Arrays

**Bad**:
```javascript
const characters = [ 'Obi-Wan', 'Vader' ]
const newCharacters = characters
newCharacters.push('Luke')
console.log(characters === newCharacters) // true :-(
```

**Good**:
```javascript
const characters = [ 'Obi-Wan', 'Vader' ]
const newCharacters = [ ...characters, 'Luke' ]
console.log(characters === newCharacters) // false
console.log(characters) // [ 'Obi-Wan', 'Vader' ]
console.log(newCharacters) // [ 'Obi-Wan', 'Vader', 'Luke' ]

```

### When to use map?

.map() when you want to transform elements in an array.

### When to use filter?

.filter() when you want to select a subset of multiple elements from an array.

### When to use find?

.find() When you want to select a single element from an array.

### When to use reduce?

.reduce() when you want derive a single value from multiple elements in an array.


### Prefer composition over inheritance
As stated famously in [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
(Change the caloric expenditure of all animals when they move).

**[⬆ back to top](#table-of-contents)**

### Open/Closed Principle (OCP)
As stated by Bertrand Meyer, "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**[⬆ back to top](#table-of-contents)**

### Liskov Substitution Principle (LSP)
This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class and child class can be used interchangeably without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

### Interface Segregation Principle (ISP)
JavaScript doesn't have interfaces so this principle doesn't apply as strictly
as others. However, it's important and relevant even with JavaScript's lack of
type system.

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use." Interfaces are implicit contracts in JavaScript because of
duck typing.

**[⬆ back to top](#table-of-contents)**

### Dependency Inversion Principle (DIP)
This principle states two essential things:
1. High-level modules should not depend on low-level modules. Both should
depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
abstractions.

This can be hard to understand at first, but if you've worked with AngularJS,
you've seen an implementation of this principle in the form of Dependency
Injection (DI). While they are not identical concepts, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through DI. A huge benefit of this is that it reduces
the coupling between modules. Coupling is a very bad development pattern because
it makes your code hard to refactor.

As stated previously, JavaScript doesn't have interfaces so the abstractions
that are depended upon are implicit contracts. That is to say, the methods
and properties that an object/class exposes to another object/class. In the
example below, the implicit contract is that any Request module for an
`InventoryTracker` will have a `requestItems` method.

## **Testing**
Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a great testing framework, you also need to use a
[good coverage tool](http://gotwarlost.github.io/istanbul/).

There's no excuse to not write tests. There are [plenty of good JS test frameworks](http://jstherightway.org/#testing-tools), so find one that your team prefers.
When you find one that works for your team, then aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single concept per test

**Bad:**
```javascript
import assert from 'assert';

describe('MakeMomentJSGreatAgain', () => {
  it('handles date boundaries', () => {
    let date;

    date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);

    date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);

    date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

**Good:**
```javascript
import assert from 'assert';

describe('MakeMomentJSGreatAgain', () => {
  it('handles 30-day months', () => {
    const date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);
  });

  it('handles leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);
  });

  it('handles non-leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

## **Error Handling**
Thrown errors are a good thing! They mean the runtime has successfully
identified when something in your program has gone wrong and it's letting
you know by stopping function execution on the current stack, killing the
process (in Node), and notifying you in the console with a stack trace.

### Don't ignore caught errors
Doing nothing with a caught error doesn't give you the ability to ever fix
or react to said error. Logging the error to the console (`console.log`)
isn't much better as often times it can get lost in a sea of things printed
to the console. If you wrap any bit of code in a `try/catch` it means you
think an error may occur there and therefore you should have a plan,
or create a code path, for when it occurs.

**Bad:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

## **React**

React is a library for building the view of an application. The view rerender based on the state. 

React looks at the virtual dom and compare it to the real dom and applies the smallest possible change to the real dom. 

### Declarative programming

It gives the structure for doing declarative programming. The view should be rendered given a certain state. With declarative programming you are just declaring a relationship.

### The DOM

We want to touch the DOM as little as possible.

**Bad:**
```javascript

const MyComponent = () => {
  <div>
    <OtherComponent type="a" className="colorful" foo={123} bar={456} />
    <OtherComponent type="b" className="colorful" foo={123} bar={456} />
  </div>
}

```

**Good:**
```javascript

const MyOtherComponent = ({type}) => {
  <div>
    <OtherComponent type={type} className="colorful" foo={123} bar={456} />
  </div>
}


const OtherComponent = () => {
  <div>
    <MyOtherComponent type="a" />
    <MyOtherComponent type="b" />
  </div>
}

```

**Bad:**
```javascript
const done = current >= goal

```

**Good:**
```javascript
const isComplete = current >= goal

```

**Bad:**
```javascript
import title from 'Title'

export const Thingie = ({description}) => {
  <div class="thingie">
    <div class="description-wrapper">
      <Description value={description} />
    </div>
  </div>
}


export const ThingieWithTitle = ({description}) => {
  <div class="thingie">
    <Title value={title} />
    <div class="description-wrapper">
      <Description value={description} />
    <div>
  <div>
}

```

**Good:**
```javascript

import title from 'Title'

export const Thingie = ({description, children}) => {
  <div class="thingie">
    {children}
    <div class="description-wrapper">
      <Description value={description} />
    </div>
  </div>
}


export const ThingieWithTitle = ({title, ...others}) => {
  <Thingie {...others}>
    <Title value={title} />
  </Thingie>
}

```

**Bad:**
```javascript
const splitLocale = locale.split('-')
const language = splitLocale[0]
const country = splitLocale[1]

```

**Good:**
```javascript
const [ language, country ] = locale.split('-')
```

### Do more in componentDidUpdate

**Bad:**
```javascript
navigateToContact: function(newContactId) {
  if (this._hasUnsavedChanges()) {
    this._saveChanges();
  }
  
  this.setState({
    currentContactId: newContactId
  });
}

navigateToContact('contact2')
```

**Good:**
```javascript
componentDidUpdate: function(prevProps, prevState) {
  if (prevState.currentContactId !== state.currentContactId) {
    if (this._hasUnsavedChanges()) {
      this._saveChanges();
    }
  }
}
```

### A container does data fetching and then renders its corresponding sub-component.

**Bad:**
```javascript
class CommentList extends React.Component {
  getInitialState () {
    return { comments: [] };
  }
 
  componentDidMount () {
    $.ajax({
      url: "/my-comments.json",
      dataType: 'json',
      success: function(comments) {
        this.setState({comments: comments});
      }.bind(this)
    });
  }
 
  render () {
    return (
      <ul>
        {this.state.comments.map(({body, author}) => {
          return <li>{body}—{author}</li>;
        })}
      </ul>
    );
  }
}
```

**Good:**
```javascript

// CommentList.js
class CommentList extends React.Component {
  render() {
    return (
      <ul>
        {this.props.comments.map(({body, author}) => {
          return <li>{body}—{author}</li>;
        })}
      </ul>
    );
  }
}
// CommentListContainer.js
class CommentListContainer extends React.Component {
  getInitialState () {
    return { comments: [] }
  }
 
  componentDidMount () {
    $.ajax({
      url: "/my-comments.json",
      dataType: 'json',
      success: function(comments) {
        this.setState({comments: comments});
      }.bind(this)
    });
  }
 
  render () {
    return <CommentList comments={this.state.comments} />;
  }
}

```

### Do not keep state in render

**Bad:**
```javascript
render () {
  let make = `Make: ${this.props.make}`;
 
  return <div>{name}</div>;
}
```

**Good:**
```javascript
render () {
  return <div>{`Make: ${this.props.make}`}</div>;
}
```

**Good:**
```javascript
get make () {
  return `Make: ${this.props.make}`;
}
 
render () {
  return <div>{this.make}</div>;
}
```

### Don’t put compound conditions in render


**Good:**
```javascript
get isEngineOn() {
  return this.state.started && this.state.running;
},
 
render() {
  return <div>{(this.isEngineOn) && 'Running..'}</div>;
}
```

### Separate stateful aspects from rendering

**Bad:**
```javascript
class User extends Component {
  state = { loading: true };

  render() {
    const { loading, user } = this.state;
    return loading
      ? <div>Loading...</div>
      : <div>
          <div>
            First name: {user.firstName}
          </div>
          <div>
            First name: {user.lastName}
          </div>
          ...
        </div>;
  }

  componentDidMount() {
    fetchUser(this.props.id)
      .then((user) => { this.setState({ loading: false, user })})
  }
}
```

**Good:**
```javascript
class User extends Component {
  state = { loading: true };

  render() {
    const { loading, user } = this.state;
    return loading ? <Loading /> : <RenderUser user={user} />;
  }

  componentDidMount() {
    fetchUser(this.props.id)
      .then(user => { this.setState({ loading: false, user })})
  }
}
```

### Rest/spread

**Bad:**
```javascript
// Dirty
const MyComponent = (props) => {
  const others = Object.assign({}, props);
  delete others.className;
  return (
    <div className={props.className}>
      {React.createElement(MyOtherComponent, others)}
    </div>
  );
};
```

**Good:**
```javascript
// Clean
const MyComponent = ({ className, ...others }) => (
  <div className={className}>
    <MyOtherComponent {...others} />
  </div>
);
```

## Mobx

## Observable state

MobX provides functions to make data observable. Observable data can be watched by other pieces of code which may efficiently update when the state changes. Observable data is primarily created in MobX using the observable function.

We figured out that if all our model objects became Observables and all our React components became Observers of the model, we wouldn’t need to apply any further magic to make sure the relevant part, and only the relevant part, of our UI gets updated. 

Use @observer (mobx-react) or autorun() and it's automatically 'subscribed'.

One of the core philosophies of MobX is that the observable state should be as minimal as possible. Everything else should be derived via computed properties. 

#### observable

What we can watch for changes.

#### Autorun

Runs when the store is changing. Runs every time observables inside of autorun changes.

#### autorunAsync

Same as autorun but you can specify how much time after it should run after the data changes in observables.

#### when

Same as autorun but you set predicate to specify exactly when it should run

## Derivations

Functions that watch observable data are called derivations. MobX has 2 primary types of derivations: computed values and reactions. Computed values update a value based on other data, while reactions produce side effects: updates to a UI, a network call, or a logging statement for example. MobX provides a computed function for defining computed values; in most cases reactions will likely be mostly defined using a framework specific helper library like mobx-react or ng2-mobx. MobX does provide some lower level libraries for reactions though, including autorun and when.

#### Computed

Derivation of your data. Mobx is using getters and setters behind the field. They are highly optimized, so use them wherever possible.


By thinking of your client-state in terms of a minimal set of observables and augmenting it with derivations (computed properties), you can model a variety of scenarios effortlessly. Computed properties derive their value from other observables. If any of these depending observables change, the computed property changes as well.

#### Reaction

reaction: same as autorun but you can choose which observable to track. What do you want things to happen, when there is a change. Take three parameters. Many different types of functions that can be executed based on observable values.

### Actions 

Code that updates observable state is known as an action. MobX has a formalized version of actions which can be defined using the action function, but it is also possible to modify state directly using any normal JavaScript code and maintain observable behavior.

### Observer
It must be applied first - before @inject - like this @inject("something") @observer class Component {}.

It creates a higher-order-component (HOC) that wraps a React component to automatically update on changes to the observable state. Internally, observer() keeps track of observables that are dereferenced in the render method of the component. When any of them change, a re-render of the component is triggered.

You should use observer on every component that displays observable data. Even the small ones. observer allows components to render independently from their parent and in general this means that the more you use observer, the better the performance become. 

@observer automatically applies the shouldComponentUpdate from PureRenderMixin. It knows that it has to rerender, so the shouldComponentUpdate is skipped.

The core observers are autorun, reaction, and when.

You can transform a react component to observe the observables used in the render() function. When they change, a re-render of the react component is triggered. Internally, observer() creates a wrapper component that uses a plain reaction() to watch the observables and re-render as a side-effect. This is why we treat UI as being just another side-effect, albeit a very visible and obvious one. 

### Provider

Provider is used to put things (dependencies) on react's context. The context is available to all descendant components. The things are identified by name: <Provider name={thing}>. Provider is a component that can pass stores (or other stuff) using React's context mechanism to child components. This is useful if you have things that you don't want to pass through multiple layers of components explicitly.

### Inject

@inject is used to retrieve these things from context in descendants of Provider. Inject takes these things from context by name and makes them available by moving them to props with the same name.
So inject("name") will take "name" thing from the context and make it available as this.props.name
inject can be used to pick up those stores. It is a higher order component that takes a list of strings and makes those stores available to the wrapped component.

```javascript
export default inject('WeathorStore')(observer(App))
```

This store typically doesn't have much logic in it, but will store a plethora of loosely coupled pieces of information about the UI. 

Things you will typically find in UI stores:

* Session information
* Information about how far your application has loaded
* Information that will not be stored in the backend
* Information that affects the UI globally
  - Window dimensions
  - Accessibility information
  - Current language
  - Currently active theme
* User interface state as soon as it affects multiple, further unrelated components:
  - Current selection
  - Visibility of toolbars, etc.
  - State of a wizard
  - State of a global overlay


## Cypress

1. Don’t target elements based on CSS attributes such as: id, class, tag
2. Don’t target elements that may change their textContent
3. Add data-* attributes to make it easy to target elements

Given a button that we want to interact with:

<button id="main" class="btn btn-large" data-cy="submit">Submit</button>

```javascript
cy.get('button').click()	 Never	Worst - too generic, no context.
cy.get('.btn.btn-large').click()	 Never	Bad. Coupled to styling. Highly subject to change.
cy.get('#main').click()	 Sparingly	Better. But still coupled to styling or JS event listeners.
cy.contains('Submit').click()	 Depends	Much better. But still coupled to text content that may change.
cy.get('[data-cy=submit]').click()	 Always	Best. Insulated from all changes.
```

 Anti-Pattern: Coupling multiple tests together.

 Best Practice: Tests should always be able to be run independently from one another and still pass.

 Simply put an .only on the test and refresh the browser.

If this test can run by itself and pass - congratulations you have written a good test.

If this is not the case, then you should refactor and change your approach.

**Bad:**
```javascript
// an example of what NOT TO DO
describe('my form', function () {
  it('visits the form', function () {
    cy.visit('/users/new')
  })

  it('requires first name', function () {
    cy.get('#first').type('Johnny')
  })

  it('requires last name', function () {
    cy.get('#last').type('Appleseed')
  })

  it('can submit a valid form', function () {
    cy.get('form').submit()
  })
})
```

**Good:**
```javascript
// a bit better
describe('my form', function () {
  it('can submit a valid form', function () {
    cy.visit('/users/new')

    cy.log('filling out first name') // if you really need this
    cy.get('#first').type('Johnny')

    cy.log('filling out last name') // if you really need this
    cy.get('#last').type('Appleseed')

    cy.log('submitting form') // if you really need this
    cy.get('form').submit()
  })
})
```

**Good:**
```javascript
// a bit better
describe('my form', function () {
  it('can submit a valid form', function () {
    cy.visit('/users/new')

    cy.log('filling out first name') // if you really need this
    cy.get('#first').type('Johnny')

    cy.log('filling out last name') // if you really need this
    cy.get('#last').type('Appleseed')

    cy.log('submitting form') // if you really need this
    cy.get('form').submit()
  })
})
```

**Good:**
```javascript
describe('my form', function () {
  beforeEach(function () {
    cy.visit('/users/new')
    cy.get('#first').type('Johnny')
    cy.get('#last').type('Appleseed')
  })

  it('displays form validation', function () {
    cy.get('#first').clear() // clear out first name
    cy.get('form').submit()
    cy.get('#errors').should('contain', 'First name is required')
  })

  it('can submit a valid form', function () {
    cy.get('form').submit()
  })
})
```

**Bad:**
```javascript
describe('Searches Page', () => {
  it('should load the searches page', () => {
    before(() => {
      // visit searches page
    })
  })

  it('should click the "My Favorites Tab"', () => {
    // Click "My Favorites Tab"
  })

  it('should have the correct column names on the My Favorites Tab', () => {
    // Assert column names
    // whoops, if the previous test fails, this will also fail
  })

  it('should click the "All Searches Tab"', () => {
    // Click "All Searches Tab"
  })

  it('should have the correct column names on the All Searches Tab', () => {
    // Assert column names
    // whoops, if the previous test fails, this will also fail
  })
})
```



**Good:**
```javascript
describe('Searches Page', () => {
  // run once for the entire "Searches Page" block
  // we use `before` instead of `beforeEach` for speed because our tests can handle it only be run once
  before(() => {
    // visit searches page
  })

  describe('My Favorites Tab', () => {
    // Run for every `it` block in the "My Favorites Tab" block
    beforeEach(() => {
      // Make sure we are in the right state for each `it` block
      // Click the "All Searches Tab"
    })

    it('should have the correct column names', () => {
      // Assert column names
    })
  })
})
```

**Bad:**
```javascript
// bad - duplicate information and hard to separate setup
describe('Verify expected columns under different searches tabs', () => {
  it('should verify expected columns under My Saved, All Searches, Shared Searches and My Favorite tabs', () => {
    // setup and assertions
    // maybe somebody later puts more here because there doesn't seem to be any focus
  })
})
```

**Good:**
```javascript
// Outline:
// Verify expected columns under different searches tabs
//   - should verify expected columns under My Saved, All Searches, Shared Searches and My Favorite tabs
// good
describe('Searches Page', () => {
  before(() => {
    // visit searches page
  })

  describe('My Saved Tab', () => {
    beforeEach(() => {
      // click "My Saved" tab
    })

    it('should have the correct column names', () => {
      // verify column names for "My Saved Tab"
    })

    // This seems like a nice place to add another test that need the "My Saved Tab" active
  })

  describe('All Searches Tab', () => {
    beforeEach(() => {
      // click "All Searches" tab
    })

    it('should have the correct column names', () => {
      // verify column names for "All Searches Tab"
    })
  })
})
// Outline:
// Searches Page
//  - My Saved Tab
//    - should have the correct column names
//  - All Searches Tab
//    - should have the correct column names
```


**Bad:**
```javascript
// bad
cy.get('body').should($body => {
  expect($body.find('[data-testid=Tab]').length === 2).to.equal(true) // expected false to equal true
})
```

**Good:**
```javascript
// better
cy.get('body').should($body => {
  expect($body).to.have.descendants('[data-testid=Tab]') // expected '<body>' to have descendants '[data-testid=Tab]'
  expect($body.find('[data-testid=Tab]')).to.have.length(2) // expected '[ <div[data-testid=Tab]>, 4 more... ]' to have a length of 2 but got 5
})

// best - we can use the shorthand
cy
  .get('body')
  .should('have.descendants', '[data-testid=Tab]') // expected '<body>' to have descendants '[data-testid=Tab]'
  .find('[data-testid=Tab]')
  .should('have.length', 2) // expected '[ <div[data-testid=Tab]>, 4 more... ]' to have a length of 2 but got 5
```

Use URL query params:

```javascript
// tell your backend server which campaign you want sent
// so you can deterministically know what it is ahead of time
cy.visit('https://app.com?campaign=A')

...

cy.visit('https://app.com?campaign=B')

...

cy.visit('https://app.com?campaign=C')
```

Now there is not even a need to do conditional testing since you are able to know ahead of time what campaign was sent. Yes, this may require server side updates, but you have to make an untestable app testable if you want to test it!



## Styled components

**Bad:**
```javascript
const darkSection = styled.div`
  p {
    color: #FFFFFF;
  }
`
```

**Good:**
```javascript
import { ThemeProvider } from 'styled-components'

const P = styled.p`
  color: ${props => props.theme.color};
`

P.defaultProps = {
  theme: {
    color: #000000;
  }
}

const DarkSection = () => (
  <ThemeProvider theme={{ color: '#000000' }}>
    /* dark section content */
  </ThemeProvider>
)
```

1. Set

You can use the Set object to handle the work of pulling out unique values. 


If you pass your array of colors into a set, you’re nearly there. ​  ​const​ colors = [​'black'​, ​'black'​, ​'chocolate'​]; ​  ​  ​const​ unique = ​new​ Set(colors); ​  ​// Set {'black', 'chocolate'}​ You probably noticed that the value of the object is a Set containing only one instance of each color. And that may seem like a problem. You don’t want a Set—you want an array of unique items. Well, by now you may have guessed the solution: the spread operator. You can use the spread operator on Set much like you did with Map. The only difference is that Set returns an array.

If you pass your array of colors into a set, you’re nearly there. ​  ​

```javascript
const​ colors = [​'black'​, ​'black'​, ​'chocolate'​];
​const​ unique = ​new​ Set(colors); ​  ​

// Set {'black', 'chocolate'}​ 

```

You probably noticed that the value of the object is a Set containing only one instance of each color. And that may seem like a problem. You don’t want a Set—you want an array of unique items. Well, by now you may have guessed the solution: the spread operator. You can use the spread operator on Set much like you did with Map. The only difference is that Set returns an array. Exactly what you want! Now you can refactor the getUnique() function to a one liner. 

If you try to add a value that already exists, it will be ignored. Order is preserved, and the initial point a value is added will remain. If you try to add an item that’s there already, it keeps the original position. 

```javascript
​let​ names = ​new​ Set(); 
 
names.add(​'joe'​); 
​// Set { 'joe'}​ 
 
names.add(​'bea'​); 
​// Set { 'joe', 'bea'}​ ​
 
names.add(​'joe'​); ​  
​// Set { 'joe', 'bea'}​

```

Say you want to keep a collection of credit cards that belong to a user. You may want to query whether a particular credit card exists in your collection. Array is a poor choice for such operations; we really need a Set. JavaScript has finally agreed with that sentiment. 