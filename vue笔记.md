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