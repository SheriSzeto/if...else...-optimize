# javascript-判断(if...else...)优化
---
### 1、使用Array.includes

```javascript
function test(fruit){
    if(fruit == 'apple' || fruit == 'watermelon') {
        console.log('red')
    }
}
```
从上面的例子看，在判断的条件较少的时候，这种写法是没什么问题的，但是如果我们需要判断更多的红色水果，那么就需要更多的||操作符来拓展语句。

这时候可以使用Array.includes重写条件语句。

```javascript
function test(fruit) {
  // 条件提取到数组中
  const redFruits = ['apple','strawberry','cherry','watermelon']
  if(redFruits.includes(fruit)){
    console.log('red')
  }
}
test('apple')
```
### 2、使用return语句减少if嵌套
* 如果没有水果，抛出错误
* 水果quantity参数，如果超过10，则打印信息
```javascript
function test(fruit, quantity){
  const redFruits = ['apple','strawberry','cherry','watermelon']
  // 条件1，fruit必须有值
  if(fruit){
    // 条件2，必须为红色
    if(redFruits.includes(fruit)){
      console.log('red')
    }
    // 条件3，数量必须大于10
    if(quantity>10){
      console.log('big quantity')
    }
  }else {
    throw new Error('No fruit!')
  }
}
test(null) // No fruit
test('apple') // red
test('apple', 20) // red, big quantity
```
从上面的代码来看，首先一个if/else过滤无效条件，3层if语句嵌套。

用return改造，将无效的条件提前筛选出来。

```javascript
function test(fruit, quantity){
  const redFruits = ['apple', 'watermelon','cherry','strawberry']
  if(!fruit) throw new Error('No fruit')
  if(redFruits.includes(fruit)){
    console.log('red')
  }
  if(quantity>10){ console.log('big quantity') }
  
}
```
或者

```javascript
function test(fruit, quantity){
  const redFruits = ['apple','strawberry','cherry','watermelon']
  if(!fruit) throw new Error('No fruit!')
  if(!redFruits.includes(fruit)) return
  console.log('red')
  if(quantity>10){
    console.log('big quantity')
  }
}
```
### 3、函数的默认参数和解构
在函数中，我们经常要对null和undefined作出默认值的处理。
```javascript
function test(fruit,quantity){
    if(!fruit) return;
    const q = quantity || 1;
    console.log(`We have ${q} ${fruit}!`)
}
test('banana') // We have 1 banana
test('apple', 2) // We have 2 apple
```
用es6的默认参数方式来改写，直接给函数参数分配一个默认值：
```javascript
function test(fruit,quantity=1){
    if(!fruit) return;
    console.log(`We have ${q} ${fruit}!`)
}
test('banana') // We have 1 banana
test('apple', 2) // We have 2 apple
```
如果传入的参数为对象，是否可以指定默认参数呢？
```javascript
function test(fruit){
    if(fruit && fruit.name) {
        console.log(fruit.name);
    } else {
        console.log('unknown')
    }
}
test(undefined) // unknown
test({}) //unknown
test({name:'apple',color:'red'}) // apple
```
我们想要的是如果 fruit.name 存在则打印水果名称，否则将打印 unknown 。我们可以使用默认函数参数和解构(destructing) 来避免 fruit && fruit.name 这样的检查。
```javascript
// 只获得name属性
// 参数默认分配空对象
function test({name}={}){
   console.log(name || 'unknown')
}
test(undefined) // unknown
test({}) //unknown
test({name:'apple',color:'red'}) // apple
```
由于我们只需要来自 fruit 的 name 属性，我们可以使用 {name} 来解构参数，然后我们可以在代码中使用 name 作为变量来取代fruit.name。

我们还将空对象 {} 指定为默认值。 如果我们不这样做，你将在执行行测试时遇到test(undefined) – Cannot destructure property name of 'undefined' or 'null'.(无法解析’undefined’或’null’的属性名称)。 因为 undefined中 没有 name 属性

### 4、Map/Object替代switch
```javascript
function test(color) {
// 使用 switch case 语句，根据颜色找出对应的水果
    switch (color) {
        case 'red':
            return ['apple', 'strawberry'];
        case 'yellow':
            return ['banana', 'pineapple'];
        case 'purple':
            return ['grape', 'plum'];
        default:
            return [];
    }
}
//测试结果
test(null); // []
test('yellow'); // ['banana', 'pineapple']
```
用object字面量可以实现相同的结果，减少代码量。
```javascript
const fruitColor = {
    red: ['apple','strawberry'],
    yellow: ['banana','pineapple'],
    purple: ['grape', 'plum']
 }
 function test(color){
    return fruitColor[color] || []
 }
}
```
用Map的实现方式：
```javascript
const fruitColor = new Map()
    .set('red', ['apple', 'strawberry'])
    .set('yellow', ['banana', 'pineapple'])
    .set('purple', ['grape', 'plum']);
function test(color) {
    return fruitColor.get(color) || [];
}
```
对于上面的示例，还可以使用Array.filter来实现：
```javascript
const fruits = [
    { name: 'apple', color: 'red' }, 
    { name: 'strawberry', color: 'red' }, 
    { name: 'banana', color: 'yellow' }, 
    { name: 'pineapple', color: 'yellow' }, 
    { name: 'grape', color: 'purple' }, 
    { name: 'plum', color: 'purple' }
];
function test(color) {
    // 使用 Array filter  ，根据颜色找出对应的水果
    return fruits.filter(f => f.color == color); // 返回的是一个数组
}
```

### 5、Array.every 和 Array.some 来处理全部/部分满足条件
```javascript
const fruits = [
    { name: 'apple', color: 'red' },
    { name: 'banana', color: 'yellow' },
    { name: 'grape', color: 'purple' }
];
function test() {
    let isAllRed = true;
    // 条件：所有的水果都必须是红色
    for (let f of fruits) {
        if (!isAllRed) break;
        isAllRed = (f.color == 'red');
    }
    console.log(isAllRed); // false
}
```
使用Array.every,这个数组全满足某条件
```javascript
const fruits = [
    { name: 'apple', color: 'red' },
    { name: 'banana', color: 'yellow' },
    { name: 'grape', color: 'purple' }
];
function test() {
    // 条件：简短方式，所有的水果都必须是红色
    const isAllRed = fruits.every(f => f.color == 'red');
    console.log(isAllRed); // false
}
```
使用Array.some,这个数组是否有一个满足条件
```javascript
const fruits = [
    { name: 'apple', color: 'red' },
    { name: 'banana', color: 'yellow' },
    { name: 'grape', color: 'purple' }
];
function test() {
    // 条件：简短方式，所有的水果都必须是红色
    const isAnyRed = fruits.some(f => f.color == 'red');
    console.log(isAnyRed); // true
}
```
