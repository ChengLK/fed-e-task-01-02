1. 描述引用计数的工作原理和优缺点

   - 工作原理
     - 设置引用数，判断当前引用数是否为0，来决定是否为垃圾对象。
     - 对象引用关系发生改变时，修改引用数值。因为数为0时立即回收。
   - 优点：
     - 发现垃圾时立即回收
     - 最大限度的减少程序暂停，时刻监控这引用数值，保证内存不会占满。
   - 缺点：
     - 没有办法回收循环引用对象。
     - 时间开销大，时刻监控着所有对象的引用数值是否需要修改。

2. 描述标记整理算法的工作流程

   - 遍历所有对象找标记活动对象。
   - 清除阶段会先进行整理，移动对象的位置，在地址上产生连续。
   - 遍历所有对清除没有标记的对象。

3. 描述V8中新生代存储区垃圾回收流程

   - 回收过程采用复制算法加标记整理算法。
   - 内存区分为两个等大小空间（使用空间：From，空闲空间：To）。
   - From空间利用一定空间后，采用标记整理操作，将活动对象拷贝到To空间。
   - 释放From空间，然后From与To交换空间。

4. 练习1：使用函数组合fp.flowRight()重新实现下面这个函数

   ```javascript
   const lastCart = cars => fp.last(cars)
   const inStock = cars_item => fp.prop('in_stock', cars_item)
   const fn = fp.flowRight(inStock, lastCart)
   fn(cars)
   ```

5. 练习2：使用fp.flowRight()、fp.prop()和fp.first()获取第一个car的name

   ```javascript
   const firstCart = cars => fp.first(cars)
   const inStock = cars_item => fp.prop('name', cars_item)
   const fn = fp.flowRight(inStock, firstCart)
   fn(cars)
   ```

6. 练习3：使用帮助函数 _average 重构 averageDollarValue，使用函数组合的方式实现

   ```javascript
   let _average = function (xs) {
     return fp.reduce(fp.add, 0, xs) / xs.length
   }
   const dollarValues = cars => fp.map(car => car.dollar_value, cars)
   const fn = fp.flowRight(_average, dollarValues)
   fn(cars)
   ```

7. 练习4：使用flowRight写一个sanitizeName()函数，返回一个下划线连续的小写字符串，把数组中的name转为这种形式：例如：sanitizeName([''Hello Word"]) => ["hello_word"]

   ```javascript
   let _underscore = fp.replace(/\s+/g, '_')
   const sanitizeNames = fp.map(car=>{
       car.name = fp.flowRight(_underscore,fp.toLower)(car.name)
       return car
   })
   sanitizeNames(cars)
   ```

8. 练习5：使用fp.add(x, y) 和 fp.map(f, x) 创建一个能让functor里的值增加的函数ex1

   ```javascript
   const fp = require('lodash/fp')
   const { Maybe, Container } = require('./support')
   
   let maybe = Maybe.of([5, 6, 1])
   let ex1 = num => maybe.map(fp.map(fp.add(num)))._value
   ex1(1) // [6, 7, 2]
   ```

9. 练习6：实现一个 ex2，能够使用 fp.first 获取列表的第一个元素

   ```javascript
   const fp = require('lodash/fp')
   const { Maybe, Container } = require('./support')
   
   let xs = Container.of(['do','ray','me','fa','so','la','ti','do'])
   let ex2 = () => xs.map(arr => fp.first(arr))._value;
   ex2()
   ```

10. 练习7：实现一个函数ex3，使用safeProp 和 fp.first 找到 user 的名字首字母

    ```javascript
    const fp = require('lodash/fp')
    const { Maybe, Container } = require('./support')
    
    let safeProp = fp.curry(function (x, o) {
      return Maybe.of(o[x])
    })
    let user = { id: 2, name: 'Albert'}
    let ex3 = user => safeProp('name')(user).map(fp.first)._value
    ex3()
    ```

11. 联系8：使用 Maybe 重写 ex4，不要有if语句

    ```javascript
    const fp = require('lodash/fp')
    const { Maybe, Container } = require('./support')
    
    //   let ex4 = function (n) {
    //     if (n) { return parseInt(n) }
    //   } 
    let ex4 = n => Maybe.of(n).map(parseInt)._value
    
    ex4(null)  // null
    ex4(10)    // 10
    ex4('demo') // NaN
    
    ```

    