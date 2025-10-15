# instanceof

## 概念
    1、instanceof 操作符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上。
    2、它会沿着对象的原型链向上查找，直到找到匹配的原型或到达原型链顶端。
    3、如果找到匹配的原型则返回 true，否则返回 false。
    4、原型链的查找会一直到 Object.prototype.__proto__ (null) 为止。

## instanceof 有哪些用法？

### 基础用法

#### 1. 检测数组类型
使用 instanceof 检测对象是否为数组类型。

```javascript
const arr = [1, 2, 3];
const obj = { name: 'test' };

console.log(arr instanceof Array);   // true
console.log(obj instanceof Array);   // false
console.log(arr instanceof Object);  // true (数组也是对象)
```

#### 2. 检测对象类型
使用 instanceof 检测对象是否为特定构造函数创建。

```javascript
const person = { name: '张三', age: 25 };
const date = new Date();
const regex = /test/;

console.log(person instanceof Object);  // true
console.log(date instanceof Date);      // true
console.log(regex instanceof RegExp);   // true
console.log(date instanceof Object);    // true (Date 继承自 Object)
```

### 自定义构造函数检测

#### 1. 基础构造函数检测
```javascript
function Person(name) {
    this.name = name;
}

const person = new Person('李四');

console.log(person instanceof Person);  // true
console.log(person instanceof Object);  // true
console.log(person instanceof Array);   // false
```

#### 2. 继承关系检测
```javascript
function Animal(name) {
    this.name = name;
}

function Dog(name, breed) {
    Animal.call(this, name);
    this.breed = breed;
}

// 设置继承关系
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

const dog = new Dog('旺财', '金毛');

console.log(dog instanceof Dog);     // true
console.log(dog instanceof Animal);  // true
console.log(dog instanceof Object);  // true
```

### 边界情况处理

#### 1. null 和 undefined 检测
```javascript
console.log(null instanceof Object);      // false
console.log(undefined instanceof Object); // false
```

#### 2. 基本类型检测
```javascript
console.log(123 instanceof Number);       // false (基本类型)
console.log("hello" instanceof String);   // false (基本类型)
console.log(true instanceof Boolean);     // false (基本类型)
```

#### 3. 包装对象检测
```javascript
const numObj = new Number(123);
const strObj = new String("hello");

console.log(numObj instanceof Number);    // true (包装对象)
console.log(strObj instanceof String);    // true (包装对象)
```

### 手动实现-instanceof

```javascript

// 手动实现 instanceof 步骤：
// 1. 判断传入的参数是否有效（对象不能为 null/undefined，构造函数必须为函数）
// 2. 获取构造函数的 prototype 属性
// 3. 获取对象的原型链（使用 Object.getPrototypeOf()）
// 4. 沿着原型链向上查找，比较每一层原型
// 5. 如果找到匹配返回 true，如果到达原型链顶端（null）返回 false

function myInstanceof(obj, constructor) {

    // 1、边界情况处理
    // 如果obj为null或undefined，返回false
    if (obj === null || obj === undefined) {
        return false;
    }
    
    // 如果constructor不是函数，抛出错误
    if (typeof constructor !== 'function') {
        throw new TypeError('Right-hand side of instanceof is not callable');
    }

    // 2、获取构造函数的 prototype
    const prototype = constructor.prototype;

    // 3、获取对象的原型链
    let currentProto = Object.getPrototypeOf(obj);

    // 4、沿着原型链向上查找
    while (currentProto !== null) {
        // 如果找到匹配的原型，返回 true
        if (currentProto === prototype) {
            return true;
        }
        // 继续向上查找
        currentProto = Object.getPrototypeOf(currentProto);
    }

    // 5、到达原型链顶端，返回 false
    return false;

}


// 测试用例
const arr = [1, 2, 3];
const obj = { name: 'test' };

// 基础测试
console.log('基础测试：');
console.log('arr instanceof Array:', myInstanceof(arr, Array));     // true
console.log('obj instanceof Array:', myInstanceof(obj, Array));      // false
console.log('arr instanceof Object:', myInstanceof(arr, Object));   // true

// 自定义构造函数测试
function Person(name) {
    this.name = name;
}
const person = new Person('张三');

console.log('自定义构造函数测试：');
console.log('person instanceof Person:', myInstanceof(person, Person)); // true
console.log('person instanceof Object:', myInstanceof(person, Object)); // true

// 继承测试
function Student(name, grade) {
    Person.call(this, name);
    this.grade = grade;
}
Student.prototype = Object.create(Person.prototype);
Student.prototype.constructor = Student;

const student = new Student('李四', '高三');
console.log('继承测试：');
console.log('student instanceof Student:', myInstanceof(student, Student)); // true
console.log('student instanceof Person:', myInstanceof(student, Person));   // true
console.log('student instanceof Object:', myInstanceof(student, Object));   // true

// 边界情况测试
console.log('边界情况测试：');
console.log('null instanceof Object:', myInstanceof(null, Object));         // false
console.log('undefined instanceof Object:', myInstanceof(undefined, Object)); // false
console.log('123 instanceof Number:', myInstanceof(123, Number));           // false

```

## instanceof 的特点总结

1. **原型链查找**：沿着对象的原型链向上查找，直到找到匹配的原型
2. **类型检测**：主要用于检测对象是否为特定构造函数的实例
3. **继承支持**：能够正确识别继承关系中的类型
4. **边界处理**：对 null、undefined 和基本类型返回 false
5. **性能考虑**：查找过程需要遍历原型链，性能相对较低

## 使用建议

- **类型检测**：推荐使用 `instanceof` 检测对象类型
- **数组检测**：推荐使用 `Array.isArray()` 替代 `instanceof Array`
- **基本类型**：基本类型使用 `typeof` 操作符更合适
- **性能考虑**：在性能敏感的场景中，考虑使用其他类型检测方法
