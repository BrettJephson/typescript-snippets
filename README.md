# TypeScript Snippets
Some snippets of TypeScript that might occasionally prove useful:

## Type the keys of an object

Create a type from the keys of an object.

```typescript
type KeysOfAnObject = Readonly<keyof typeof anObject>
```

### Usage

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

## Keys of a type

Given an object and a type of value (T), this type will include object keys only where the corresponding value is T.

```typescript
type KeysOfType<T, V> = ({[P in keyof T]: T[P] extends V ? P : never })[keyof T];
type KeyOfStringValue<T> = KeysOfType<T, string>;
```

### Usage

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

## Numbers in a range

I take no credit for this one but came across this [Playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAQgrgSwDYBMAKB7A7hATgZwHkAzAJgBkIA7Ac2AAsBBXXAQxHwB4A5KCAD2DUU+KFTgBbAEZ4ANFABKfQcNFUIANzwBtALp6AfFAC8AWABQURdoAM+7ruVCqIsZrxQA-IqgAuWIiomDgEJBTUdEws7Fzc8traAHTJCra68smJqXbpUJkKugYA3BYWoJBQAMIYVADGrMDkrLg0EPjAAKpUwMgAIjUQPE6qYpIyuPJKAs6u6lq4enryMMMuau4LhSYWVjDaAERIEQz7jtMjvN4r-kkpaRnJMPZnKmtu8ztWXlU19Y3NrXaXR6SH66h4k1Wrm02VymQQVGIHg6jm8HSh63mi1RUHR-jmHnxG2WBk+Vn81TqDSaLTanW6fQGEJ85zeMPueWSCKRuFxOPRrNmG2x3zx70J4omUFuWQ5mSexVK5nK0AUEDASFYtUGU1erlYVBASygABUjMYoABvaUAaSgCKgAGsICAMMRFLp-CaoABfJUq01wDUQEicE3yXiCtRjPDm0bSDxRqCXU16PxWz7aO0Ohz+T7wZDobB4IhkSi0BjMNgcTg2+LaAm4XRbJPc5GijGS7GfbxqjVawaUv40wH0kFgwZ13HxQryM3pxsLjYlcw+7QOf3gVUG1qhyN66MJ3BxtDNHqsJBhoNHUNwKiOqjYKgRgwGA5HCv0U4rspbxQ7kMyE4AAxXAMAkTtxCPOcMEgmNjxMKAAFF+FqJA4BQHUANDE0MAMSZsOIECwIkV8oAAHygUDwJ-ZU-wADUQhRCNITgAGZZAAVlJcwLCAA)

```typescript
type BuildPowersOf2LengthArrays<N extends number, R extends never[][]> = R[0][N] extends never ? R : BuildPowersOf2LengthArrays<N, [[...R[0], ...R[0]], ...R>;

type ConcatLargestUntilDone<N extends number, R extends never[][], B extends never[]> = 
  B["length"] extends N ? B : [...R[0], ...B][N] extends never
  ? ConcatLargestUntilDone<N, R extends [R[0], ...infer U] ? U extends never[][] ? U : never : never, B>
  : ConcatLargestUntilDone<N, R extends [R[0], ...infer U] ? U extends never[][] ? U : never : never, [...R[0], ...B]>;
  
type Replace<R extends any[], T> = { [K in keyof R]: T }

type TupleOf<T, N extends number> = number extends N ? T[] : {
  [K in N]:
  BuildPowersOf2LengthArrays<K, [[never]]> extends infer U ? U extends never[][]
  ? Replace<ConcatLargestUntilDone<K, U, []>, T : never : never;
}[N]

type RangeOf<N extends number> = Partial<TupleOf<unknown, N>>["length"];

type Range<From extends number, To extends number> = Exclude<RangeOf<To>, RangeOf<From>> | From;
```

### Usage

```typescript
export type DaysOfDecember = Range<1, 31>;

const outOfRange: DaysOfDecember = 50; // ts error as value is out of range
const inRange: DaysOfDecember = 10; // ok as value is between 1 and 31
```
