# Front End Style Guide

Style guide for javascript projects. Adapted from the Airbnb style guides.
## Table of contents

## JavaScript

### Types

- Primitives: When you access a primitive type you work directly on its value.
  - `string`
  - `number`
  - `boolean`
  - `null`
  - `undefined`
  - `symbol`
  - `bigint`

```js
const foo = 1;
let bar = foo;

bar = 9;

console.log(foo, bar); // => 1, 9
```

  - Symbols and BigInts cannot be faithfully polyfilled, so they should not be used when targeting browsers/environments that don’t support them natively.

- Complex: When you access a complex type you work on a reference to its value.
  - `object`
  - `array`
  - `function`

```js
const foo = [1, 2];
const bar = foo;

bar[0] = 9;

console.log(foo[0], bar[0]); // => 9, 9
```

### References

- Use `const` for all of your references; avoid using `var`. eslint: `prefer-const`, `no-const-assign`

> This ensures that you can’t reassign your references, which can lead to bugs and difficult to comprehend code.

```js
// bad
var a = 1;
var b = 2;

// good
const a = 1;
const b = 2;
```

- If you must reassign references, use `let` instead of `var`. eslint: `no-var`

> Why? `let` is block-scoped rather than function-scoped like `var`.

```js
// bad
var count = 1;
if (true) {
  count += 1;
}

// good
let count = 1;
if (true) {
  count += 1;
}
```

### Objects

- Use the literal syntax for object creation. eslint: `no-new-object`

```js
// bad
const item = new Object();

// good
const item = {};
```

- Use computed property names when creating objects with dynamic property names.

> They allow you to define all the properties of an object in one place.

```js
function getKey(k) {
  return `a key named ${k}`;
}

// bad
const obj = {
  id: 5,
  name: 'San Francisco',
};
obj[getKey('enabled')] = true;

// good
const obj = {
  id: 5,
  name: 'San Francisco',
  [getKey('enabled')]: true,
};
```

- Use object method shorthand. eslint: `object-shorthand`

```js
// bad
const atom = {
  value: 1,
  addValue: function (value) {
    return atom.value + value;
  },
};

// good
const atom = {
  value: 1,
  addValue(value) {
    return atom.value + value;
  },
};
```

- Use property value shorthand. eslint: `object-shorthand`

> It is shorter and descriptive.

```js
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  lukeSkywalker: lukeSkywalker,
};

// good
const obj = {
  lukeSkywalker,
};
```

- Group your shorthand properties at the beginning of your object declaration.

> It’s easier to tell which properties are using the shorthand.

```js
const anakinSkywalker = 'Anakin Skywalker';
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  lukeSkywalker,
  episodeThree: 3,
  mayTheFourth: 4,
  anakinSkywalker,
};

// good
const obj = {
  lukeSkywalker,
  anakinSkywalker,
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  episodeThree: 3,
  mayTheFourth: 4,
};
```

- Only quote properties that are invalid identifiers. eslint: `quote-props`

> In general we consider it subjectively easier to read. It improves syntax highlighting, and is also more easily optimized by many JS engines.

```js
// bad
const bad = {
  'foo': 3,
  'bar': 4,
  'data-blah': 5,
};

// good
const good = {
  foo: 3,
  bar: 4,
  'data-blah': 5,
};
```

- Do not call `Object.prototype` methods directly, such as `hasOwnProperty`, `propertyIsEnumerable`, and `isPrototypeOf`. eslint: `no-prototype-builtins`

> These methods may be shadowed by properties on the object in question - consider `{ hasOwnProperty: false }` - or, the object may be a null object (`Object.create(null)`).

```js
// bad
console.log(object.hasOwnProperty(key));

// good
console.log(Object.prototype.hasOwnProperty.call(object, key));

// best
const has = Object.prototype.hasOwnProperty; // cache the lookup once, in module scope.
console.log(has.call(object, key));
```

- Prefer the object spread syntax over `Object.assign` to shallow-copy objects. Use the object rest operator to get a new object with certain properties omitted. eslint: `prefer-object-spread`

```js
// very bad
const original = { a: 1, b: 2 };
const copy = Object.assign(original, { c: 3 }); // this mutates `original` ಠ_ಠ
delete copy.a; // so does this

// bad
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }

// good
const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }

const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
```

### Arrays

- Use the literal syntax for array creation. eslint: `no-array-constructor`

```js
// bad
const items = new Array();

// good
const items = [];
```

- Use Array#push instead of direct assignment to add items to an array.

```js
const someStack = [];

// bad
someStack[someStack.length] = 'abracadabra';

// good
someStack.push('abracadabra');
```

- Use array spreads `...` to copy arrays.

```js
// bad
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i += 1) {
  itemsCopy[i] = items[i];
}

// good
const itemsCopy = [...items];
```

- To convert an iterable object to an array, use spreads `...` instead of `Array.from`.

```js
const foo = document.querySelectorAll('.foo');

// good
const nodes = Array.from(foo);

// best
const nodes = [...foo];
```

- Use `Array.from` for converting an array-like object to an array.

```js
const arrLike = { 0: 'foo', 1: 'bar', 2: 'baz', length: 3 };

// bad
const arr = Array.prototype.slice.call(arrLike);

// good
const arr = Array.from(arrLike);
```

- Use `Array.from` instead of spread `...` for mapping over iterables, because it avoids creating an intermediate array.

```js
// bad
const baz = [...foo].map(bar);

// good
const baz = Array.from(foo, bar);
```

- Use return statements in array method callbacks. It’s ok to omit the return if the function body consists of a single statement returning an expression without side effects, eslint: `array-callback-return`

```js
// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => x + 1);

// bad - no returned value means `acc` becomes undefined after the first iteration
[[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
  const flatten = acc.concat(item);
});

// good
[[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
  const flatten = acc.concat(item);
  return flatten;
});

// bad
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  } else {
    return false;
  }
});

// good
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  }

  return false;
});
```

### Destructuring

- Use object destructuring when accessing and using multiple properties of an object. eslint: `prefer-destructuring`

> Destructuring saves you from creating temporary references for those properties, and from repetitive access of the object. Repeating object access creates more repetitive code, requires more reading, and creates more opportunities for mistakes. Destructuring objects also provides a single site of definition of the object structure that is used in the block, rather than requiring reading the entire block to determine what is used.

```js
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}

// good
function getFullName(user) {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
}

// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```

- Use array destructuring. eslint: `prefer-destructuring`

```js
const arr = [1, 2, 3, 4];

// bad
const first = arr[0];
const second = arr[1];

// good
const [first, second] = arr;
```

- Use object destructuring for multiple return values, not array destructuring.

> You can add new properties over time or change the order of things without breaking call sites.

```js
// bad
function processInput(input) {
  // then a miracle occurs
  return [left, right, top, bottom];
}

// the caller needs to think about the order of return data
const [left, __, top] = processInput(input);

// good
function processInput(input) {
  // then a miracle occurs
  return { left, right, top, bottom };
}

// the caller selects only the data they need
const { left, top } = processInput(input);
```

## Strings
