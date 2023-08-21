# JavaScript 基础学习

## 1. JS的调用方式与执行顺序

### 1.1. 使用方式

HTML页面中的任意位置加上`<script type="module"></script>`标签即可。

常见使用方式有以下几种：

- 直接在`<script type="module"></script>`标签内写JS代码。

- 直接引入文件：`<script type="module" src="/static/js/index.js"></script>`。

- 将所需的代码通过 import 关键字引入到当前作用域。

例如：

**`/static/js/index.js文件中的内容为：`**

```js
let name = "acwing";

function print() {
    console.log("Hello World!");
}

export {
    name,
    print
}
```

**`<script type="module"></script>中的内容为：`**


```js
<script type="module">
    import { name, print } from "/static/js/index.js";

    console.log(name);
    print();
</script>
```


### 1.2. 执行顺序

类似于HTML与CSS，按从上到下的顺序执行；

事件驱动执行；

HTML, CSS, JavaScript三者之间的关系

CSS控制HTML

JavaScript控制HTML与CSS

为了方便开发与维护，尽量按照上述顺序写代码。例如：不要在HTML中调用JavaScript中的函数。



## 2. 变量与运算符

### 2.1. let与const

用来声明变量，作用范围为当前作用域。

- let用来定义变量；
- const用来定义常量；

例如：

```js
let s = "fanxy", x = 5;

let stu = {
    name: "wxf",
    age: 18,
}

const n = 100;

获取对象的属性两种方式 
console.log(stu.age);
console.log(stu["age"]);

后者可以传变量
```



### 2.2. 变量类型

- **number：数值变量**，例如1, 2.5
- **string：字符串**，例如"fanxy", 'www'，单引号与双引号均可。字符串中的每个字符为只读类型。
- **boolean：布尔值**，例如true, false
- **object：对象**，类似于C++中的指针，例如[1, 2, 3]，{name: "yxc", age: 18}，null
- **undefined：未定义的变量**
- 类似于Python，**JavaScript中的变量类型可以动态变化。**

```js
console.log(typeof stu.age, typeof null)
可以看到 null 是对象类型
```



### 2.3. 运算符

与C++、Python、Java类似，不同点：

- `**`表示乘方
- 等于与不等于用 `===` 和 `!==` ，而 `==` 会隐式转换类型， `1 == "1"` 为 `true`
- `Math.round(x)` 、`Math.ceil(x)`和`Math.floor(x)`分别表示将x向0、向上和向下取整
- `parseInt(x)`表示也可以实现向0取整 ，类似 `JAVA`



## 3. 输入与输出

### 3.1. 输入

从HTML与用户的交互中输入信息，例如通过input、textarea等标签获取用户的键盘输入，通过click、hover等事件获取用户的鼠标输入。

**通过Ajax与WebSocket从服务器端获取输入**



### 3.2. 输出

调试用console.log，会将信息输出到浏览器控制台

改变当前页面的HTML与CSS

通过Ajax与WebSocket将结果返回到服务器



**事件监听：**

```css
.input {
    width: 300px;
    height: 300px;
    background-color: lightblue;
}

.output {
    width: 300px;
    height: 300px;
    background-color: bisque;
}
```

```js
let input = document.querySelector(".input");
let run = document.querySelector("button");
let output = document.querySelector("pre");

function main() {
    run.addEventListener("click", function () {
        let s = input.value;
        output.innerHTML = s;
    })
}

export {
    main
}
```

```html
<label>
    文本框 <br>
<textarea class="input">

</textarea>
</label>

<br>
<button class="inputButton" style="font-size: 20px">提交</button>
<br>
<pre class="output">

</pre>

<script type="module">
    import {main} from "/static/js/index2.js";
    main();
</script>
```



### 3.3. 格式化字符串

字符串中填入数值：

```js
let name = 'wxf', age = 18;
let s = `My name is ${name}, I'm ${age} years old.`;
```



## 4. 判断语句

JavaScript中的if-else语句与C++、Python、Java中类似。

例如：

```js
let score = 90;
if (score >= 85) {
    console.log("A");
} else if (score >= 70) {
    console.log("B");
} else if (score >= 60) {
    console.log("C");
} else {
    console.log("D");
}
```

JavaScript中的逻辑运算符也与C++、Java中类似：

- **&&表示与**
- **||表示或**
- **!表示非**



## 5. 循环语句

**JavaScript中的循环语句与C++中类似，也包含for、while、do while循环。**

**for循环**

```js
for (let i = 0; i < 10; i++) {
    console.log(i);
}
```

**枚举对象或数组时可以使用：**

- for-in循环，可以枚举数组中的下标，以及对象中的key
- for-of循环，可以枚举数组中的值，以及对象中的value

**while循环**

```js
let i = 0;
while (i < 10) {
    console.log(i);
    i++;
}
```

**do while循环**
do while语句与while语句非常相似。唯一的区别是，do while语句限制性循环体后检查条件。不管条件的值如何，我们都要至少执行一次循环。

```js
let i = 0;
do {
    console.log(i);
    i++;
} while (i < 10);
```



## 6. 对象【大括号】

英文名称：Object。

类似于C++中的map，由key:value对构成。

value可以是变量、数组、对象、函数等。

函数定义中的this用来引用该函数的“拥有者”。

例如：

```js
let person = {
    name: "wxf",
    age: 18,
    money: 0,
    add_money: function (x) {
        this.money += x;
    }
}
```

对象属性与函数的调用方式：

```js
第一种方式
person.name		person.add_money()

第二种方式
person["name"]  person["add_money"]()
```



## 7. 数组【中括号】

数组是一种特殊的对象。

类似于C++中的数组，但是数组中的元素类型可以不同。

数组中的元素可以是变量、数组、对象、函数。

js 中的数组和函数没有下标的概念，可以越过不存在的下标，直接定义一个很远的下标值是允许的

例如：

```js
let a = [1, 2, "a", "yxc"];

let b = [
    1,  // 变量
    "yxc",  // 变量
    ['a', 'b', 3],  // 数组
    function () {  // 函数
        console.log("Hello World");
    },
    { name: "yxc", age: 18 }  // 对象
];
```

如果一个函数没有返回值，使用 `console.log(function())` 会把函数的定义输出

如果想要使用数组中的函数，只需要加括号

```js
console.log(b[3]())
```

没有返回值会输出 `undefined` ，有返回值输出返回值



**访问数组中的元素**

通过下标。

例如：

```js
a[0] = 1; // 访问数组a[]的第0个元素
console.log(a[0]);
```

**数组的常用属性和函数**

- **属性length**：返回数组长度。注意length是属性，不是函数，因此调用的时候不要加()
- **函数push()**：向数组末尾添加元素
- **函数pop()**：删除数组末尾的元素
- **函数splice(a, b)**：删除从a开始的b个元素
- **函数slice(a, b)**：类似 string 的 substring，返回 (frontIndex, endIndex]
- **函数sort()**：将整个数组从小到大排序
  - **自定义比较函数：array.sort(cmp)，函数 cmp 输入两个需要比较的元素，返回一个实数，负数表示第一个参数小于第二个参数，0表示相等，正数表示大于。**

```js
array.sort(function(a, b) => {
           return a - b
           });
```



## 8. 函数

函数是用对象来实现的。【但是 typeof 类型是 function】

函数也C++中的函数类似。



**定义方式**

```js
function add(a, b) {
    return a + b;
}

let add = function (a, b) {
    return a + b;
}

let add = (a, b) => {
    return a + b;
}
```



**返回值**

如果未定义返回值，则返回 `undefined`。



## 9. 类

与C++中的Class类似。但是不存在私有成员。

**this指向类的实例。**

```js
class Point {
    constructor(x, y) {  // 构造函数
        this.x = x;  // 成员变量
        this.y = y;

        this.init();
    }

    init() {
        this.sum = this.x + this.y;  // 成员变量可以在任意的成员函数中定义
    }

    toString() {  // 成员函数
        return '(' + this.x + ', ' + this.y + ')';
    }
}

let p = new Point(3, 4);
console.log(p.toString());
```



**继承**

```js
class ColorPoint extends Point {
    constructor(x, y, color) {
        super(x, y); // 这里的super表示父类的构造函数
        this.color = color;
    }

    toString() {
        return this.color + ' ' + super.toString(); // 调用父类的toString()
    }
}
```

**注意：**

- `super`这个关键字，既可以当作函数使用，也可以当作对象使用。

  - 作为函数调用时，代表父类的构造函数，且只能用在子类的构造函数之中。

  - `super`作为对象时，指向父类的原型对象。

- 在子类的构造函数中，只有调用`super`之后，才可以使用this关键字。

- 成员重名时，子类的成员会覆盖父类的成员。类似于C++中的多态。



**静态方法**

在成员函数前添加 `static` 关键字即可。静态方法不会被类的实例继承，只能通过类来调用。例如：

```js
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }

    toString() {
        return '(' + this.x + ', ' + this.y + ')';
    }

    static print_class_name() {
        console.log("Point");
    }
}

let p = new Point(1, 2);
Point.print_class_name();
p.print_class_name();  // 会报错
```



**静态变量**

**在ES6中，只能通过`class.propname`定义和访问。【目前可以通过 `static` 关键字来绑定】**

```js
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;

        Point.cnt++;
    }

    toString() {
        return '(' + this.x + ', ' + this.y + ')';
    }
}

Point.cnt = 0;

let p = new Point(1, 2);
let q = new Point(3, 4);

console.log(Point.cnt);
```



## 10. 事件

`JavaScript`的代码一般通过事件触发。

**可以通过`addEventListener`函数为元素绑定事件的触发函数。**

常见的触发函数有：



**鼠标**

- **click**：鼠标左键点击
- **dblclick**：鼠标左键双击
- **contextmenu**：鼠标右键点击
- **mousedown**：鼠标按下，包括左键、滚轮、右键
- **mouseup**：鼠标弹起，包括左键、滚轮、右键【必须在指定的位置（事件监听的地方）】
- **event.button**：0表示左键，1表示中键，2表示右键



**键盘**

- **keypress：**按了某个键，**但是必须字符键，而下面两个可以是功能键**

- **keydown**：某个键是否被按住，事件会连续触发
  - **event.code**：返回按的是哪个键，返回的是 "keyX"，`x` 代表按的那个键
  - **event.altKey、event.ctrlKey、event.shiftKey** 分别表示是否同时按下了alt、ctrl、shift键。
- **keyup**：某个按键是否被释放
  - **event**常用属性同上
  - **keypress**：紧跟在keydown事件后触发，只有按下字符键时触发。适用于判定用户输入的字符。
  - **event**常用属性同上

**keydown、keyup、keypress 的关系类似于鼠标的mousedown、mouseup、click**



这里首先必须是一个可以聚焦的组件，这里**其实可以通过给组件加 `tabindex=xxxx` 使得组件可以聚焦。**

**它接受一个整数作为值，具有不同的结果，具体取决于整数的值：**

- **tabindex=负值 (通常是 tabindex=“-1”)**，表示元素是可聚焦的，但是不能通过键盘导航来访问到该元素，用 JS 做页面小组件内部键盘导航的时候非常有用。
- **`tabindex="0"`** ，表示元素是可聚焦的，并且可以通过键盘导航来聚焦到该元素，它的相对顺序是当前处于的 DOM 结构来决定的。
- **tabindex=正值**，表示元素是可聚焦的，并且可以通过键盘导航来访问到该元素；它的相对顺序按照**tabindex** 的数值递增而滞后获焦。如果多个元素拥有相同的 **tabindex**，它们的相对顺序按照他们在当前 DOM 中的先后顺序决定。

根据键盘序列导航的顺序，值为 `0` 、非法值、或者没有 **tabindex** 值的元素应该放置在 **tabindex** 值为正值的元素后面。



**表单**

- **focus**：聚焦某个元素
- **blur**：取消聚焦某个元素
- **change**：某个元素的内容发生了改变，比如应用于输入框



**窗口**

需要作用到window元素上。【比如网页的页面】

- **resize**：当窗口大小发生变化
- **scroll**：滚动指定的元素
- **load**：当元素被加载完成



## 11. 常用API

### 11.1. setTimeout与setInterval

**setTimeout(func, delay)**

delay毫秒后，执行函数func()。



**clearTimeout()**

关闭定时器，例如：

```js
let timeout_id = setTimeout(() => {
    console.log("Hello World!")
}, 2000);  // 2秒后在控制台输出"Hello World"

clearTimeout(timeout_id);  // 清除定时器
```

**setInterval(func, delay)**

每隔delay毫秒，执行一次函数func()。

第一次在第delay毫秒后执行。



**clearInterval()**

关闭周期执行的函数，例如：

```js
let interval_id = setInterval(() => {
    console.log("Hello World!")
}, 2000);  // 每隔2秒，输出一次"Hello World"

clearInterval(interval_id);  // 清除周期执行的函数
```



### 11.2. requestAnimationFrame(func)

该函数会在下次浏览器刷新页面之前执行一次，通常会用递归写法使其每秒执行60次func函数。调用时会传入一个参数，表示函数执行的时间戳，单位为毫秒。

例如：

```js
let step = (timestamp) => {  // 每帧将div的宽度增加1像素
    let div = document.querySelector('div');
    div.style.width = div.clientWidth + 1 + 'px';
    requestAnimationFrame(step);
};

requestAnimationFrame(step);
```

与`setTimeout`和`setInterval`的区别：

- **`requestAnimationFrame`渲染动画的效果更好，性能更加。**
  该函数可以保证每两次调用之间的时间间隔相同，但`setTimeout`与`setInterval`不能保证这点。setTmeout两次调用之间的间隔包含回调函数的执行时间；setInterval只能保证按固定时间间隔将回调函数压入栈中，但具体的执行时间间隔仍然受回调函数的执行时间影响。
- **当页面在后台时，因为页面不再渲染，因此`requestAnimationFrame`不再执行。但`setTimeout`与`setInterval`函数会继续执行。**



### 11.3. Map与Set

**Map**

Map 对象保存键值对。

用for...of或者forEach可以按插入顺序遍历。

键值可以为任意值，包括函数、对象或任意基本类型。

**常用API：**

- set(key, value)：插入键值对，如果key已存在，则会覆盖原有的value
- get(key)：查找关键字，如果不存在，返回undefined
- size：返回键值对数量
- has(key)：返回是否包含关键字key
- delete(key)：删除关键字key
- clear()：删除所有元素



**Set**

Set 对象允许你存储任何类型的唯一值，无论是原始值或者是对象引用。

用for...of或者forEach可以按插入顺序遍历。

**常用API：**

- add()：添加元素
- has()：返回是否包含某个元素
- size：返回元素数量
- delete()：删除某个元素
- clear()：删除所有元素



### 11.4. localStorage

可以在用户的浏览器上存储键值对。

类似 Cookie 但是可以存比 Cookie 更大内存的数据。

常用API：

**setItem(key, value)**：插入

**getItem(key)**：查找

**removeItem(key)**：删除

**clear()**：清空



### 11.5. JSON

JSON对象用于序列化对象、数组、数值、字符串、布尔值和null。

常用API：

- **JSON.parse()**：将字符串解析成对象
- **JSON.stringify()**：将对象转化为字符串



### 11.6. 日期

返回值为整数的API，数值为1970-1-1 00:00:00 UTC（世界标准时间）到某个时刻所经过的毫秒数：

- **Date.now()**：返回现在时刻。
- **Date.parse("2022-04-15T15:30:00.000+08:00")**：返回北京时间2022年4月15日 15:30:00的时刻。

**与Date对象的实例相关的API：**

- **new Date()**：返回现在时刻。
- **new Date("2022-04-15T15:30:00.000+08:00")**：返回北京时间2022年4月15日 15:30:00的时刻。
  两个Date对象实例的差值为毫秒数
- **getDay()**：返回星期，0表示星期日，1-6表示星期一至星期六
- **getDate()**：返回日，数值为1-31
- **getMonth()**：返回月，数值为0-11
- **getFullYear()**：返回年份
- **getHours()**：返回小时
- **getMinutes()**：返回分钟
- **getSeconds()**：返回秒
- **getMilliseconds()**：返回毫秒



### 11.7. WebSocket

**HTTP只能客户端发起通信，但很多时候都需要服务器给用户端发请求**

与服务器建立全双工连接。

常用API：

- **new WebSocket('ws://localhost:8080');**：建立ws连接。
- **send()**：向服务器端发送一个字符串。一般用JSON将传入的对象序列化为字符串。
- **onopen**：类似于onclick，当连接建立时触发。
- **onmessage**：当从服务器端接收到消息时触发。
- **close()**：关闭连接。
- **onclose**：当连接关闭后触发。



### 11.8. window

- **window.open("https://www.bilibili.com") **                     在新标签栏中打开页面。
- **location.reload()**                                                                     刷新页面。
- **location.href = "https://www.bilibili.com"**：                 在当前标签栏中打开页面。



### 11.9. canvas

画图的API                H5 新加的API

官网                         https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial























