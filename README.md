# vue-tutorial

## 数据绑定语法

### 1、插值

#### 文本

数据绑定最基础的形式是文本插值，使用 “Mustache” 语法（双大括号）：

``` html
    <span>Message: {{ msg }}</span>
```

Mustache 标签会被相应数据对象的 msg 属性的值替换。每当这个属性变化时它也会更新。
你也可以只处理单次插值，今后的数据变化就不会再引起插值更新了：

``` html
    <span>This will never change: {{* msg }}</span>
```


#### 输出原始 HTML

双 Mustache 标签将数据解析为纯文本而不是 HTML。为了输出真的 HTML 字符串，需要用三 Mustache 标签：

``` html
    <div>{{{ raw_html }}}</div>
```
内容以 HTML 字符串插入——数据绑定将被忽略。


## 计算属性

### 1、computed

``` html
    <div id="example">
      a={{ a }}, b={{ b }}
    </div>
```

``` javascript
    var vm = new Vue({
      el: '#example',
      data: {
        a: 1
      },
      computed: {
        // 一个计算属性的 getter
        b: function () {
          // `this` 指向 vm 实例
          return this.a + 1
        }
      }
    })
```

这里我们声明了一个计算属性 b。我们提供的函数将用作属性 vm.b的 getter。

    console.log(vm.b) // -> 2
    vm.a = 2
    console.log(vm.b) // -> 3

你可以像绑定普通属性一样在模板中绑定计算属性。Vue 知道 vm.b 依赖于 vm.a，因此当 vm.a 发生改变时，依赖于 vm.b 的绑定也会更新。而且最妙的是我们是声明式地创建这种依赖关系：计算属性的 getter 是干净无副作用的，因此也是易于测试和理解的。

### 2、计算属性 vs.$watch
Vue.js 提供了一个方法 $watch，它用于观察 Vue 实例上的数据变动。当一些数据需要根据其它数据变化时， $watch 很诱人 —— 特别是如果你来自 AngularJS。不过，通常更好的办法是使用计算属性而不是一个命令式的 $watch 回调。考虑下面例子：

    <div id="demo">{{fullName}}</div>

    var vm = new Vue({
      data: {
        firstName: 'Foo',
        lastName: 'Bar',
        fullName: 'Foo Bar'
      }
    })

    vm.$watch('firstName', function (val) {
      this.fullName = val + ' ' + this.lastName
    })

    vm.$watch('lastName', function (val) {
      this.fullName = this.firstName + ' ' + val
    })

上面代码是命令式的重复的。跟计算属性对比：
    
    var vm = new Vue({
      data: {
        firstName: 'Foo',
        lastName: 'Bar'
      },
      computed: {
        fullName: function () {
          return this.firstName + ' ' + this.lastName
        }
      }
    })

显然后者更优雅

### 3、计算 setter

计算属性默认只是 getter，不过在需要时你也可以提供一个 setter：

    // ...
    computed: {
      fullName: {
        // getter
        get: function () {
          return this.firstName + ' ' + this.lastName
        },
        // setter
        set: function (newValue) {
          var names = newValue.split(' ')
          this.firstName = names[0]
          this.lastName = names[names.length - 1]
        }
      }
    }
    // ...

现在在调用 vm.fullName = 'John Doe' 时，setter 会被调用，vm.firstName 和 vm.lastName 也会有相应更新。