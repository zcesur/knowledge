---
Title: TypeScript
---
## Type System

### Any

The `any` type is a powerful way to work with existing JavaScript, allowing you to gradually opt-in and opt-out of type checking during compilation.
You might expect `Object` to play a similar role, as it does in other languages.
However, variables of type `Object` only allow you to assign any value to them. You can’t call arbitrary methods on them, even ones that actually exist:

```language-ts
let notSure: any = 4;
notSure.ifItExists(); // okay, ifItExists might exist at runtime
notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)

let prettySure: Object = 4;
prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.
```

> Note: Avoid using `Object` in favor of the non-primitive `object` type as described in our [Do’s and Don’ts](https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html#general-types) section.

The `any` type is also handy if you know some part of the type, but perhaps not all of it.
For example, you may have an array but the array has a mix of different types:

```language-ts
let list: any[] = [1, true, "free"];

list[1] = 100;
```

### Null and Undefined

By default `null` and `undefined` are subtypes of all other types.
That means you can assign `null` and `undefined` to something like `number`.

However, when using the `--strictNullChecks` flag, `null` and `undefined` are only assignable to `any` and their respective types (the one exception being that `undefined` is also assignable to `void`).
This helps avoid _many_ common errors.
In cases where you want to pass in either a `string` or `null` or `undefined`, you can use the union type `string | null | undefined`.

### Intersection Types

An intersection type combines multiple types into one.
This allows you to add together existing types to get a single type that has all the features you need.
For example, `Person & Serializable & Loggable` is a `Person` _and_ `Serializable` _and_ `Loggable`.
That means an object of this type will have all members of all three types.

### Union Types

A union type describes a value that can be one of several types.
We use the vertical bar (`|`) to separate each type, so `number | string | boolean` is the type of a value that can be a `number`, a `string`, or a `boolean`.

If we have a value that has a union type, we can only access members that are common to all types in the union.

```language-ts
interface Bird {
    fly();
    layEggs();
}

interface Fish {
    swim();
    layEggs();
}

function getSmallPet(): Fish | Bird {
    // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim();    // errors
```

### Interfaces

In TypeScript, two types are compatible if their internal structure is compatible.
This allows us to implement an interface just by having the shape the interface requires, without an explicit `implements` clause.

```language-ts
interface Person {
    firstName: string;
    lastName: string;
}

function greeter(person: Person) {
    return "Hello, " + person.firstName + " " + person.lastName;
}

let user = { firstName: "Jane", lastName: "User" };

document.body.textContent = greeter(user);
```

### Type Aliases

Type aliases create a new name for a type.
Type aliases are sometimes similar to interfaces, but can name primitives, unions, tuples, and any other types that you’d otherwise have to write by hand.

```language-ts
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === "string") {
        return n;
    }
    else {
        return n();
    }
}
```

Aliasing doesn’t actually create a new type - it creates a new _name_ to refer to that type.
Aliasing a primitive is not terribly useful, though it can be used as a form of documentation.

Just like interfaces, type aliases can also be generic - we can just add type parameters and use them on the right side of the alias declaration:

```language-ts
type Container<T> = { value: T };
```

We can also have a type alias refer to itself in a property:

```language-ts
type Tree<T> = {
    value: T;
    left: Tree<T>;
    right: Tree<T>;
}
```

Together with intersection types, we can make some pretty mind-bending types:

```language-ts
type LinkedList<T> = T & { next: LinkedList<T> };

interface Person {
    name: string;
}

var people: LinkedList<Person>;
var s = people.name;
var s = people.next.name;
var s = people.next.next.name;
var s = people.next.next.next.name;
```

#### Interfaces vs. Type Aliases

As we mentioned, type aliases can act sort of like interfaces; however, there are some subtle differences.

One difference is that interfaces create a new name that is used everywhere.
Type aliases don’t create a new name — for instance, error messages won’t use the alias name.
In the code below, hovering over `interfaced` in an editor will show that it returns an `Interface`, but will show that `aliased` returns object literal type.

```language-ts
type Alias = { num: number }
interface Interface {
    num: number;
}
declare function aliased(arg: Alias): Alias;
declare function interfaced(arg: Interface): Interface;
```

In older versions of TypeScript, type aliases couldn’t be extended or implemented from (nor could they extend/implement other types). As of version 2.7, type aliases can be extended by creating a new intersection type e.g. `type Cat = Animal & { purrs: true }`.

Because [an ideal property of software is being open to extension](https://en.wikipedia.org/wiki/Open/closed_principle), you should always use an interface over a type alias if possible.

On the other hand, if you can’t express some shape with an interface and you need to use a union or tuple type, type aliases are usually the way to go.

### Enum

Enums allow us to define a set of named constants. Using enums can make it easier to document intent, or create a set of distinct cases. TypeScript provides both numeric and string-based enums.

#### Computed and constant members

Each enum member has a value associated with it which can be either _constant_ or _computed_

```language-ts
enum FileAccess {
    // constant members
    None,
    Read    = 1 << 1,
    Write   = 1 << 2,
    ReadWrite  = Read | Write,
    // computed member
    G = "123".length
}
```

#### Union enums and enum member types

There is a special subset of constant enum members that aren’t calculated: literal enum members.
A literal enum member is a constant enum member with no initialized value, or with values that are initialized to

* any string literal (e.g. `"foo"`, `"bar`, `"baz"`)
* any numeric literal (e.g. `1`, `100`)
* a unary minus applied to any numeric literal (e.g. `-1`, `-100`)

When all members in an enum have literal enum values, some special semantics come to play.

The first is that enum members also become types as well!
For example, we can say that certain members can _only_ have the value of an enum member:

```language-ts
enum ShapeKind {
    Circle,
    Square,
}

interface Circle {
    kind: ShapeKind.Circle;
    radius: number;
}

interface Square {
    kind: ShapeKind.Square;
    sideLength: number;
}

let c: Circle = {
    kind: ShapeKind.Square, // Error! Type 'ShapeKind.Square' is not assignable to type 'ShapeKind.Circle'.
    radius: 100,
}
```

The other change is that enum types themselves effectively become a _union_ of each enum member.

### Singleton Types

Much of the time when we talk about “singleton types”, we’re referring to both enum member types as well as numeric/string literal types, though many users will use “singleton types” and “literal types” interchangeably.

#### String Literal Types

```language-ts
type Easing = "ease-in" | "ease-out" | "ease-in-out";
```

#### Numeric Literal Types

```language-ts
function rollDice(): 1 | 2 | 3 | 4 | 5 | 6 {
    // ...
}
```

#### Enum Member Types

As mentioned in [our section on enums](https://www.typescriptlang.org/docs/handbook/enums.html#union-enums-and-enum-member-types), enum members have types when every member is literal-initialized.

### Discriminated Unions

You can combine singleton types, union types, type guards, and type aliases to build an advanced pattern called _discriminated unions_, also known as _tagged unions_ or _algebraic data types_.
Discriminated unions are useful in functional programming.
Some languages automatically discriminate unions for you; TypeScript instead builds on JavaScript patterns as they exist today.
There are three ingredients:

1.  Types that have a common, singleton type property — the _discriminant_.
2.  A type alias that takes the union of those types — the _union_.
3.  Type guards on the common property.

```language-ts
interface Square {
    kind: "square";
    size: number;
}
interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}
interface Circle {
    kind: "circle";
    radius: number;
}
```

First we declare the interfaces we will union.
Each interface has a `kind` property with a different string literal type.
The `kind` property is called the _discriminant_ or _tag_.
The other properties are specific to each interface.
Notice that the interfaces are currently unrelated.
Let’s put them into a union:

```language-ts
type Shape = Square | Rectangle | Circle;
```

Now let’s use the discriminated union:

```language-ts
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
    }
}
```

#### Exhaustiveness checking

We would like the compiler to tell us when we don’t cover all variants of the discriminated union.
For example, if we add `Triangle` to `Shape`, we need to update `area` as well:

```language-ts
type Shape = Square | Rectangle | Circle | Triangle;
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
    }
    // should error here - we didn't handle case "triangle"
}
```

There are two ways to do this.
The first is to turn on `--strictNullChecks` and specify a return type:

```language-ts
function area(s: Shape): number { // error: returns number | undefined
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
    }
}
```

Because the `switch` is no longer exhaustive, TypeScript is aware that the function could sometimes return `undefined`.
If you have an explicit return type `number`, then you will get an error that the return type is actually `number | undefined`.
However, this method is quite subtle and, besides, `--strictNullChecks` does not always work with old code.

The second method uses the `never` type that the compiler uses to check for exhaustiveness:

```language-ts
function assertNever(x: never): never {
    throw new Error("Unexpected object: " + x);
}
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
        default: return assertNever(s); // error here if there are missing cases
    }
}
```

Here, `assertNever` checks that `s` is of type `never` — the type that’s left after all other cases have been removed.
If you forget a case, then `s` will have a real type and you will get a type error.
This method requires you to define an extra function, but it’s much more obvious when you forget it.

## Miscellaneous

### Optional Chaining

At its core, optional chaining lets us write code where TypeScript can immediately stop running some expressions if we run into a `null` or `undefined`.
The star of the show in optional chaining is the new `?.` operator for _optional property accesses_.
When we write code like

```language-ts
let x = foo?.bar.baz();
```

this is a way of saying that when `foo` is defined, `foo.bar.baz()` will be computed; but when `foo` is `null` or `undefined`, stop what we’re doing and just return `undefined`.”

More plainly, that code snippet is the same as writing the following.

```language-ts
let x = (foo === null || foo === undefined) ?
    undefined :
    foo.bar.baz();
```

Note that if `bar` is `null` or `undefined`, our code will still hit an error accessing `baz`.
Likewise, if `baz` is `null` or `undefined`, we’ll hit an error at the call site.
`?.` only checks for whether the value on the _left_ of it is `null` or `undefined` - not any of the subsequent properties.

You might find yourself using `?.` to replace a lot of code that performs repetitive nullish checks using the `&&` operator.

```language-ts
// Before
if (foo && foo.bar && foo.bar.baz) {
    // ...
}

// After-ish
if (foo?.bar?.baz) {
    // ...
}
```

Keep in mind that `?.` acts differently than those `&&` operations since `&&` will act specially on “falsy” values (e.g. the empty string, `0`, `NaN`, and, well, `false`), but this is an intentional feature of the construct.
It doesn’t short-circuit on valid data like `0` or empty strings.

Optional chaining also includes two other operations.
First there’s the _optional element access_ which acts similarly to optional property accesses, but allows us to access non-identifier properties (e.g. arbitrary strings, numbers, and symbols):

```language-ts
/**
 * Get the first element of the array if we have an array.
 * Otherwise return undefined.
 */
function tryGetFirstElement<T>(arr?: T[]) {
    return arr?.[0];
    // equivalent to
    //   return (arr === null || arr === undefined) ?
    //       undefined :
    //       arr[0];
}
```

There’s also _optional call_, which allows us to conditionally call expressions if they’re not `null` or `undefined`.

```language-ts
async function makeRequest(url: string, log?: (msg: string) => void) {
    log?.(`Request started at ${new Date().toISOString()}`);
    // roughly equivalent to
    //   if (log != null) {
    //       log(`Request started at ${new Date().toISOString()}`);
    //   }

    const result = (await fetch(url)).json();

    log?.(`Request finished at at ${new Date().toISOString()}`);

    return result;
}
```

The “short-circuiting” behavior that optional chains have is limited property accesses, calls, element accesses - it doesn’t expand any further out from these expressions.
In other words,

```language-ts
let result = foo?.bar / someComputation()
```

doesn’t stop the division or `someComputation()` call from occurring.
It’s equivalent to

```language-ts
let temp = (foo === null || foo === undefined) ?
    undefined :
    foo.bar;

let result = temp / someComputation();
```

That might result in dividing `undefined`, which is why in `strictNullChecks`, the following is an error.

```language-ts
function barPercentage(foo?: { bar: number }) {
    return foo?.bar / 100;
    //     ~~~~~~~~
    // Error: Object is possibly undefined.
}
```

### Nullish Coalescing

You can think of this feature - the `??` operator - as a way to “fall back” to a default value when dealing with `null` or `undefined`.
When we write code like

```language-ts
let x = foo ?? bar();
```

this is a new way to say that the value `foo` will be used when it’s “present”;
but when it’s `null` or `undefined`, calculate `bar()` in its place.

Again, the above code is equivalent to the following.

```language-ts
let x = (foo !== null && foo !== undefined) ?
    foo :
    bar();
```

The `??` operator can replace uses of `||` when trying to use a default value.
For example, the following code snippet tries to fetch the volume that was last saved in [`localStorage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) (if it ever was);
however, it has a bug because it uses `||`.

```language-ts
function initializeAudio() {
    let volume = localStorage.volume || 0.5

    // ...
}
```

When `localStorage.volume` is set to `0`, the page will set the volume to `0.5` which is unintended.
`??` avoids some unintended behavior from `0`, `NaN` and `""` being treated as falsy values.
