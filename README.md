# javascript-判断(if...else...)优化
JavaScript（ES6）逻辑判断条件优化
---
###1、使用Array.includes

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

###2、使用return语句减少if嵌套
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
