---
layout:
  - post
title: Vue条件渲染&列表渲染&列表过滤排序&监测原理
abbrlink: 9a3c70e
author: polariis
top: true
cover: true
toc: true
mathjax: false
tags:
  - Vue
  - 排版待优化
categories: Vue
date: 2022-01-30 00:00:00
img:
coverImg:
password:
summary:
---

## 1. 条件渲染 v-if、v-show

- v-show 控制 display 显示隐藏，v-if 控制节点是否存在。
- 当变化很频繁建议使用 v-show，防止 v-if 反复删除添加节点。
- 当使用 v-if 和 v-else-if 或 v-else 时，所在的模板代码被打断，必须这几行写一起。
- template 不会破坏结构，但是只能和 v-if 配合使用，v-show 不行。

## 2. 列表渲染 v-for

- 使用 v-for 一定要搭配 :key，它相当于遍历每一项当中的唯一标识。
- 除了可以遍历数组类型，还可以遍历对象、遍历字符串（很少）、遍历指定次数（更少）。

## 3. key 的作用与原理

> 视频讲解：https://www.bilibili.com/video/BV1Zy4y1K7SH?p=30&t=620.3

当遍历中的 index 作为 key，流程图解：

![image-index作为key](https://s2.loli.net/2022/10/05/QFhjls4NwgUJpES.png)

**两者流程对比对比：**

当节点自己的 id 作为 key，流程图解：

![image-对比id作为key](https://s2.loli.net/2022/10/05/TCNvDWuXU63A8Kp.png)

注：绿色框里的是复用过来的，红色框是 diff 算法对比后发现没有新生成的。

**１、虚拟 DOM 中 key 的作用：**

key 是虚拟 DOM 对象的标识，当状态中的数据发生变化时，Vue 会根据**新数据生成新的虚拟 DOM**，随后 Vue 进行**新虚拟 DOM 与旧虚拟 DOM 的差异比较**，比较规则如下：

**2. 对比规则：**

- 旧虚拟 DOM 中找到了与旧虚拟 DOM 相同的 key:
  - 若虚拟 DOM 中内容没变，直接复用之前的真实 DOM；
  - 若虚拟 DOM 中内容变了，则生成心的真实 DOM，然后替换页面中之前的真实 DOM。
- 旧虚拟 DOM 中未找到与新虚拟 DOM 相同的 key：
  - 创建新的真是 DOM，然后渲染到页面。

**3. 用 index 作为 key 可能会引发的问题：**

1. 若对数据进行：逆序添加、逆序删除等破坏了顺序的操作：
   1. 会产生没必要的真实 DOM 更新 =》界面效果没问题，但效率低。
2. 若结构中还包含输入类的 DOM（input 框）：
   1. 会产生错误的 DOM 更新 =》界面有问题。

**4. 开发中怎么选择 key：**

1. 最好使用每条数据的唯一表示作为 key，比如 id、手机号、身份证号、学号等唯一值。
2. 若不存在对数据的逆序添加/删除等破坏顺序的操作，仅用于渲染列表，使用 index 作为 key 没有问题。

## 4. 列表过滤

### 4.1 用 watch 实现

分析：

- step1.获取用户输入；step2. 给列表过滤。
- 可以用 v-model。
- 当数据发生改变时就再次过滤，怎么知道数据改变？用 watch。

模板代码：

```
    <h2>人员列表</h2>
    <input type="text" placeholder="请输入名字" v-model="keyWord">
    <ul>
      <!-- 这里相应的也要改成filPersonList： -->
      <li v-for="(p, index) in filPersonList" :key="index">
        {{p.name}} - {{p.age}} - {{p.sex}}
      </li>
    </ul>
```

script：

```
      data: {
        keyWord: '',
        personList: [
          { id: '001', name: '沈眉庄', age: 19, sex: '女' },
          { id: '001', name: '安小鸟', age: 18, sex: '女' },
          { id: '001', name: '温子仁', age: 25, sex: '男' },
          { id: '001', name: '果郡王', age: 22, sex: '男' },
        ],
        // 过滤出来的东西放在这里 以防数据被过滤丢失：
        filPersonList: []
      },

      watch: {
        // 传过来的newValue和oldVlue,这里只需要newValue。
        keyWord: {
          immediate: true,
          handler(val) {
            // filter不会改变原数组，会返回一个新的数组。所以这个新的数组应该被用上。
            // 而且这时会出现个问题：过滤之后旧的数据被丢了，越搜索越少。
            this.filPersonList = this.personList.filter((p) => {
              // 判断一个字符串里是否包含指定的字符：若包含：返回-1；不包含返回它在第几位。
              return p.name.indexOf(val) !== -1;
            })
          }
        }
      }
```

**一个关键点：**

- 用 indexOf 判断一个字符串里是否包含一个空字符串，它会返回 0，0 代表它在字符串中第一位。
- 所以一开始为空输入然后删掉后会出现每一项，因为删掉后输入框还存在个空串，一旦空串被过滤，列表里每一项都被展示出来。
- 所以 handler 前加个 immediate 为 true，这样一开始没等输入就执行匹配的是空串，每一项会在一开始被展示出来！
- （一个矛盾点：若一开始为空会有空串那为啥输入再删掉和不输入有区别？？
- 懂了：一开始未输入就没有空串。一开始未输入就什么都没匹配，自然什么都不展示。而删掉后输入框就有了变动且有了空串导致每一项被展示。）

### 4.2 用 computed 实现

```
computed: {
  filPersonList() {
      // 这里return是计算属性规定的：
      return this.personList.filter((p) => {
      // 里面的逻辑和watch中的一样，但这里不能用watch返回的新旧value了，所以改为keyWord。
      // 这里return是filter规定的，要给它返回去。
      return p.name.indexOf(this.keyWord) !== -1;
    })
  }
}
```

**对比：**

- computed 是通过 keyWord 变化后重新计算一遍，watch 是监听有没有变化。在这里用 computed 写代码更精简些。

## 5. 列表排序

**场景**：给三个按钮按钮，分别实现列表的：升序、降序、原顺序。

**分析**：

1. 排序是在过滤后的基础上排。
2. 要区分开用户点的是哪种排序：新增一个属性来存储。
3. 展示出来的列表依赖于 filPersonList，所以只要维护好 filPersonList 就可以过滤好，想要排序就维护好 filPersonList 过滤好的。
4. 所以过滤好的先别让它返回。给个数组来接收到过滤好的列表，然后判断一下是否需要排序，进行排序相关操作后返回。

```
<button @click="sortType === 2">年龄升序</button>
<button @click="sortType === 1">年龄降序</button>
<button @click="sortType === 0">原顺序</button>
```

```
// 新增字段来存储，默认为原顺序：
sortType: 0,   // 0原顺序；1降序；2升序。
```

放在**computed**里：

```
computed: {
  filPersonList() {
    // 这里return是给计算属性的：
    const arr = this.personList.filter((p) => {
      // 里面的逻辑和watch中的一样：
      // 但这里不能用watch返回的新旧value了，所以改为keyWord。
      return p.name.indexOf(this.keyWord) !== -1; // 这里return是给filter规的，要给它返回去。
    })
    // 判断一下是否需要排序: 读sortType就可以了。
    // 并且不用先判断sortType的值，直接让它为真的时候（值不是0时）才进入判断：
    if(this.sortType) {
      // 拿到过滤好的数据再来用数组api排序：
      // sort方法会给两个参数（前一项&后一项）：
      arr.sort((p1, p2) => {
        // 三元表达式判断需要哪种排序，然后使用不同的数组排序方式：
        // 若降序则p2-p1使其降序，否则p1-p2使其升序：
        // （注意：这里的p1、p2是一个对象，所以.age：）
        return this.sortType === 1 ? p2.age - p1.age : p1.age - p2.age;
      })
    }
    // 最后一定要将返回给arr数组的返回值在返回给函数filPersonList，否则filPersonList还是拿不到结果：
    return arr;
  }
}
```

PS：复习一下数组的**sort**方法：（**两个参数**：前一项、后一项。）

![image-数组的sort方法](https://s2.loli.net/2022/10/05/1ki4ae8CMbwyR7D.png)

```
    // sort是改变原数组的。
    let arr = [1,3,5,6,2,7,8];
    // 当想让数组升序：前-后；反之降序：后-前。
    arr.sort((a, b) => {
      return a-b;
    });
    console.log(arr);
```

## 6. Vue 监测数据改变的原理

**总结：**

1. Vue 会监视 data 中所有层次的数据。

2. 如何监测**对象**中的数据：

   通过 setter 实现监视，且要在 new Vue 时就传入要监测的数据。

   - 1. 对象中后追加的属性，Vue 默认不做响应式处理。
     2. 如需给后添加属性做响应式，使用以下 API：
        1. Vue.set(target, propertyName/index, value)，或
        2. vm.$set(target, propertyName/index, value)

3. 如何监测**数组**中的数据：

通过包裹数组更新元素的方法实现，本质上做了两件事：

- 1. 调用原生对应的方法对数组进行更新。
  2. 重新解析模板，进而更新页面。

在 Vue 修改数组中的某个元素一定要用以下方法：

- 1. 使用这些 API：push()、pop()、shift()、unshift()、splice()、sort()、reverse()。
  2. Vue.set() 或 vm.$set。

4. **注意**：**Vue.set() 和 vm.$set() 不能给 vm 或 vm 的根数据对象添加属性！！！**

### 6.1 Vue 监测数据改变的原理——对象

- Vue 中会有默认的监视，当 data 中数据发生变化，它就会去找用到的地方去更新。至于 watch，是 Vue 提供出来被配置使用的。而两者的原理是一样的。

**PS：**列表排序是一种破坏顺序的行为，v-for 遍历时绑定的 key 应该改为列表每一项中自己的 id。

#### 1. 一种情况：

在点击事件的函数里这样修改列表中的数据：（是无效的，Vue 监测不到变化。）

```
this.personList[0] = {id:'001', name:'罗老师', age:'40', sex:'男'}
```

**原因**：直接操作了数组的索引值通过赋值的方式改，而这数组中的某一项是个对象，没有给它的 getter、setter。

**正确方式：**

```
this.persons.splice(0, 1, '{id:'001', name:'罗老师', age:'40', sex:'男'}');
```

- 红色框里的被 Vue 管理了，所以就不再是正常原型对象上的 push 了。Vue 里的 push 做了两件事：1. 调了正常数组上的方法；2. 重新解析模板 生成虚拟 DOM 等一系列操作。
- 也就是说**Vue 对数组的监测靠的是包装数组上常用的修改数组的方法实现的**。

![image-png](https://s2.loli.net/2022/10/05/lXZHMDQ7ULdaNjY.png)

#### 2. 复习一下数据代理：

![image-数据代理](https://s2.loli.net/2022/10/05/n2SJtCkAuXZlMjz.png)

第一步中加工 data 是为了实现响应式，也就能实现对对象里的属性的监视。改了 data 中的就会引起 setter 的调用来重新解析模板。

#### 3. Vue 监测原理：

##### 3.1 先举个反例&引出原理：

![image-反例](https://s2.loli.net/2022/10/05/f8DoHKvxUjhYAEq.png)

这种代码会一直递归导致栈溢出。原因是读取数据就调 getter 执行红色框，接着执行到绿色框读到 data.name 就又跑去调 getter，这样不停调不停读是个死循环。同理，setter 也是。一旦 data.name 被修改就去调 setter，执行赋值就又出发了 setter。

##### 3.2 模拟一个数据监测：

~~它里面写了个构造函数，~~

```
    let data = {
      name: '天天',
      address: '软件园二期'
    }

    // Observer是构造函数得用new去调用，然后让obs接收到Observer实例对象。
    // 创建一个监视的实例对象，用于监视data中属性的变化：
    const obs = new Observer(data);

    // 准备一个vm实例对象：
    let vm = {};
    // 写好的obs给data，再给_data：
    vm._data = data = obs;

    // Observer构造函数可以：创建一个监视的实例对象。它能收到一个对象作为参数。
    function Observer(obj) {
      // STEP1. 拿到这个对象后：汇总对象中所有的属性形成一个数组。
      const keys = Object.keys(obj) // 可以利用这个api形成数组。
      console.log(keys); // 会发现name，address都在。
      // 遍历：
      //遍历拿到的每一个可以用k来当形参：
      keys.forEach((k) => {
        // 这里用到的技巧，它不会直接去动data，而是加在this上，而this是Observer的实例对象并不是data。然后读取k的值。
        Object.defineProperty(this,k, {
          get() {
            return obj[k];
          },
          // 会收到修改的值：
          set(val) {
            // 然后写个模板字符串打印下：
            console.log(`${k}被改了`);
            // 然后把传入的对象里的k给改了：
            obj[k] = val;
          }
        })
      })
    }
```

这里只是个大概，Vue 中写的很完善，无论嵌套了几层对象它都可以找到而且数组中的对象也可以。

### 6.2 Vue.set 的使用

- Vue.set() 给传**三个参数**：target（往谁身上追加属性）、key（追加什么属性）、value（属性值）。
- 它**只能给 data 里追加，不能再在某个嵌套里**。也就是说第一个参数不允许是 vm 或 vm 里的数据对象 data。

官方文档中：

![image-官方文档中](https://s2.loli.net/2022/10/05/XBKle7aSyRZtCsT.png)

**与$set 的区别：**

- 用法一模一样。
- 但它不在 Vue 身上，它在 vm 上。

例：

```
  methods: {
    addSex() {
      // 写法1.
      Vue.set(this.student, 'sex', '男');
      // 写法2.
      this.$set(this.student, 'sex', '男');
    }
  }
```

### 6.3 Vue 监测数据改变的原理——数组

1. 使用数组 API；
2. 用 Vue.set() ，用的不多。

可以直接去看上面的总结 & 代码笔记的 7.1-7.3。（这里没记全）

**补充**：

![image-官方文档中](https://s2.loli.net/2022/10/05/xqEC7orFsuULh8g.png)

另外关于数据劫持：

若有人改了 data 中某个数值，setter 就像劫持一样马上出面然后拿着这些数据去做两件事：

1. 拿着接收到的数据去改数据；

2. 重新解析模板。

**PS：它和数据代理都离不开 Object.defineProperty。**
