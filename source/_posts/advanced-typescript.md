---
title: Typescript 进阶
tags:
- Typescript
- JavaScript
---

### 泛型

不用泛型的话，这个函数可能是下面这样

```javascript
function identity(arg: number): number {
  return arg;
}
```

或者，我们使用any类型来定义函数：

```javascript
function identity(arg: any): any {
  return arg;
}
```

使用**any**类型会导致这个函数可以接收任何类型的**arg**参数，这样就丢失了一些信息：传入的类型与返回的类型应该是相同的。 如果我们传入一个数字，我们只知道任何类型的值都有可能被返回。

因此，我们需要一种方法使返回值的类型与传入参数的类型是相同的。 这里，我们使用了类型变量，它是一种特殊的变量，只用于表示类型而不是值。

```javascript
function identity<T>(arg: T): T {
  return arg;
}
```

我们给**identity**函数添加了类型变量**T**，此时的**T**就犹如**JavaScript**里的函数形参。它的存在是帮助我们捕获用户传入的类型，比如**number**，之后我们再次使用了**T**当做返回值类型。现在我们可以知道参数类型与返回值类型是相同的了。

我们把这个版本的**identity**称之为泛型，对比前面两版的代码更加灵活及准确。

#### 泛型约束


我们还可以定义一个接口来描述约束条件。 创建一个包含**name**的**interface**


```javascript
  interface IName {
    name: string;
  }

  function test<T extends IName>(arg: T): T {
      console.log(arg.name);  // Now we know it has a .length property, so no more error
      return arg;
  }
```

现在这个泛型函数被定义了约束，因此它不再是适用于任意类型：

```javascript
  test(3);  // Error, number doesn't have a .name property

  // 我们需要传入符合约束类型的值，必须包含必须的属性：
  test({name: "jack"});
```

#### 在泛型约束中使用类型参数

比如，现在我们想要用属性名从对象里获取这个属性。 并且我们想要确保这个属性存在于对象obj上，因此我们需要在这两个类型之间使用约束。

```javascript
  function getProperty<T, K extends keyof T>(obj: T, key: K) {
    return obj[key];
  }

  let x = { a: 1, b: 2, c: 3, d: 4 };

  getProperty(x, "a"); // okay
  getProperty(x, "m"); // error: Argument of type 'm' isn't assignable to 'a' | 'b' | 'c' | 'd'.
```

### 内置工具类型

#### Required & Partial & Pick

```javascript
  type Partial<T> = {
    [P in keyof T]?: T[P];
  };

  type Required<T> = {
    [P in keyof T]-?: T[P];
  };

  type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
  };

  interface User {
    id: number;
    age: number;
    name?: string;
  };

  // 相当于: type RequiredUser = { id: number; age: number; name: string; }
  type RequiredUser = Required<User>;

  // 相当于: type PartialUser = { id?: number; age?: number; name?: string; }
  type PartialUser = Partial<User>

  // 相当于: type PickUser = { id: number; age: number; }
  type PickUser = Pick<User, "id" | "age">

```

#### Record & Dictionary & Many

**Record**
组成了一组类型为Type的属性的Keys类型，该内置类型可以将一个类型属性映射为另一个属性。

```javascript
type Record<K extends keyof any, T> = {
    [P in K]: T;
  };

interface PageInfo {
  title: string;
}

type Page = "home" | "about" | "contact";

const nav: Record<Page, PageInfo> = {
  about: { title: "about" },
  contact: { title: "contact" },
  home: { title: "home" },
};

// ^ = const nav: Record
```

```javascript
  type Record<K extends keyof any, T> = {
    [P in K]: T;
  };

  interface Dictionary<T> {
    [index: string]: T;
  };

  interface NumericDictionary<T> {
    [index: number]: T;
  };

  const data:Dictionary<number> = {
    a: 3,
    b: 4
  }
```