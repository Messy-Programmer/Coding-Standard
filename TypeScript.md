## Messy Programmer TypeScript coding Guidelines

These are the TypeScript coding guidelines that we expect all Messy Programmer developer to follow.

## Names

1. Use PascalCase for type names.
2. Do **not** use `I` as a prefix for interface names.
3. Use PascalCase for enum values.
4. Use camelCase for function names.
5. Use camelCase for property names and local variables.
6. Use `_` as a prefix for private properties.
7. Use whole words in names when possible. Only use abbreviations where their use is common and obvious.
8. We use "Id", "3d", "2d" rather than capital D.

## Files

1. Use the **.ts** file extension for TypeScript files
2. Use the .tsx file extension for React files

## Types

1. Do not export types/functions unless you need to share it across multiple components.
2. Do not introduce new types/values to the global namespace.
3. Within a file, type definitions should come first.

## Do not use `null`

* Use `undefined`. Do not use `null` except where external libraries require it.

## `===` and `!==` Operators

* Use `===` and `!==` operators whenever possible.
* The `==` and `!=` operators do type coercion, which is both inefficient and can lead to unexpected behavior.

## Strings

* Use double quotes for strings

## General Constructs

1. Always use semicolons. JavaScript does not require a semicolon when it thinks it can safely infer its existence. Not using a semicolon is confusing and error prone.
2. Use curly braces `{}` instead of `new Object()`.
3. Use brackets `[]` instead of `new Array()`.

## Make judicious use of vertical screen space

Programmer monitors are almost always wider than they are tall. It is common for widths to be at least 120 columns but heights to be less than 100. Therefore to make the greatest use of screen real estate, it is desireable to preserve vertical screen space wherever possible.

On the other hand, vertical whitespace can contribute significantly to code readability by making the logical structure clearer. The following guidelines are intended to strike a balance between readability and code density.

1. Some codebases advocate breaking lines at 80 columns. With current screen sizes, this is silly and wasteful. Don't break lines before 120 columns.
2. Don't use blank lines unnecessarily. For example the first line of a function *not* should be a blank line.
3. There should never be more than one blank line in a row.
4. **Don't use** clever/pretty multi-line comment blocks to separate sections of code. One line suffices, if you absolutely feel the need to include them. Usually they aren't necessary. Your well written, accurate and complete documentation and logical source organization is all the help anyone needs to understand your code.
5. Don't put each import in an import statement on a separate line.
6. If a function has only a single statement, it should **not** be on one line. Many debuggers refuse to allow breakpoints to be set on single-line functions.

```typescript
// No, cannot set breakpoint !!!
public middle(): number { return this.minimum + ((this.maximum - this.minimum) / 2.0); }
```

```typescript
// Correct, breakpoint may be set on body of function !!!
public middle(): number {
    return this.minimum + ((this.maximum - this.minimum) / 2.0);
}
```

7. The body of an `if` statement or a loop should be on a separate line, even if the body contains only a single line of code.

```typescript
// No (body on same line as conditional, cannot set breakpoint) !!!
if (meow) return "cat";
```

```typescript
// Correct (body on separate line from conditional) !!!
if (meow){
  return "cat";
}
```

8. A closing curly brace should be followed by a blank line.

```typescript
// No (missing blank line after closing brace) !!!
if (minimum > maximum) {
  const temp = minimum;
  minimum = maximum;
  maximum = temp;
}
return maximum - minimum;
```

```typescript
// Correct (blank line after closing brace) !!!
if (minimum > maximum) {
  const temp = minimum;
  minimum = maximum;
  maximum = temp;
}

return maximum - minimum;
```

9. Use curly braces On single-line code blocks...

```typescript
// No
if (meow)
  return "cat";

```

```typescript
// Correct (use curly braces) !!!
if (meow) {
  return "cat";
}

```

## Style

1. Use arrow functions over anonymous function expressions.
2. Open curly braces always go on the same line as whatever necessitates them.
3. **Never** use `var`. Instead use `const` where possible and otherwise use `let`.
4. Use a single declaration per variable statement (i.e. use `let x = 1; let y = 2;` over `let x = 1, y = 2;`).
5. Parenthesized constructs should have no surrounding whitespace. A single space follows commas, colons, semicolons, and operators in those constructs. For example:
   1. `for (let i = 0, n = str.length; i < 10; ++i) { }`
   2. `if (x < 10) { }`
   3. `public calculate(x: number, y: string): void { . . . }`
6. Use 2 spaces per indentation. Do not use tabs!
7. Turn on `eslint` in your editor to see violations of these rules immediately.

## Return

f a return statement has a value you should not use parenthesis () around the value.

```typescript
return ("Hello World!"); // bad

return "Hello World!"; // good
```

Certain schools of programming advice hold that every method should have only one return statement. This could not be more misguided. *Always* return as soon as you know there's *no reason to proceed* .

```typescript
// bad!!
public getFirstUser(): Person | undefined {
  let firstUser?: Person;
  if (this.hasUsers()) {
    if (!this.usersValidated()) {
      if (this.validateUsers())
        firstUser = this.getUser(0);
    } else {
        firstUser = this.getUser(0);
    }
  }
  return firstUser;
}

// ok!!
public getFirstUser(): Person | undefined {
  if (!this.hasUsers())
    return undefined;

  if (!this.usersValidated() && !this.validateUsers())
      return undefined;

  return return this.getUser(0);
}

// best!!! For a simple case like this that is deciding between 2 return values
public getFirstUser(): Person | undefined {
  const userInvalid = !this.hasUsers() || (!this.usersValidated() && !this.validateUsers());
  return userInvalid ? undefined : this.getUser(0);
}
```

Always explicitly define a return type for methods that are more than one line. This can help TypeScript validate that you are always returning something that matches the correct type.

```typescript
// bad!! No return type specified
public getOwner(name: string) {
  if (this.isReady)
    return this.widget.getOwner(); // is this a Person???
  ...
  return new Person(name);
}

// good!!
public getOwner(name: string): Person {
  if (this.isReady)
    return this.widget.getOwner(); // if this is not a Person, compile error
  ...
  return new Person(name);
}
```

for methods that are one line and for which the return type is obvious, it is not necessary to include the return type:

```typescript
// fine, return type is obvious
public getCorner() {
  return new Point3d(this.x, this.y);
}
```

When calling methods, the best practice would be to explicitly specify the return type. In the case of async methods calls, these calls almost always involve awaiting the results, and this practice guards against omitting the await keyword - a frequent cause of hard to debug race conditions.

```typescript
// bad!! no return type specified for async call, and missing await is not caught by the compiler
const iModels = iModelHub.getIModels(projectId);

// good!! omitting the await would be caught by the compiler as a type mismatch
const iModel: IModel[] = await iModelHub.getIModels(projectId);
```

## Getter and Setters

A common pattern is to have a `private` member that is read/write privately within the class, but read-only to the public API. This can be a good use for a getter. For example:

```typescript
class Person {
  private _name: string;
  public get name(): string { return _name; } // read-only to public API, so no setter provided
}
```

Note, however, if the value supplied by the getter is established in the constructor and *can never be changed* , the following may be preferable:

```typescript
class Person {
   constructor(public readonly name: string) { }
}
```

Another valid use of getters and setters is when you want to give the appearance of having a public member but you don't actually store the data that way. For example:

```typescript
class PersonName {
  constructor (public firstName: string, public lastName: string) { }
  public get fullName(): string { return this.firstName + " " + this.lastName; }
  public set fullName(name: string): void {
    const names: string[] = name.split(" ");
    this.firstName = names[0] || "";
    this.lastName = names[1] || "";
  }
}
```

It is also good to use getters and setters if data validation is required (which isn't possible in the case of a direct assignment to a public member).

There are cases where getters and setters would be overkill. For example:

```typescript
// This is fine!
class Corner {
  constructor(public x: number, public y: number, public z: number) { }
}
```

## use "?:" syntax vs. " | undefined"

When declaring member variables or function arguments, use the TypeScript "?:" syntax vs. adding " | undefined" for variables that can be undefined. For example

```typescript
class Role {
  public name: string;
  public description: string | undefined; // Wrong !!
}
```

```typescript
class Role {
  public name: string;
  public description?: string; // Correct !!
}
```

## Prefer getters where possible

If a public method takes no parameters and its name begins with a keyword such as "is", "has", or "want", the method should be a getter (specified by using the "get" modifier in front of the method name). This way the method is accessed as a property rather than as a function. This avoids confusion over whether it is necessary to include parenthesis to access the value, and the caller will get a compile error if they are included. This rule is enforced by ESLint.

If the value being returned is expensive to compute, consider using a different name to reflect this. Possible prefixes are "compute" or "calculate", etc.

A getter should have no side effects, with the possible exception of a getter that computes and caches its value the first time it is called. In particular, avoid getters that return a different value each time they are called, as some code optimizers [may assume](https://github.com/angular/angular-cli/issues/12128#issuecomment-472309593) a property access always returns the same value.

## Don't export const enums

Exported `const enum`s require a .d.ts file to be present when a file that consumes one is transpiled. This prevents the [--isolatedModules](https://www.typescriptlang.org/docs/handbook/compiler-options.html) option required by [create-react-app](https://reactjs.org/docs/create-a-new-react-app.html) and are therefore forbidden. An ESLint rule enforces this.

> Note: `const enum`s are slightly more efficient, so there may be reasons to use them in non-exported code. The ESLint rule must be disabled with `// eslint-disable-line no-restricted-syntax` to allow them.

## Don't repeat type names unnecessarily

TypeScript is all about adding types to JavaScript. However, the compiler automatically infers type by context, and it is therefore not necessary to decorate every member or variable declaration with its type, if it is obvious. That only adds clutter and obscures the real code. For example,

```typescript
  let width: number = 7.3; // useless type declaration
  public isReady: boolean = false; // useless type declaration
  public readonly origin: Point3d = new Point3d(); // useless type declaration

  let width = 7.3; // correct
  public isReady = false; // correct
  public readonly origin = new Point3d(); // correct
  const upVector: Vector3d = rMatrix.getRow(1); // good, helps readability. Not strictly necessary.
```

However, as stated above, it is a good idea to always include the return type of a function if it is more than one line, to make sure no return path has an unexpected type.

## Error Handling

For public-facing APIs we have decided to prefer exceptions (`throw new Error`) and rejecting promises (`Promise.reject`) over returning status codes. The reasons include:

1. Exceptions can keep your code clean of *if error status then return* clutter. For example, a series of API calls could each be affected by network outages but the error handling would be the same regardless of which call failed.
2. Exceptions let you return the natural return value of success rather than an unnatural composite object.
3. Exceptions can carry more information than a status return.
4. Status returns can be ignored, but exceptions can't. If the immediate layer does not handle the exception it will be bubbled up to the outer layer.
5. The optional `message` property of an `Error` should (if defined) hold an English debugging message that is not meant to be localized. Instead, applications should catch errors and then separately provide a context-appropriate localized message.

> Note: Returning `SomeType` and throwing an `Error` is generally preferred over returning `SomeType | undefined`.

## Asynchronous Programming

1. Use `Promise`
2. Use `return Promise.reject(new MyError())` rather than resolving to an error status. The object passed into `Promise.reject` should be a subclass of `Error`. It is easy to forget the `return` so be careful.
3. Prefer `async`/`await` over `.then()` constructs

## Reference Documentation Comments

We have standardized on [TypeDoc](http://typedoc.org/guides/doccomments/) for generating reference documentation.

TypeDoc runs the TypeScript compiler and extracts type information from the generated compiler symbols. Therefore, TypeScript-specific elements like classes, enumerations, property types, and access modifiers will be automatically detected. All comments are parsed as markdown. Additionally, you can link to other classes, members, or functions using double square brackets.

The following JavaDoc tags are supported by TypeDoc:

* `@param`
  * The parameter name and type are automatically propagated into the generated documentation, so `@param` should only be included when more of a description is necessary.
  * Use plain `@param`. Do not use `@param[in]` or `@param[out]` as this confuses TypeDoc.
  * The parameter description should start with a capital letter.
* `@returns`
  * The return type is automatically propagated into the generated documentation, so `@returns` should only be included when more of a description is necessary.
  * The `@returns` description (when provided) should start with *Returns* for readability within the generated documentation.
  * The `@return` JavaDoc tag is also supported, but `@returns` is preferred for readability and consistency with `@throws`.
