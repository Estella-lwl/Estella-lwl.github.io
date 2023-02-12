---
layout:
  - post
title: Vue3语法变更总结
abbrlink: 97e721dd
date: 2022-08-24 15:55:35
author: polariis
top: true
cover: true
toc: true
mathjax: false
categories: Vue3
tags:
  - Vue3
#date:
img:
coverImg:
password:
summary:
---



## 1.生命周期的变更

- 销毁的概念改为**卸载**，更名：

  - beforeDestroy改名为<span style="background-color: #f2f4f5;color: darkblue;">beforeUnmounted</span>。
  - destroyed改名为<span style="background-color: #f2f4f5;color: darkblue;">unmounted</span>。

- vue2中的两次判断改为一次（如图）。

  ![image-20220810114820457](https://s2.loli.net/2022/08/10/9kmtAb8YxeMSqQV.png)

- Vue3提供的**<span style="color: #0089FB;">Composition API</span>**形式对比：

  - beforeCreate	 => 	<span style="color: #E95200;">setup()</span>
  - created 	=> 	<span style="color: #E95200;">setup()</span>
  - beforeMount	 => 	<span style="color: #E95200;">onBeforeMount</span>
  - mounted		 => 	<span style="color: #E95200;">onMounted</span>
  - beforeUpdate 	 => 	<span style="color: #E95200;">onBeforeUpdate</span>
  - updated	 	=> 	<span style="color: #E95200;">onUpdated</span>
  - beforeUnmounted 	=>	 <span style="color: #E95200;">onBeforeUnmounted</span>
  - unmounted 	=> 	<span style="color: #E95200;">onUnmounted</span>

- 组合式API的vue2生命钩子的**优先级：**

  - 下图打印结果可以看到：setup中的<span style="color: #FFB700;">组合式API优先级更高</span>。

  <img src="https://s2.loli.net/2022/08/10/c82bMyzlovfEU9j.png" style="zoom:80%;" />



------

## 2. setup函数

###  2.1 setup的两个注意点：

- **setup执行时机**：

  - 在beforeCreated之前执行一次，并且this是undefined。

- **setup接收两个参数**：

  - 1.**<span style="color: #0089FB;">props</span>**：值为对象。包含：组件外部传递进来，且在组件内部声明接收了的属性。

    - 当子组件传了，父组件却没接收/接收少了，都会控制台警告。接收多了则会拿到undefined。

  - 2. **<span style="color: #0089FB;">context</span>**：<span style="background-color: #E9E9E9;color: #0089FB;">上下文对象</span>

       - **<span style="color: #E95200;">attrs</span>**：值为对象。包含：组件外部传递过来但没有在props配置中生命的属性，相当于this.$attrs。

       - **<span style="color: #E95200;">slots</span>**：收到的插槽内容，相当于this.$slots。

       - **<span style="color: #E95200;">emit</span>**：分发自定义事件的函数，相当于this.$emit。


### 2.2 setup语法糖

-  <span style="color: #E95200;">`<script setup>`</span>是单文件组件使用组合式API（上面笔记代码中）的语法糖。里面的代码会被编译为setup函数的内容。
- 无需export default，里面也不用return。
- 官方文档描述：https://staging-cn.vuejs.org/guide/introduction.html#api-styles

**什么时候使用**<span style="color: #E95200;">`setup()`</span>：

![image-20220818121239514](https://s2.loli.net/2022/08/18/LVO2ayMNCs3rB5p.png)


--------------------------------------------------------------------------------
### 2.3 其他补充

#### 1. vue3中插槽的使用变更：

- 在vue3中使用插槽尽量用`v-slot`。
- vue2中template里使用插槽可以使用：1. `name="xxx"`  2. `v-slot="xxx"`  。但由于vue3的兼容问题，使用第一种方法依旧显示的是默认插槽，自定义插槽名会失效。



#### 2. vue3中props接收数据：

**vue2中props接收数据**：

- 如果不用props，在VC的实例对象中也能拿到，通过`this.$attrs`；但如果使用props后，attrs中的数据就会消失（attrs就像捡漏）。



------

## 3. Ref

- **作用**：定义一个响应式的数据。

- **语法**：`const xxx = ref(initValue)`  

  - 创建一个包含响应式数据的<span style="color: #ff0001;">引用对象（reference对象）</span>。
  - 模板中使用引用对象不用后面加.value，而<span style="color: #CC0000;">setup中使用该值时需要加.value</span>。

- **接收的数据**：

  - 可以是：基本类型、对象类型。
  - ref处理 **<span style="background-color: #f2f4f5;color: darkblue;">基本类型： 响应式</span>**<span style="background-color: #f2f4f5;color: darkblue;">依旧使用是**defineProperty**</span> ，以set & get数据劫持的方式。
  - 而处理 **<span style="background-color: #f2f4f5;color: darkblue;">对象类型</span>**<span style="background-color: #f2f4f5;color: darkblue;">时使用**proxy**实现的</span>（proxy是封装在reactive函数中的，ref内部会自动通过reactive转为代理对象）。
  - 补充：使用ref数据要<span style="color: #FFB700;"> .value</span> 是因为<span style="color: #FFB700;">Object.defineProperty本身无法作用于基本数据类型，所以通过对象做了一层转化</span>。

- **使用ref获取节点**：

- **<span style="color: darkblue;">注意：</span>**如果要在setup中拿到DOM元素，声明的变量名<span style="color: #ff0001;">必须与元素中写的ref</span>**<span style="color: #CC0000;">一致</span>**。

  ```
  <template>
    <div id="countBox" ref="countupRef">
      <span class="countSpan"></span>
    </div>
  </template>
  ```

- 在**<span style="background-color: #f2f4f5;color: darkblue;">onMounted</span>**中可以拿到节点：

  ```
    setup() {
        // 这里的变量名countupRef必须与ref写的一样：
      const countupRef = ref(null);
      console.log("ref", countupRef);
  
      onMounted(() => {
        // 拿到ref节点
        console.log(countupRef);
      });
  
      return {
        countupRef
      };
    },
  ```

  

## 4. reactive

- **作用**：定义一个**对象/数组类型**的响应式数据（基本类型别用，要用ref函数）。

- **语法**：`const 代理对象 = reactive(源对象);`  接收一个对象或数组，返回一个**<span style="color: #CC0000;">代理对象</span>**<span style="color: #ff0001;">（Proxy的实例对象，简称proxy对象）</span>。

  - 绿色框里的就是源对象，粉色的是代理对象：

  ![image.png](https://s2.loli.net/2022/08/18/UschZL52Pi7kz81.png)

- reactive定义的响应式数据是”深层次的“。

- 内部基于ES6的Proxy实现，通过代理对象操作源对象内部的数据。



## **5. ref对比reactive总结**

- **从定义数据据角度对比**：
  - ref定义：<span style="color: #ff0001;">基本类型数据</span>。
  - reactive定义：<span style="color: #ff0001;">对象/数组类型数据</span>。
  - <span style="color: #0089FB;">注意</span>：ref也可以定义对象/数组类型数据，它内部会自动通过`reactive`<span style="color: #ff0001;">转为代理对象</span>。

- **从原理角度对比**：
  - ref：通过<span style="color: #CC0000;">`Object.defineProperty()`</span>的`get`和`set`实现响应式（数据劫持）。
  - reactive：通过使用<span style="color: #CC0000;">`Proxy`</span>实现响应式（数据劫持），并通过<span style="color: #CC0000;">`Reflect`</span>操作源对象内部的数据。

- **从使用角度对比**：
  - ref定义的数据：操作数据<span style="color: #CC0000;">需要</span>.value，读取数据时模板会直接读取<span style="color: #CC0000;">不用</span>`.value`。
  - reactive定义的数据：操作数据与读取数据<span style="color: #CC0000;">均不需要</span>`.value`。




## **6. Vue2&Vue3响应式原理对比**

### 6.1 Vue2的响应式

- **实现原理**：

- - **对象类型**：通过**Object.defineProperty()** 对属性的读取、修改进行拦截（**数据劫持**）。
  - **数组类型**：通过**重写更新数组的方法实现拦截**。
    - 比如：调用push方法，其实调用的是vue2二次封装后的push。
    - 这个push里做了两件事：
      - 1.帮助正常调用push方法。
      - 2.帮助更新界面。

- **存在的问题**：

   - 新增属性 or 删除属性，**界面不会自动更新**。
   - 直接通过下标修改数组，界面也不会自动更新。

   ```
   // 修改对象：
   //     添加属性：
   this.$set(target, 'name', value);
   Vue.set(target, 'name', value);
   // 删除属性：
   this.$delete(target, 'name', value);
   Vue.delete(target, 'name', value);
   
   // 修改数组：
   this.xxx.splice(0, 1, '');
   ```

   <img src="https://s2.loli.net/2022/08/18/KywkVsvl6uzL3CS.png" alt="image.png" style="zoom: 67%;" />

   

- **Object.defineProperty**:

   - 读取和修改有**get**和**set**方法，但是添加和删除是捕获不到的。
   - **configurable**配置项：能否使用delete删除属性，默认值为false。

   

### **6.2 Vue3的响应式**

#### **6.2.1实现原理：**

- 通过**Proxy**（代理）：拦截对象中任意属性的变化，**包括**：属性的读写 、添加、删除 等。
- 通过**Reflect**（反射）：对源对象（被代理）的属性进行操作。
- MDN文档中描述的**Proxy**与**Reflect**：
  - **Proxy**： window上内置的，接收**两个参数**（<span style="color: #FFB700;">必填</span>）：**1**. target(代理对谁的操作)；**2.** 对象，至少要有个空对象占位。
  - **Reflect**：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect

- vue2和vue3响应时原理对比的补充：https://juejin.cn/post/7001999813344493581
  - **模拟Vue3中实现响应式**：（不完整，看下面的**<span style="color: #ff0001;">reflect</span>**。）


```
// 模拟Vue3中实现响应式：
const p = new Proxy(person,  {
    // 读取某个属性时调用此方法：接收两个参数：源对象&当前属性名
    get(target, propName) {
        console.log(`${propName}属性被读取~`);
        return target[pcropName];    
    },
    // 修改/增加某个属性时调用此方法：接收两个参数：比get多收到一个value值。
    set(target, propName, value) {
        console.log(`${propName}属性被修改，准备更新界面`);
        target[propName] = value;    
    },
    // 删除某个属性时调用此方法：
    deleteProperty(target, propName) {
        console.log(`${propName}属性被修改，准备更新界面`);
        // delete target[propName]  最好是把真实结果给他返回：
        return delete target[propName]   
    }
})
```

绿色框在真正修改源数据：

<img src="https://s2.loli.net/2022/08/18/CiEjvBrpIYJAsm5.png" alt="image.png" style="zoom: 67%;" />



#### 6.2.2. 响应式中<span style="color: #ff0001;">Reflect</span>的应用：

- **ES6**，**<span style="color: #0089FB;">Windows内置</span>**。

- 增删改查**用法**：reflect.get、reflect.set、 reflect.delete。

- reflect里也有defineProperty方法，且有返回值（若不返回的话就不知道是否操作成功，因为它不报错。返回值是布尔值）。

  

**与Object.definProperty<span style="color: #CC0000;">对比</span>：**

- **在做封装的时候，Object.definProperty捕获错误只能通过try catch。若使用reflect就可以避免。**https://www.bilibili.com/video/BV1Zy4y1K7SH?p=147&t=672.1
- **模Vue3中实现响应式**（使用**reflect**<span style="color: #ff0001;">改进后</span>）：
  - 用到了proxy代理对象，及reflect反射对象。


```
// 模拟Vue3中实现响应式：
const p = new Proxy(person,  {
    // 读取某个属性时调用此方法：接收两个参数：源对象&当前属性名
    get(target, propName) {
        console.log(`${propName}属性被读取~`);
        return Reflect.get(target, propName);    
    },
    // 修改/增加某个属性时调用此方法：接收两个参数：比get多收到一个value值。
    set(target, propName, value) {
        console.log(`${propName}属性被修改，准备更新界面`);
        Reflect.set(target, propName, value);    
    },
    // 删除某个属性时调用此方法：
    deleteProperty(target, propName) {
        console.log(`${propName}属性被修改，准备更新界面`);
        // delete target[propName]  最好是把真是结果给他返回：
        return Reflect.deleteProperty(target, propName); 
    }
})
```



------

## 7. toRef & toRefs

### 1. toRef

- **作用**：创建个ref对象，其value的值指向另一个对象的某个属性。

- **应用**：要将响应式对象中的某个

- **参数**：1.目标对象；2.属性名。

  - **举例**：

  ```
  const obj = {
      a: 10,
      b: 'start'
  }
  
  const obj2 = toRef(obj)
  
  return {
      obj,
      // 返回的格式：
      ...toRef(obj)
  }
  ```

  

- 和ref的区别：

​	**对比**：

```
let person = reactive({
    name: '',
    hobby: {
        food: ''    
    }
})

// 这里拿到的只是一个属性值：
const name = person.name;
console.log('name', name)

// 使用toRef：
const name2 = toRef(person, 'name');
// 此时name不再是一个值，而是包在ref对象里的属性。
console.log('name2', name2)
```

如果要在setup函数return中：

**注意**：这里return中ref也可以但ref生成的是新的数据，但使用toRef数据依旧是保持联系的。这是**两者的区别**。

```
return {
    name: toRef(person, 'name');
    food: toRef(person.hobby, 'food')
}
```

当setup中的数据，return一个对象类型数据后，模板中每次使用都需要对象.xxxx。当在return时就写好对象.xxxx的话，会出现一种情况：数据不再是响应式。

**举例验证一下原因**：

相当于：最后一行name是另外新定义的，和`p.name`没任何关联，所以响应式会没了。

<img src="https://s2.loli.net/2022/08/18/ifGqWZsKODSmjhy.png" alt="image.png" style="zoom: 67%;" />



### 2. toRefs

- **作用**：需要使用<span style="color: #0089FB;">多个toRef</span>时，可以用toRefs简写。
- **使用**：只需给一个参数。无需重复给多个属性写toRef。
- **语法**：toRefs(obj)

**注意**：

- return的时候不能直接就用，return的是对象，而toRefs返回的也是对象，直接写会报错。
- 按照这种格式（<span style="color: #FFB700;">**...obj**</span>）：

```
return {
    // 这样数据的格式才正确：
    ...toRefs(person)
}
```



------

## 8. computed、watch与watchEffect

### 1. computed

- 与vue2中computed配置功能一致。
- 写法：

```
import { computed } from 'vue';

...
    // vue3中computed与vue2语法一样：
    let personObj = computed({
      name: 'a',
      age: 22
    });
```

完整：

<img src="https://s2.loli.net/2022/08/18/O5n2zmlfTRqIHN1.png" alt="image.png" style="zoom:50%;" />





### 2. watch函数

- 与vue2中配置功能一致。
- 两个小坑：
  - 监视`reactive`定义的响应式数据时：<span style="color: #CC0000;">oldValue无法正确获取；强制开启了深度监视（deep配置失效）</span>。
  - 监视`reactive`定义的响应式数据中的**某个属性**时：<span style="color: #CC0000;">deep配置有效</span>。


#### 2.1 vue3中watch的变化：

- watch能传递**3个参数**：
  - 监视谁：字符串/数组。
  - 监视的回调：函数。
  - 配置项：对象。

- 监视reactive所定义的数据时，默认开启deep深度监视。且修改无效。
- **immediate配置项**的使用：
  - 情况1&2中，配置有效。

- **deep配置项**的使用：
  - 当监视的数据由`reactive`**直接管理时**，**deep默认开启**。
  - 当嵌套层级较深，则需**手动配置**开启。

- **获取不到oldValue**的情况：可以总结为当是复杂数据类型时就获取不到。



#### 2.2 六种使用情况：

<span style="color: #0089FB;">1. </span>监视<span style="color: #2742B2;">一个ref</span>定义的数据       			 `基本数据类型。immediate可用；deep无需配置`

<span style="color: #0089FB;">2. </span>监视<span style="color: #2742B2;">多个ref</span>定义的数据   			     `基本数据类型。immediate可用；deep无需配置`

<span style="color: #0089FB;">3. </span>监视<span style="color: #2742B2;">reactive定义的全部数据</span>	       	       `deep配置无效；oldValue无法获取`

<span style="color: #0089FB;">4. </span>监视<span style="color: #2742B2;">一个reactive</span>定义的数据中的属性	 	`监视的属性要写为函数形式；oldValue可获取`

<span style="color: #0089FB;">5. </span>监视<span style="color: #2742B2;">多个reactive</span>定义的数据中的属性	     	`监视的属性要写在数组里且每项都是函数；oldValue可获取`

<span style="color: #0089FB;">6. </span>**特殊的情况**：监视<span style="color: #2742B2;">reactive定义的数据中的对象</span>属性 	`对象嵌套对象所以deep配置有效；oldValue无法获取`

**全部代码示例：**

```
const num = ref(0);
const msg = ref('hello');
let obj = reactive({
  name: '',
  age: 30,
  hobby: {
    food: 'anything'
  }
});

// 情况1. 当监视一个ref定义的数据时：
watch(
  num,
  (newVal, oldVal) => {
    console.log('', newVal, oldVal);
  },
  { immediate: false }
);

// 情况2. 当监视多个ref定义的数据时：
watch(
  [num, msg],
  (newVal, oldVal) => {
    console.log('', newVal, oldVal);
  },
  { immediate: false }
);

// 情况3. 当监视reactive定义的全部数据时：
// 注意：
//    1.此时无法拿到正确的oldValue。
//    2.默认开启deep；且修改无效。
watch(
  obj.age,
  (newVal, oldVal) => {
    console.log('', newVal, oldVal);
  },
  { deep: false }
);

// 情况4. 当监视一个reactive定义的数据中的属性时：
// 注意：当监视这种数据时，watch的第1个形参需要是一个函数，函数里要有返回值,监视谁就返回谁。
// 这时oldValue正确。
watch(
  () => {
    return obj.age;
  },
  (newVal, oldVal) => {
    console.log('', newVal, oldVal);
  },
  { deep: false }
);

// 情况5. 当监视多个reactive定义的数据中的属性时：
// 注意：watch的第1个形参是一个数组，监视几个数据里面就包几个函数。
// 这时oldValue正确。
watch(
  [
    () => {
      return obj.name;
    },
    () => {
      return obj.age;
    }
  ],
  (newVal, oldVal) => {
    console.log('', newVal, oldVal);
  },
  { deep: false }
);

// 特殊的情况6. 当监视reactive定义的数据中的对象属性时：
// 注意：当监视这种数据时，watch的第1个形参是函数。
// oldValue无法获取。
watch(
  () => {
    return obj.hobby;
  },
  (newVal, oldVal) => {
    console.log('', newVal, oldVal);
  },
  { deep: false }
);
```

**注意：**

- 使用watch监视基本数据类型时，别用`.value`，这样拿到的是一个属性真正的值而非属性。

- 但<span style="color: #E95200;">对象类型（ref定义时）</span>：要用`.value`才会被监测到。**因为**<span style="color: #CC0000;">此时的value是由reactive函数生成的proxy实例对象</span>。

  - 除了上面的`.value`，还有一个办法：加配置项 `deep: true`; 

  

------

### 3. watchEffect函数

- watchEffect也是一个监视函数，但它不用指明监视目标。<span style="color: #E95200;">`默认开启immediate: true`</span>。

- **参数**：监视的回调

- **作用**：观察<span style="color: #E95200;">监视的回调中用到了谁</span>，智能的选择监视谁。<span style="color: #E95200;">可以监视深层级数据</span>。

- **使用场景**：不用再多次写watch函数然后按照不同数据的语法写。watchEffect会自动识别并监视。

- `watchEffect`有点像`computed`，具体**<span style="color: #CC0000;">区别</span>**：

- 1.**computed注重计算出来的<span style="color: #E95200;">值</span>**（回调函数的返回值），所以必须要有返回值。执行时机：初始化时；所依赖的数据发生变化时。

- 2. **watchEffect要的是过程（使用了谁），不用返回值**。watchEffect也是所依赖的数据发生变化时重新执行一次。



------

## 9. hook

- 本质是函数，把setup中使用的Composition API进行了封装。类似于vue2中的mixin。
- 自定义hook的优势：复用代码，让setup中的逻辑更清楚。
- 把该功能相关的数据、方法、生命周期钩子全都抽离出去。
  - 新建hook文件后，将以上内容都放在个函数里。

- ps：hook文件取名一般为useXxx。

**使用示例**：

- 实现一个点击鼠标拿到坐标的功能：（完整代码在vue3-practice）

  - **改为hook前**：

  ```
  <script setup>
  import { reactive } from '@vue/reactivity';
  import { onBeforeUnmount, onMounted } from '@vue/runtime-core';
  
  const position = reactive({
    x: 0,
    y: 0
  });
  
  // // 组件挂载后执行
  // onMounted(() => {
  //   // 这种写法的问题：组件卸载前销毁该事件时无法操作：
  //   window.addEventListener('click', function (event) {
  //     console.log('FROM UseHook position: ', event.pageX, event.pageY);
  //     // 数据为响应式：
  //     position.x = event.pageX;
  //     position.y = event.pageY;
  //   });
  // });
  
  // 基于以上问题，改为这样（将点击事件的回调拎出去）：
  onMounted(() => {
    // 事件回调写进来：
    window.addEventListener('click', savePosition);
  });
  
  function savePosition(event) {
    console.log('FROM UseHook position: ', event.pageX, event.pageY);
    // 数据为响应式：
    position.x = event.pageX;
    position.y = event.pageY;
  }
  
  // 需要注意的是，当组件被卸载时该事件也该失效，所以要在卸载前另外处理下：
  onBeforeUnmount(() => {
    // 这里需要两个参数：1.移除的事件；2.移除的事件所对应的回调。
    window.removeEventListener('click', savePosition);
  });
  
  
  </script>
  
  ```

  

  - **hook写法**：   以下代码全都可以抽出去

<img src="https://s2.loli.net/2022/08/18/L9qoVb2UTGaZnHu.png" alt="image.png" style="zoom:80%;" />



usePosition文件中：

```
import { reactive, onMounted, onBeforeUnmount } from 'vue';

// 默认暴露可以不用起名，汇依据使用时起的名字。
export default function () {
  const position = reactive({
    x: 0,
    y: 0
  });

  function savePosition(event) {
    position.x = event.pageX;
    position.y = event.pageY;
    console.log('FROM UseHook position: ', position.x, position.y);
  }

  onMounted(() => {
    window.addEventListener('click', savePosition);
  });

  onBeforeUnmount(() => {
    window.removeEventListener('click', savePosition);
  });

  // 是函数，所以需要给返回值：
  // 将组件中需要的数据传出去：

  return position;
}

```





------

## 10. 其它Composition API

### **1.** shallowReactive和shallowRef

- **shallowReactive：**只处理对象中第一层的响应式，里层的不管（浅响应式）。
- **shallowRef：**
  - 只处理基本类型，和ref的使用效果无区别。
  - 当用来处理对象类型时，不再去借助reactive处理，而是直接不管了。

- **使用时机**：
  - 如果一个对象数据，嵌套层级多，但只是外层属性变化(为了提升性能)。   ==》<span style="color: #FFB700;">shallowReactive</span>
  - 如果一个对象类型数据，后续使用不会修改对象中的属性(修改会失效)，而是生成新的对象替换。   ==》<span style="color: #FFB700;">shallowRef</span>
  - https://www.bilibili.com/video/BV1Zy4y1K7SH?p=158&spm_id_from=pageDriver&vd_source=d839eda9a2b875da47ce4ee8814c818f&t=636.4


### 2. readOnly和shallowReadOnly

- **readOnly**：让响应式数据变为只读状态（<span style="color: #FFB700;">深只读</span>）。
- **shallowReadOnly**：让响应式数据变为只读状态（浅只读，除第一层只读外，里层的还可以修改）。
- **应用场景**：让数据不被修改。



### 3. toRaw和markRaw

raw：原始的

- **toRaw**：
  - **作用**：将`reactive`生成的响应式数据转为普通对象（ref不行）。
  - **使用场景**：用于读取响应式对象对应的普通对象，使其所有操作都不会引起页面更新（数据会变但不响应式）。

- **markRaw**：
  - **作用**：标记一个对象，使其之后不能再成为响应式对象。
  - **应用场景**：（应用场景比`toRaw`广）
    - 1.不应该设为响应式的值，比如复杂的第三方类库等。
    - 2.当渲染具有不可变数据源的大列表时，跳过响应式转换可以提高性能。


### 4. customRef

- **作用**：创建一个自定义的ref，并跟踪其依赖项和更新触发进行显示控制。

**使用示例**：（完整代码在vue3-practice的useCustomRef中）

```
import { customRef } from 'vue';

// 使用customRef自定义一个ref：
function myRef(initVal, delay) {
  // customRef接收两个参数：
  return customRef((track, trigger) => {
    // 下面的返回是customRef规定的：
    return {
      get() {
        console.log('正在读value：', initVal);
        // 在return之前通知vue追踪数据
        track();
        return initVal;
      },
      set(newVal) {
        console.log('正在修改value：', newVal);
        // // 只有把新值赋回给初始值的变量才能动态的变化起来：
        // initVal = newVal;
        // // 通知vue重新解析模板：所以这里可以自定义的使用值。比如规定3秒后再执行。
        // trigger();

        // 这离开了定时器会出现一个状况：当输入速度太快，等到了页面刷新时，部分被积压的输入被刷掉，页面上能看到输入在回退。
        // 解决办法：先定义一个timer定时器，每次进来执行前先清除定时器。（参考js防抖的原理）
        clearTimeout();
        setTimeout(() => {
          // 只有把新值赋回给初始值的变量才能动态的变化起来：
          initVal = newVal;
          // 通知vue重新解析模板：所以这里可以自定义的使用值。比如规定3秒后再执行。
          trigger();
        }, delay);
      }
    };
  });
}

// 给myRef两个实参：1.initVal初始值；2.delay延迟执行的时间。
let num = myRef(0, 500);
console.log('myRef自定义的数据:', num);

```



- **可以优化的地方**：延迟执行的时间是写死的，可以写为动态改变的。（**已改**）

  - 实现：多传递一个变量（比如delay）。

- 实参写在调用`myRef`时的`()`中。

  

------

## 11. provide & inject

- provide和inject（注入）是一种组件间通信的方式。
- **作用**：实现祖孙组件间的通信。
- **使用**：父组件使用<span style="color: #FFB700;">`provide`</span>提供数据，后代组件通过<span style="color: #FFB700;">`inject`</span>接收使用数据。
  - provide两个参数：1.给传递的数据起的名字；2.实参。
  - inject通过传递的参数名拿到真实数据。（如下代码块）

- **语法**：

```
// 父组件中：
let num = ref(0);
provide('xxx', num)

// 后代组件中接收：
const xxx = inject('xxx');
```

- **注意**：所有后代组件都可以使用`inject`拿到数据，但一般用在祖孙之间传递。



------

## 12. 几个用于判断响应式数据的API

**几个常用的用来判断的API：**

1. **isRef**：检查一个值是否是一个`ref`对象。
2. **isReactive**：检查一个对象是否是由`reactive`创建的响应式代理。
3. **isReadonly**：检查一个对象是否由`readonly`创建的只读代理。
4. **isProxy**：检查一个对象是否由`reactive或者readonly`方法创建的代理。

**使用场景**：判断一个数据是否经过某些api的修饰。

**需要注意的一点**：

- 当`isProxy`判断一个由~所定义的数据时，它也会返回true。
- 这是因为`readonly`属性不会改变原数据的形式。依旧是proxy代理，而非转为普通object对象。

```
let obj1 = reactive({
    a: 1,
    b: 2
})
let obj2 = readonly(obj)
// 打印为true：
console.log(isProxy(obj2));
```





------

## 13. 新的组件

### 1. Fragment

> Fragment：碎片

- 在**vue2**中：组件必须有个根标签。
- **vue3**中：无需根标签，内部已经将多个标签包在一个Fragment虚拟元素中。
- **优势**：减少标签层级嵌套，减小内存占用。

### 2. Teleport

> teleport：瞬间移动；传送；

- **作用**：将一段HTML结构移动到指定位置。
- to属性用来指定位置。

```
<teleport to="body/或任意其他位置">
    <div></div>
<teleport>
```



### 3. Suspense （试验阶段）

> Suspense:悬念

- **作用**：异步引入组件。

- <span style="color: #FFB700;">**静态**</span> **&** <span style="color: #FFB700;">**动态**</span><span style="color: #FFB700;">(异步)</span>**引入组件的对比**：

  - 写法：

    ```
    // 静态引入：
    import child from './文件路径';
    ```

    ```
    import { defineAsnycComponent } from 'vue';
    // 动态(异步)引入组件：
    // import在以前用作一个关键词，在这里用作一个函数。
    const child = defineAsnycComponent(()=> import('./文件路径')))
    ```

  - **静态引入**：当网速慢的时候，造成几个组件都不显示都在等着。

  - **动态引入**：

- **Suspense使用**：Suspense组件标签中内置了两个插槽：
  - 1.要放入展示的组件；
  - 2.组件未加载时loading状态的内容。
  - **注意**：<span style="color: #FFB700;">插槽的名字不能换</span>。


```
<template>
  <div class="app">
  <h3>我是App组件</h3>
  <Suspense>
    <template v-slot:default>
      <Child/>
    </template>
    
    <template v-slot:fallback>
      <h3>加载中.....</h3>
    </template>
  </Suspense>
  </div>
</template>
```

**PS**：当组件用了异步引入时，setup可以是async函数。

   <img src="https://s2.loli.net/2022/08/18/gi8sOUXRAJlphVy.png" alt="image.png" style="zoom:67%;" />





------

## 14. vue3的其它调整

### 1. 全局API的转移：

![image.png](https://s2.loli.net/2022/08/18/ksXfFxdm8Hr65nI.png)

### 2. 其它：

- data始终应被声明为函数。
- 过渡类名的更改：

![image.png](https://s2.loli.net/2022/08/18/arnC83f6p4hHLRN.png)

- 移除keyCode作为 v-on 的修饰符，同时也不再支持`config.keyCodes`

- 移除`v-on.native`修饰符

- - 父组件中绑定事件

```
 <my-component
   v-on:close="handleComponentEvent"
   v-on:click="handleNativeClickEvent"
 />
```

- - 子组件中声明自定义事件

```
 <script>
   export default {
     emits: ['close']
   }
 </script>
```

- 移除过滤器（filter）

> 过滤器虽然这看起来很方便，但它需要一个自定义语法，打破大括号内表达式是 “只是 JavaScript” 的假设，这不仅有学习成本，而且有实现成本！建议用方法调用或计算属性去替换过滤器。

- 其余去看官方文档...

- - https://staging-cn.vuejs.org/api/application.html
