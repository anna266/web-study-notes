[笔记参考视频](https://www.bilibili.com/video/BV1Zy4y1K7SH?p=27)

### vue学习笔记

- vue 中容器与实例必须是一一对应的关系
- vue的模板中只能用**vue实例vm中的属性或方法**，无法用实例外部的内容。

### 模板语法（template）：

 插值语法：用于解析标签体内容 {{}}

**MVVM模型**  M（model）模型：可类比于data中的数据，V（View）视图：可类比于模板代码，VM：视图模型（ViewModel）：可类比于Vue实例。

**ES6中一些属性回顾**：

1. **Object.definepropety**(对象，要添加属性名称，该属性的一些性质)  其中的属性常见的有：value（对应该属性的值），enumerable(默认为false，即不可枚举），writable(默认为false，即不可修改)，configurable(默认为false，即不可删除的，删除时只要用delete obj.key即可删除)

   高级使用方法：get函数（简称getter）get（）{return num} 在读取当前设定属性时会调用，

   ​                           set函数（简称setter）  set (value) { } 当有人修改当前设定属性时会被调用，且设定的值为value传入set函数中。

2. Object.keys()

3. for （let key in 对象）{}  //person[key]表示对应当前属性的值

**数据代理**：通过一个对象代理对另一个对象中属性的操作（包括读/写）=>借助object.definepropty函数中的getter和setter来实现。

例子：

```
/*利用obj2来实现对obj1中数据的修改*/
    let obj1 = {x:100};
    let obj2 = {y:200};
    Object.defineproperty(obj2,x,{
        get(){
          return obj1.x;
        },
        set(value){
          obj1.x = value;
        }
    })
```

vue实例vm中的_data其实就是配置对象data的值，只不过 _data中采用了一种数据劫持的方法来实现和页面的响应式（只有vue配置对象里的data里的数据会做数据劫持）。

**事件对象身上的一些属性**：

event.target：事件目标，即对应的事件标签，如button标签

**指定语法**：v-...

- 属性值的绑定， 例子v-bind :href = "url" 加上v-bind会将href中的数据当做JS表达式来处理，不加当做字符串处理。

- 单向数据绑定：v-bind  能从date中取出数据到template，但无法逆向操作。

- 双向数据绑定：v-model   可实现date中数据和template中数据的双向操作。

​                                 双向绑定一般应用在表单类元素上（如input,select等）；

​                                 **v-model：value 可以简写为v-model**，因为v-model默认收集的就是**value**的值。

- vue中模板与实例的两种绑定方式： el:"#root",  vm.$mount('#root') 其中vm表示vue实例

- data的两种写: 1, data:{   } 2.data: function(){ return {}}，该方式可简写为 data(){return {}}，其中的this对应的是vue实例对象，若写为箭头函数形似，则直接指向全局window。

**事件处理v-on：**

vue配置对象中的methods中各个函数的写法：

​      eventName(){ //函数中的this指向vm} ; eventName:()=>{ //函数中的this指向window}

```
<button v-on:click="eventName">点我提示信息</button> 
//其中v-on:click可以简写为@click
/*v-on传参问题*/
1.<button v-on:click="eventName">点我提示信息</button> 
如果事件名称后面没有加括号，则没有传递参数，在 eventName(e){}中e表示的将是事件本身
2.<button v-on:click="eventName（a,b）">点我提示信息</button> 
如果事件名称后面有参数，则在 eventName(a,b){}中传入的将是对应的参数，但无法对event进行处理
3.<button v-on:click="eventName（a,b,$event）">点我提示信息</button> 
引入$event，在eventName(a,b,c){}中对应位置，即可得到事件event本身。

//绑定事件的时候 @xxx="yyy",其中xxx为事件类型，yyy为所需要执行的函数，当yyy内容较少时，可直接在里面写表达式语句，但不能写if for类非js表达式语句。
```

**事件修饰符**

1. prevent  阻止默认事件(click为事件)

```
<a href="http://www.atguigu.com" @click.prevent="enventName">点击</a>
//类似于 e.preventDefault()
```

2. stop 阻止事件冒泡

```
<div @click="enventName">
    <div @click.stop="enventName">点击</div>
    <a href="http://www.atguigu.com" @click.stop.prevent="enventName">点击</a> //先阻止冒泡再阻止默认事件
</div>
//类似于 e.stopPropagation(),如此一来里面盒子上的事件将不会冒泡到最外面那层
```

3. once 事件只会触发一次

```
 <div @click.once="enventName">点击</div>
```

4. capture 使用事件的捕获模式

```
<div @click.capture="enventName">
    盒子1
    <div @click.stop="enventName">盒子2</div>
</div>
//这样就可以将最外层盒子的事件放在捕获阶段去执行，即即使点击的是盒子2，也会在捕获阶段先将盒子1对应的事件执行。
```

5. self  只有event.target是当前操作的元素时才触发事件

```
<div @click.self="enventName">
    <button @click.stop="enventName">点击</button>
</div>
/*捕获或者冒泡时，所有触发的事件对应的target都是点击的那个标签，即点击了上面的点击按钮后，根据冒泡原则将会依次触发里外两个事件，而这两个事件对应的$event都是“点击”这个button按钮*/
```

6. passive 事件的默认行为立即执行，无需等待事件回调执行完毕（因为**有些事件需要处理完事件对应的程序之后再去执行默认行为**，移动端应用较多）

```
/*@scroll=“eventName”当滚动条滚动时，触发eventName事件*/
/*@wheel=“eventName”当鼠标滚轮发生滚动时（滚轮从滚动到停止算一次），触发eventName事件*/
<ul @wheel.passave=“eventName”>
    <li></li>
    <li></li>
    <li></li>
</ul>
不加passave会先执行eventName，再执行默认的滚动条的滚动动作。
加上passave，则会先执行默认的滚动条的滚动动作，再执行eventName。
```

**键盘事件**

一些按键的别名（常用）

回车 enter 、删除 delete（Backspace和delete键都响应）、推出 esc、空格 space、换行 tab（需要配合keydown使用，不要用keyup，因为该按键本身有失去焦点的作用）、上 up、下 down、左 left、右 right

```
<input @keydown.enter="eventName"/> //按下回车键，触发eventName事件。
```

系统修饰符ctrl shift meta alt

```
<input @keydown.ctrl.y="eventName"/> //按下Ctrl+y，触发eventName事件。
```

**计算属性**（使用已经写完属性重新加工成一个新的属性）

```
computed:{
    fullName:{
    //当有人读取fullName时，getter就会被调用，且返回值就作为fullName的值
    //get函数调用时机:第一次读取时和get所依赖的数据发生变化时
      get(){
          return xx ;
      },
      //当fullName被修改时，需要调用setter,若确定该计算属性不会被修改，则无需使用写setter
      set(value){
      	console.log(value) //value表示修改的值
      }
    }
}
```

- **当vue中data的数据发生改变时，则会重新更新模板。**

计算属性简写：

```
//若确定该计算属性只需读取不需改写，则可删掉setter
computed:{
    fullName:{
      get(){
          return xx ;
      },
    }
}
//在只有getter的情况下又可简写为
fullName: function(){
	return xx;
}
//函数写表达形式又可简写为
fullName(){
	return xx;
}
注：虽然简写后表达形式上fullName像函数，但它其实还是计算属性，使用时不要在后面加括号。
```

**监视属性**（监视是针对属性进行监视的，包括基本属性和计算属性）

1. 在创建vue实例时的配置对象中添加监视

```
watch：{
	isHot:{
		immediate:true,  //初始化时让handler先调用一遍
		//每当isHot发生变化时，handler函数就会被调用
        handler(newValue,oldVAlue){
            console.log('xxx')
        }
	}
}
```

2. 在实例对象vm创建完成后，添加监视的方法

```
vm.$watch('isHot',{
    immediate:true,  //初始化时让handler先调用一遍
    //每当isHot发生变化时，handler函数就会被调用
    handler(newValue,oldVAlue){
    console.log('xxx')
    }
})
小细节：在使用$watch时，属性名称使用的是'isHot'，而在上面那种方式中采用的是isHot，这是因为，上面那种方式中使用了一种对象的简写，key:value,中key可简写为不加引号的，实际都是应该有引号的，第二种方式中有了引号只有就不会被当做变量去读取里面的值了。
```

**深度监视**（watch默认只检测一层）

```
//检测多级结构中某个属性的变化
data:{
    number:{
        a:1,
        b:2
    },
    watch:{
        'number.a':{     //要实现深度监视，需要将简写的key还原成原始带引号的写法，即加上'number.a'
            handler(){
                console.log('xx')
            }
        }
    }
}
//检测多级结构中所有属性的变化
data:{
    number:{
        a:1,
        b:2
    },
    watch:{
        number:{
        	deep:true,  //如果不加这个属性，a和b的任何一个值改变都不会触发handler
            handler(){
                console.log('xx')
            }
        }
    }
}
```

**监视属性简写**

```
watch：{
//当所监视的属性只需要handler配置项时，可直接简写为如下形式
     isHot(newValue,oldVAlue){
         console.log('xxx')
     }
}
vm.$watch('isHot',function(newValue,oldVAlue){})//同理于创建完实例后的检测方式
```

**计算属性与监听器的区别：**

- 对于一些需要异步返回数据的，计算属性由于需要return将不再适用
- 计算属性能实现的功能，监视属性一定能实现

**绑定class样式**

字符串写法，适用于：样式的类名不确定，需要动态指定。

```
<div class="basic" :class="mood" @clock="changeMood">{{name}}</div>
```

数组写法，适用于：要绑定的样式个数不确定，名字也不确定

```
<div class="basic" :class="classArr" @clock="changeMood">{{name}}</div>
//对应的data中
data：{
     classArr:['x1','x2','x3'] //这样写，这几种样式最终都会被vue添加到上面的div盒子上
}
```

对象写法，适用于：要绑定的样式个数确定，名字确定，但用不用不确定

```
<div class="basic" :class="classObj" @clock="changeMood">{{name}}</div>
//对应的data中
data：{
     classObj:{
       x1:false, //false表示不适用该属性，true表示使用该属性
       x2:false
     }
}
```

**绑定style样式**

```
//这里需要改成{}的形式
<div class="basic" :style={fontSize: fsize + 'px'}>{{name}}</div>
//对应的data中
data：{
      fsize:40
}
//另一种写法 （加上冒号之后就会将后面引号中的js按照表达式去执行）
<div class="basic" :style={styleObj}>{{name}}</div>
//对应的data中
data：{
      styleObj:{
       	fontSize:'40px'
      }
}
注：需要改的样式较多时，第二种方式更加合适
//也可不适用{}形式，而是使用数组(不常用)
<div class="basic" :style=[styleObj1,styleObj2]>{{name}}</div>
data：{
      styleObj1:{
       	fontSize:'40px'
      },
      styleObj2:{
       	backgroundColor:'yellow'
      }
}
```

<img src="E:\vue学习\picture\微信图片_20220324220219.png" style="zoom:60%;" />

**条件渲染**

v-show: <h2 v-show="false"> 哈哈哈</h2>  这个效果相当于display：none，节点还是存在的。v-show="false"引号中知道是js表达式即可。

v-if:<h2 v-if="false"> 哈哈哈</h2>  这个效果会直接将整个节点删掉。v-if、v-else-if、v-else

注意：若变化的比较频繁，则建议使用v-show,否则建议使用v-if。

**template标签：**

该标签中可以用来写指令，但不会产生新的盒子来破坏结构。

注意：template不能和v-show一起使用，但可以和v-if一起使用。

**列表渲染**

v-for 在所需要的循环标签中加入v-for

```
<li v-for="p in persons" :key="p.id">{{p.name}}-{{p.age}}</li>
<li v-for="(p,index) in persons" :key="p.id">{{p.name}}-{{p.age}}</li> //前面是由两个参量的，一个是遍历的某一项，一个是对应于数组的索引值。
```

**其不仅可以对数组进行遍历，也可以对对象、字符串进行遍历**，同样都是第一个是内容，第二个是索引。

可也遍历指定次数，不过用的不多

```
<li v-for="(number,index) in 3">{{number}}-{{index}}</li>
%%输出结果为：
    1-0
    2-1
    3-2
```

**列表过滤**

注意：**一个字符串中indexOf('')返回的结果都是0索引值，而不是-1**

**vue检测对象数据变化的原理**（看视频）

**向vue中添加响应式数据的方法**:

```
Vue.set(target,key,val)
vm.$set(target,key,val)
注意：
1. 在数组中这个key表示的是索引值。
2. 虽然vm的数组中没有自己的getter和setter，但数组中如果是有对象的，那么对象中的属性其实是有响应式的。
3. 若要改变的数组不会改变原来的数组，如filter等，则可以将过滤后的值重新赋值给该数组。
```

![](E:\vue学习\picture\微信图片_20220326222545.png)

**收集表单数据：**

多选框，select等表单数据中，v-model和value的写法

![](E:\vue学习\picture\微信图片_20220326234259.png)

- 像checkedbox这种类型的，也需要配置value值，否则将会读取checked中的布尔值作为value值
- 设置好了value后，还需要将v-model对应的data数据写为数组的形式，如果仍然写为字符串，则还是会读取checked中的布尔值。

- v-model也有修饰符，如v-model.number="xxx" 限制输入的值只能是数字； v-model.lazy="xxx"懒收集，只有在当前输入框失去焦点时才进行收集，可用于多输入框中，不用实时监控，以此来提高效率；v-model.trim使得收集数据时自动去掉用户输入的最前面和最后面的空格，中间的空格不会去掉。

**过滤器：**

![](E:\vue学习\picture\微信图片_20220327120148.png)

 过滤器已在vue3中被移除，具体可参考视频。

![](E:\vue学习\picture\微信图片_20220327165853.png)

**内置指令**

- **v-text**：向其所在的节点中渲染文本内容；与插值语法的区别在于v-text会替换文本中的内容，{{x}}不会。

```
<h3 v-text="name"></h3>
<h3>{{name}}</h3>   //该方法用的更多，更灵活
```

- **v-html**：可以渲染带标签的内容，v-text只能解析字符串，不能解析标签，具体区别看下图

![](E:\vue学习\picture\微信图片_20220327175828.png)

- **v-clock：**没有值，本质是一个特殊属性，当**vue实例创建完成并接管容器后**就会删掉该属性；与CSS的**属性选择器**配合可以解决网速慢时页面展示出{{xx}}的问题。使用方法：

```
[v-clock]{
	display:none;
}
```

- **v-once**：没有值，其所在节点在**初次**渲染后就视为**静态内容**，之后数据的改变不会引起v-once所在结构的更新。

- **v-pre：**为标签加上该属性可以使vue在解析模板时跳过该结点的解析，一般用于没有插值语法和vue指令的标签中，可提高代码效率。

**自定义指令**

- 函数式

```
<h2>放大10倍后的n值是：<span v-big="n"></span></h2>
在vue的配置对象中加入
directives:{
    big(element,binding){
    	element.innerYext = binding.value * 10
    }
}
//这里面的element指向v-big绑定的标签span（真实的DOM）,binding里面的value值表示v-big绑定的数据,binding中还有很多其它数据，需要时可在调试器中进行查阅。
//big何时会被调用？1.指令与元素成功绑定时（一上来），2.指令所在的模板被重新解析时。
```

- 对象式

```
<input v-fbind:value = 'n'/>
directives:{
    fbind:{
            bind(element,binding){     //指令与元素成功绑定时调用
            element.value = binding.value
        },
            inserted(element,binding){    //指令所在元素插入页面时调用
            element.focus()
        },
            update(element,binding){    //指令所在模板被重新解析时调用
            element.value = binding.value
        }
    }
}
// 函数式写法，其实就是只调用了对象式写法中的bind和update。
```

- 容易踩的坑
  1. 指令的命名：当指令是多个单词连写时，要用-隔开，而不是使用小驼峰。v-big-number，在写函数式指令时，要将其key写为原始形式'big-number'(){ }
  2. 指令directives对象中的this都是指向的window，而不是vm

- 用于全局中

  Vue.directive('fbind',{})或Vue.directive('fbind',function(){})//后面的括号就是前面fbind对应的内容。

**生命周期**

- mounted(){} //vue完成模板的解析并把初始的真实DOM元素放入页面后（挂载完毕），调用mounted（只在第一次页面挂载时调用）

顺序：

beforeCreate、created、beforeMouth、mounted(初始化，重要)、beforeUpdate、updated、beforeDestroy（收尾工作，重要）、destroyed

**组件**

- 组件中的data必须写为函数return的形式，因为组件可以应用于多个位置，每次return都可以创造出一个新的对象，避免了造成数据污染。但通过new出来的组件对象是可以写成对象的。

#### 非单文件写法

1. 创建组件：const school = Vue.extend({配置对象的内容}  里面的**模板**用template对象写
2. 注册组件：

```
new Vue({
    el:'#root',
    components:{     //组件注册过程（局部注册）
        xuexiao:school,
    }
})
```

3. 编写组件标签：

```
<div id = "root">
     <xuexiao></xuexiao>
</div>
```

全局注册组件方式： Vue.component('组件标签名','定义的组件')

**组件的注意点**

1. 组件的命名：

   单个单词可以用小写也可以首字母大写

   多个单词组成需要用横杆-连接，或者每个单词首字母都大写，但这种方式只能应用在脚手架里

2. 可以在组件配置项中用name来定义组件在开发者工具中的名字

3. 在脚手架中可以使用组件的自闭合标签

4. 一个简写的方式：const school = Vue.extend(option) 可简写为 const school = option,直接传入对象，在注册组件时，vue会自动利用Vue.extend()去调用。

5. const school = Vue.extend(option) 注意这里生成的school是VueComponent构造函数，当将该构造函数写成标签的形式时，vue源码就会自动创建其vc实例

6. 每个组件在执行const school = Vue.extend(option) 之后，都会产生一个新的VueComponent构造函数。

**组件嵌套**

子组件需要在父组件的components中进行注册，并在父组件中进行调用，从而形成嵌套关系。

在进行组件开发时，一般选择一个总的APP组件作为最外层组件来管理各个层级的组件。

**VueComponent**

组件中各配置项中的this指向VueComponent的实例对象，简称为vc, 而在new Vue(option)中，option中的this都指向vm。

在写组件标签时，就是在new一个VueComponent。

**一个重要的内置关系：**

```
VueComponent.prototype.__proto__ === Vue.prototype
```

这样做的目的是**让组件实例对象vc也可以访问到vue原型上的属性和方法**。

#### **单文件组件**

单文件组件中**能写的标签**

```
<template>组件的结构</template>
<script>组件交互相关的改吗（数据、方法等）</script>
<style>组件的样式</style>
```

.vue文件就是组件，里面不能创建vue的实例。

在脚手架中，一般main.js文件中一般是用来构造vue实例，APP.vue用于构造总的单文件组件，index.html中存放main.js中的模板标签。在index.html中用script标签引入main.js文件（要引用在标签之后）。

### 使用vue脚手架

安装步骤：

- 进第一次需要，执行该命令后，后续可直接使用vue命令进行脚手架的安装  npm i -g @vue/cli
- 切到要创建项目的目录，然后使用命令创建项目  vue create xxx
- 进入项目目录cd xxx       开始运行项目npm run serve

两个地址： localhost是自己用的，NetWork是给同局域网中其他人用的。

src中的assets文件夹中一般存放项目中的静态文件（图片、视频啥的），components中写项目的组件。

**render配置项**

在引入库时（import Vue from 'vue'），这里面的vue对应的具体文件路径可从vue文件的package.json的module中获得。

vue.js和vue.runtime.xxx.js的区别：

（1） vue.js是完整版的Vue，包含核心功能+模板解析器

 （2） vue.runtime.xxx.js是运行版本的Vue,只包含核心功能没有模板解析器

所以在引入的是 vue.runtime.xxx.js时，不能使用template配置项，需要使用render函数接收到的creatElement函数指定具体用法。

**修改默认配置**

webpack配置文件webpack.config.json

vue inspect > output.json 可以将脚手架中的隐藏配置文件以output.json文件的形式输出。

vue.config.js文件中可以写一些脚手架的配置修改项（进行个性化定制），如入口等，最终vue会以该文件为主与webpack进行合并。具体修改项可以看vue脚手架的官网。在其中加入lintOnSave:false可以关闭语法检查。

![](E:\vue学习\picture\微信图片_20220407203645.png)



**ref的使用**

![](E:\vue学习\picture\微信图片_20220406215504.png)

**props配置**

传入数据的几种接收方式：

从组建中传入：<Student name='张三‘ sex="男"  :age = "18"/>  //**注意这里age前面加上冒号，表示引号中的内容要进行js表达式运算，不加冒号则会将引号中整个内容以字符串的形式进行传递。**

1. 简单声明接收： props:['name','age','sex']

2. 接收的同时对数据进行类型限制：

   ```
   props:{
       name:String,
       age:Number,
       sex:String
   }
   ```

3. 接收的同时对数据进行类型限制+默认值的指定+必要性的限制

   ```
   props:{
       name:{
           type: String,
           required:true
       },
       age:{
            type: Number,
            default:99    
       },
       sex:{
       	type: String,  //不写required表示该属性不是必须的
       }
   }
   ```

   注意：**props传入的属性在被传入组件中不要进行修改**，如果必须要进行修改，可以通过重新在data或计算属性中定义一个新的属性来进行更改。props中的属性名不要与data的重合，若有重合props的优先级更高。
   
   props也可接收函数类型的数据，**实现子组件向父组件传递信息**
   
   ```
   <Student :hellow="hellow"/>  //父组件向子组件中传递hellow方法
   methods:{
   	hellow(a){}
   }
   ```

**mixin混入**

功能：可以把多个组件共用的配置提取成一个混入对象

使用方式：

```
//第一步定义混合
export const mixin = {  //用分别暴露比较合适
    data(){...}
    method:{...}
    ...
}
//第二步使用混入
（1）全局混入：Vue.mixin(xxx) 这一步骤写在main.js中，这样全局所有的vm和vc上都将混入xxx中的内容
（2）局部混入：mixins:['xxx'],这一项与data、methods等同级。
```

注意：混入时，里面的data与组件中的data将进行一个整合，若有名字冲突，则会选择组件中的内容。mounted（）这种生命周期钩子若两者里面都有，则都会执行一遍，且先执行混入中的钩子，在执行组件中的。

**Vue插件编写**

![](E:\vue学习\picture\微信图片_20220408204148.png)

例如：

![vue的](E:\vue学习\picture\微信图片_20220408204343.png)

使用方法：import plugins from './plugins'    Vue.use(plugins,a,b,c)后面还可以向里面传递参数，第一个参数是Vue的构造函数。

可以看出，所写的插件中一般是全局的一些东西，可直接应用于所有的vm和vc上。

**Scoped样式**

- 作用：让样式在局部组件内生效，方式冲突，若冲突后，会按照引入的顺序进行覆盖。

- 写法：<style scoped>

- <style lang="CSS"> <style>表示标签中的内容要按照CSS来解析，如果是less则按照less解析。less的话需要下载相应的插件。

- npm view **webpack** versions 查看当前webpack的版本。

- 一般APP组件中不写scoped，里面一般写全局的一些样式。

**组件的自定义事件**

一种组件间的通信方式：适用于**子组件向父组件传递信息**

适用场景：A是父组件，B是子组件，B想给A传数据，那么就要在A中给B绑定自定义事件（事件的回调在A中）

绑定自定义事件：

1. 在父组件中使用 <Demo @atguigu = "test"/> 若需要事件只触发一次，可以加上事件修饰符.once
2. 在父组件中

```
<Demo ref="demo"/>
……
mounted(){
   this.refs.demo.$on('atguigu',this.test)
}
methods:{
    test(name,...a){ //...a是ES6的缩写表示，把接收到的参数除了name外，全部放在a中，包裹成一个a对象
    }
}
// 若需要事件只触发一次，需要将$on改为$once
```

3. 触发自定义事件：this.$emit('atguigu',参数)  在子组件中进行自定义事件的触发
4. 解绑自定义事件：

```
this.$off('atguigu') //只解绑atguigu事件
this.$off(['atguigu','demo'])  //解绑多个事件
this.$off() //解绑该组件上的所有事件
```

5. 组件上若需要绑定原生DOM事件，需要加上**.native修饰符**，否则将会被认定为自定义事件。
6. 注意：通过this.$refs.xxx.$on('atguigu',回调)绑定自定义事件时，回调要么配置在methods函数中，要么使用箭头函数，否则this指向会出现问题。**$on表示为当前组件绑定自定义事件。**

**全局事件总线** (能够实现任意组件间的通信)  用的更多一点

事件总线要同时满足 1.所有组件以及vm都能够访问到 2.能够调用$on,$off.$emit(不难发现能够满足该条件的只有Vue的原型，即Vue.proptype)

```
new Vue({
    el:'#app',
    render: h =>h(App),
    beforeCreate(){     //在创建之前在vue的原型上绑定x，安装全局事件总线
        Vue.proptype.x = this
    }
})
//一般开发中我们将x命名为$bus，其中$只是为了迎合Vue的设计。
在不用该事件后，最好需要进行解绑
beforeDestroy(){
	this.$bus.$off('hello')
}
```

**消息订阅与发布**（能够实现任意组件间的通信）

类似的库有很多，这里用到的是pubsub-js

使用前先安装该库： npm i pubsub-js

```
首先在发布和订阅之前要引入pubsub-js,即 import pubsub from 'pubsub-js'
需要接受数据的组件需要订阅消息，可写在mounted中
mounted(){
    this.pubId = pubsub.subscribe('hello',(msgName,data)=>{  
     //每次订阅的消息都会生成一个不同的pubId，解绑时要用到
     //注意接受到的第一个参数是该订阅消息的名称，这里是hello
     //这里需要用箭头函数，防止出现问题
    })
}
beforeDestoryed(){
	pubsub.unsubscribe(this.pubId)  //由于库的设计，在解绑时，不是解绑消息名，而是解绑该消息对应的pubId。
}
需要发送消息的组件发布消息
mounted(){
	pubsub.publish('hello',data)
}
```

**nextTick**

1. 语法：this.$nextTick(function(){this.$refs.inputTitle.fouces()})  //这里的this指向的是vc(只要是vue的东西，都会保证this的指向是vc)

2. 作用：在下一次DOM更新结束后执行其指定的回调
3. 使用场景：当数据改变之后，要基于更新后的DOM进行某些操作时，要在$nextTick所指定的回调函数中执行

### **插槽**

作用：让父组件可以向子组件指定位置插入HTML结构，也是一种组件间通信的方式，适用于**父组件=>子组件**

1. **默认插槽**

​    父组件 ： <Student> <img src="#"><Student/>

​    Student子组件：<slot>当未传入数据时显示内容</slot>   //**这里使用一个slot标签对父组件中的组件标签中的内容进行接收，如果没有传递过来数据，则直接展示slot标签中的内容**，至于插槽内的样式，在父组件或者子组件写都可以。

2. **具名插槽**

父组件： 

```
<Student> 
    <img src="#" slot="center"/>
    <a slot = "footer" src = "#">
    <a slot = "footer" src = "#">
<Student/>
//若将后面的两个a标签放到一起写，则重新写为
<Student> 
    <img src="#" slot="center"/>
    <template v-slot:footer>  //这里也可以写为slot="footer",若是在template标签中，也可以使用v-slot:footer这种写法
        <a slot = "footer" src = "#">
        <a slot = "footer" src = "#">
    <template>
<Student/>
```

Student子组件：

```
<slot name="center">当未传入数据时显示内容</slot>
<slot name="footer">当未传入数据时显示内容</slot>
```

- [x] 默认插槽和具名插槽的使用场景是子组件所需要的**数据都在父组件中**

**作用域插槽**：

理解：数据在组件自身，但根据数据生成的结构需要组件的使用者决定

- [x] 作用域插槽可以解决**所需要的插槽数据存放在子组件中的情况**

父组件：

```
<Category>
    <template scope="atguigu">
      <ui>
         <li v-for="(g,index) in atguigu.games" :key="index">{{g}}</li>
      </ui>
    <template/>
</Category>
```

- 注意：这里必须要**使用template标签进行包裹**，scope用于接收子组件slot标签带过去的数据，atguigu是随意取的，其为一个对象，**对象中包含所有件slot标签带过去的数据**（之所以这么写是因为有可能传过去的是多个数据），这里也指示简写形式即写为 **scope="{games}"**，此外新版本中可以将scope写为slot-scope。

子组件

```
<div>
	<slot :games = "games"></slot>
</div>
data(){
	games:['数据一','数据二','数据三']
}
```

- 注意：这里slot标签中的第一个games表示传过去的名字，第二个games表示data中的games数据

**作用域插槽也可以结合具名插槽一起使用**

### vuex