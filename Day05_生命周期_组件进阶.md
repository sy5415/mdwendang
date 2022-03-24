# Day05

* 回顾

```
组件:  .vue文件  依赖webpack框架   结构 样式 交互 
      效率更好，复用！
      样式：scoped 私有化？生成data-9089a79  div[data-9089a79]
通信：
     原因：各个组件内部变量，各自作用域！
     引入：复用！
     需要1：看起来样子交互行为一样功能就是同一个组件，显示文字信息不同！
     知识1：父---->子
         子：准备自定义标准属性 props:["自定义属性"]  简单写法
         父：子组件在父组件注册，<子组件 自定义属性="要传入的值" :自定义属性="父级的变量"/>
         
     单向数据流（了解）:尽量要求大家，父级给子级传递数据，要修改数据，尽量通知父亲去修改！如果父级给的是：
          传递的是简单数据：报错！需要 子----->父
          传递的是复杂数据：不会报错！（传递是地址，如果子元素修改的话，相当于在父级同一个数据直接修改）
     
     知识2：子---->父
         子：在某个函数内，需要通知父亲；  this.$emit("自定义事件名称",要传递数据)
         父：子组件在父组件注册，<子组件  @自定义事件名称="父亲执行函数"/>
             父亲执行函数(形参){ 形参获取传递过来的数据 }
             
             
     知识3：组件与组件之间通信！
         // 1.main.js 
         let bus = new Vue();  // 事件总线
         Vue.prototype.bus = bus; // 以为未来所有组件（Vue实例化对象）都用于使用总线功能！
         // new Vue({}) 在这个之前
         
         // 2.某个组件1：发送
         this.bus.$emit("自定义事件名称",要传递数据);
         
         // 3.某个组件2：接收
         this.bus.$on("自定义事件名称",回调函数);  形参获取传递的数据
```



## 知识点自测

- [ ] 知道window.onload的作用
- [ ] ajax是什么, XMLHttpRequest的使用步骤
- [ ] jQ的 $.ajax底层是什么?
- [ ] Promise的作用
- [ ] async和await的作用-如何和Promise配合
- [ ] 同步异步的概念, 代码执行顺序
- [ ] 请求和响应, 以及JSON解析能力
- [ ] Vue基础, 组件使用, props传值, 组件通信, 计算属性使用, 对象引用类型使用

## 今日学习目标

1. 能够说出vue组件生命周期：

2. 能够掌握axios的使用   语法糖 ：promise  async await  

   ```
   fs.readfile("路径",回调)
   
   封装fs promise对象.then(回调)
   
   let res = await 封装fs promise对象
   ```

3. 能够了解$refs, $nextTick使用

4. 能够完成购物车案例开发

## 生命周期

### 介绍

> 一组件从 创建 到 销毁 的整个过程就是生命周期

![image-20210111193143574](images/image-20210111193143574.png)

Vue_生命周期

![image-20210511152835915](images/image-20210511152835915.png)

> 目标: **Vue** 框架内置函数，随着组件的生命周期阶段，自动执行

作用: 特定的时间点，执行特定的操作；

场景: 组件创建完毕后，可以在created 生命周期函数中发起Ajax 请求，从而初始化 data 数据

分类: 4大阶段8个方法

- 初始化
- 挂载
- 更新
- 销毁

| **阶段** | **方法名**    | **方法名** |
| -------- | ------------- | ---------- |
| 初始化   | beforeCreate  | created    |
| 挂载     | beforeMount   | mounted    |
| 更新     | beforeUpdate  | updated    |
| 销毁     | beforeDestroy | destroyed  |

[官网文档](https://cn.vuejs.org/v2/guide/instance.html#%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%9B%BE%E7%A4%BA)

下图展示了实例的生命周期。你不需要立马弄明白所有的东西，不过随着你的不断学习和使用，它的参考价值会越来越高。

钩子函数

![Day03](images/Day03.png)

### 初始化阶段

> 目标: 掌握初始化阶段2个钩子函数作用和执行时机

含义讲解:

1.new Vue() – Vue实例化(组件也是一个小的Vue实例)

2.Init Events & Lifecycle – 初始化事件和生命周期函数

3.beforeCreate – 生命周期钩子函数被执行

**4.Init injections&reactivity – Vue内部添加data和methods等**

5.created – 生命周期钩子函数被执行, 实例创建

6.接下来是编译模板阶段 –开始分析

7.Has el option? – 是否有el选项 – 检查要挂到哪里

​	没有. 调用$mount()方法

​	有, 继续检查template选项

![image-20210511153050932](images/image-20210511153050932.png)



components/Life.vue - 创建一个文件

```html

<script>
export default {
    data(){
        return {
            msg: "hello, Vue"
        }
    },
    // 一. 初始化
    // new Vue()以后, vue内部给实例对象添加了一些属性和方法, data和methods初始化"之前"
    beforeCreate(){
        console.log("beforeCreate -- 执行");
        console.log(this.msg); // undefined
    },
    
    // data和methods初始化以后  场景: 网络请求, 注册全局事件
    created(){
        console.log("created -- 执行");
        console.log(this.msg); // hello, Vue

        this.timer = setInterval(() => {
            console.log("哈哈哈");
        }, 1000)
    }
}
</script>
```

App.vue - 引入使用

```vue
<template>
  <div>
    <h1>1. 生命周期</h1>
 	<Life></Life>
  </div>
</template>

<script>
import Life from './components/Life'
export default {
  components: {
    Life
  }
}
</script>
```

### 挂载阶段

> 目标: 掌握挂载阶段2个钩子函数作用和执行时机

含义讲解:

1.template选项检查

​	有 - 编译template返回render渲染函数

​	无 – 编译el选项对应标签作为template(要渲染的模板)

2.虚拟DOM挂载成真实DOM之前

3.beforeMount – 生命周期钩子函数被执行

4.Create … – 把虚拟DOM和渲染的数据一并挂到真实DOM上

5.真实DOM挂载完毕

6.mounted – 生命周期钩子函数被执行

![image-20210511153649298](images/image-20210511153649298.png)



components/Life.vue - 创建一个文件

```html
<template>
  <div>
      <p>学习生命周期 - 看控制台打印</p>
      <p id="myP">{{ msg }}</p>
  </div>
</template>

<script>
export default {
    // ...省略其他代码
    
    // 二. 挂载
    // 真实DOM挂载之前
    // 场景: 预处理data, 不会触发updated钩子函数
    beforeMount(){
        console.log("beforeMount -- 执行");
        console.log(document.getElementById("myP")); // null

        this.msg = "重新值"
    },
    // 真实DOM挂载以后
    // 场景: 挂载后真实DOM
    mounted(){
        console.log("mounted -- 执行");
        console.log(document.getElementById("myP")); // p
    }
}
</script>
```

### 更新阶段

> 目标: 掌握更新阶段2个钩子函数作用和执行时机

含义讲解:

1.当data里数据改变, 更新DOM之前

2.beforeUpdate – 生命周期钩子函数被执行

3.Virtual DOM…… – 虚拟DOM重新渲染, 打补丁到真实DOM

4.updated – 生命周期钩子函数被执行

5.当有data数据改变 – 重复这个循环

![image-20210511154016777](images/image-20210511154016777.png)



components/Life.vue - 创建一个文件

准备ul+li循环, 按钮添加元素, 触发data改变->导致更新周期开始

```html
<template>
  <div>
      <p>学习生命周期 - 看控制台打印</p>
      <p id="myP">{{ msg }}</p>
      <ul id="myUL">
          <li v-for="(val, index) in arr" :key="index">
              {{ val }}
          </li>
      </ul>
      <button @click="arr.push(1000)">点击末尾加值</button>
  </div>
</template>

<script>
export default {
    data(){
        return {
            msg: "hello, Vue",
            arr: [5, 8, 2, 1]
        }
    },
    // ...省略其他代码

    // 三. 更新
    // 前提: data数据改变才执行
    // 更新之前
    beforeUpdate(){
        console.log("beforeUpdate -- 执行");
        console.log(document.querySelectorAll("#myUL>li")[4]); // undefined
    },
    // 更新之后
    // 场景: 获取更新后的真实DOM
    updated(){
        console.log("updated -- 执行");
        console.log(document.querySelectorAll("#myUL>li")[4]); // li
    }
}
</script>
```

### 销毁阶段

> 目标: 掌握销毁阶段2个钩子函数作用和执行时机

含义讲解:

1.当$destroy()被调用 – 比如组件DOM被移除(例v-if)

2.beforeDestroy – 生命周期钩子函数被执行

3.拆卸数据监视器、子组件和事件侦听器

4.实例销毁后, 最后触发一个钩子函数

5.destroyed – 生命周期钩子函数被执行

![image-20210511154330252](images/image-20210511154330252.png)



components/Life.vue - 准备生命周期方法(Life组件即将要被删除)

```html
<script>
export default {
    // ...省略其他代码
    
    // 四. 销毁
    // 前提: v-if="false" 销毁Vue实例
    // 场景: 移除全局事件, 移除当前组件, 计时器, 定时器, eventBus移除事件$off方法
    beforeDestroy(){
        // console.log('beforeDestroy -- 执行');
        clearInterval(this.timer)
    },
    destroyed(){
        // console.log("destroyed -- 执行");
    }
}
</script>
```

主要: App.vue - 点击按钮让Life组件从DOM上移除 -> 导致Life组件进入销毁阶段

```vue
<Life v-if="show"></Life>
<button @click="show = false">销毁组件</button>

<script>
    data(){
        return {
            show: true
        }
    },
</script>
```







## axios

### 获取

[axios文档](http://www.axios-js.com/)

特点

* 支持客户端发送Ajax请求
* 支持服务端Node.js发送请求
* 支持Promise相关用法
* 支持请求和响应的拦截器功能
* 自动转换JSON数据
* axios 底层还是原生js实现, 内部通过Promise封装的

axios的基本使用

```js

// 4. 全局配置
axios.defaults.baseURL = "http://123.57.109.30:3006"



axios({
      method: '请求方式', // get post
      url: '请求地址',
      data: {    // 拼接到请求体的参数,  post请求的参数
        xxx: xxx,
      },
      params: {  // 拼接到请求行的参数, get请求的参数
        xxx: xxx 
      }
    })
    .then(res => {
        console.log(res.data) // 后台返回的结果   回调函数（回：回头）
    })
    .catch(err => {
        console.log(err) // 后台报错返回
    })

// ajax:状态 400 500
```

```
app.get("/asx",function(req,res){
	req.body    post
    req.query   get
    req.params; 动态路由额参数
}); //nodesjs参数接受
```



> 目标: 调用文档最后_获取所有图书信息接口

功能: 点击调用后台接口, 拿到所有数据 – 打印到控制台

接口: 参考预习资料.md – 接口文档

引入: 下载axios, 引入后才能使用

效果:

![image-20210511154911824](images/image-20210511154911824.png)

例子如下:

components/UseAxios.vue

```vue
<template>
  <div>
    <p>1. 获取所有图书信息</p>
    <button @click="getAllFn">点击-查看控制台</button>
  </div>
</template>

<script>
// 目标1: 获取所有图书信息
// 1. 下载axios  yarn add axios
// 2. 引入axios
// 3. 发起axios请求
    
import axios from "axios";
import axios from "axios";
export default {
  methods: {
    getAllFn() {
      axios({
        url: "http://123.57.109.30:3006/api/getbooks",
        method: "GET", // 默认就是GET方式请求, 可以省略不写
      }).then((res) => {
        console.log(res);
      });
      // axios()-原地得到Promise对象
    },
  }
};
</script>
```

### 查询

> 目标: 调用接口-获取某本书籍信息

功能: 点击调用后台接口, 查询用户想要的书籍信息 – 打印到控制台

接口: 参考预习资料.md – 接口文档

效果:

![image-20210511160538891](images/image-20210511160538891.png)

例子如下:

components/UseAxios.vue

```vue
<template>
  <div>
    <p>2. 查询某本书籍信息</p>
    <input type="text" placeholder="请输入要查询 的书名" v-model="bName" />
    <button @click="search">查询</button>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      bName: ""
    };
  },
  methods: {
    // ...省略了查询所有的代码
    // 查询：使用 async await语法
    async search(){
      let res =  await axios({
        url: "http://123.57.109.30:3006/api/getbooks",
        method: "GET",
        params:{
          id:1,
          bookname:"西游记"
        }
      });
      console.log(res);
    }
  },
};
</script>
```

### 发布

> 目标: 完成发布书籍功能

功能: 点击新增按钮, 把用户输入的书籍信息, 传递给后台 – 把结果打印在控制台

接口: 参考预习资料.md – 接口文档

效果:

![image-20210511161239034](images/image-20210511161239034.png)

例子如下:

components/UseAxios.vue

```vue
<template>
  <div>
    <p>3. 新增图书信息</p>
    <div>
        <input type="text" placeholder="书名" v-model="bookObj.bookname">
    </div>
    <div>
        <input type="text" placeholder="作者" v-model="bookObj.author">
    </div>
    <div>
        <input type="text" placeholder="出版社" v-model="bookObj.publisher">
    </div>
    <button @click="sendFn">发布</button>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      bName: "",
      bookObj: { // 参数名提前和后台的参数名对上-发送请求就不用再次对接了
          bookname: "",
          author: "",
          publisher: ""
      }
    };
  },
  methods: {
    // ...省略了其他代码
    sendFn(){
       axios({
           url: "http://123.57.109.30:3006/api/addbook",
           method: "POST",
           data: {
               appkey: "7250d3eb-18e1-41bc-8bb2-11483665535a",
               ...this.bookObj
            // 等同于下面
            // bookname: this.bookObj.bookname,
            // author: this.bookObj.author,
            // publisher: this.bookObj.publisher
           }
       }) 
    }
  },
};
</script>
```

### 全局配置

> 目标: 避免前缀基地址, 暴露在逻辑页面里, 统一设置

```js
axios.defaults.baseURL = "http://123.57.109.30:3006"

// 所有请求的url前置可以去掉, 请求时, axios会自动拼接baseURL的地址在前面
getAllFn() {
    axios({
        url: "/api/getbooks",
        method: "GET", // 默认就是GET方式请求, 可以省略不写
    }).then((res) => {
        console.log(res);
    });
    // axios()-原地得到Promise对象
},
```





## $refs 

### 获取DOM

> 目标: 利用 ref 和 $refs 可以用于获取 dom 元素

components/More.vue

```vue
<template>
  <div>
      <p>1. 获取原生DOM元素</p>
      <h1 id="h" ref="myH">我是一个孤独可怜又能吃的h1</h1>
  </div>
</template>

<script>
// 目标: 获取组件对象
// 1. 创建组件/引入组件/注册组件/使用组件
// 2. 组件起别名ref
// 3. 恰当时机, 获取组件对象
export default {
    mounted(){
        console.log(document.getElementById("h")); // h1
        console.log(this.$refs.myH); // h1
    }
}
</script>

<style>

</style>
```

> 总结: 通过id / ref, 都可以获取原生DOM标签

### 获取组件对象

> 目标: 获取组件对象, 调用组件里方法

components/Child/Demo.vue

```vue
<template>
  <div>
      <p>我是Demo组件</p>
  </div>
</template>

<script>
export default {
    methods: {
        fn(){
            console.log("demo组件内的方法被调用了");
        }
    }
}
</script>
```

More.vue - 获取组件对象 - 调用组件方法

```vue
<template>
  <div>
      <p>1. 获取原生DOM元素</p>
      <h1 id="h" ref="myH">我是一个孤独可怜又能吃的h1</h1>
      <p>2. 获取组件对象 - 可调用组件内一切</p>
      <Demo ref="de"></Demo>
  </div>
</template>

<script>
// 目标: 获取组件对象
// 1. 创建组件/引入组件/注册组件/使用组件
// 2. 组件起别名ref
// 3. 恰当时机, 获取组件对象
import Demo from './Child/Demo'
export default {
    mounted(){
        console.log(document.getElementById("h")); // h1
        console.log(this.$refs.myH); // h1

        let demoObj = this.$refs.de;
        demoObj.fn()
    },
    components: {
        Demo
    }
}
</script>
```

> 总结: ref定义值, 通过$refs.值 来获取组件对象, 就能继续调用组件内的变量

* 父级组件对子组件：
  * 传入数据：父---->子；
  * **ref随意获取子元素内部：数据和方法；（子组件：设计方法，具有形参！）**



## $nextTick使用

### 基本使用

> 目标: 点击count++, 马上通过"原生DOM"拿标签内容, 无法拿到新值

components/Move.vue - 继续新增第三套代码

```vue
<template>
  <div>
      <p>1. 获取原生DOM元素</p>
      <h1 id="h" ref="myH">我是一个孤独可怜又能吃的h1</h1>
      
      
      <p>2. 获取组件对象 - 可调用组件内一切</p>
      <Demo ref="de"></Demo>
      
      
      <p>3. vue更新DOM是异步的</p>
      <p ref="myP">{{ count }}</p>
      <button @click="btn">点击count+1, 马上提取p标签内容</button>
  </div>
</template>

<script>
// 目标: 获取组件对象
// 1. 创建组件/引入组件/注册组件/使用组件
// 2. 组件起别名ref
// 3. 恰当时机, 获取组件对象
import Demo from './Child/Demo'
export default {
    mounted(){
        console.log(document.getElementById("h")); // h1
        console.log(this.$refs.myH); // h1

        let demoObj = this.$refs.de;
        demoObj.fn()
    },
    components: {
        Demo
    },
    data(){
        return {
            count: 0
        }
    },
    methods: {
        btn(){
            this.count++; // vue监测数据更新, 开启一个DOM更新队列(异步任务)
            console.log(this.$refs.myP.innerHTML); // 0

            // 原因: Vue更新DOM异步
            // 解决: this.$nextTick()
            // 过程: DOM更新完会挨个触发$nextTick里的函数体
            this.$nextTick(() => {
                console.log(this.$refs.myP.innerHTML); // 1
            })
        }
    }
}
</script>
```

> 总结: 因为DOM更新是异步的

### 使用场景

> 目标: 点击搜索按钮, 弹出聚焦的输入框, 按钮消失

![$nextTick使用](images/$nextTick使用.gif)

components/Tick.vue

```vue
<template>
  <div>
      <input ref="myInp" type="text" placeholder="这是一个输入框" v-if="isShow">
      <button v-else @click="btn">点击我进行搜索</button>
  </div>
</template>

<script>
// 目标: 点按钮(消失) - 输入框出现并聚焦
// 1. 获取到输入框
// 2. 输入框调用事件方法focus()达到聚焦行为
export default {
    data(){
        return {
            isShow: false
        }
    },
    methods: {
        btn(){
            this.isShow = true;
            
            
            this.$refs.myInp.focus(); // 没有效果
            
            // 原因: data变化更新DOM是异步的
            // 输入框还没有挂载到真实DOM上
            // 解决:
            // this.$nextTick(() => {
            //     this.$refs.myInp.focus()
            // })
        }
        //async btn(){  
        //    this.isShow = true;
        //    
        //    // 扩展: await取代回调函数
        //    // $nextTick()原地返回Promise对象
        //    await this.$nextTick()
        //    this.$refs.myInp.focus()
        //}
    }
}
</script>
```





## 购物车

![购物车全效果](images/购物车全效果.gif)

### 初始化

> 目标: 初始化新项目, 清空不要的东西, 下载bootstrap库, 下载less模块

```bash
vue create shopcar
yarn add bootstrap
yarn add less less-loader@5.0.0 -D
```

图示:

![image-20210307092110985](images/image-20210307092110985.png)

1. 按照需求, 把项目页面拆分成几个组件, 在components下创建

* MyHeader组件

* MyFooter组件
* MyGoods组件 - 商品

2. 然后引入到App.vue上注册

3. 在main.js中引入bootStrap库

```js
import "bootstrap/dist/css/bootstrap.css" // 引入第三方包里的某个css文件
```

MyHeader.vue

```vue
<template>
  <div class="my-header">购物车案例</div>
</template>

<script>
export default {

}
</script>

<style lang="less" scoped>
  .my-header {
    height: 45px;
    line-height: 45px;
    text-align: center;
    background-color: #1d7bff;
    color: #fff;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 2;
  }
</style>
```

MyGoods.vue

```vue
<template>
  <div class="my-goods-item">
    <div class="left">
      <div class="custom-control custom-checkbox">
        <input type="checkbox" class="custom-control-input" id="input"
        >
        <label class="custom-control-label" for="input">
          <img src="http://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg" alt="">
        </label>
      </div>
    </div>
    <div class="right">
      <div class="top">商品名字</div>
      <div class="bottom">
        <span class="price">¥ 100</span>
        <span>
            <div class="my-counter">
                <button type="button" class="btn btn-light" >-</button>
                <input type="number" class="form-control inp" >
                <button type="button" class="btn btn-light">+</button>
            </div>
        </span>
      </div>
    </div>
  </div>
</template>

<script>
export default {

}
</script>

<style lang="less" scoped>
.my-goods-item {
  display: flex;
  padding: 10px;
  border-bottom: 1px solid #ccc;
  .left {
    img {
      width: 120px;
      height: 120px;
      margin-right: 8px;
      border-radius: 10px;
    }
    .custom-control-label::before,
    .custom-control-label::after {
      top: 50px;
    }
  }
  .right {
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    .top{
        font-size: 14px;
        font-weight: 700;
    }
    .bottom {
      display: flex;
      justify-content: space-between;
      padding: 5px 0;
      align-items: center;
      .price {
        color: red;
        font-weight: bold;
      }
    }
  }
}

    
.my-counter {
  display: flex;
  .inp {
    width: 45px;
    text-align: center;
    margin: 0 10px;
  }
  .btn, .inp{
    transform: scale(0.9);
  }
}
</style>
```

> components/MyFooter.vue

```vue
<template>
  <!-- 底部 -->
  <div class="my-footer">
    <!-- 全选 -->
    <div class="custom-control custom-checkbox">
      <input type="checkbox" class="custom-control-input" id="footerCheck">
      <label class="custom-control-label" for="footerCheck">全选</label>
    </div>
    <!-- 合计 -->
    <div>
      <span>合计:</span>
      <span class="price">¥ 0</span>
    </div>
    <!-- 按钮 -->
    <button type="button" class="footer-btn btn btn-primary">结算 ( 0 )</button>
  </div>
</template>

<script>
export default {
  
}
</script>

<style lang="less" scoped>
.my-footer {
  position: fixed;
  z-index: 2;
  bottom: 0;
  width: 100%;
  height: 50px;
  border-top: 1px solid #ccc;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 10px;
  background: #fff;

  .price {
    color: red;
    font-weight: bold;
    font-size: 15px;
  }
  .footer-btn {
    min-width: 80px;
    height: 30px;
    line-height: 30px;
    border-radius: 25px;
    padding: 0;
  }
}
</style>
```

* app.vue

```vue
<template>
  <div>
    <MyHeader></MyHeader>
    <div class="main">
      <MyGoods></MyGoods>
    </div>
    <MyFooter></MyFooter>
  </div>
</template>

<script>

import MyHeader from './components/MyHeader'
import MyGoods from './components/MyGoods'
import MyFooter from './components/MyFooter'
export default {
  components: {
    MyHeader,
    MyGoods,
    MyFooter
  },
}
</script>

<style scoped>
  .main{
    padding-top: 45px;
    padding-bottom: 50px;
  }
</style>
```



### 头部自定义

> 目的: 头部的标题, 颜色, 背景色可以随便修改, props类型的校验

思路

1. 在MyHeader.vue中准备props里变量, 然后使用
2. 在使用MyHeader.vue组件时, 传入相应的值 (color和backgroundColor)

MyHeader.vue

```vue
<template>
  <div class="my-header" :style="{backgroundColor: background, color}">{{ title }}</div>
</template>

<script>
// 目标: 让Header组件支持不同的项目 - 自定义
// 1. 分析哪些可以自定义 (背景色, 文字颜色, 文字内容)
// 2. (新) 可以对props的变量的值 进行校验
// 3. 内部使用props变量的值
// 4. 外部使用时, 遵守变量名作为属性名, 值的类型遵守
export default {
    props: {
        background: String, // 外部插入此变量的值, 必须是字符串类型, 否则报错
        color: {
            type: String, // 约束color值的类型
            default: "#fff" // color变量默认值(外部不给 我color传值, 使用默认值)
        },
        title: {
            type: String,
            required: true // 必须传入此变量的值
        }
    }
}
</script>

<style lang="less" scoped>
  .my-header {
    height: 45px;
    line-height: 45px;
    text-align: center;
    background-color: #1d7bff;
    color: #fff;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 2;
  }
</style>
```

App.vue传入相应自定义的值

```vue
<MyHeader title="购物车案例"></MyHeader>
```

> 总结:  
>
> props: [] - 只能声明变量和接收, 不能类型校验
>
> props: {} - 声明变量和校验类型规则 - 外部传入值不对则报错

### 数据

> 目标: 使用axios把数据请求回来

数据地址: https://www.escook.cn/api/cart  (get方式)

1. 下载axios

```vue
yarn add axios
```

2. main.js - 原型上挂载

```js
// 目标: 请求数据 - 打印
// 1. 下载axios库, main.js - 全局绑定属性 (确保任意.vue文件可以都访问到这个axios方法)
import axios from 'axios'
// 2. 基础地址
axios.defaults.baseURL = "https://www.escook.cn"
// 3. axios方法添加到Vue的原型上
Vue.prototype.$axios = axios

new Vue({
    render: h => h(App),
}).$mount('#app')

```

3. App.vue请求使用

```vue
<script>
export default {
  data(){
    return {
      list: [] // 商品所有数据
    }
  },
  created(){
    // 不必在自己引入axios变量, 而是直接使用全局属性$axios
    this.$axios({
      url: "/api/cart"
    }).then(res => {
      console.log(res);
      this.list = res.data.list
    })
  }
}
</script>
```

> 总结: 利用axios, 调用接口, 把数据请求回来

### 商品

App.vue

```vue
<MyGoods v-for="obj in list" 
         :key="obj.id"
         :gObj="obj"
></MyGoods>
```

MyGoods.vue

```vue
<template>
  <div class="my-goods-item">
    <div class="left">
      <div class="custom-control custom-checkbox">
        <input type="checkbox" class="custom-control-input" :id="gObj.id"
        v-model="gObj.goods_state"
        >
        <label class="custom-control-label" :for="gObj.id">
          <img :src="gObj.goods_img" alt="">
        </label>
      </div>
    </div>
    <div class="right">
      <div class="top">{{ gObj.goods_name }}</div>
      <div class="bottom">
        <span class="price">¥ {{ gObj.goods_price }}</span>
        <span>
            <div class="my-counter">
              <button type="button" class="btn btn-light" 
              :disabled="gObj.goods_count == 1"
              @click="gObj.goods_count > 1 && gObj.goods_count--">-</button>
              <input type="number" class="form-control inp" v-model.number="gObj.goods_count">
              <button type="button" class="btn btn-light" @click="gObj.goods_count++">+</button>
            </div>
        </span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  // 另外一种写法：规定类型
  props: {
    gObj: Object
  },
  watch: {
    gObj: {
      deep: true,
      handler(){ // 拿到商品数量, 判断小于1, 强制修改成1
        if (this.gObj.goods_count < 1) {
          this.gObj.goods_count = 1
        }
      }
    }
  }
}
</script>
```





### 全选

> 目标: 在底部组件上, 完成全选功能

![image-20210223141427684](images/image-20210223141427684.png)

思路: 

1. 点击获取它的选中状态
2. 同步给上面每个小选框 - 而小选框的选中状态又在数组里
3. 把数组传给MyFooter, 然后更新即可 - 因为对象都是引用关系的

app.vue

```vue
<mfooter :arr="list"></mfooter>
```

MyFooter.vue

```vue
<template>
  <!-- 底部 -->
  <div class="my-footer">
    <!-- 全选 -->
    <div class="custom-control custom-checkbox">
      <input type="checkbox" class="custom-control-input" id="footerCheck" v-model="isAll"> 
      <label class="custom-control-label" for="footerCheck">全选</label>
    </div>
    <!-- 合计 -->
    <div>
      <span>合计:</span>
      <span class="price">¥ 0</span>
    </div>
    <!-- 按钮 -->
    <button type="button" class="footer-btn btn btn-primary">结算 ( 0 )</button>
  </div>
</template>

<script>
export default {
  props: {
    arr: Array
  },
  computed: {
    isAll: {
      set(val){ // val就是关联表单的值(true/false)
        this.arr.forEach(obj => obj.goods_state = val)
      },
      get(){
        // 查找小选框关联的属性有没有不符合勾选的条件
        // 直接原地false
        return this.arr.every(obj => obj.goods_state === true)
      }
    },
    
    
  }
}
</script>
```



### 总计

> 目标: 完成底部组件, 显示选中的商品的总数量

MyFooter.vue

```js
allCount(){
    return this.arr.reduce((sum, obj) => {
        if (obj.goods_state === true) { // 选中商品才累加数量
            sum += obj.goods_count;
        }
        return sum;
    }, 0);
    
    // 
    //let sum = 0;
    //this.list.forEach();
    //return sum;
},
```

> 总结: 对象之间是引用关系, 对象值改变, 所有用到的地方都跟着改变

> 目标: 完成选中商品计算价格

components/MyFooter.vue

```js
allPrice(){
    return this.arr.reduce((sum, obj) => {
        if (obj.goods_state){
            sum += obj.goods_count * obj.goods_price
        }
        return sum;
    }, 0)
}
```

> 总结: 把数组传给了MyFooter组件, 统计总价



## 今日总结

vue的生命周期哪4个阶段, 哪8个方法

axios是什么, 底层是什么, 具体如何使用

axios返回的是什么, 如何接收结果

知道ref和$refs使用和作用以及场景

知道$nextTick的作用

跟着老师的视频完成购物车案例

## 面试题

### 1、Vue 的 nextTick 的原理是什么? （高薪常问）

​    \1. 为什么需要 nextTick ，Vue 是异步修改 DOM 的并且不鼓励开发者直接接触 DOM，但有时候业务需要必须对数据更改--刷新后的 DOM 做相应的处理，这时候就可以使用 Vue.nextTick(callback)这个 api 了。

​    \2. 理解原理前的准备 首先需要知道事件循环中宏任务和微任务这两个概念,常见的宏任务有 script, setTimeout, setInterval, setImmediate, I/O, UI rendering 常见的微任务有 process.nextTick(Nodejs),Promise.then(), MutationObserver;

​    \3. 理解 nextTick 的原理正是 vue 通过异步队列控制 DOM 更新和 nextTick 回调函数先后执行的方式。如果大家看过这部分的源码，会发现其中做了很多 isNative()的判断，因为这里还存在兼容性优雅降级的问题。可见 Vue 开发团队的深思熟虑，对性能的良苦用心。

### 2、vue生命周期总共分为几个阶段？（必会）

   Vue 实例从创建到销毁的过程，就是生命周期。也就是从开始创建、初始化数据、编译模板、挂载Dom→渲染、更新→渲染、卸载等一系列过程，我们称这是 Vue 的生命周期。

**1****）beforeCreate**

​    在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。

**2****）created**

​    在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测 (data observer)， 属性和方法的运算，watch/event 事件回调。然而，挂载阶段还没开始，$el 属性目前不可见。

**3****）beforeMount**

​    在挂载开始之前被调用：相关的 render 函数首次被调用。

**4****）mounted**

​    el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用该钩子。如果 root 实例挂载了一个文档内元素，当 mounted 被调用时 vm.$el 也在文档内。

**5****）beforeUpdate**

​    数据更新时调用，发生在虚拟 DOM 打补丁之前。这里适合在更新之前访问现有的 DOM，比如手动移除已添加的事件监听器。该钩子在服务器端渲染期间不被调用，因为只有初次渲染会在服务端进行。

**6****）updated**

​    由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。

**7****）activated**

​    keep-alive 组件激活时调用。该钩子在服务器端渲染期间不被调用。

**8****）deactivated**

​    keep-alive 组件停用时调用。该钩子在服务器端渲染期间不被调用。

**9****）beforeDestroy**

​    实例销毁之前调用。在这一步，实例仍然完全可用。该钩子在服务器端渲染期间不被调用。

**10****）destroyed**

​    Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。该钩子在服务器端渲染期间不被调用。

**11****）errorCaptured（2.5.0+ 新增）**

​    当捕获一个来自子孙组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回 false 以阻止该错误继续向上传播。

### 3、第一次加载页面会触发哪几个钩子函数？（必会）

   当页面第一次页面加载时会触发 beforeCreate, created, beforeMount, mounted 这几个钩子函数





## 今日作业

把课上购物车再来一遍





## 接口文档

> axios请求接口使用

根域名: http://123.57.109.30:3006

### 获取

* 请求方式:  GET
* 请求地址: 根域名/api/getbooks
* 请求参数: 

不传参获取所有默认书籍, 也可以选择传递下面任意1-多个参数, 获取指定的相关书籍信息

| 参数名称  | 参数类型 | 是否必选 | 参数说明 |
| --------- | -------- | -------- | -------- |
| id        | Number   | 否       | 图书Id   |
| bookname  | String   | 否       | 图书名称 |
| author    | String   | 否       | 作者     |
| publisher | String   | 否       | 出版社   |
| appkey    | String   | 否       | 个人ID   |

* 返回示例:

```json
{
  "status": 200, // 状态码200都代表完全成功 - 无任何意义随便写, 只是方便前端写判断条件
  "msg": "获取图书列表成功", // 后台返回的提示消息, 随便写, 只是方便前端直接打印提示消息
  "data": [ // 后台返回的数据
    { "id": 1, "bookname": "西游记", "author": "吴承恩", "publisher": "北京图书出版社" },
    { "id": 2, "bookname": "红楼梦", "author": "曹雪芹", "publisher": "上海图书出版社" },
    { "id": 3, "bookname": "三国演义", "author": "罗贯中", "publisher": "北京图书出版社" }
  ]
}

```

### 添加

* 请求方式: POST
* 请求地址: 根域名/api/addbook
* 请求参数:

| 参数名称  | 参数类型 | 是否必选 | 参数说明                                          |
| --------- | -------- | -------- | ------------------------------------------------- |
| bookname  | String   | 是       | 图书名称                                          |
| author    | String   | 是       | 作者                                              |
| publisher | String   | 是       | 出版社                                            |
| appkey    | String   | 是       | 个人ID - 用'7250d3eb-18e1-41bc-8bb2-11483665535a' |

* 返回示例:

```js
{
    "status": 201, // 后台返回数据逻辑层的状态码, 201代表后台已经新增加了一个资源
    "data": {
        "author": "施大神"
        "bookname": "水浒传2"
        "id": 41
        "publisher": "未来出版社"
    }
    "msg": "添加图书成功"
}
```

