## 文档对象模型

### No.1 DOM 基本的操作
 
**查询：**
    ```
    getElementById()、getElementsByTagName()、getElementsByName()
    querySelector()、querySelectorAll()
    ```

**创建：**
    ```
    createElement(element)、createTextNode(text)
    ```

**添加：**
    ```
    父节点.appendChild(child)
    ```

**删除：**
    ```
    父节点.removeChild(child)
    ```

**插入：**
    ```
    父节点.insertBefore(prev, next)
    ```

**替换：**
    ```
    父节点.replaceChild(new, old)
    ```

**复制：**
    ```
    node.cloneNode(deep)
    ```
    
**样式 - 获取元素当前的样式：**
    ```
    function getStyle(obj , name) {
        if(window.getComputedStyle) {
                return getComputedStyle(obj,null)[name]; 
        } else {
                return obj.currentStyle[name];// IE8及以下
        }
    }
    ```

### No.2 事件的处理

**绑定dom元素的事件监听**
    ```
    addEventListener('evType', cb, false)
    attachEvent('on'+'evType', cb) // IE8及以下
    ```

**解绑dom事件监听**
    ```
    removeEventListener()
    detachEvent() // IE8及以下
    ```

**事件对象 event**
    ```
    button.onclick = function(event) {
        // 获取事件对象的相关信息
        event = event || window.event // IE8及以下
        
        // 获取触发事件的目标元素
        target
        srcElement // IE8及以下
        
        // 阻止默认行为
        preventDefault()
        return false // IE8及以下
        
        // 阻止事件冒泡
        cancelBubble // IE8及以下
        stopPropagation()
    }
    ```

**模拟事件**
    ```
    dispatchEvent()
    fireEvent() // IE8及以下
    ```

### No.3 事件的传递

DOM 事件流存在三个阶段：事件捕获阶段、处于目标阶段、事件冒泡阶段。

**捕获：**
即点击了子元素，如果父元素通过事件捕获方式注册了对应的事件的话，会先触发父元素绑定的事件。

**冒泡：**
与事件捕获恰恰相反，事件冒泡顺序是由内到外进行事件传播，直到根节点。

在通过 DOM2 的方式(addEventListener)添加事件监听时，可通过该函数的第三个参数指定是否采用事件捕获进行事件捕捉，默认情况下为 false，即采用事件冒泡方式。

事件委托，事件委托就是利用事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。<br>
采用事件委托的优势，
    1. 减少事件的绑定
    2. 统一处理新增 DOM 元素的事件效果

### No.9 如何通过 JS 获取 url 中的参数信息?

#### 方式一：采用正则表达式获取地址栏参数
```
function getRequestParam(key) {
    // 取 &key=value& 结构进行字符串匹配
    let reg = new RegExp("(?:^|&)" + key + "=([^&]*)(?:&|$)", "i");
    // 取 url 中 ? 后面的字符串
    let regArr = window.location.search.substr(1).match(reg);
    if (regArr !== null) {
        return encodeURI(regArr[1]);
    };
    return null;
}
```

#### 方式二：split 拆分法
```
function getRequestParam(key) {
    let queryStr = window.location.search.substring(1);
    // 以 & 字符对查询字符串进行拆分
    let params = queryStr.split("&");
    for (let i = 0; i < params.length; i++) {
        // 以 = 字符对数组每个元素(即参数)进行拆分
        let pair = params[i].split("=");
        if (pair[0] === key) {
            return pair[1];
        }
    }
    return null;
}
```