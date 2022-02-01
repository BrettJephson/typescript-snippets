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

## Either / Or

Allow an object to have either one set of fields or another.

```typescript
type Only<T, U> = {
    [P in keyof T]: T[P];
} & {
    [P in keyof U]?: never;
};

type Either<T, U> = Only<T, U> | Only<U, T>;
```

### Usage

```typescript
interface Label {
    label: string;
}

interface LabelledBy {
    labelledBy: string;
}

type LabelOptions =  Either<Label, LabelledBy>;

const label: LabelOptions = { label: 'test' };
const labelledBy: LabelOptions = { labelledBy: 'test' };
const fail: LabelOptions = { label: 'test', labelledBy: 'test' }; // this one fails because it can't be both a Label and LabelledBy
```

[Example on TypeScript Playground](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgDJwEYQDbIN4BQyxy2mOAXMgM5hSgDmA3AQL4EGiSyIrpbZsEACYAhAJ74iJMgKFjxVWvRDM2HMOIAOKAPIhs4gDwAVADTIAqgD5kAXiklkAbQAKyUMgDWEcQHsYZBMAXSoTN2CWVmQAMkcSNw8Qb18Aq2CAfioQCAA3aCiWAk0dZABRYDAAC2hTCxt7ZH1DOqtbAB8mg2NLCxNrIpK+cmxdLTBgPxBqRvLKmqgjfhwLZcERCQGOBCnaUhGqNbGJ3ca8fYEqAHJIWivkVhYd6bALnHkJQ5Hjyemzt-WCmutzA90eBGee3gwGwXwEP1ODnOskoyBuEDuFhRgM+aJBYKYQA)

## Prefix

Map an object's keys to another object where the keys are prefixed. Using template literal.

```typescript
type Prefixed<T, Prefix extends string> = { [K in keyof T & string as `${Prefix}.${K}`]?: T[P]; }
```

### Usage

```typescript
const currentState = {
  id: 0,
  label: "test"
};

const newState: Prefixed<typeof currentState, 'update'> = {
  "update.id": 1,
  "update.label": "pass"
};

const failingState: Prefixed<typeof currentState, 'update'> = {
  "error.label": "fail"
};
```

[Example on TypeScript Playground](https://www.typescriptlang.org/play?ssl=4&ssc=90&pln=4&pc=88#code/FAYw9gdgzgLgBGARgKwIJwLxwN5ymAWwFMYALASwgHMAuOAIgENER6AaORqougRgAY4AXwDcwYDACeAByJwACgCciAM3IAPIgBMAPABUOS1RrhF1MIhC1Q8MRZSoA+TDjgBtANJxKcANZFJMBU4PTgAMlt7ak4bAAMAEmwjNXUhADpEjyFYgF0AagB+Oj1PHJFhcXBoeCRkACE6ZI1tHSlZIIQUVA56ZWkAG0YQInpnLGxgOCmGPsHhtPxiMgd6OnoIIgB3OAA3Rn6AVxG2SeneogGhojSuEboAJgBWYFFTuCA)

## Infer Prefix

Infer the prefix from an object's keys and a set of values.

```typescript
type InferPrefix<T, K extends string & keyof T, Suffix extends string> = K extends `${infer Prefix}${Suffix}` ? Prefix : never;
```

### Usage

```typescript
const obj = {
 something100: "Test 1",
 something200: "Test 2",
 another100: "Test 3"
};

type Obj = typeof obj;
type ObjKeys = keyof Obj;

const pass: InferPrefix<Obj, ObjKeys, "100" | "200"> = "something";
const fail: InferPrefix<Obj, ObjKeys, "100" | "200"> = "nothing"; // fails because prefix inferred as 'something' | 'another';
```

[Example on TypeScript Playground](https://www.typescriptlang.org/vo/play?#code/C4TwDgpgBAwgFhAxgawAoCcIDMCWAPAHgBUAaKAaSgj2AgDsATAZyieHRzoHMoAyKZBBAB7LFFJQAygFcsuPFRr1mrdpy4A+KAF4Ki2oxYADACQBvTlgjooGbPgC+5mXMdGoAfluZ5UAFxQdBAAbtYA3ABQEYjCdGxQwgBGAFY6UGYRrMIAthDAcOoAjAAMxQEAREQQ8YXlJJlMOXkF3ABMpRVV8a3lEQ6REaCQUADyKWlDEKIJKZGToynkQiy6giJiY8kDMXHAUGAAhkxMAfBIaD74BJtkm0sgTGTlJcXlUAA+UOXtr1q65Y1cvl1OVIjt4lgDjgADanBAoOzya4pW6LZZPF5vT7fUrlP5fOjCYHcUFAA)

## Validate Template Literal (recursion)

A bit of recursion goes a long way in TypeScript types. It doesn't have to be too unreadable either. 

```typescript
type ValidateNext<RestOfString extends string, Valid extends string, Subject extends string> = 
    RestOfString extends Validate<RestOfString, Valid> ? Subject : never;

type ValidateChar<Subject extends string, Valid extends string> = 
    Subject extends `${Valid}-${infer RestOfString}` ? ValidateNext<RestOfString, Valid, Subject> : never;
  
type Validate<Subject extends string, Valid extends string> = 
  Subject extends Valid ? Subject : ValidateChar<Subject, Valid>;
```

### Usage

```typescript

type ValidLetters = "a" | "b" | "c";

const validator = <Subject extends string>(subject: Validate<Subject, ValidLetters>) => subject;

const pass = validator("b-a-a-b-c-c-a");
const fail = validator("b-A-D");
```

[Validate Letters example on Playground](https://www.typescriptlang.org/play?ssl=10&ssc=82&pln=3&pc=1#code/C4TwDgpgBAaghgGwJYBMAyFjAgJwM5QC8UARHCVAD6kBGF1JAxiQFAuiSyKpzYByEAB7AAPACUIeYAHkAZgGVgOJADsA5lCHYVKAlOXqANF2QoAktgC2AFXDQtEHXqWq1x+QFcaAKwiNgmsKOulD6rgB8RFAsULFQElJyigYaDk4mPNjikjIKLkYZ5la2kJEA-FCePn4BAFxQKhAAbrgA3Gwc0PCmvBAAwgAWcDgiVb7+gdohYQXdqBYQNnaTwc4pkcQxcWM1K+kABgAkAN5zRYslEAC+ALQnqrK48TlJ+WpX+1AVZ70CwtmJPIpYxnBZLSDuLzjYCReqNFo4dqxdjLH5ZHYTNLTN4g7jncH2ILpGZqDbRWIYgJYgig4rLCqUqD1NH9IYjSm40xgy7hdosRgAexUUigTTxvAFOCioyhu2poTe4QAFHhZf5meL0WrgJzUBgsLg8OEAJRESKq6r+PmC4UBMBwPAEYhinrASVKsg3Gg3Rg3cjG9o2kX2x1oIVqJ7OzXuz3e31wL0+v2J+Mp5PehOphPZ5M5ugBthBgKyOBIBBRF2ZGMZm4oEgBoA)

