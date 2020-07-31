# TypeScript Snippets
Some snippets of TypeScript that might occasionally prove useful:

## Type the keys of an object:

Create a type from the keys of an object.

```typescript
type KeysOfAnObject = Readonly<keyof typeof anObject>
```

### Usage: 

```typescript
const anObject = {
  id: number;
  name: string;
  description: string;
};

type KeysOfAnObject = Readonly<keyof typeof anObject>;

const test: KeysOfAnObject = 'id';
const testFails: KeysOfAnObject = 'type';

console.log(test, testFails);
```

[Example on Codesandbox](https://codesandbox.io/s/exciting-night-lgp52)

## Keys of a type:

Given an object and a type of value (T), this type will include object keys only where the corresponding value is T.

```typescript
type KeysOfType<T, V> = ({[P in keyof T]: T[P] extends V ? P : never })[keyof T];
type KeyOfStringValue<T> = KeysOfType<T, string>;
```

### Usage:

```typescript
interface Item {
  id: number;
  name: string;
  description: string;
  isNew: boolean;
}

const item: Item = {
  id: 1,
  name: "Some item 123",
  description: "A thing that is useful for something",
  isNew: true
};

function doSomethingWithProperty<T>(obj: T, key: KeyOfStringValue<T>) {
  console.log(obj[key]);
}

doSomethingWithProperty(item, 'id'); // ts error as property value is a number
doSomethingWithProperty(item, 'name'); // ok as property value is a string
```

[Example on Codesandbox](https://codesandbox.io/s/m9ecl)
