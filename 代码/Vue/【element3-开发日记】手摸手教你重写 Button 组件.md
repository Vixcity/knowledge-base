## 【element3-开发日记】手摸手教你重写 Button 组件

## 重写 Button 组件

### 背景

可能有同学会问我们为什么要重写组件呢？

其实 element3 现在组件的实现逻辑都是强行从 options api 改写成 composition api 的形式的

代码组织很乱，不具备可读性可维护性以及可扩展性

那可能还会有同学问为什么不在原有逻辑上重构呢？

说实话原有逻辑实在是乱，甚至会影响到你的思路

所以不妨我们大胆一点，重写

这篇文章主要是详细的记录了重构 Button 组件的方式以及步骤

主要是给想给贡献源码的同学一个重写组件的思路

> 本文内容很干，可能干到全是代码。请谨慎阅读

### 流程

重写一个组件，大概会分为以下几个点

-   确认需求
-   Tasking
-   Tdd
-   snapshot

我们接着依次来看一看

### 需求

在重写前我们先来定义一下我们要重写成什么样子才能满足我们的需求

首先，对外的接口是不能修改的，比如：

-   props
-   emits
-   slots

这些都是对外的接口，都要保持和原有逻辑一致

接着我们逻辑是要用 composition api 来实现

最后还有更重要的是，需要保证单元测试覆盖率在百分之90以上

好，着就是我们对组件重写的需求了

### Tasking

本着以终为始的思想，我们需要先确定 Button 到底有什么功能，我们先一一列举出来

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ae53d84b1a8f4f0287751cfd388655d0~tplv-k3u1fbpfcp-watermark.awebp)

其实我们看看 element 官网关于 Button 的文档，我们就知道 Button 具体有什么功能了

#### 功能列表

-   基于 size 属性可以设置 Button 的尺寸
-   基于 type 属性可以设置 Button 的类型
    -   不同的类型，Button 的 style 是不一致的
-   基于 plain 属性可以设置 Button 是否为朴素按钮
    -   朴素按钮其实也是一种 style 的改变
-   基于 round 属性可以设置 Button 是否为圆角按钮
    -   也是 style 的改变
-   基于 circle 属性可以设置 Button 是否为圆形按钮
    -   还是 style 的改变
-   基于 loading 属性可以设置 Button 是否是加载中状态
    -   如果设置了 loading 后，会显示一个 “加载” 的 icon，一直显示
-   基于 disabled 属性可以设置 Button 是否为禁用状态
    -   样式上有变化，显示一个禁用的 icon
    -   不可以点击
-   基于 icon 属性可以设置 Button 上显示的 icon
-   基于 autofocus 属性可以设置 Button 是否默认聚焦
-   基于 native-type 属性可以设置 Button 原生 type 属性

除了表面的这些功能点，其实还有一些更细致的功能点，比如：

-   如果是 loading 状态下，不能在显示通过 icon 设置的图标了
    -   也就是说组件只能有一个 icon 显示
        -   要不是 loading，要不是设置的 icon
-   loading 状态下，组件不可以点击
-   可以有三个点来控制 Button 的 size
    -   自身的 props
    -   父级 FormItem 时，可以获取 Item 的 Size
    -   可以通过全局配置来设置 size
-   可以有两个点来控制 Button 的 Disabled
    -   自身的 props
    -   父级为 Form 时，Form.disabled 也可以控制
    -   以上两个点，只要一个为 true ，那么 Button 都不会显示
-   用户可以通过 slot 的方式，定义组件的内容

好，终于把之前所有的 Button 功能都列举出来了，其实重写一个组件这个点是最关键的，只有这一步先捋顺了，后面写起来才会顺利

我自己的习惯是把所有的任务都列出来

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/05d37569b0a04b228e1e0688de1ed1c6~tplv-k3u1fbpfcp-watermark.awebp)

后面当完成一个任务的时候就勾选一个

有种打游戏做任务的感觉，每勾选一个 经验就+1

当然我把这个称之为”看的见的进度“

这样你就可以知道自己距离完成这个功能还差多久了

### TDD

有同学可能会问 TDD 是什么？这里我就不科普了，感兴趣的同学可以百度去学习

这里简单说一下 TDD 是一种编程方式

-   先写一个失败的测试
-   然后只写让这个失败的测试通过的逻辑
-   重构

那问题来了，我们写单元测试要测试什么？其实我们要测试的点都已经在 Tasking 那一步列举出来了

> 这个章节其实涉及了很多重构小步骤，全部写出来的话十分浪费时间，所以我采用贴代码的形式，提高效率

#### 用户可以通过 slot 的方式，定义组件的内容

先找最简单的功能来实现，这个最简单

> 先找软柿子捏

##### 测试

```js
import Button from '../src/Button.vue'
import { mount } from '@vue/test-utils'
describe('Button.vue', () => {
  it('should show content', () => {
    const content = 'foo'

    const wrapper = mount(Button, {
      slots: {
        default: content
      }
    })

    expect(wrapper.text()).toContain(content)
  })
})

复制代码
```

##### 代码实现

```vue
<template>
 <button>
   <slot></slot>
 </button>
</template>

<script>
export default {
  setup() {
    return {}
  }
}
</script>

复制代码
```

#### 基于 size 属性可以设置 Button 的尺寸

##### 测试

```js
  describe('set button size', () => {
    it.only('by props.size', () => {
      const size = 'small'

      const wrapper = mount(Button, {
        props: {
          size
        }
      })
			
      expect(wrapper.classes()).toContain(`el-button--${size}`)
    })
  })
复制代码
```

使用 toContain 这种断言方式可以在测试失败的时候帮助我们打印出 wrapper 当前所拥有的 classes ，是更方便调试的一种测试写法

##### 代码实现

```vue
<template>
    <button
    class="el-button"
    :class="[
      buttonSize ? `el-button--${size}` : ''
    ]"
  >
    <slot></slot>
  </button>
</template>

<script>
import { toRefs } from 'vue'
export default {
  props: {
	size: {
      type: String,
      validator(val) {
        if(val === "") return true
        return ['medium', 'small', 'mini'].indexOf(val) !== -1
      }
    },
  }
}
</script>

复制代码
```

这里实现了 props size 的校验

#### 基于 elFormItem.elFormItemSize 来设置 Button 的尺寸

##### 测试

```js
    it('by elFormItem.elFormItemSize', () => {
      const size = 'small'
      const wrapper = mount(Button, {
        global: {
          provide: {
            elFormItem: reactive({
              elFormItemSize: size
            })
          }
        }
      })

      expect(wrapper.classes(`el-button--${size}`)).toBeTruthy()
    })
复制代码
```

##### 代码实现

```vue
<template>
  <button
    class="el-button"
    :class="[
      buttonSize ? `el-button--${buttonSize}` : '',
    ]"
  >
    <slot></slot>
  </button>
</template>
<script>
import { toRefs, inject, computed } from 'vue'
export default {
  props: [
   	size: {
      type: String,
      validator(val) {
  		if (val === '') return true
        return ['medium', 'small', 'mini'].indexOf(val) !== -1
      }
    },
  ],
  setup(props) {
    const { size } = toRefs(props)

    const buttonSize = useButtonSize(size)

    return {
      buttonSize
    }
  }
}

const useButtonSize = (size) => {
  return computed(() => {
    const elFormItem = inject('elFormItem', {})
    return size?.value || elFormItem.elFormItemSize
  })
}
</script>
复制代码
```

因为有了测试做保障，重构起来也十分有自信

#### 基于全局配置 size 来设置 Button 的尺寸

##### 测试

```js
    it('by global config ', () => {
      const size = 'small'
      const wrapper = mount(Button, {
        global: {
          config: {
            globalProperties: {
              $ELEMENT: {
                size
              }
            }
          }
        }
      })

      expect(wrapper.classes()).toContain(`el-button--${size}`)
    })
复制代码
```

##### 代码实现

```js
const useButtonSize = (size) => {
  return computed(() => {
    const elFormItem = inject('elFormItem', {})
    return (
      size?.value ||
      elFormItem.elFormItemSize ||
      getCurrentInstance().ctx.$ELEMENT?.size
    )
  })
}
复制代码
```

关于 size 的任务我们就闯关成功啦

#### 基于 type 属性可以设置 Button 的类型

##### 测试

```js
  it('set button type by prop type ', () => {
    const type = 'success'

    const wrapper = mount(Button, {
      props: {
        type
      }
    })

    expect(wrapper.classes()).toContain(`el-button--${size}`)
  })
复制代码
```

##### 代码实现

```vue
<template>
  <button
    class="el-button"
    :class="[
      buttonSize ? `el-button--${buttonSize}` : '',
      type ? `el-button--${type}` : ''
    ]"
  >
    <slot></slot>
  </button>
</template>
<script>
export default {
  props: {
    size: {
      type: String,
      validator(val) {
        if (val === '') return true
        return ['medium', 'small', 'mini'].indexOf(val) !== -1
      }
    },
    type: {
      type: String,
      validator(val) {
        return (
          ['primary', 'success', 'warning', 'danger', 'info', 'text'].indexOf(
            val
          ) !== -1
        )
      }
    }
  }
</script>
复制代码
```

通过 class 来控制显示 type 的样式

#### 基于 plain 属性可以设置 Button 是否为朴素按钮

##### 测试

```js
  it('set button plain by prop type', () => {
    const wrapper = mount(Button, {
      props: {
        plain: true
      }
    })

    expect(wrapper.classes()).toContain(`is-plain`)
  })
复制代码
```

##### 代码实现

```vue
<template>
  <button
    class="el-button"
    :class="[
      buttonSize ? `el-button--${buttonSize}` : '',
      type ? `el-button--${type}` : '',
      {
        'is-plain': plain
      }
    ]"
  >
    <slot></slot>
  </button>
</template>
<script>
	...
  props:{
    plain: Boolean
  }
  ...
</script>
复制代码
```

#### 基于 round 属性可以设置 Button 是否为圆角按钮

##### 测试

```js
  it('set button round by prop type', () => {
    const wrapper = mount(Button, {
      props: {
        round: true
      }
    })

    expect(wrapper.classes()).toContain(`is-round`)
  })
复制代码
```

##### 代码实现

```vue
<template>
  <button
    class="el-button"
    :class="[
      buttonSize ? `el-button--${buttonSize}` : '',
      type ? `el-button--${type}` : '',
      {
        'is-plain': plain,
        'is-round': round
      }
    ]"
  >
    <slot></slot>
  </button>
</template>
<script>
  ……
	props:{
  	  round:Boolean
	}
  ……
</script>
复制代码
```

加一个 class 即可

#### 基于 circle 属性可以设置 Button 是否为圆形按钮

##### 测试

```js
  it('set button circle by prop type', () => {
    const wrapper = mount(Button, {
      props: {
        circle: true
      }
    })

    expect(wrapper.classes()).toContain(`is-circle`)
  })
复制代码
```

##### 代码实现

```vue
<template>
...
      {
        'is-plain': plain,
        'is-round': round,
        'is-circle': circle
      }
...
  >
</template>

<script>
  ……
  	props:{
       circle: Boolean
    }
  ……
</script>
复制代码
```

#### 通过设置 loading ，来让按钮呈现加载中状态

如果是 loading 状态的话，按钮应该是不可以点击的，并且显示 loading icon

##### 测试

```js
  it('set button loading by prop loading', async () => {
    const wrapper = mount(Button, {
      props: {
        loading: true
      }
    })
    
    expect(wrapper.classes()).toContain(`is-loading`)
    expect(wrapper.attributes()).toHaveProperty('disabled')
  })
复制代码
```

这里只需要验证 button 上有没有 disabled 属性即可

##### 代码实现

```vue
<template>
...
	:disabled="loading"
	:class="[
      {
        'is-plain': plain,
        'is-round': round,
        'is-circle': circle,
   		'is-loading': loading
      }
		]
    <i class="el-icon-loading" v-if="loading"></i>
	<slot></slot>
...
  >
</template>
<script>
  export default {
     	props:{
     		 loading: Boolean
    	} 
  }
}
</script>
复制代码
```

#### 基于 disabled 属性可以设置 Button 是否为禁用状态

##### 测试

```js
  describe('set button disabled', () => {
    it('by props.disabled', () => {
      const wrapper = mount(Button, {
        props: {
          disabled: true
        }
      })

      expect(wrapper.classes()).toContain(`is-disabled`)
      expect(wrapper.attributes()).toHaveProperty('disabled')
    })
  })
复制代码
```

因为 disabled 会涉及到 2 个功能点，一个是通过 props 一个是通过父级组件 Form 来控制，所以我们用 describe 来组织测试

这里的测试稍微和之前的不同，不光要验证有 is-disabled 类名，我们还需要给组件设置 disabled ，这样组件才是失效的

##### 代码实现

```vue
<template>

  <button
    class="el-button"
    :disabled="disabled || loading"
    :class="[
      buttonSize ? `el-button--${buttonSize}` : '',
      type ? `el-button--${type}` : '',
      {
        'is-disabled': disabled
      }
    ]"
  ></template>
<script>
props:{
  disabled: Boolean
}
</script>
复制代码
```

#### 如果父级组件未 From ，并且 From 的 disabled 为 true，那么当前组件也会受影响

##### 测试

```js
    it('by elForm.disable', () => {
      const wrapper = mount(Button, {
        global: {
          provide: {
            elForm: reactive({
              disabled: true
            })
          }
        }
      })

      expect(wrapper.classes()).toContain(`is-disabled`)
      expect(wrapper.attributes()).toHaveProperty('disabled')
    })
复制代码
```

##### 代码实现

```vue
<template>
  <button
    class="el-button"
    :disabled="buttonDisabled || loading" 
    :class="[
      buttonSize ? `el-button--${buttonSize}` : '',
      type ? `el-button--${type}` : '',
      {
        'is-plain': plain,
        'is-round': round,
        'is-circle': circle,
        'is-loading': loading,
        'is-disabled': buttonDisabled
      }
    ]"
  >
    <slot></slot>
  </button>
</template>
<script>

  setup(props){
    const { size, disabled } = toRefs(props)
    const buttonDisabled = useButtonDisabled(disabled)

    return {
   		...
	    buttonDisabled
    }
  }
  
  const useButtonDisabled = (disabled) => {
  return computed(() => {
    const elForm = inject('elForm', {})

    return disabled?.value || elForm.disabled
  })
}

</script>
复制代码
```

#### 基于 icon 属性可以设置 Button 上显示的 icon

##### 测试

```js
  it('set button icon by props.icon', () => {
    const wrapper = mount(Button, {
      props: {
        icon: 'el-icon-edit'
      }
    })

    expect(wrapper.find('.el-icon-edit').exists()).toBe(true)
  })

复制代码
```

检测一个元素的存在需要 find + exists 配合使用

##### 代码实现

```vue
<template>
		……
    + <i :class="icon" v-if="icon"></i>
  </button>
</template>
<script>
props:{
  icon:String
}
</script>
复制代码
```

继续，我们还有一个逻辑，如果 loading 显示的话，那么 icon 就不可以显示了

##### 如果是 loading 状态下，不能在显示通过 icon 设置的图标

#### 测试

```js
    it("don't show icon when loading eq true", () => {
      const wrapper = mount(Button, {
        props: {
          icon: 'el-icon-edit',
          loading: true
        }
      })

      expect(wrapper.find('.el-icon-edit').exists()).toBe(false)
      expect(wrapper.find('.el-icon-loading').exists()).toBe(true)
    })
复制代码
```

##### 代码实现

```vue
<template>
	……
   <i class="el-icon-loading" v-if="loading"></i>
   <i :class="icon" v-else-if="icon"></i>
	……
</template>
复制代码
```

实现起来也很简单，因为 loading 和 icon 只能保留一个，所有我们使用 v-else-if 来实现即可

#### 基于 autofocus 属性可以设置 Button 是否默认聚焦

这个其实不需要实现，在外面设置 autofocus 时会自动添加到 内部 button 上的

```html
<Button autofocus></Button>
复制代码
```

#### 基于 native-type 属性可以设置 Button 原生 type 属性

##### 测试

```js
  it('set native-type by props.native-type', () => {
    const nativeType = 'reset'

    const wrapper = mount(Button, {
      props: {
        nativeType
      }
    })

    expect(wrapper.attributes('type')).toBe(nativeType)
  })
复制代码
```

##### 代码实现

```vue
<template>
	<button
  	:type="nativeType"        
  >
    
  </button>
</template>
<script>
	props:{
  	  nativeType:String
	}
</script>

复制代码
```

#### 重构

##### 重构前

```vue
<template>
  <button
    class="el-button"
    :type="nativeType"
    :disabled="buttonDisabled || loading"
    :class="[
      buttonSize ? `el-button--${buttonSize}` : '',
      type ? `el-button--${type}` : '',
      {
        'is-plain': plain,
        'is-round': round,
        'is-circle': circle,
        'is-loading': loading,
        'is-disabled': buttonDisabled
      }
    ]"
  >
    <i class="el-icon-loading" v-if="loading"></i>
    <i :class="icon" v-else-if="icon"></i>
    <slot></slot>
  </button>
</template>

<script>
import { toRefs, inject, computed, getCurrentInstance } from 'vue'
export default {
  props: {
    size: {
      type: String,
      validator(val) {
        if (val === '') return true
        return ['medium', 'samll', 'mini'].indexOf(val) !== -1
      }
    },
    type: {
      type: String,
      validator(val) {
        return (
          ['primary', 'success', 'warning', 'danger', 'info', 'text'].indexOf(
            val
          ) !== -1
        )
      }
    },
    plain: Boolean,
    round: Boolean,
    circle: Boolean,
    loading: Boolean,
    disabled: Boolean,
    icon: String,
    nativeType: String
  },
  setup(props) {
    const { size, disabled } = toRefs(props)

    const buttonSize = useButtonSize(size)
    const buttonDisabled = useButtonDisabled(disabled)

    return {
      buttonSize,
      buttonDisabled
    }
  }
}

const useButtonDisabled = (disabled) => {
  return computed(() => {
    const elForm = inject('elForm', {})

    return disabled?.value || elForm.disabled
  })
}

const useButtonSize = (size) => {
  return computed(() => {
    const elFormItem = inject('elFormItem', {})
    return (
      size?.value ||
      elFormItem.elFormItemSize ||
      getCurrentInstance().ctx.$ELEMENT?.size
    )
  })
}
</script>

复制代码
```

我不是太喜欢 class 都在 template 中处理，所以我要重构这个逻辑点

因为得益于单元测试，所以我可以十分有自信的去重构

##### 重构后

```js
<template>
  <button
    class="el-button"
    :class="classes"
    :type="nativeType"
    :disabled="buttonDisabled || loading"
  >
    <i class="el-icon-loading" v-if="loading"></i>
    <i :class="icon" v-else-if="icon"></i>
    <slot></slot>
  </button>
</template>

<script>
import { toRefs, inject, computed, getCurrentInstance } from 'vue'
export default {
  name: 'ElButton',
  props: {
    size: {
      type: String,
      validator(val) {
        if (val === '') return true
        return ['large', 'medium', 'small', 'mini'].indexOf(val) !== -1
      }
    },
    type: {
      type: String,
      validator(val) {
        return (
          ['primary', 'success', 'warning', 'danger', 'info', 'text'].indexOf(
            val
          ) !== -1
        )
      }
    },
    nativeType: {
      type: String,
      default: 'button'
    },
    plain: Boolean,
    round: Boolean,
    circle: Boolean,
    loading: Boolean,
    disabled: Boolean,
    icon: String
  },
  setup(props) {
    const { size, disabled } = toRefs(props)

    const buttonSize = useButtonSize(size)
    const buttonDisabled = useButtonDisabled(disabled)
    const classes = useClasses({
      props,
      size: buttonSize,
      disabled: buttonDisabled
    })

    return {
      buttonDisabled,
      classes
    }
  }
}

const useClasses = ({ props, size, disabled }) => {
  return computed(() => {
    return [
      size.value ? `el-button--${size.value}` : '',
      props.type ? `el-button--${props.type}` : '',
      {
        'is-plain': props.plain,
        'is-round': props.round,
        'is-circle': props.circle,
        'is-loading': props.loading,
        'is-disabled': disabled.value
      }
    ]
  })
}

const useButtonDisabled = (disabled) => {
  return computed(() => {
    const elForm = inject('elForm', {})

    return disabled?.value || elForm.disabled
  })
}

const useButtonSize = (size) => {
  return computed(() => {
    const elFormItem = inject('elFormItem', {})
    return (
      size?.value ||
      elFormItem.elFormItemSize ||
      getCurrentInstance().ctx.$ELEMENT?.size
    )
  })
}
</script>

复制代码
```

至此，我们所有的任务都已经完成了，不知道大家有没有感觉到，其实我们每次都只关注于一个小功能，实现起来十分简单

组件逻辑都已经完成了，那么我们要看看组件的样式了

### 增加 snapshot

其实在添加 snapshot 之前，我们需要先手动去看看组件的样式，毕竟刚刚 TDD 的过程我们是都没有看 UI 的

#### Snapshot 测试

```js
  it('snapshot', () => {
    const wrapper = mount(Button)
    expect(wrapper.element).toMatchSnapshot()
  })
复制代码
```

snapshot 的测试很简单，写上着几行代码后， jest 会帮助我们生成当前组件的快照

```snap
// button/tests/_snapshots__/Button.spec.js.snap
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`Button.vue snapshot 1`] = `
<button
  class="el-button"
  type="button"
>
  <!--v-if-->
  
  
</button>
`;

复制代码
```

### ## 测试覆盖率

最后，基于我们的需要是要达到 90% 的测试覆盖率

我们看看我们现在的覆盖率是多少

执行以下命令

```shell
yarn test packages/button/tests/Button.spec.js --coverage
复制代码
```

可以看到以下结果

```js
PASS  packages/button/tests/Button.spec.js
  Button.vue
    ✓ snapshot (20 ms)
    ✓ should show content (10 ms)
    ✓ set button type by prop type  (2 ms)
    ✓ set button plain by prop type (2 ms)
    ✓ set button round by prop type (2 ms)
    ✓ set button circle by prop type (2 ms)
    ✓ set button loading by prop loading (2 ms)
    ✓ set button loading by prop loading (2 ms)
    ✓ set native-type by props.native-type (2 ms)
    set button size
      ✓ by props.size (3 ms)
      ✓ by elFormItem.elFormItemSize (1 ms)
      ✓ by global config  (2 ms)
    set button disabled
      ✓ by props.disabled (2 ms)
      ✓ by elForm.disable (1 ms)
    set button icon
      ✓  by props.icon (6 ms)
      ✓ don't show icon when loading eq true (2 ms)

-----------------|---------|----------|---------|---------|-------------------
File             | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
-----------------|---------|----------|---------|---------|-------------------
All files        |     100 |      100 |     100 |     100 |
 src             |     100 |      100 |     100 |     100 |
  Button.vue     |     100 |      100 |     100 |     100 |
 tests           |     100 |      100 |     100 |     100 |
  Button.spec.js |     100 |      100 |     100 |     100 |
-----------------|---------|----------|---------|---------|-------------------
Test Suites: 1 passed, 1 total
Tests:       16 passed, 16 total
Snapshots:   1 passed, 1 total
Time:        3.359 s
复制代码
```

测试覆盖率达到了百分之百

因为我们是用 TDD 来开发的，所以达到百分之百的测试覆盖率是常规操作

### 总结

以上就是重写 Button 组件的全部了，稍微总结总结

我们需要先确定组件的功能

然后基于 TDD 的方式一点一点去实现

最终我们会得到一个测试覆盖率达到百分百的组件

即使功能在复杂的组件，也是由一个个小功能实现的，我们在 TDD 的过程中，其实是降低了心智负担，让我们只关心一个小功能的实现，并且因为有测试的保障，可以随时的重构

后面 element3 所有的组件也都会是通过以上方式来完成重写的。

最大程度保证代码的质量，当然这也是为了后续新特性的扩展

> 后续的文章会简化 TDD 步骤，因为实在太麻烦了！！！

---

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/058f20e8cee84bdb9c0a62b36dc084e5~tplv-k3u1fbpfcp-watermark.awebp)

-   这是我们[花果山团队](https://link.juejin.cn/?target=https%3A%2F%2Fwww.yuque.com%2Fhugsun "https://www.yuque.com/hugsun")的开源项目 [element3](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fkkbjs%2Felement3 "https://github.com/kkbjs/element3")
-   一个支持 vue3 的前端组件库