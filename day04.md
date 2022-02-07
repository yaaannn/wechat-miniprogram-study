[toc]

# Day 04--基础加强

## 1 自定义组件

### 1.1 组件的创建与引用

#### 1.1.1 创建组件

1. 在项目的根目录中，鼠标右键，创建 components -> test 文件夹
2. 在新建的文件夹中，鼠标右键，新建 Component
3. 键入组件名称，建议不同组件放入不同文件夹

#### 1.1.2 引用组件

局部引用和全局引用

- 局部引用：组件只能在当前被引用的页面内使用

- 全局引用：组件可以在每个小程序页面中使用

#### 1.1.3 局部引用组件

在页面的.json 配置文件中应用组件的方式，称为局部引用

```json
{
  "usingComponents": {
    "my-test1":"/components/test/test"
  }
}
```

#### 1.1.4 全局引用组件

在 app.json 全局配置文件中引用组件 

#### 1.1.5 全局引用 VS 局部引用

根据组件的使用频率和范围，来选择合适的引用方式：

- 如果某组件在多个页面中经常被用到，建议进行“全局引用”

- 如果某组件只在特定的页面中被用到，建议进行“局部引用”

#### 1.1.6 组件和页面的区别

从表面来看，组件和页面都是由 .js、.json、.wxml 和 .wxss 这四个文件组成的。但是，组件和页面的 .js 与 .json 文件有明显的不同：

- 组件的 .json 文件中需要声明 "component": true 属性

- 组件的 .js 文件中调用的是 Component() 函数

- 组件的事件处理函数需要定义到 methods 节点中

### 1.2 样式

#### 1.2.1 组件样式隔离

默认情况下，组件的自定义样式只对当前组件生效，不会影响组件之外的 UI 结构

#### 1.2.2 组件样式隔离的注意点

- app.wxss 中的全局样式对组件无效
- 只有 class 选择器会有样式隔离效果，id 选择器、属性选择器、标签选择器不受样式隔离的影响

建议：在组件和引用组件的页面中使用 class 选择器

#### 1.2.3 修改组件样式隔离选项

默认情况下，自定义组件的样式隔离特性能够防止组件内外样式互相干扰的问题。但有时，我们希望在外界能够控制组件内部的样式，此时，可以通过 `styleIsolation` 修改组件的样式隔离选项

```
// components/test/test.js
Component({
  options:{
    styleIsolation:"isolated"
  }
})

```

#### 1.2.4 styleIsolation 的可选值

|  **可选值**  | 默认值 |                           **描述**                           |
| :----------: | :----: | :----------------------------------------------------------: |
|   isolated   |   是   | 表示启用样式隔离，在自定义组件内外，使用 class 指定的样式将不会相互影响 |
| apply-shared |   否   | 表示页面 wxss  样式将影响到自定义组件，但自定义组件  wxss  中指定的样式不会影响页面 |
|    shared    |   否   | 表示页面 wxss 样式将影响到自定义组件，自定义组件 wxss 中指定的样式也会影响页面和其他设置了 apply-shared 或 shared 的自定义组件 |

### 1.3 数据 方法 属性

#### 1.3.1 data 数据

在小程序组件中，用于组件模板的私有数据，需要定义到 data 节点中

#### 1.3.2 method 方法

在小程序中，事件处理函数和自定义方法需要定义到 method 节点中

#### 1.3.3 properties 属性

在小程序组件中，properties 是组件的对外属性，用来接收外界传递到组件中的数据

```js
// components/test/test.js
Component({

  options: {
    styleIsolation: "apply-shared"
  },

  /**
   * 组件的属性列表
   */
  properties: {
    // 第一种方式 简化方式
    // max:Number,

    // 第二种方式 完整方式

    max: {
      type: Number,
      value: 10
    }
  },
  /**
   * 组件的初始数据
   */
  data: {
    count: 0
  },

  /**
   * 组件的方法列表
   */
  methods: {
    // 点击事件处理函数

    addCount() {
      this.setData({
        count: this.data.count + 1
      })

      this._showCount()
    },

    // 自定义方法，建议下划线开头
    _showCount() {
      wx.showToast({
        title: 'count是' + this.data.count,
        icon: "none"
      })
    }
  }
})
```

#### 1.3.4 data 和 properties 的区别

在小程序中，data 和 properties 用法相同，他们都是可读可写的

- data 更倾向于存储组件的私有数据
- properties 更倾向与存储外界传到组件中的数据

#### 1.3.5 使用 setData 修改 properties 的值

由于 data 数据和 properties 属性在本质上没有任何区别，因此 properties 属性的值也可以用于页面渲染，或使用 setData 为 properties 中的属性重新赋值

### 1.4 数据监听器

#### 1.4.1 什么是数据监听器

数据监听器用于监听和响应任何属性和数据字段的变化，从而执行特定的操作

#### 1.4.2 数据监听器的基本用法

```html
<view>{{n1}}+{{n2}}={{sum}}</view>

<button bindtap="addN1">n1+1</button>
<button bindtap="addN2">n2+1</button>
```

```js
// components/test2/test2.js
Component({
  data: {
    n1: 0,
    n2: 0,
    sum: 0
  },
  methods: {
    addN1() {
      this.setData({
        n1: this.data.n1 + 1
      })
    },
    addN2() {
      this.setData({
        n2: this.data.n2 + 1
      })
    }
  },
  observers: {
    "n1, n2": function (newn1, newn2) {
      this.setData({
        sum: newn1 + newn2
      })
    }
  }
})
```

#### 1.4.3 监听对象的变化

数据监听器支持监听对象中单个或多个属性的变化

### 1.5 案例 - 数据监听器

```html
<view style="background-color: rgb({{fullColor}});" class="colorBok">
  颜色值{{fullColor}}
</view>

<button size="mini" type="default" bindtap="changeR">r</button>
<button size="mini" type="primary" bindtap="changeG">g</button>
<button size="mini" type="warn" bindtap="changeB">b</button>

```

```js
// components/test3/test3.js
Component({
  data: {
    rgb: {
      r: 0,
      g: 0,
      b: 0
    },
    fullColor: '0,0,0'
  },
  methods: {
    changeR() {
      this.setData({
        "rgb.r": this.data.rgb.r + 5 > 255 ? 255 : this.data.rgb.r + 5
      })
    },
    changeG() {
      this.setData({
        "rgb.g": this.data.rgb.g + 5 > 255 ? 255 : this.data.rgb.g + 5
      })
    },
    changeB() {
      this.setData({
        "rgb.b": this.data.rgb.b + 5 > 255 ? 255 : this.data.rgb.b + 5
      })
    }
  },
  observers: {
    'rgb.**': function (obj) {
      this.setData({
        fullColor: `${obj.r},${obj.g},${obj.b}`
      })

    }
  }
})
```

### 1.6 纯数据字段

#### 1.6.1 什么是纯数据字段

纯数据字段指的是那些不用于数据渲染的 data 字段

应用场景：例如有些情况下，某些 data 中的字段既不会展示在界面上，也不会传递给其他组件，仅仅在当前组件内部使用。带有这种特性的 data 字段适合被设置为纯数据字段。

好处：纯数据字段有助于提升页面更新的性能。

#### 1.6.2 使用规则

在 Component 构造器的 option 节点中，指定 pureDataPattern 为一个正则表达式，字段名符合这个正则表达式的字段将成为纯数据字段

#### 1.6.3 使用纯数据字段改造 1.5 案例

```js
Component({
options:{
  pureDataPattern:/^_/
},

  data: {
    _rgb: {
      r: 0,
      g: 0,
      b: 0
    },
    fullColor: '0,0,0'
  }
)}
```

### 1.7 组件的生命周期

#### 1.7.1 组件的全部生命周期函数

|  生命周期函数  |   **参数**   |               **描述说明**               |
| :------------: | :----------: | :--------------------------------------: |
| ***created***  |   ***无***   |     ***在组件实例刚刚被创建时执行***     |
| ***attached*** |   ***无***   |   ***在组件实例进入页面节点树时执行***   |
|     ready      |      无      |       在组件在视图层布局完成后执行       |
|     moved      |      无      | 在组件实例被移动到节点树另一个位置时执行 |
| ***detached*** |   ***无***   | ***在组件实例被从页面节点树移除时执行*** |
|     error      | Object Error |        每当组件方法抛出错误时执行        |

#### 1.7.2 组件的主要生命周期函数

在小程序组件中，最重要的生命周期函数有 3 个，分别是 created、**attached**、detached。它们各自的特点如下：

1. 组件实例**刚被创建好**的时候，created 生命周期函数会被触发
   - 此时还不能调用 setData
   - 通常在这个生命周期函数中，只应该用于给组件的 this 添加一些自定义的属性字段

2. 在组件**完全初始化完毕、进入页面节点树**后， attached 生命周期函数会被触发
   - 此时， this.data 已被初始化完毕
   - 这个生命周期很有用，绝大多数初始化的工作可以在这个时机进行（例如发请求获取初始数据）

3. 在组件**离开页面节点树**后， detached 生命周期函数会被触发
   - 退出一个页面时，会触发页面内每个自定义组件的 detached 生命周期函数
   - 此时适合做一些清理性质的工作

#### 1.7.3 lifetimes节点

在小程序组件中，生命周期函数可以直接定义在 Component 构造器的第一级参数中，可以在 lifetimes 字段内进行声明（这是推荐的方式，其优先级最高）

### 1.8 组件所在页面的生命周期

#### 1.8.1 什么是组件所在页面生命周期

有时，自定义组件的行为依赖于页面状态的变化，此时就需要用到组件所在页面的生命周期。在自定义组件中，组件所在页面的生命周期函数有如下 3 个，分别是：

| **生命周期函数** |  **参数**   |           **描述**           |
| :--------------: | :---------: | :--------------------------: |
|       show       |     无      |  组件所在的页面被展示时执行  |
|       hide       |     无      |  组件所在的页面被隐藏时执行  |
|      resize      | Object Size | 组件所在的页面尺寸变化时执行 |

#### 1.8.2 pageLifetimes 节点

```js
// components/test3/test3.js
Component({
  pageLifetimes: {
    show() {
      console.log("show");
    },
    hide() {
      console.log("hide");
    },
    resize() {
      console.log("resize");
    }
  }
})
```

### 1.9 插槽

#### 1.9.1 什么是插槽

在自定义组件的 wxml 结构中，可以提供一个 \<slot> 节点（插槽），用于承载组件使用者提供的 wxml 结构。

![](figures\08.png)

#### 1.9.2 单个插槽

```html
<view>
  <view>这里是组件内部结构</view>
  <slot></slot>
</view>
------------------
<my-test4>
  <view>这是通过插槽填充的内容</view>
</my-test4>
```

#### 1.9.3 启用多个插槽

在小程序的自定义组件中，需要使用多 \<slot> 插槽时，可以在组件的 .js 文件中，通过如下方式进行启用。

```js
// components/test4/test4.js
Component({
  options:{
    multipleSlots:true
  }
})
```

#### 1.9.4 定义并使用多个插槽

可以在组件的 .wxml 中使用多个 \<slot> 标签，以不同的 name 来区分不同的插槽。

```html
<view>
  <slot name="before"></slot>
  <view>这里是组件内部结构</view>
  <slot name="after"></slot>
</view>
```

在使用带有多个插槽的自定义组件时，需要用 slot 属性来将节点插入到不同的 \<slot> 中。示例代码如下：

```html
<my-test4>
  <view slot="before">这是通过插槽填充的内容</view>
</my-test4>
```

### 1.10 父子组件之间的通信

#### 1.10.1 父子组件之间通信的三种方式

1. 属性绑定
   - 用于父组件向子组件的指定属性设置数据，仅能设置 JSON 兼容的数据
2. 事件绑定
   - 用于子组件向父组件传递数据，可以传递任意数据
3. 获取组件实例
   - l父组件还可以通过 this.selectComponent() 获取子组件实例对象
   - 这样就可以直接访问子组件的任意数据和方法

#### 1.10.2 属性绑定

属性绑定用于实现父向子传值，而且只能传递普通类型的数据，无法将方法传递给子组件。父组件的示例代码如下：

```html
data: {
  count:0
}
------------
<my-test5 count="{{count}}"></my-test5>
<view>----------------</view>
<view>父组件中count：{{count}}</view>
```

子组件在 properties 节点中声明对应的属性并使用。示例代码如下：

```html
properties: {
  count:Number
}
-------------
<view>子组件中count：{{count}}</view>
```

#### 1.10.3 事件绑定

事件绑定用于实现子向父传值，可以传递任何类型的数据。使用步骤如下：

1. 在**父组件**的 js 中，定义一个函数，这个函数即将通过自定义事件的形式，传递给子组件

   ```js
   syncCount(){
     console.log("hello");
   },
   ```

2. 在**父组件**的 wxml 中，通过自定义事件的形式，将步骤 1 中定义的函数引用，传递给子组件

   ```html
   <my-test5 count="{{count}}" bind:sync="syncCount"></my-test5>
   <view>----------------</view>
   <view>父组件中count：{{count}}</view>
   ```

3. 在**子组件**的 js 中，通过调用 this.triggerEvent('自定义事件名称', { /* 参数对象 */ }) ，将数据发送到父组件

   ```js
   // components/test5/test5.js
   Component({
     properties: {
       count: Number
     },
     methods: {
       addCount() {
         this.setData({
           count: this.properties.count + 1
         })
         // 触发自定义事件，将数值同步给父组件
         this.triggerEvent('sync', {
           value: this.properties.count
         })
       }
     }
   })
   ```

4. 在**父组件**的 js 中，通过 e.detail 获取到子组件传递过来的数据

   ```js
   syncCount(e){
     console.log("hello");
     console.log(e);
     this.setData({
       count:e.detail.value
     })
   }
   ```

#### 1.10.4 获取组件实例

可在父组件里调用 this.selectComponent("id或class选择器") ，获取子组件的实例对象，从而直接访问子组件的任意数据和方法。调用时需要传入一个选择器，例如 this.selectComponent(".my-component")。

```js
getChild(){
  const child= this.selectComponent(".customA")
  // console.log(child);

  child.setData({
    count:child.properties.count +1  })
}
----------
<my-test5 count="{{count}}" bind:sync="syncCount" class="customA"></my-test5>

```

### 1.11 behaviors

#### 1.11.1 什么是 behaviors

behaviors 是小程序中，用于实现组件间代码共享的特性，类似于 Vue.js 中的 “mixins”。

#### 1.11.2 behaviors 的工作方式

每个 behavior 可以包含一组属性、数据、生命周期函数和方法。组件引用它时，它的属性、数据和方法会被合并到组件中。

每个组件可以引用多个 behavior，behavior 也可以引用其它 behavior。

#### 1.11.3 创建 behaviors

调用 Behavior(Object object) 方法即可创建一个共享的 behavior 实例对象，供所有的组件使用：

```js
module.exports =  Behavior({
  data:{
    username:"zs",
  },
  properties:{},
  methods:{}
})
```

#### 1.11.4 导入并使用 behaviors

在组件中，使用 require() 方法导入需要的 behavior，挂载后即可访问 behavior 中的数据或方法，示例代码如下：

```js
const myBehavior =  require('../../behaviors/my-behavior')

Component({
  behaviors:[myBehavior],
})
```

#### 1.11.5 behavior 中所有可用的节点

| **可用的节点** |   **类型**   | **是否必填** |      **描述**       |
| :------------: | :----------: | :----------: | :-----------------: |
|   properties   |  Object Map  |      否      |    同组件的属性     |
|      data      |    Object    |      否      |    同组件的数据     |
|    methods     |    Object    |      否      | 同自定义组件的方法  |
|   behaviors    | String Array |      否      | 引入其它的 behavior |
|    created     |   Function   |      否      |    生命周期函数     |
|    attached    |   Function   |      否      |    生命周期函数     |
|     ready      |   Function   |      否      |    生命周期函数     |
|     moved      |   Function   |      否      |    生命周期函数     |
|    detached    |   Function   |      否      |    生命周期函数     |

#### 1.11.6 同名字段的覆盖和组合规则*

组件和它引用的 behavior 中可以包含同名的字段，此时可以参考如下 3 种同名时的处理规则：

①同名的数据字段 (data)

②同名的属性 (properties) 或方法 (methods)

③同名的生命周期函数

关于详细的覆盖和组合规则，大家可以参考微信小程序官方文档给出的说明：

https://developers.weixin.qq.com/miniprogram/dev/framework/custom-component/behaviors.html

## 2 使用 npm 包

## 3 全局数据共享

## 4 分包

## 5 案例 - 自定义 tabBar



