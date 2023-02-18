---
layout:
  - post
title: 使用TS封装axios
abbrlink: #会自动填充，别写
author: polariis
top: true
cover: true #是否放入首页轮播
toc: true #是否开启目录
mathjax: false #是否有数学公式
tags:
  - TypeScript
  - Axios
  - 封装
categories: TypeScript
date: 2022-12-11 17:53:41
img:
coverImg:
password:
summary:
---

<span style="color: #8c7be9">大致步骤</span>👇🏻（PS：过程中的代码可能不是最终的，可以在后面看最终的总结及代码。）

## 1. 封装 axios 实例

- 封装后可以 new 出多个实例，每个实例可以有不同的 BASE_URL 等。

### 1.1 使用 class 封装 axios

- 这个类用作一种类型约束，在封装 X 时可以使用。
- 使**用 class 封装的原因**：

- - class 有更强的封装性； [类 class](./类 class（JS & TS）.md)
  - constructor 每次执行是一个新的；

request/index.ts：

```
import axios, { AxiosInstance } from "axios";

export class SelfAxios {
  instance?: AxiosInstance; //axios实例的类型
}
```

#### 1.1.1 关于封装 axios 时的类型：

- 其中<span style="background-color: #ebf5ff;">**instance**</span>属性的 ts 类型由 Axios 提供：**`AxiosInstance`**。

- - 每个由 axios 创建出的**实例**，都会有 AxiosInstance 类型。

- 而 instance 实例传进的<span style="background-color: #ebf5ff;">**config 参数**</span>也有类型：**`AxiosRequestConfig`**。

request/index.ts：

```
import axios, { AxiosInstance, AxiosRequestConfig } from "axios";

export class SelfAxios {
  instance: AxiosInstance; // axios提供的实例的类型
  constructor(config: AxiosRequestConfig) {
    this.instance = axios.create(config); //在构造器中将实例赋值axios的create方法
  }
}
```

### 1.2 创建实例 & 添加 baseurl

- new 一个或多个实例，在以后写请求时调用该实例的 request 方法；

- 在实例中添加`baseURL、timeout`等信息。

- - `baseURL`可以根据环境变量来决定。

新建文件 api/common/config.ts： （根据环境切换 baseurl）

```
//  根据process.env.NODE_ENV区分
// 开发环境: development
// 生成环境: production
// 测试环境: test

let BASE_URL = "";
const TIME_OUT = 10000;

if (process.env.NODE_ENV === "development") {
  BASE_URL = "http://123.207.32.32:8000";
} else if (process.env.NODE_ENV === "production") {
  BASE_URL = "http://123.207.32.32:8000";
} else {
  BASE_URL = "http://123.207.32.32:8000";
}

// 这里的定义方式只能这样导出，属于ES Module的语法：
export { BASE_URL, TIME_OUT };

```

api/index.ts： （创建实例）

```
// 统一出口：
import { SelfAxios } from "./request";
import { BASE_URL, TIME_OUT } from "./common/config";

/* 本段创建实例的代码已放在 /request/index */
// export function request(config: AxiosRequestConfig) {
//   // 创建一个实例对象：
//   const instance = axios.create({
//     baseURL: "",
//     timeout: 500
//   });
//   return instance(config);
// }

export const request = new SelfAxios({
  baseURL: BASE_URL, // 这里不会写死而是拿到不同配置文件中的值
  timeout: TIME_OUT
});
```

api/request/index： （这个文件可以放在 utils 目录下）

```
import axios, { AxiosInstance, AxiosRequestConfig } from "axios";

// *****该文件后续可能会移至utils文件夹*****

// 这里使用class因为它有更强的封装性：
export class SelfAxios {
  instance: AxiosInstance; // axios提供的实例的类型
  constructor(config: AxiosRequestConfig) {
    this.instance = axios.create(config); //在构造器中将实例赋值axios的create方法
  }
  //以后实例化SelfAxios后可以使用这里的request方法：
  request(config: AxiosRequestConfig) {
    this.instance.request(config).then((res) => {
      console.log("res", res);
    });
  }
}

```

## 2. 添加拦截器

<span style="background-color: #ebf5ff;">三种拦截器</span>： （都已记录）

1.  axios 实例中添加拦截器
2.  全局添加拦截器
3.  单个请求中添加拦截器

### 2.1 axios 实例中添加拦截器

- 之后在划分模块时，一定会有一些**公有的逻辑** =》可以将它们封装在请求中，写进拦截器中。

- - 比如<span style="color: #0091ff">携带 token</span>、<span style="color: #0091ff">loading 加载</span>。
  - 除了上面的基本配置，在创建实例时**还可以传进一些 hooks**。

- <span style="color: #CC0000">**注意**</span>：但不能直接传 hook，因为这里 config 要求传进的是<span style="color: #8c7be9">`AxiosRequestConfig`</span>类型（这个类型里没有 hook 属性），所以<span style="color: #8c7be9">要自定义 hook </span>👇🏻。

新建 request/types.ts：

- 1. 对原本的 config 类型<span style="color: #8c7be9">`AxiosRequestConfig`</span>**做扩展**；
  2. 定义好传入哪些拦截器。

```
import { AxiosRequestConfig, AxiosResponse } from "axios";

// 在这里定义好可以传入哪些hook（拦截器）：
export interface AxiosRequestInterceptors {
  // 可以有四个interceptor:
  //  当interceptor传进来后，会被放进request实例中(看17行的使用)；
  //  拦截器参数config的类型就是AxiosRequestConfig，其返回值也是AxiosRequestConfig。
  requestInterceptor?: (config: AxiosRequestConfig) => AxiosRequestConfig;
  requestInterceptorCatch?: (err: any) => any; //错误拦截；any类型；

  //  响应拦截：
  responseInterceptor?: (res: AxiosResponse) => AxiosResponse;
  responseInterceptorCatch?: (err: any) => any; //错误拦截；any类型
}

// 对原本的AxiosRequestConfig做扩展，使其可以做到传递一些hooks拦截器：
export interface SelfRequestConfig extends AxiosRequestConfig {
  //继承AxiosRequestConfig后，给它添加一个interceptor扩展。
  interceptors?: AxiosRequestInterceptors;
}
```

api/request/index.ts：

```
import axios, { AxiosInstance, AxiosRequestConfig } from "axios";
import { AxiosRequestInterceptors, SelfRequestConfig } from "./types";

// *****该文件后续可能会移至utils文件*****

// 这里使用class因为它有更强的封装性：
export class SelfAxios {
  instance: AxiosInstance; // axios提供的实例的类型
  interceptors?: AxiosRequestInterceptors; //到时传进来的所有拦截器
  constructor(config: SelfRequestConfig) {
    //这里config由AxiosRequestConfig换成上面SelfRequestConfig
    this.instance = axios.create(config); //在构造器中将实例赋值axios的create方法
    this.interceptors = config.interceptors; //这里也可以不保存起来；保存起来后下行可以使用到👇🏻
    //在这里可以将上面扩展好的interceptors使用起来：
    this.instance.interceptors.request.use(
      this.interceptors?.requestInterceptor,
      this.interceptors?.requestInterceptorCatch
    );
    //响应拦截：
    this.instance.interceptors.response.use(
      this.interceptors?.responseInterceptor,
      this.interceptors?.responseInterceptorCatch
    );
    //👆🏻这样就可以给实例传入什么拦截就应用什么拦截。
  }
  //实例化SelfAxios后可以使用这里的request方法：
  request(config: AxiosRequestConfig) {
    this.instance.request(config).then((res) => {
      console.log("res", res);
    });
  }
}

```

**最终在实例中添加了拦截器的** api/index：

```
// 统一出口：
import axios from "axios";
import type { AxiosInstance } from "axios";
import type { SelfRequestInterceptors, SelfRequestConfig } from "./types";
import { SelfAxios } from "./request";
import { BASE_URL, TIME_OUT } from "./common/config";

export class IRequestClass {
  instance?: AxiosInstance;
}

/* 本段创建实例的代码已放在./request/index */
// export function request(config:SelfRequestConfig) {
//   // 创建一个实例对象：
//   const instance = axios.create({
//     baseURL: "",
//     timeout: 500
//   });
//   return instance(config);
// }

export const request = new SelfAxios({
  baseURL: BASE_URL, // 这里不会写死而是拿到不同配置文件中的值
  timeout: TIME_OUT,
  //1. 除了上面的基本配置还可以传进hooks，里面对应的是一个个的拦截器。
  //2. 但不能直接传hook，因为这里要求传进的是AxiosRequestConfig类型，所以要自定义hook。
  interceptors: {
    requestInterceptor: (config) => {
      //在拦截时需要进行的操作可以在这里添加：
      console.log("请求成功的拦截", config);
      return config;
    },
    requestInterceptorCatch: (err) => {
      console.log("请求失败的拦截", err);
      return err;
    },
    responseInterceptor: (config) => {
      console.log("响应成功的拦截", config);
      return config;
    },
    responseInterceptorCatch: (err) => {
      console.log("响应失败的拦截", err);
      return err;
    }
  }
});

```

### 2.2 全局添加拦截器

api/request/index.ts：

```
import axios, { AxiosInstance, AxiosRequestConfig } from "axios";
import { AxiosRequestInterceptors, SelfRequestConfig } from "./types";

// *****该文件后续可能会移至utils文件*****

// 这里使用class因为它有更强的封装性：
export class SelfAxios {
  instance: AxiosInstance; // axios提供的实例的类型
  interceptors?: AxiosRequestInterceptors; //到时传进来的所有拦截器
  constructor(config: SelfRequestConfig) {
    //这里config由AxiosRequestConfig换成上面SelfRequestConfig
    this.instance = axios.create(config); //在构造器中将实例赋值axios的create方法
    this.interceptors = config.interceptors; //这里也可以不保存起来；保存起来后下行可以使用到👇🏻
    //在这里可以将上面扩展好的interceptors使用起来：
    this.instance.interceptors.request.use(
      this.interceptors?.requestInterceptor,
      this.interceptors?.requestInterceptorCatch
    );
    //响应拦截：
    this.instance.interceptors.response.use(
      this.interceptors?.responseInterceptor,
      this.interceptors?.responseInterceptorCatch
    );
    //👆🏻这样就可以给实例传入什么拦截就应用什么拦截。

    //********以上是给实例添加拦截器，以下为全局的拦截器添加方式：
    //  这种未指定实例的就是全局的：
    this.instance.interceptors.request.use(
      (config) => {
        console.log("全局请求拦截");
        return config;
      },
      (err) => {
        console.log("全局请求拦截");
        return err;
      }
    );
    this.instance.interceptors.response.use(
      (res) => {
        console.log("全局响应拦截");
        return res.data;
      },
      (err) => {
        console.log("全局响应拦截");
        return err;
      }
    );
  }
  //实例化SelfAxios后可以使用这里的request方法：
  request(config: AxiosRequestConfig) {
    this.instance.request(config).then((res) => {
      console.log("res", res);
    });
  }
}

```

### 2.3 单个请求中添加拦截器

- request 方法的 config 参数类型需要做转化，

- - 方式 1. 转化 config 参数
  - 方式 2. 判断当有请求拦截器传入时，使用 config.interceptors.requestInterceptor 的方式转化。

```
// 有两种方式：  （注意这里的类型换了）
  request(config: SelfRequestConfig): void {
    //config类型要先换成扩展后的
    // // 方式1. 将config这里转化一下(transformRequest是本来就有提供的)：
    // this.instance.request({ ...config, transformRequest }).then((res) => {
    //   console.log("res", res);
    // });

    // 方式2. 当👇🏻存在时，使用config.interceptors.requestInterceptor的方式转化：
    if (config.interceptors?.requestInterceptor) {
      config = config.interceptors.requestInterceptor(config);
    }
    this.instance.request(config).then((res) => {
      // 也可以对响应结果做处理：
      if (config.interceptors?.responseInterceptor) {
        // 转换res:
        res = config.interceptors.responseInterceptor(res);
      }
      console.log("res", res);
    });
  }
```

**最终添加完拦截器的** api/request/index.ts：

- - 其中声明的类型已单独新建&放进 ./types.ts 中（在上面已记录）。

```
import axios, { AxiosInstance, AxiosRequestConfig } from "axios";
import { AxiosRequestInterceptors, SelfRequestConfig } from "./types";

// *****该文件后续可能会移至utils文件*****

// 这里使用class因为它有更强的封装性：
export class SelfAxios {
  instance: AxiosInstance; // axios提供的实例的类型
  interceptors?: AxiosRequestInterceptors; //到时传进来的所有拦截器
  constructor(config: SelfRequestConfig) {
    //这里config由AxiosRequestConfig换成上面SelfRequestConfig
    this.instance = axios.create(config); //在构造器中将实例赋值axios的create方法
    this.interceptors = config.interceptors; //这里也可以不保存起来；保存起来后下行可以使用到👇🏻
    //在这里可以将上面扩展好的interceptors使用起来：
    this.instance.interceptors.request.use(
      this.interceptors?.requestInterceptor,
      this.interceptors?.requestInterceptorCatch
    );
    //响应拦截：
    this.instance.interceptors.response.use(
      this.interceptors?.responseInterceptor,
      this.interceptors?.responseInterceptorCatch
    );
    //👆🏻这样就可以给实例传入什么拦截就应用什么拦截。

    //********以上是给实例添加拦截器，以下为全局的拦截器添加方式：
    //  这种未指定实例的就是全局的：
    this.instance.interceptors.request.use(
      (config) => {
        console.log("全局请求拦截");
        return config;
      },
      (err) => {
        console.log("全局请求拦截");
        return err;
      }
    );
    this.instance.interceptors.response.use(
      (res) => {
        console.log("全局响应拦截");
        return res.data;
      },
      (err) => {
        console.log("全局响应拦截");
        return err;
      }
    );
  }
  // //实例化SelfAxios后可以使用这里的request方法：
  // request(config: AxiosRequestConfig) {
  //   this.instance.request(config).then((res) => {
  //     console.log("res", res);
  //   });
  // }

  // ******如果要给单个请求做拦截，request的写法：   ***********
  // 有两种方式：  （注意这里的类型换了）
  request(config: SelfRequestConfig): void {
    //config类型要先换成扩展后的
    // // 方式1. 将config这里转化一下(transformRequest是本来就有提供的)：
    // this.instance.request({ ...config, transformRequest }).then((res) => {
    //   console.log("res", res);
    // });

    // 方式2. 当👇🏻存在时，使用config.interceptors.requestInterceptor的方式转化：
    if (config.interceptors?.requestInterceptor) {
      config = config.interceptors.requestInterceptor(config);
    }
    this.instance.request(config).then((res) => {
      // 也可以对响应结果做处理：
      if (config.interceptors?.responseInterceptor) {
        // 转换res:
        res = config.interceptors.responseInterceptor(res);
      }
      console.log("res", res);
    });
  }
}

```

### 3. 💡 以上封装的思路总结：

1. **封装 axios**：

2. 1. 注意 instance 的类型和 request 方法的参数 config 的类型。
   2. 根据需要给类型做扩展，在之后的使用中直接使用扩展好的。

3. **封装拦截器**： （以下可 3 中可以都封装进去）

4. 1. 每个<span style="color: #678f00">**实例单独的拦截器**</span>：

   2. 1. 原先封装时的`constructor`**默认**只能传 **`AxiosRequestConfig` 类型**，但需要考虑到传递`hooks`的情况（拦截器） =》可以自定义个 class 再利用`extends`继承`AxiosRequestConfig`，**实现对它的扩展**。

      2. 然后在封装 axios 中替换为 👆🏻 新的 class，这样每个实例可以有不同的拦截器。

      3. 1. PS：需要注意，由于拦截器是可选的，在实例中使用时应该写为<span style="color: #8c7be9">**可选链**</span>形式。

   3. <span style="color: #678f00">**全局所有实例的拦截器**</span>：

   4. 1. PS：请求拦截是后添加的先执行；响应拦截是先添加的先响应。

   5. **请求单独的拦截器**：

   6. 1. 这时封装 axios 实例时 request 的 config 参数不能使用默认的`AxiosRequestConfig`类型，而是为其做了拓展的`SelfRequestConfig`。
      2.
      3. PS：一般单个请求的拦截不会拦截 error。

- - 举例：

  - - 封装的地方：

```
...外层代码略...
// ******如果要给单个请求做拦截，request的写法：   ***********
  // 有两种方式：  （注意这里的类型换了）
  request(config: SelfRequestConfig): void {
    //config类型要先换成扩展后的
    // // 方式1. 将config这里转化一下(transformRequest是本来就有提供的)：
    // this.instance.request({ ...config, transformRequest }).then((res) => {
    //   console.log("res", res);
    // });

    // 方式2. 当👇🏻存在时，使用config.interceptors.requestInterceptor的方式转化：
    if (config.interceptors?.requestInterceptor) {
      config = config.interceptors.requestInterceptor(config);
    }
    this.instance.request(config).then((res) => {
      // 也可以对响应结果做处理：
      if (config.interceptors?.responseInterceptor) {
        // 转换res:
        res = config.interceptors.responseInterceptor(res);
      }
      console.log("res", res);
    });
  }
...
```

- - - 请求的地方：

```
export function loginRequest(account: IAccount) {
  return request.request({
    url: LoginApi.AccountLogin,
    method: "get",
    data: account, //data是放在account中的

    // 给当前请求单独设置拦截：
    interceptors: {
      requestInterceptor: (config) => {
        console.log("单独请求的config", config);
        return config;
      },
      responseInterceptor: (res) => {
        console.log("单独响应的res", res);
        return res;
      }
    }
  });
}
```

控制台结果打印：

​ ![image-20230218153030722](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230218153030722.png)

此处待整理：

> token 一般会放在请求头中。

## 4. 完善封装

### 4.1 错误拦截的全局提示

响应失败的错误返回分两种：

- <span style="background-color: #ebf5ff; color: #8c7be9">HttpErrorCode</span>：403、404、501、502

- 返回 200，错误信息放在了返回中：

- -

```
...外层代码已略...
this.instance.interceptors.response.use(
      (res) => {
        console.log("全局响应拦截");
        return res.data;
      },
      (err) => {
        console.log("全局响应拦截");
        //全局响应失败的拦截： (根据后端返回的错误码做出提示)
        switch (err.response.status) {
          case "404":
            alert("404");
            break;
          case "501":
            alert("501");
            break;
        }
        return err;
      }
    );
```

### 4.2 全局 loading & 可配置

- 有些请求是不需要有`loading`状态的，所以应该是<span style="color: #FFB700">**可配置**</span>的。
- 和上面的拦截器同理，可以在给 <u>AxiosRequestConfig 做的扩展中添加</u> 。

utils/request/types.ts：

```
// 对原本的AxiosRequestConfig做扩展，使其可以做到传递一些hooks拦截器：
export interface SelfRequestConfig extends AxiosRequestConfig {
  //继承AxiosRequestConfig后，给它添加一个interceptor扩展。
  interceptors?: AxiosRequestInterceptors;
  showLoading?: boolean;    //将loading设为可选的
}
```

在 utils/request/index 中：

```
export class SelfAxios {
  instance: AxiosInstance; // axios提供的实例的类型
  interceptors?: AxiosRequestInterceptors; //到时传进来的所有拦截器
  showLoading: boolean; //记录是否showLoading;没有定义为可选的原因：使用👇🏻16行的写法使其有默认值
  loading?: LoadingInstance;
  //。。。后面省略。。。
```

- 某个接口中需要使用时：

```
export function loginRequest(account: IAccount) {
  return request.request({
    url: 'xxx',
    method: "get",
    showLoading: true // 如果这里不传，就会使用封装时设置的默认值true
  });
}
```

- <span style="color: #CC0000">**注意**</span>：

- - 如果某一接口将 loading 设置为 false 了，要<span style="color: #FFB700">在它后面设回 true 才可以在进入下一个页面后正常出现 loading</span>👇🏻。
  - 同时，也要注意 catch err 后的 loading 状态。

​ ![image-20230218153523649](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230218153523649.png)

### 4.3 封装完善 & 响应拦截的类型完善

> <span style="color: #3850b8">前言 & 大致思路</span>：
>
> ① 前部分已将 axios 基本封装&加入了 loading，但存在一个**问题**👇🏻：
>
> request 的 res 数据此时在封装的 instance 实例中拿到，而非具体的某个接口中。
>
> ②<span style="color: #8c7be9">**解决**</span>：
>
> 1. 用 `return` 一个 `promise` 代替此时直接 `this.instance...` 的写法（这里使用类封装的，对比最开始的写法是 const instance 并 return）。
> 2. 由此引出了 TS 类型约束的更换：
>    1. 先将`request`及 Promise 中拿到的返回分别更换为 Promise 类型、泛型。
>    2. 使用 promise 后需要将 request 的 res 结果 `resolve` 返回出去 & `reject`也要。
>    3. SelfRequestConfig 添加泛型（具体看 S2.）。
>
> 以下是具体实现过程 👇🏻：

#### S1. 使用 Promise 写法 & 更换其类型：

1. 将 request 的封装更改为 promise 的写法；
2. 这时类型也需要更换 👉🏻 将 request 类型由`void`<span style="color: #0091ff">换为`Promise`类型</span>（因为给 request return 出去的就是个 Promise 对象）；
3. Promise 中拿到的<span style="color: #0091ff"><u>返回的类型</u></span>应该是由请求者决定，所以使用<span style="color: #8c7be9">**泛型**</span>。

- 1. before：

  ![image-20230218154123851](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230218154123851.png)

after：

![image-20230218154134102](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230218154134102.png)

- 1. 相应的，接口处调用 request 时指定一个<span style="color: #0091ff">输入类型</span>。（**存疑**，预计不是最终形式）

```
// 接口处为promise的返回泛型指定一个输入类型：
interface DataType {
  data: any;
  returnCode: string;
  success: boolean;
}

request.request<DataType>({
  url: LoginApi.AccountLogin,
  method: "get",
  data: account, //data是放在account中的
}).then((res) => {
    //此处res就会是DataType类型，并且有很好的提示：
    console.log('', res);
});
```

4. 使用 Promise 后要把 res 结果 `resolve` 返回出去：`resolve(res);` ；以及 `reject` 也是。

- - <span style="color: #ff0001">**引出的问题**</span>：提示<span style="color: #0091ff">要求 res 是 `AxiosResponse` 类型</span>，<span style="color: #ff0001">而这里是 `<T>`</span>（res 的类型是跟着 request 的）。

![image-20230218154538075](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230218154538075.png)

- - <span style="color: #0091ff">**解决**</span>：<span style="background-color: #ebf5ff;">既然被推导错了，则<span style="color: #3850b8">直接指定 request 为 <any, T></span></span>（指定时需要注意要求有两个泛型）。

  - - **PS**：由于 AxiosResponse 要从<span style="color: #3850b8">`.data`</span>中获取数据，而之前对 AxiosResponse 类型做过转化 → 赋值已是<span style="color: #3850b8">`res.data`</span>，所以此时 instance 实例的 request 类型根本不是提示中的 AxiosResponse 类型了，这里被<span style="color: #ff0001">推导错了</span>，应该就是 `<T>` 。

​ ![image-20230218154928398](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230218154928398.png)

​ <span style="color: #CC0000">**注意**</span>：request 的泛型中有<span style="color: #CC0000">两个</span>：`T、AxiosResponse<T>`👇🏻，要改的是后者。

​ ![0](https://note.youdao.com/yws/res/24754/WEBRESOURCEcd1d5151294312cd7f2c6d9bf9188bce)

- - - （**临时，S2 中已改**）上面图中提到被注释的那行有问题， 暂时将响应拦截器类型改为 any👇🏻：

    - - utils/request/type.ts：

```
// responseInterceptor?: (res: AxiosResponse) => AxiosResponse; //由于request类型被推导后为其指定了泛型，为处理res的那行代码正常使用所以本行暂时改为：
  responseInterceptor?: (res: any) => any;
```

<span style="color: #E95200">**PS**</span>：

- 以上（指 4.3 小节）是为发送请求时清楚拿到的`res的类型`是什么所做的处理；
- 这部分代码可以再参考 👉🏻 [jekip](https://github.com/jekip) 封装的方式：https://github1s.com/jekip/naive-ui-admin/blob/HEAD/src/utils/http/axios/Axios.ts （53 行、72 行）

#### S2. 更改响应拦截器的类型： （最终版）

- 上面提到实例的`request`被推导错误遂将其指定为`<any, T>`类型，并暂时将 res 的处理代码注释了，之后又暂时将响应拦截器类型设为`any`（因为得有 res 的处理但这只是临时的权宜之计 👇🏻）。

```
//  响应拦截：
// responseInterceptor?: (res: AxiosResponse) => AxiosResponse; //由于request类型被推导后为其指定了泛型，为处理res的那行代码正常使用所以本行暂时改为：
responseInterceptor?: (res: any) => any;
```

1. 给接口`SelfRequestConfig`和接口`AxiosRequestInterceptors`添加泛型&默认值`AxiosResponse`。
   - 当接口中的方法的类型是不确定的，则需要<span style="background-color: #ebf5ff;">将其类型提到接口上</span><u>（接口可以加泛型，但里面不行</u>），<span style="background-color: #ebf5ff;">这样里面的属性就可以使用泛型了</span>。

```
// 给接口使用泛型 & 给默认值（否则23行使用该接口时会报错）：
export interface AxiosRequestInterceptors<T = AxiosResponse> {
  // 可以有四个interceptor:
  //  当interceptor传进来后，会被放进request实例中(看17行的使用)；拦截器参数config的类型就是AxiosRequestConfig，其返回值也是AxiosRequestConfig。
  requestInterceptor?: (config: AxiosRequestConfig) => AxiosRequestConfig;
  requestInterceptorCatch?: (err: any) => any; //错误拦截；any类型

  //  响应拦截：
  // responseInterceptor?: (res: AxiosResponse) => AxiosResponse; //由于request类型被推导后为其指定了泛型，为处理res的那行代码正常使用所以本行暂时改为下一行：
  // responseInterceptor?: (res: any) => any;   // 临时更改；最后改为下一行：
  responseInterceptor?: (res: T) => T;
  responseInterceptorCatch?: (err: any) => any; //错误拦截；any类型
}
```

2. 再结合**S1.** 中提到的`res的T类型与要求的AxiosResponse不符`，要做的是使 AxiosResponse 类型改为 T 类型。<span style="color: #ff0001">**改的方法是**</span>：

- - 将 T 类型传至`SelfRequestConfig`接口中，再由它传至`AxiosRequestInterceptors`的类型中（因为`SelfRequestConfig`的`interceptors`属性使用了该接口），这时 `AxiosResponse` 类型就被改为 `T` 类型了。

​ ![image-20230218155822404](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230218155822404.png)

- <span style="color: #CC0000">**总之关键点在于**</span>：将`request`的参数`config`的类型`SelfRequestConfig`也加上泛型`<T>`，使其逐步传递至嵌套的接口中最后被识别为 T 泛型，因为 👇🏻 使用的就是 T 类型：

```
res = config.interceptors.responseInterceptor(res);
```

- - 这时**该泛型传递的顺序**：<span style="color: #FFB700">SelfRequestConfig  </span>=》<span style="color: #FFB700">SelfRequestConfig</span> =》<span style="color: #FFB700">SelfRequestConfig 中的 interceptors</span> =》<span style="color: #FFB700">AxiosRequestInterceptors 中的 responseInterceptor </span>。

  - **最后这一块的代码**：

  - - utils/request.index：

```
request<T>(config: SelfRequestConfig<T>): Promise<T> {
    //改用return Promise的方式；注意不再是void类型而是promise👆🏻。
    // 注意：promise拿到结果的类型应该是由请求者决定，所以使用泛型👆🏻。
    return new Promise((resolve, reject) => {
      //config类型要先换成扩展后的
      // // 方式1. 将config这里转化一下(transformRequest是本来就有提供的)：
      // this.instance.request({ ...config, transformRequest }).then((res) => {
      //   console.log("res", res);
      // });

      // 方式2. 当👇🏻存在时，使用config.interceptors.requestInterceptor的方式转化：
      if (config.interceptors?.requestInterceptor) {
        config = config.interceptors.requestInterceptor(config);
      }
      // 判断是否需要显示loading：
      if (config.showLoading === false) {
        this.showLoading = config.showLoading;
      }
      this.instance
        .request<any, T>(config)
        .then((res) => {
          // 也可以对响应结果做处理：
          //  1. 单个请求对数据的处理：
          if (config.interceptors?.responseInterceptor) {
            // 转换res:
            res = config.interceptors.responseInterceptor(res);
          }
          // 将showLoading设置为true，避免影响下一个请求：
          this.showLoading = true;
          console.log("res", res);
          resolve(res);
        })
        .catch((err) => {
          // 将showLoading设置为true，避免影响下一个请求：
          this.showLoading = true;
          reject(err);
          return err;
        });
    });
  }
```

- - - utils/request/types：

```
export interface AxiosRequestInterceptors<T = AxiosResponse> {
  // 可以有四个interceptor:
  //  当interceptor传进来后，会被放进request实例中(看17行的使用)；拦截器参数config的类型就是AxiosRequestConfig，其返回值也是AxiosRequestConfig。
  requestInterceptor?: (config: AxiosRequestConfig) => AxiosRequestConfig;
  requestInterceptorCatch?: (err: any) => any; //错误拦截；any类型

  //  响应拦截：
  // responseInterceptor?: (res: AxiosResponse) => AxiosResponse; //由于request类型被推导后为其指定了泛型，为处理res的那行代码正常使用所以本行暂时改为下一行：
  // responseInterceptor?: (res: any) => any;   // 临时更改；最后改为下一行：
  responseInterceptor?: (res: T) => T;
  responseInterceptorCatch?: (err: any) => any; //错误拦截；any类型
}

// 对原本的AxiosRequestConfig做扩展，使其可以做到传递一些hooks拦截器：
// 很多地方使用到了SelfRequestConfig，可能会被单独使用到，最好给它也指定泛型和默认值：
export interface SelfRequestConfig<T = AxiosResponse>
  extends AxiosRequestConfig {
  interceptors?: AxiosRequestInterceptors<T>; //继承AxiosRequestConfig后，给它添加一个interceptor扩展。
  showLoading?: boolean;
}
```

### 4.4 其它完善 （略）

- 在请求时可能不只有 request 形式的，或许会有 get、post 等。

- - 所以继续在封装处添加 👇🏻： （本段未写在项目代码中...）

- ![image-20230218160230231](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230218160230231.png)

- - 由于**4.3 小节 S2.** 中对 SelfRequestConfig 类型加了，所以此处也要加上 👇🏻：

## 5. 划分模块

- 根据页面划分不同文件夹。
