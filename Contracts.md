# Guidelines for writing smart contracts based on TDOS (Chapter of AssemblyScript)

[TOC]

## AssemblyScript  Introduction

 AssemblyScript is a variant of TypeScript. Unlike TypeScript, AssemblyScript uses strict typing.

## AssemblyScript Basic Type

The type of each variable in AssemblyScript is immutable. There are two types in AssembyScript, one is the basic type, and the other is the reference type. All the basic types of AssemblyScript are listed below:

| AssemblyScript Type | WebAssembly Type | Description      |
| ------------------- | ---------------- | ----------------- |
| ```i32```           | ```i32```        | 32 bit  Signed integer |
| ```u32```           | ```i32```        | 32 bit  Unsigned integer |
| ```i64```           | ```i64```        | 64 bit  Signed integer |
| ```u64```           | ```i64```        | 64 bit  Unsigned integer |
| ```f32```           | ```f32```        | Single precision floating point |
| ```f64```           | ```f64```        | Double precision floating point |
| ```i8```            | ```i32```        | 8 bit Signed integer |
| ```u8```            | ```i32```        | 8 bit  Unsigned integer |
| ```i16```           | ```i32```        | 16 bit Signed integer |
| ```u16```          | ```i32```        | 16 bit  Unsigned integer |
| ```bool```          | ```i32```        | Boolean        |


In addition to the basic types in the above table, other types are reference types.

### Type conversion

 When the AssemblyScript compiler detects a potentially incompatible implicit type conversion, the compilation will terminate with an abnormal result. If you need to perform a type conversion that may be incompatible, use a forced type conversion.


  In AssemblyScript, each type mentioned above has a corresponding coercion function. For example, to force a 64-bit unsigned integer into a 32-bit unsigned integer:

```typescript
const i: u64 = 123456789;
const j = u64(i);
```

### Type declaration

The AssemblyScript compiler must know the type of each expression at compile time. This means that variables and parameters must be declared at the same time. If the type is not declared, the compiler will first assume that the type is ```i32```, and then consider ```i64``` when the value is too large. If it is a floating point number, it will use ```f64```. If the variable is the return value of another function, the type of the variable is the type of the return value of the function. In addition, the return value type of all functions must be declared in advance to help the compiler type inference.

Legal function：

```typescript
function sayHello(): void{
    log("hello world");
}
```

Function with incorrect syntax:

```typescript
function sayHello(): { // 缺少类型声明 sayHello(): void
    log("hello world");
}
```

### Null value


Many programming languages have a special ```null``` type to represent null values, such as javascript and java's ```null```, go language and python's ```nil```. In fact, the introduction of the ``null'' type has brought a lot of unpredictability to the program, and the omission of the null value check will bring security risks to the smart contract. Therefore, the preparation of the TDS smart contract does not introduce the ``null`` `Type.

### Type conversion compatibility


In the table below, the conversion compatibility of all basic types is listed. Tick to indicate that implicit type conversion can be performed from left to right.


| ↱                   | ```bool``` | ```i8```/```u8``` | ```i16```/```u16``` | ```i32```/```u32``` | ```i64```/```u64``` | ```f32``` | ```f64``` |
| ------------------- | ---------- | ----------------- | ------------------- | ------------------- | ------------------- | --------- | --------- |
| ```bool```          | ✓          | ✓                 | ✓                   | ✓                   | ✓                   | ✓         | ✓         |
| ```i8```/```u8```   |            | ✓                 | ✓                   | ✓                   | ✓                   | ✓         | ✓         |
| ```i16```/```u16``` |            |                   | ✓                   | ✓                   | ✓                   | ✓         | ✓         |
| ```i32```/```u32``` |            |                   |                     | ✓                   | ✓                   |           | ✓         |
| ```i64```/```u64``` |            |                   |                     |                     | ✓                   |           |           |
| ```f32```           |            |                   |                     |                     |                     | ✓         | ✓         |
| ```f64```           |            |                   |                     |                     |                     |           | ✓         |

### Numerical comparison


 When the comparison operators ```!=``` and ```==``` are used, if the two values are compatible during the type conversion, the comparison can be performed without the need for type conversion.


 The operators ```>```, ```<```, ```>=```, ```<=``` have different comparison methods for unsigned integers and signed integers. The two values to be compared are either signed integers or unsigned integers, and have conversion compatibility.

### Shift operation


 The result type of the shift operator ```<<```, ```>>``` is the type at the left end of the operator, and the right end type is implicitly converted to the left end type. If the type on the left end is a signed integer, an arithmetic shift is performed, and if the left end is an unsigned integer, a logical shift is performed.


 The unsigned right shift operator ```>>>``` is similar, but always performs a logical shift.

## Modular


  An AssemblyScript smart contract project may consist of multiple files. There can be a mutual reference relationship between files and the content exported by each other can be mutually used. . When the AssemblyScript project is compiled into wasm bytecode, you need to specify an entry file, and only the exported functions in this entry file can be called in the future.

### Function export

```typescript
export function add(a: i32, b: i32): i32 {
  return a + b
}
```

### Global variable export

```typescript
export const foo = 1
export var bar = 2
```

### Class export

```typescript
export class Bar {
    a: i32 = 1
    getA(): i32 { return this.a }
}
```

### Import


If you create the following multi-file project, specify ```index.ts``` as the entry file at compile time.

```sh
indext.ts
foo.ts
```

In the foo.ts file contains the following:

```typescript
export function add(a: i32, b: i32): i32{
    return a + b;
}
```

 Import the ```add``` function in index.ts:

```typescript
import {add} from './foo.ts'

function addOne(a: i32): i32{
    return add(a, 1);
}
```

## Standard library

### Global variable

| variable name  |  Class                   | description              |
| -------------- | ------------------------ | --------------------------------------- |
| ```NaN```      | ```f32``` or ```f64``` | not a number，Means not a valid floating point number  |
| ```Infinity``` | ```f32``` or ```f64``` | Represents infinity  ```-Infinity``` Represents infinitesimal |

### Global function

| Function name    | Number of parameters|  parameter list   | Return value type | description                                                       |
| ---------- | -------- | ---------------------- | ---------- | ------------------------------------------------------------ |
| ```isNaN``` | 1        | ```f32``` or ```f64``` | ```bool``` | Determine whether a floating point number is invalid |
| ```isFinite``` | 1        | ```f32``` or ```f64``` | ```bool``` | Determine that a floating-point number satisfies: 1. Not infinite 2. Not infinitely small 3. Valid  |
| ```parseInt``` | 1 or  2 | ```(string, radisx?: i32)``` | ```i64```  | Parse the string into an integer. If ```radix``` is equal to 10, the decimal system is used. The default ```radix``` is 10|
| ```parseFloat``` | 1        | ```(string)```         | ```f64```  |Parse the string into a floating point number, using decimal                    |

### Array

```Array<T>``` in AssemblyScript is very similar to Array in JavaScript. The difference is that after the arrays other than the basic types are initialized, the elements in the array must be displayed and initialized before they can be accessed. E.g:

1.Initialize using basic types:

```typescript
const arr = new Array<u64>(10); // Create an array using the basic type u64
const zero = arr[0]; // The value of zero is 0 and the type is u64
```

2.Use reference type initialization:

```typescript
const arr = new Array<string>(10); // Create an array using the basic type u64
const zero = arr[0]; // Because the TDS contract does not allow null values, an error will be reported here because arr[0] is not initialized

// The correct way is to initialize
for(let i = 0; i < arr.length; i++){
    arr[i] = "";
}
```

3.```Array<T>``` Commonly used members of the class:

| Name | Class  | Number of parameters | Parameter Type | Return value type| Example | description |
|------|--------|----------|----------|------------|------|------|
| ```new``` | Constructor | 0or1  | ```i32``` |      ```Array<T>```     |  ```new Array<i32>(1)```    |  Constructor    |
|  ```isArray```  | Static function      |  1        |    Arbitrary    |  ```bool```    |  ```Array.isArray(arr)```   |   Determine whether a variable is an array   |
|  ```length```  | Field    |    -      |    -      |     ```i32```     | ```arr.length``` | The length of the array |
| ```concat``` | method | 1 | ```Array<T>``` | ```Array<T>``` | ```arr0.concat(arr1)``` | Concatenate two arrays into one array |
| ```every``` | method| 1 | ```fn: (value: T, index: i32, array: Array<T>) => bool``` | ```bool``` | ```arr.every(fn)``` | Determine whether each element of the array satisfies ```fn```|
| ```fill``` | method| 1、2or3 | ```(value: T, start?: i32, end?: i32)``` | Return to itself| ```arr.fill(0, 0, arr.length)``` | Fill the array with ```value```, ```start``` and ```end``` are the start index (inclusive) and end index (not included) of the filling, respectively |
| ```filter``` | method| 1 | ```fn: (value: T, index: i32, array: Array<T>) => bool``` | ```Array<T>``` | ```arr.filter(fn)``` | Filter out the elements in the array that do not meet ```fn``` |
| ```findIndex``` | method| 1 | ```fn: (value: T, index: i32, array: Array<T>) => bool``` | ```i32``` | ```arr.findIndex(fn)``` | Get the index of the first element satisfying ```fn``` or ```-1``` |
| ```forEach``` | method| 1 | ```fn: (value: T, index: i32, array: Array<T>) => void``` | ```void``` | ```arr.forEach(fn)``` | Traverse the array with ```fn``` |
| ```includes``` | method| 1or2 | ```(value: T, fromIndex?: i32)``` | ```bool``` | ```arr.includes(1,0)``` | Determine whether the array contains ```value``` |
| ```indexOf``` | method| 1or2 | ```fn: (value: T, index: i32, array: Array<T>) => bool``` | ```bool``` | - | Whether each element of the array satisfies ```fn``` |
| ```join``` | method| 1 | ```(sep: string)``` | ```string``` | ```arr.join(',')``` | Connect each string in the array with characters ```sep```|
| ```lastIndexOf``` | method| 1or2 | ```(value: T, fromIndex?: i32)``` | ```i32``` | ```arr.lastIndexOf('.')``` | Get the index of the last element equal to ```value``` or ```-1```|
| ```map``` | method| 1 | ```(fn: (value: T, index: i32, array: Array<T>) => U)``` | ```Array<U>``` | ```arr.map(fn)``` | Map the elements of the array ```arr``` as the parameters of the function ```fn``` to a new array |
| ```pop``` | method| 0 | - | ```T``` | ```arr.pop()``` | Pop the last element of the array |
| ```push``` | method| 1 | ```(value: T)``` | ```i32``` | ```arr.push(1)``` | Add an element to the end of the array and return the length of the array|
| ```reduce``` | method| 1or2| ```(fn: (acc: U, cur: T, idx: i32, src: Array) => U, initialValue: U)``` | ```U``` | ```arr.reduce(fn, 0)``` | Accumulate the array from the left, often used in conjunction with ```map```|
| ```reduceRight``` | method| 1or2| ```(fn: (acc: U, cur: T, idx: i32, src: Array) => U, initialValue: U)``` | ```U``` | ```arr.reduceRight(fn, 0)``` | Accumulate the array from the right end|
| ```reverse``` | method| 0| - | Return to itself | ``arr.reverse()`` | Invert the array|
| ```shift``` | method| 0| - | ```T``` | ```arr.shift()``` | Pop the first element of the array|
| ```slice``` | method| 0、1or2 | ```(start?: i32, end?: i32)``` | ```Array<T>``` | ```arr.slice(0, arr.length)``` | Intercept from ```start``` (inclusive) of the array to ```end``` (not included)|
| ```some``` | method| 1 | ```fn: (value: T, index: i32, array: Array<T>) => bool``` | ```bool``` | ```arr.some(fn)``` | Determine whether there is at least one element in the array that satisfies ```fn```|
| ```sort``` | method | 0or1 | ```fn?: (a: T, b: T) => i32``` | Return to itself | ```arr.sort(fn)``` |To sort the array, you can pass in the comparison function ```fn```|
| ```splice``` | method | 1or2 | ```(start: i32, deleteCount?: i32)``` | ```Array<T>``` | ```arr.splice(1, 2)``` | See the truncation part from the array, ```start``` indicates the position to start the truncation, and ```deleteCount``` indicates how many to cut off |
| ```unshift``` | method | 1 | ```(value: T)``` | ```i32``` | ```arr.unshift(el)``` | Add an element to the left end of the array|

### String


Inside of string is a fixed-length UTF-16 encoded byte string. The working principle of string in AssemblyScript is very similar to string in JavaScript.

1.Common members of ```string``` class:

|Name | Class  | Number of parameters | Parameter Type | Return value type| Example | description |
|------|--------|----------|----------|------------|------|------|
| ```charAt``` | method | 1  | ```i32``` |      ```(pos: i32)```     |  ```str.charAt(0)```  |  Find the utf16 unit of ```pos``` according to the index  |
|  ```charCodeAt```  | method       |  1        |      Arbitrary     |  ```i32```  |  ```str.charCodeAt(0)```  |   Find the utf16 unit of ```pos``` according to the index   |
|  ```length```  |   Field     |    -      |    -      |     ```i32```     | ```str.length``` | The length of the string |
| ```concat``` | method | 1 | ```string``` | ```string``` | ```str0.concat(str1)``` | Concatenate strings, you can also use the plus sign to concatenate |
| ```endsWith``` | method| 1or2 | ```(search: string, end?: i32)``` | ```bool``` | ```str.endsWith('suffix')``` | To determine whether the string ends with ```search```, you can use ```end``` to specify the stop position of the search |
| ```includes``` | method| 1or2 | ```(search: string, start?: i32)``` | ```bool``` | ```str.includes('some')``` | To determine whether the string contains ```search```, you can use ```start``` to specify the starting position of the search|
| ```indexOf``` | method| 1or2 | ```(search: string, start?: i32)``` | ```i32``` | ```arr.indexOf('s')``` | Search from left to right the index of ``search``` or ```-1```|
| ```lastIndexOf``` | method| 1or2 | ```(search: string, start?: i32)``` | ```i32``` | ```arr.lastIndexOf('s')``` | Search from right to left where ``search``` is located or ```-1``` |
| ```padStart``` | method| 2 | ```(length: i32, pad:string)``` | ```string``` | ```str.padStart(2, '0')``` | Use ```pad``` to fill in the left end of the string to make the length of the string equal to ```length``` |
| ```padEnd``` | method| 2 | ```(length: i32, pad:string)```                              | ```string``` | ```str.padEnd(2,'0')``` | Use ```pad``` to fill in the right end of the string. Make the string length equal to ```length```|
| ```repeat``` | method| 0or1 | ```(count?:i32)``` | ```string``` | ```str.repeat(2)``` | Get the result of string repeating ```count``` times|
| ```replace``` | method| 2 | ```(search: string, replacement: string)``` | ```string``` | ```str.replace('a','b')``` | Replace the first found ```search``` in the string with ```replacement``` |
| ```replaceAll``` | method| 2 | ```(search: string, replacement: string)```                  | ```string``` | ```str.replaceAll('a','b')``` | Replace all ```search``` in the string with ```replacement``` |
| ```slice``` | method| 1or2 | ```(start: i32, end?: i32)``` | ```string``` | ```str.slice(1)``` |String slice, ```start``` start bit (included), ```end``` means end bit (not included) |
| ```split``` | method| 0、1or2 | ```(sep?: string, limit?: i32)``` | ```Array<string>``` | ```str.split(',')``` | Split the string with the separator ```sep```, ```limit``` is used to specify the maximum number of divisions |
| ```startsWith``` | method| 1 | ```(search: string, start?: i32)``` | ```i32``` | ```str.startsWith()``` | To determine whether the string starts with ```search```, you can use ```start``` to specify the starting position of the search |

### ArrayBuffer


 ArrayBuffer is used to represent a binary byte string, and the operation of the binary byte string usually uses the DataView interface

 The members of ArrayBuffer are as follows:

| Name | Class  | Number of parameters | Parameter Type | Return value type| Example | description |
|------|--------|----------|----------|------------|------|------|
| ```new``` | Constructor | 1   | ```i32``` |      ```ArrayBuffer```     |  ```new ArrayBuffer(1)```    |  Constructor   |
|  ```isView```  | Static function      |  1        |     Arbitrary    |  ```bool```    |  ```ArrayBuffer.isView```   |   Determine whether a value is TypedArray or DataView   |
|  ```byteLength```  |   Field   |    -      |    -      |     ```i32```     | ```buf.byteLength``` | Length of byte string |
| ```slice``` | method| 0、1 or2 | ```(begin?: i32, end?: i32)``` | ```ArrayBuffer``` | ```buf.slice(0, buf.byteLength)``` | Slice the byte string, ```begin``` includes, ```end``` does not include |

### DataView 

DataView provides an interface for operating on binary byte strings.

DataView members are as follows:

| Name | Class  | Number of parameters | Parameter Type | Return value type| Example | description |
|------|--------|----------|----------|------------|------|------|
| ```new``` | Constructor | 1、2or3 | ```(buffer: ArrayBuffer, byteOffset?: i32, byteLength?: i32)```      |      ```DataView```     |  ```new DataView(buf, 0, buf.byteLength)```    |  Constructor    |
| ```buffer``` | Field      |  -       |     -    |  ```ArrayBuffer```         |  ```view.buffer```  |   Binary byte string  |
|  ```byteLength```  |   Field     |    -      |    -      |     ```i32```     | ```buf.byteLength``` | Length of byte string |
| ```byteOffset``` | Field| - | - | ```i32``` |```buf.byteOffset```|Current offset|
| getFloat32 | method | 1 or 2 | ```(byteOffset: i32, littleEndian?: bool)``` | f32 |```view.getFloat32(0)```|Read a single-precision floating-point number from a binary byte string, using big-endian encoding by default, or you can specify ```littelEndian``` to true to use little-endian encoding|
| getFloat64 | method | 1 or 2 | ```(byteOffset: i32, littleEndian?: bool)``` | f64 |```view.getFloat64(0)```|Read a double-precision floating-point number from a binary byte string, using big-endian encoding by default, or you can specify ```littelEndian``` as true to use little-endian encoding|
| getInt8 | method | 1 | ```byteOffset: i32``` | i8 |```view.getInt8(0)```|Read an 8bit signed integer from a binary byte string|
| getInt16 | method | 1or2 | ```(byteOffset: i32, littleEndian?: bool)``` | i16 |```view.getInt16(0)```|Read a 16-bit signed integer from a binary byte string, using big-endian encoding by default, you can also specify ```littelEndian``` to true to use little-endian encoding|
| getInt32 | method | 1or2 | ```(byteOffset: i32, littleEndian?: bool)``` | i32 |```view.getInt32(0)```|Read a 32-bit signed integer from a binary byte string, using big-endian encoding by default, or you can specify ```littelEndian``` to true to use little-endian encoding|
| getInt64 | method | 1or2 | ```(byteOffset: i32, littleEndian?: bool)``` | i64 |```view.getInt64(0)```|Read a 64-bit signed integer from a binary byte string, using big-endian encoding by default, or you can specify ```littelEndian``` to true to use little-endian encoding|
| getUint8 | method| 1 | ```byteOffset: i32``` | u8 |```view.getUint8(0)```|Read an 8-bit unsigned integer from the binary byte string|
| getUint16 | method | 1or2 | ```(byteOffset: i32, littleEndian?: bool)``` | i16 |```view.getUint16(0)```|Read a 16-bit unsigned integer from a binary byte string, using big-endian encoding by default, or you can specify ```littelEndian``` to true to use little-endian encoding|
| getUint32 | method | 1or2 | ```(byteOffset: i32, littleEndian?: bool)``` | u32 |```view.getUint32(0)```|Read a 32-bit unsigned integer from a binary byte string, using big-endian encoding by default, you can also specify ```littelEndian`'' to true to use little-endian encoding|
| ```getUint64``` | method | 1or2 | ```(byteOffset: i32, littleEndian?: bool)``` | ```u64``` |```view.getUint64(0)```|Read a 64bit unsigned integer from a binary byte string, using big-endian encoding by default, you can also specify ```littelEndian``` to ```true``` to use little-endian encoding|
| ```setFloat32``` | method | 2or3 | ```(byteOffset: i32, value: f32, littleEndian?: bool)``` | ```void``` |```view.setFloat32(0,1.0)```|Put a single-precision floating-point number into the binary string, and use big-endian encoding by default. You can also specify ```littelEndian``` as ```true``` to use little-endian encoding|
| ```setFloat64``` | method | 2or3 | ```(byteOffset: i32, value: f64, littleEndian?: bool)``` | ```void``` |```view.setFloat64(0,1.0)```|Put a double-precision floating-point number into the binary string, and use big-endian encoding by default. You can also specify ```littelEndian``` as ```true``` to use little-endian encoding|
| ```setInt8``` | method | 2 | ```(byteOffset: i32, value: i8)``` | ```void``` |```view.setInt8(0,8)```|Put an 8-bit signed integer into the binary string|
| ```setInt16``` | method | 2 | ```(byteOffset: i32, value: i16,littleEndian?: bool)``` | ```void``` |```view.setInt16(0,8)```|Put a 16bit signed integer into the binary string, the big-endian encoding is used by default, or you can specify ```littelEndian``` as ```true``` to use little-endian encoding|
| ```setInt32``` |method | 2 | ```(byteOffset: i32, value: i32,littleEndian?: bool)``` | ```void``` |```view.setInt32(0,8)```|Put a 32bit signed integer into the binary string, the big-endian encoding is used by default, or you can specify ```littelEndian``` as ```true``` to use little-endian encoding|
| ```setInt64``` | method | 2 | ```(byteOffset: i32, value: i64,littleEndian?: bool)``` | ```void``` |```view.setInt64(0,8)```|Put a 64bit signed integer into the binary string|
| ```setUint8``` | method | 2 | ```(byteOffset: i32, value: u8)``` | ```void``` |```view.setUint8(0,8)```|Put an 8bit unsigned integer into the binary string|
| ```setUint16``` | method | 2 | ```(byteOffset: i32, value: u16,littleEndian?: bool)``` | ```void``` |```view.setUint16(0,8)```|Put a 16bit unsigned integer into the binary string, and use big-endian encoding by default. You can also specify ```littelEndian``` as ```true``` to use little-endian encoding|
| ```setUint32``` | method | 2 | ```(byteOffset: i32, value: u32,littleEndian?: bool)``` | ```void``` |```view.setUint32(0,8)```|Put a 32bit unsigned integer into the binary string, and use big-endian encoding by default. You can also specify ```littelEndian``` as ```true``` to use little-endian encoding|
| ```setUint64``` | method | 2 | ```(byteOffset: i32, value: u64,littleEndian?: bool)``` | ```void``` |```view.setUint64(0,8)```|Put a 64bit unsigned integer into the binary string, and use big-endian encoding by default. You can also specify ```littelEndian``` as ```true``` to use little-endian encoding|

### Map<K,V>

1.Map<K,V> represents the mapping of common keys to common values. Because the TDS smart contract does not support the ```null``` type, querying non-existent keys will cause errors.

```typescript
const map = new Map<i32,string>();

const str = map.get(1); // An error will be reported here, because key 1 has no corresponding value

const str1 = map.has(1) ? map.get(1) : ""; // Avoid exceptions by checking whether the value exists
```

2.The members of Map<K,V> are as follows:

| Name | Class  | Number of parameters | Parameter Type | Return value type| Example | description |
|------|--------|----------|----------|------------|------|------|
| ```new``` | Constructor | 0   | -     |      ```Map<K, V>```     |  ```new Map<u32, string>();```    |  Constructor   |
|  ```size```  | Field        |  -        |    -    |  ```i32```    |  -   |   The number of key-value pairs in the map  |
|  ```clear```  |   method    |    0      |    -      |     ```void```     | ```map.clear();``` | Clear a map |
| ```delete``` | method | 1 | ```(key: K)``` | ```bool``` | ```map.delete(1);``` |  Delete a key-value pair from the map. If the key to be deleted does exist, return ```true```|
| ```get``` | method| 1 | ```(key: K)``` | ```V``` | ```map.get(1);``` |Read the value corresponding to ```key``` from the map, and throw an exception if it does not exist |
| ```keys``` | method| 0 | - | ```Array<K>``` | ```map.keys()``` | all keys contained in the map |
| ```values``` | method| 0 | -                                                            | ```Array<V>``` | ```map.values()``` |all values contained in the map |

### Math 

1.The members of Math are as follows:

In the following table, the type parameter ```T``` means ```f32``` or ```f64```.

| Name | Class  | Number of parameters | Parameter Type | Return value type| Example | description |
|------|--------|----------|----------|------------|------|------|
| ```E``` | Static field | -  | -     |     ```T```     |  ```Math.E```  |  Natural base e  |
|  ```PI```  | Static field   |  -        |    -    | ```T``` |  ```Math.PI```  |   PI  |
|  ```abs```  |  Static method  |    1     | ```(x: T)``` |     ```T```     | ```Math.abs(-1)``` | Find the absolute value |
| ```acos``` | Static method | 1 | ```(x: T)``` | ```T``` | ```Math.acos(1)``` | Inverse cosine |
| ```cos``` | Static method | 1 | ```(x: T)``` | ```T```                                | ```Math.cos(1)``` | Find the cosine |
| ```asin``` | Static method | 1 | ```(x: T)``` | ```T```  | ```Math.asin(1)``` | Arc sine |
| ```sin``` | Static method | 1 | ```(x: T)```                                       | ```T```  | ```Math.sin(1)``` | Find the sine |
| ```atan``` | Static method | 1 | ```(x: T)``` | ```T```  | ```Math.atan(1)``` | Find the arc tangent |
| ```tan``` | Static method | 1 | ```(x: T)```                                       | ```T```  | ```Math.tan(1)``` | Find the tangent |
| ```max``` | Static method | 2 | ```(value1: T, value2: T)``` | ```T```  | ```Math.max(2.0, 1.0)``` | The larger value of two floating-point numbers |
| ```min``` | Static method | 2 | ```(value1: T, value2: T)``` | ```T```  | ```Math.min(2.0, 1.0)``` | The smaller value of two floating point numbers |
| ```pow``` | Static method | 2 | ```(value1: T, value2: T)``` | ```T``` | ```Math.pow(2.0, 3.0)``` | Exponential calculation|
| ```log``` | Static method | 1 | ```(x: T)``` | ```T``` | ```Math.log(2)``` | Find the natural logarithm |
| ```ceil``` | Static method | 1 | ```(x: T)``` | ```T``` | ```Math.ceil(2.1)``` | Rounded up |
| ```floor``` | Static method | 1 | ```(x: T)``` | ```T``` | ```Math.floor(2.1)``` | Rounded down |
| ```round``` | Static method | 1 | ```(x: T)``` | ```T``` | ```Math.round(2.1)``` | 4 to Rounded down and 5 to Rounded up |

## Smart contract development

###  Download smart contract writing template

``` sh
git clone https://github.com/TrustedDataFramework/assembly-script-template
cd assembly-script-template
npm install
```

 There are two important dependencies in ```package.json```:

``` json
{
  "@salaku/js-sdk": "^0.2.10",
  "@salaku/sm-crypto": "^0.1.8"
}
```

Among them, [```@salaku/sm-crypto```](https://github.com/TrustedDataFramework/sm-crypto) contains the javascript implementation of Chinese State Secrets Algorithm: sm2, sm3 and sm4, we need it for hash value calculation and transaction signature, etc.[` ` `@salaku/js-sdk` ``](https://github.com/TrustedDataFramework/js-sdk) encapsulates the method of transaction construction and rpc call, which can simplify the development of smart contracts.

### Compile and deploy the contract

1.New local directory

``` sh
mkdir local # local folder is ignored in git
```

2.Write contract source code

Then create a new hello-world.ts file, copy the following content to hello-world.ts

``` sh
touch local/hello-world.ts
```

``` typescript
import {log} from "../lib";

// every contract should contains a function named 'init'
// which will be called when contract deployed
export function init(): void{
  log('hello world');
}

export function invoke(): void{
    log("hello world");
}
```

3.Write configuration file

Create a new config.json file and copy the following content to config.json

``` sh
touch local/config.json
```

``` json
{
  "version": "1634693120",
  "host": "192.168.1.171",
  "port": "7010",
  "source": "local/hello-world.ts",
  "private-key": "**",
  "asc-path": "node_modules/.bin/asc",
  "gas-price": 0
}
```

  + The description of each field is as follows:

    - version  indicates the version of the transaction
    - host  indicates the host of the node where the contract is deployed
    - port  indicates the rpc port number of the node
    - source indicates the source code file representing the contract
    - private-key   need to fill in the private key, used to sign the transaction
    - asc-path   is the path of the compiler, for linux and mac it is generally ```node_modules/.bin/asc```, for windows it is generally ```node_modules/.bin/asc.cmd```
    - gas-price   represents the unit price of the handling fee. For private chains or alliance chains, generally fill in 0

4.Read configuration file

The require function provided by nodejs can easily read the json file, because our newly created file ```config.json``` is located in the local directory, so we need to pass the path of the configuration file through environment variables

```js
const path = require('path')

function getConfigPath() {
  // If the environment variable CONFIG= has no value, select the config.json file in the current directory
    if (!process.env['CONFIG'])
        return path.join(process.cwd(), 'config.json')
      
    const c = process.env['CONFIG']
    // Determine whether the CONFIG= value of the environment variable is an absolute path, if it is an absolute path, return directly
    if (path.isAbsolute(c))
        return c
    // If it is a relative path, join the working directory and the relative path to get an absolute path   
    return path.join(process.cwd(), c)
}

// Read configuration
// Now we can pass the path of the configuration file through the environment variable CONFIG
const conf = require(getConfigPath());
```

5.Compile 

You can use the method provided by ```@salaku/js-sdk``` to compile the contract:

``` js
const tool = require('@salaku/js-sdk')
// getConfigPath code refer to 4.Read Configration File
const conf = require(getConfigPath());

async function main() {
    // Compile the contract source code to get the wasm binary bytecode, note that the type of wasm is buffer
    const wasm = await tool.compileContract(conf['asc-path'], conf.source)
}
```

6.Construct transaction

``` js
// Transaction construction tool
const builder = new tool.TransactionBuilder(conf.version, conf['private-key'], conf['gas-price'] || 0)
```

With the transaction construction tool and wasm bytecode, the transaction deployed by the contract can be constructed

``` js
const tx = builder.buildDeploy(wasm, 0)
```

7.Create rpc tool, issue transaction

``` js
const rpc = new tool.RPC(conf.host, conf.port)
```

With the rpc tool, real-time nonce can be obtained, and new transactions need to be filled with nonce before signing

``` js
const pk = tool.privateKey2PublicKey(conf['private-key']) // Convert private key to public key
tx.nonce = (await rpc.getNonce(pk)) + 1 // Use public key to get nonce
builder.sign(tx) // Sign the transaction
const resp = await rpc.sendTransaction(tx) // Send the transaction to get the return value of the node
```

8.Complete code(deploy.js):

``` js
/**
 * Smart contract deployment example
 */

function getConfigPath() {
    if (!process.env['CONFIG'])
        return path.join(process.cwd(), 'config.json')
    const c = process.env['CONFIG']
    if (path.isAbsolute(c))
        return c
    return path.join(process.cwd(), c)
}

const tool = require('@salaku/js-sdk')
const path = require('path')

// Read configuration
const conf = require(getConfigPath());
const sk = conf['private-key']
const pk = tool.privateKey2PublicKey(sk)

// Transaction construction tool
const builder = new tool.TransactionBuilder(conf.version, sk, conf['gas-price'] || 0)
// rpc Tool
const rpc = new tool.RPC(conf.host, conf.port)

async function main() {
    // Compile the contract to get the binary content
    const o = await tool.compileContract(conf['asc-path'], conf.source)
    // Construct the contract deployment transaction
    const tx = builder.buildDeploy(o, 0)

    if (!tx.nonce) {
        tx.nonce = (await rpc.getNonce(pk)) + 1
    }

    tool.sign(tx, sk)
    console.log( `contract address = ${tool.getContractAddress(pk, tx.nonce)}` )
    const resp = await tool.sendTransaction(conf.host, conf.port, tx)
    console.log(resp)
}

main().catch(console.error)
```

Call method

``` sh
CONFIG=local/config.json node deploy.js
```

### Contract code structure

1.Function declaration


A smart contract code can consist of one or more source code files, and only functions declared as export in the contract can be triggered

``` typescript
import {log} from "../lib";
export function init(): void{ 
  log('hello world');
}

export function invoke(): void{ 
  execute();
}

function execute(): void{ // init not be exported
  log('hello world');
}
```

In this contract, the invoke function can be triggered by constructing a transaction or by rpc, while execute cannot be triggered.

2.init function


Every contract must contain at least one function named init, and this function must be exported. The code in this init function will be called when the contract is deployed.

Wrong contract (init was not exported):

``` typescript
import {log} from "../lib";
function init(): void{ // init not be exported
  log('hello world');
}
```

  Wrong contract (missing init function):

``` typescript
import {log} from "../lib";
export function main(): void{ //  missing init function
  log('hello world');
}
```

### State storage <span id="anchor1"></span>

1.Temporary storage

Unlike solidity, TDS contract code does not implement persistent storage by declaring global variables. For example, in the following code:

``` typescript
let c: u64;

export function init(): void{
  c = 0;
}

export function inc(): void{
  c++;
}
```


 In this contract, c is declared as a global variable, and the inc function can be triggered externally by constructing a transaction to realize the self-increment of c. It seems that as long as the inc function is called, c will increase by one. In fact, the location of c storage here is the memory of the wasm engine, and the memory of the wasm engine will not be persisted in the blockchain. C is essentially a temporary storage. Therefore, no matter how many times the inc function is triggered, the value of c is still 0.

2.Permanent storage

The TDS smart contract provides a method for permanent storage, which is implemented by the built-in object ```DB```. ```DB``` is essentially a key value storage. The stored keys and values are in binary format, which is used by Assemblyscript Uint8Array represents binary data.

3.DB basic operations

```typescript
import {DB, DBIterator} from './lib'

// Before saving the string key-value pair, first convert the string to binary data
function str2bin(str: string): Uint8Array{
  return Uint8Array.wrap(String.UTF8.encode(str));
}

// Convert binary data read from DB into character string
function bin2str(bin: Uint8Array): string{
  return String.UTF8.encode(bin.buffer);
}

export function init(): void{
  // Save a string key-value pair (add, change)
  DB.set(str2bin('key'), str2bin('value'));

  // Delete a string key-value pair (delete)
  DB.remove(str2bin('key'));

  // Determine whether the key exists (check)
  const exists = DB.has(str2bin('key'));

  if(!exists){
    DB.set(str2bin('key'), str2bin('value'));
  } else{
      // Print the value of value (check)
      // Because Assemblyscript does not have a null type, if exists is false, calling DB.get(str2bin('key')) will report an exception and the contract execution will terminate
    log(bin2str(DB.get(str2bin('key')));
  }
}
```

4.DBIterator


Use iterators to traverse the entire contract state storage

```typescript
import {DB, DBIterator, log, Context} from './lib'

// Before saving the string key-value pair, first convert the string to binary data
function str2bin(str: string): Uint8Array{
  return Uint8Array.wrap(String.UTF8.encode(str));
}

// Convert binary data read from DB into character string
function bin2str(bin: Uint8Array): string{
  return String.UTF8.encode(bin.buffer);
}

export function iterate(): void{
  DBIterator.reset();
  while(DBIterator.hasNext()){
    const entry = DBIterator.next();
    log('key = ' + bin2str(entry.key) + ' value = ' + bin2str(entry.value));
  }
}
```

###Trigger <span id="anchor0"></span>

There are two ways to trigger the contract, one is through rpc, and the other is through transaction.

1.rpc trigger

The limitation of triggering through rpc is that the method of triggering must be read-only for the contract state storage, and the context objects of the blockchain cannot be obtained, such as the current transaction, the hash value of the parent block, in the following contract:

```typescript
import {DB, DBIterator, log, Result} from './lib'

// Before saving the string key-value pair, first convert the string to binary data
function str2bin(str: string): Uint8Array{
  return Uint8Array.wrap(String.UTF8.encode(str));
}

// Convert binary data read from DB into character string
function bin2str(bin: Uint8Array): string{
  return String.UTF8.encode(bin.buffer);
}

// Set key to 0
export function init(): void{
  DB.set(str2bin('key'), str2bin('0'));
}

// Increase the key
export function inc(): void{
  const val = DB.get(str2bin('key'));
  const v = parseInt(bin2str(val));
  v++;
  DB.set(str2bin('key'), str2bin(v.toString()));
}

// Print key
export function logKey(): void{
  const val = DB.get(str2bin('key'));
  log(bin2str(val));
}
```


 In this contract, the ```inc``` function modifies the contract state, because the ```inc``` function cannot be triggered through rpc, and the ```logKey``` function does not modify the contract state , Is a read-only function, so you can use rpc to trigger the ```logKey``` function.


 We can use rpc to trigger the read-only function in the contract to get the contract status, and the built-in object ```Result``` is the return value of rpc and the contract function transfer interface, we need to pass the ```Result``` provided` ``write``` api to get the data in the contract. For example, we add a function ```getKey``` on the basis of the above contract.

```typescript
export function getKey(): void{
  const val = DB.get(str2bin('key'));
  Result.write(val);
}
```


  ```Result.write``` accepts data in binary format and presents it in hexadecimal encoding in external applications. The value in the contract needs to be transcoded to display externally.

```js
const tool = require('@salaku/js-sdk')
const rpc = new tool.RPC(conf.host, conf.port)
async function main(){
  const data = await rpc.viewContract('***合约地址***', 'getKey')
  const val = Buffer.from(data, 'hex').toString('utf8')
  console.log(`val = ${val}`)
}

main()
```

2.Pass parameters when rpc is triggered

When rpc triggers the function in the contract, you can use the built-in object ```Context``` to get parameters. For example, I modified ```getKey``` slightly and changed it to the ```getKeyAddN``` function

```typescript
export function getKeyAddN(): void{
  // Read additional parameters
  const p = Context.args().parameters;
  // Transcode to int type
  const j = parseInt(bin2str(p));
  // Read the value in db
  const val = parseInt(bin2str(DB.get(str2bin('key'))));
  // Return after addition
  Result.write(str2bin((j + val).toString()));
}
```

Trigger code:

```js
const tool = require('@salaku/js-sdk')
const rpc = new tool.RPC(conf.host, conf.port)
async function main(){
  const data = await rpc.viewContract('***合约地址***', 'getKeyAddN', Buffer.from('128', 'ascii'))
  const val = Buffer.from(data, 'hex').toString('utf8')
  console.log(`val = ${val}`)
}

main()
```

3.Transaction trigger

Through transaction triggering, operations such as writing, deleting and modifying the contract state can be performed, and the context object of the blockchain can also be obtained in the triggered function.

For example, to trigger the ```inc``` function in the above contract through a transaction to execute nodejs code:

```js
const tool = require('@salaku/js-sdk')
const rpc = new tool.RPC(conf.host, conf.port)
const builder = new tool.TransactionBuilder(conf.version, conf['private-key'], conf['gas-price'] || 0)
const pk = tool.privateKey2PublicKey(conf['private-key']) // 把私钥转公钥

async function main(){
  const tx = builder.buildContractCall('**合约地址**', tool.buildPayload('inc'), 0)
  tx.nonce = (await rpc.getNonce(pk)) + 1

  builder.sign(tx)
  rpc.sendTransaction(tx)
}

main()
```

4.Take parameters when the transaction is triggered


When a transaction triggers a function in the contract, you can also use the built-in object ```Context``` to obtain parameters. For example, I modified the ```inc``` slightly and changed it to the ```incN``` function

```typescript
export function incN(): void{
  // Read additional parameters
  const p = Context.args().parameters;
  // Transcode to int type
  const j = parseInt(bin2str(p));
  // Read the value in db
  const val = parseInt(bin2str(DB.get(str2bin('key'))));
  // Store after addition
  DB.set(str2bin('key'), str2bin((p + j).toString()));
}
```


For example, to trigger the ```incN``` function in the above contract through a transaction, add 128 to the value corresponding to ```key``` to execute the nodejs code:

```js
const tool = require('@salaku/js-sdk')
const rpc = new tool.RPC(conf.host, conf.port)
const builder = new tool.TransactionBuilder(conf.version, conf['private-key'], conf['gas-price'] || 0)
const pk = tool.privateKey2PublicKey(conf['private-key']) // Convert private key to public key

async function main(){
  const payload = tool.buildPayload('incN', Buffer.from('128', 'ascii'))
  const tx = builder.buildContractCall('**合约地址**', payload, 0)
  tx.nonce = (await rpc.getNonce(pk)) + 1
  builder.sign(tx)
  rpc.sendTransaction(tx)
}

main()
```

### Built-in objects


We take advantage of the rich extensibility of Assemblyscript and built-in some objects and classes to make the writing of smart contracts easier. The following are examples of the use of built-in objects

1.log function

 When the blockchain node executes the log function, it will print the log parameters to the standard output, which can be used to debug smart contracts


```typescript
import {log} from './lib'

export function init(): void{
  log('hello world');
}
```

2.Context Class

For rpc-triggered functions, you can get the parameters called by rpc through Context. For transaction-triggered functions, you can get the payload parameters of the transaction through Context. For details, please refer to the [Trigger](#anchor0) chapter.

 In addition, the function or init function triggered by the transaction can get the blockchain context object through Context. Examples are as follows:

```typescript
import {Context} from './lib'

export function init(): void{
  // Get the block header of the block where the transaction is located
  // Contains the hash of the parent block, the creation time of the block and the height of the block
  const header = Context.header();

  // Get the current transaction, including all fields of the transaction
  const tx = Context.transaction();

  // The address of the current contract, the nonce at deployment and the address of the creator of the contract
  const contract = Context.contract();
}
```

3.DB 

For the use of DB, please refer to the [Status Storage](#anchor1) chapter.

4.Decimal

The Decimal class is used to implement the four arithmetic operations of precise decimal finite decimals


```typescript
import {Decimal} from './lib'

export function init(): void{
  // addition
  let d = Decimal.add('0.01', '0.02');

  // Subtraction
  d = Decimal.sub(d, '0.02');

  // multiplication
  d = Decimal.mul(d, 10);

  // Divide, limit to 10 digits after the decimal point, throw an exception if the division is not enough
  d = Decimal.div(d, 2, 10);

  // Compare
  let c = Decimal.compare('0.01', '0.02');
}
```

5.Event class

 Event is used to asynchronously notify the outside world from the inside of the contract. The blockchain node will create a socketio service when it starts. The default port is 10004. External programs can listen to the events of the smart contract after connecting to the socketio service.

 For example, after the following contract is deployed, the front end can monitor the ```event-name``` event sent when the emit function is triggered.

```typescript
import  from './lib'

export function init(): void{

}

export function emit(): void{
  const arr = new Uint8Array(1);
  arr[0] = 255;
  Event.emit('event-name', arr);
}
```

Frontend code：

```html
<html>
  <body>
    <script type="text/javascript">
      function connectSocket() {
          let socket = window.socket
          if(socket != null){
              socket.disconnect()
          }
          window.socket = io.connect('http://localhost:10004')
          socket = window.socket
          socket.on('***contract address***:event-name', console.log)
      }
      connectSocket()          
    </script>
  </body>
</html>
```


Because the hexadecimal code of [255] is "ff" when emit is triggered, the front end will print out the object:

```json
{
  "data": "ff"
}
```

6.Hex class

Hex includes hexadecimal encoding and decoding

```typescript
import {Hex} from './lib'

export function init(): void{
  // Hexadecimal encoding to binary array
  const arr = Hex.decode('ff');

  // Binary array to hexadecimal encoding
  log(Hex.encode(arr));
}
```

7.Hash class

Hash contains commonly used hash value algorithms

```typescript
import {Hash} from './lib'

export function init(): void{
  // keccak256
  let digest = Hash.keccak256(Hex.decode('ff'));

  // sm3
  digest = Hash.sm3(Hex.decode('ff'));
}
```

### RLP encode/decode

The TDS smart contract provides a binary key-value storage interface, but the objects in the contract are not necessarily binary types, so corresponding type conversions are required when storing and reading.

The object encoding method is to recursively convert the fields in the object into a tree structure. The following is a reference for rlp encoding and reverse encoding of a sender

```typescript
// sender
class Sender {
    constructor(
        // address
        public address: Uint8Array,
        // Mail item
        public type: string,
        // actual name
        public name: string,
        // identity number
        public id: string,
        // telephone number
        public phone: string,
        // Brief description
        public description: string,
        // Winding height
        public height: u64,
        // Transaction hash on the chain
        public hash: Uint8Array,
    ) {
    }

    // decode from rlp 
    static fromEncoded(data: Uint8Array): Sender {
        const li = RLPList.fromEncoded(data);
        const r = new RLPListReader(li);
        return new Sender(
            r.bytes(), r.string(), r.string(),
            r.string(), r.string(), r.string(),
            r.u64(), r.bytes()
        )
    }

    // encode to rlp
    getEncoded(): Uint8Array {
        const elements = [
            RLP.encodeBytes(this.address),
            RLP.encodeString(this.type),
            RLP.encodeString(this.name),
            RLP.encodeString(this.id),
            RLP.encodeString(this.phone),
            RLP.encodeString(this.description),
            RLP.encodeU64(this.height),
            RLP.encodeBytes(this.hash)
        ];
        return RLP.encodeElements(elements);
    }
}
```