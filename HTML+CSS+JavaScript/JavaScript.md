# 事件

## 事件对象

* 当我们为事件绑定一个函数时，浏览器会自动传入一个对象，这个对象含有事件的相关属性。
* 例如鼠标移动事件，该事件浏览器传入一个对象，可以通过该对象获取鼠标的x坐标y坐标。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
        window.onload = function (){
            var elementById = document.getElementById("div1");
            elementById.onmousemove = function (event){
                var elementById1 = document.getElementById("div2");
                elementById1.innerHTML = "x="+event.clientX+"y="+event.clientY;
            }
        }
    </script>
</head>
<body>
<div id="div1">我是一个div</div>
<div id="div2"></div>

</body>
</html>
```

* 不存在变量提升

![image-20220426222722382](D:\note_Typora\HTML+CSS+JavaScript\JavaScript.assets\image-20220426222722382.png)