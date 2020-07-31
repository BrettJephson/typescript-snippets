# TypeScript Snippets
Some snippets of TypeScript that might occasionally prove useful:

## Keys of a type:

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

[On Codesandbox](https://codesandbox.io/s/m9ecl)
