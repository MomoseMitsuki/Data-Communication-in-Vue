# 组件通信 & 状态管理

在Vue中，组件之间的数据通信可以通过多种方式实现，包括使用 props 和 $emit 进行父子组件间的直接通信，使用 provide / inject 在多个组件层级间共享数据，以及利用事件总线 eventbus 进行跨组件通信。Vuex 和 Pinia 则提供了集中式的状态管理，简化了全局状态的访问和管理。 



### 简单的父子数据传递 props & $emit

原理：利用 **自定义属性** 和 **自定义事件** 来传递

> props官网说明：https://cn.vuejs.org/guide/components/props.html
>
> 事件官方说明：https://cn.vuejs.org/guide/components/events.html

##### Vue 2

父传子，通过在子组件声明自定义属性，在父组件传入数据进入子组件

1. props传入数组

```vue
props:['msg','code']
```

2. props传入对象，添加简单的类型校验

```vue
props:{						// 简单的类型校验
	msg:string
    code:number
}
```

3. props传入对象，添加复杂的设置：传入类型、默认值、是否必传、校验函数

```vue
props:{
	msg:{
		type:type,						// 传入类型
       	default:val,					// 默认值
       	required:boolean,				// 是否必须
       	validator(value,props){			// 校验函数
       		...
       		return (true or false)
       	}
}
```


复杂类型（如对象、数组、函数）default，validator会有区别，这里暂不讲解，推荐去看官方文档

```vue
<template>
	// 传入数据 在子组件中使用
	<MyComponent :msg="'hello'" :code="100"></MyComponent>
</template>
```



子传父，通过在子组件触发自定义事件，携带参数来到父组件层面

```vue
// 子组件
<template>
	<button @click="sendMessage">触发事件</button>
</template>
<script>
	export default {
        data(){
            return { msg:'ciallo~' }
        }
        methods:{
            sendMessage(){
                // 触发父组件层面上的事件
                this.$emit('getMessage',this.msg)
            }
        }
    }
</script>
```

```vue
// 父组件
<template>
	<MyComponent @getMessage="setTitle"></MyComponent>
	<p>{{ title }}</p>
</template>
<script>
	export default {
        data(){
            return { title:'' }
        }
        methods:{
            setTitle(msg){
                this.title = msg
            }
		}
    }
</script>
```

props 的数据是外部的  →  不能直接改，要遵循单向数据流

>  单向数据流：父级 props 的数据更新，会向下流动，影响子组件。这个数据流动是单向的



##### Vue 3

Vue 3 的 实现逻辑和 Vue 2 基本上像

我们使用  `defineProps()` 编译器宏函数 定义 props，向子组件传递数据

使用  `defineEmit()` 编译器宏函数定义 emit ，向父组件传递数据

使用 `defineExpose()` 向父组件暴露自身的属性和方法

> 在父组件不能直接使用，因为`<script setup>` 发生在实例化之前，无法访问到属性



> 编译器宏函数：编译器宏函数是在编译时处理的宏定义，它可以将宏函数的调用替换为对应的代码块，而不是像普通宏一样简单地进行文本替换。

```vue
// defineProps defineEmits defineExpose 的使用
<script setup>
	import { ref, defineProps, defineEmits, defineExpose } from 'vue'	//导入对应的函数
    const message = ref('Hello, The message comes from The Son Component')
    const props = defineProps({			// 自定义属性
        'msg':String,
        'code':Number
    })
    const Emits = defineEmits(['getMessage'])	// 声明自定义事件名
    const send = () => {
        emits('getMessage',message)		// 触发自定义事件
    }
    defineExpose({			// 将数值名作为该组件实例的属性向外暴露
        message
    })
</script>
```





### 跨层级组件共享数据 provide & inject（依赖注入）

作用：解决跨层级组件共享数据问题

使用props：

![](https://cn.vuejs.org/assets/prop-drilling.XJXa8UE-.png)

使用 provide & inject 实现跨层级数据共享：

![](https://cn.vuejs.org/assets/provide-inject.C0gAIfVn.png)







### 事件总线 eventBus

作用：非父子组件之间，进行简易消息传递。（复杂场景 → Vuex）

##### Vue 2 中 eventBus 的实现

1. 我们需要创建一个都能访问到的事件总线（空 Vue 实例） → utils/EventBus.js

```javascript
import Vue from 'vue'
const Bus = new Vue()
export default Bus
```

实质上EventBus是一个不具备 DOM 的组件，它具有的仅仅只是它的实例方法而已，因此它非常的轻便

2. A组件（接收方），监听 **Bus实例** 的事件

```vue
// 生命周期hook函数，在创建Vue实例后注册监听事件
created(){
	// 监听 sendMsg 事件，得到参数传入自身data中的msg
	Bus.$on('sendMsg',msg => {
		this.msg = msg
	})
}
data(){
	return { msg }
}
```

3. B组件（发送方），触发 **Bus实例** 的事件

```vue
Bus.$emit('sendMsg','这是一个消息')
```

当我们使用eventbus的一个组件被销毁时，它所注册的事件依然会存在，有可能会造成内存泄漏

4. 所以我们需要在组件销毁的同时注销事件

```vue
beforeDestory(){
	Bus.$off('sendMsg')
}
// Bus.$off('事件名')    移除bus内对该事件的所有监听
// Bus.$off()			移除bus内所有的事件
```



##### Vue 3 中 eventBus 的实现

Vue 3 与 Vue 2 的 eventBus实现方法不同，Vue 3 不再使用 Vue 实例作为 EventBus，而是可以使用一个简单的 JavaScript 对象或 `mitt` 库来实现事件总线。下面是两种常见的方法来实现 EventBus。

```javascript
// eventBus.js
const EventBus = {
  events: {},
  // 注册事件
  on(event, listener) {
     // 检查该events事件对象内是否已经注册过该事件
    if (!this.events[event]) {
        // 为 events 新赠属性，并且值为一个数组
      this.events[event] = [];
    }
      // 向对应的事件属性下 新赠方法
    this.events[event].push(listener);
  },
  // 触发事件
  emit(event, data) {
      // 检查是否注册了该事件
    if (this.events[event]) {
        // 遍历 事件属性内存储的函数数组，并依次传入参数遍历运行函数
      this.events[event].forEach(listener => listener(data));
    }
  },
  // 移除事件
  off(event, listener) {
      // 检查是否注册了该事件
    if (!this.events[event]) return;
      // 遍历函数数组，移除传入的函数名
    this.events[event] = this.events[event].filter(l => l !== listener);
  }
};
export default EventBus;
```

或者我们可以使用 `mitt` 库，来完成 eventBus.js

> npm i mitt

```javascript
import mitt from 'mitt'
const EventBus = mitt()
export default EventBus
// EventBus.on('NameOfEvent',callbackFn)		注册监听事件
// EventBus.emit('NameOfEvent',arg)				触发监听事件
// EventBus.off('NameOfEvent')					移除监听事件
```

在 组件 内使用

```vue
// 监听方组件
<script setup>
	import EventBus from 'eventBus.js'
    import { ref,onMount,onBeforeUnmount } from 'vue'
    const message = ref('')
    const getMessage = msg => {
		message.value = msg
    }
    onMounted(() => {
        EventBus.on('sendMessage',getMessage)
    })
    onBeforeUnmount(() => {
        EventBus.off('sendMessage',getMessage)
    })
</script>
```

```vue
// 发送方组件
<script setup>
	import EventBus from 'eventBus.js'
    import { ref } from 'vue'
    const msg = ref('hello world')
    const sendMessage = () => {
        EventBus.emit('sendMessage',msg)
    }
</script>
```



使用 eventBus 所产生的问题：

- **单页应用特性**：Vue是单页应用（SPA），在页面刷新时，整个应用的状态和上下文都会被重置。

- **事件监听器失效**：当页面刷新时，EventBus实例被销毁，之前注册的事件监听器也随之消失。

- **业务影响**：如果业务逻辑依赖于EventBus进行通信，刷新页面后可能会导致数据丢失或功能失效。



### 集中管理多个组件数据 Vuex



### Vue 3 中状态管理的替代品 Pinia



### 总结：

**Props 和 $emit**

- **使用场景**：适用于父子组件之间的通信。

- **优势**：

  - 简单直观，易于理解。
  - 通过props传递数据，使用$emit发送事件，保持了组件的单向数据流。

  

**Provide 和 Inject**

- **使用场景**：适用于祖先组件与后代组件之间的通信，尤其是当组件层级较深时。

- **优势**：

  - 避免了过多的props传递（prop drilling）。
  - 适合紧密耦合的组件，能够在不直接传递props的情况下共享数据。

  

**事件总线（EventBus）**

- **使用场景**：适用于不直接相关的组件之间的通信。

- **优势**：

  - 允许组件之间的松耦合通信。
  - 适合小型应用，但在大型应用中可能导致维护困难。

  

**Vuex**

- **使用场景**：适用于需要集中管理状态的中大型应用。

- **优势**：

  - 提供了一个集中式的状态管理方案，便于跟踪状态变化。
  - 支持时间旅行调试和状态快照，方便开发和调试。

  

**Pinia**

- **使用场景**：作为Vuex的现代替代品，适用于Vue 3及以上版本。
- **优势**：
  - 更加轻量和灵活，API设计更简洁。
  - 支持模块化和组合式API，易于使用和维护。

通过这些不同的通信方式和状态管理工具，开发者可以根据具体需求选择最合适的方案，以实现高效的组件间数据流动和状态管理。
