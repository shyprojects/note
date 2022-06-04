# let声明变量的特点

* 声明变量不能重复

![image-20220426222027899](D:\note_Typora\HTML+CSS+JavaScript\ES6.assets\image-20220426222027899.png)

* 块级作用域

![image-20220426222413993](D:\note_Typora\HTML+CSS+JavaScript\ES6.assets\image-20220426222413993.png)

* 不影响作用域链

![image-20220426222924006](D:\note_Typora\HTML+CSS+JavaScript\ES6.assets\image-20220426222924006.png)

# const声明变量的特点

* 必须赋初始值
* 常量名一般为大写
* 常量值不能修改
* 块级作用域
* 对数组和对象的元素修改，不算对常量的修改，不会报错。

# 变量的解构赋值

* ES6允许按照一定模式从数组和对象中提取值，对变量进行复制，这被称为解构赋值

  * 数组的解构赋值：

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <script>
          const arr = [123,'zhangsan','lisi'];
          let [num,name1,name2] = arr;
          console.log(num);
          console.log(name1);
          console.log(name2);
      </script>
  </head>
  <body>
  </body>
  </html>
  ```

  * 对象的解构赋值

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <script>
          const obj = {
              name:'zhangsan',
              age:23,
              fun:function (){
                  console.log('法外狂徒');
              }
          }
          let {name,age,fun} = obj;
          console.log(name);
          console.log(age);
          console.log(fun);
          fun();
      </script>
  </head>
  <body>
  </body>
  </html>
  ```

# 模板字符串

* es6中可以使用``声明字符串
* 特点：
  * 内容中可以直接换行
  * 新的变量拼接方式

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
        let str = `zhangsan
                   lisi
                   wangwu`;
        let str1 = `张三`;
        let str2 = `${str1}是我心目中最好的演员`;
        console.log(str);
        console.log(str2);
    </script>
</head>
<body>
</body>
</html>
```

# 简化的对象写法

* es6中允许在{}中直接写入变量和函数，作为对象的属性和方法。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
        let name = `张三`;
        let fun = function (){
            console.log(`法外狂徒`);
        }
        const obj = {
            name,
            fun,
            show(){
                console.log(`法外狂徒-张三`);
            }
        }
        console.log(obj);
    </script>
</head>
<body>
</body>
</html>
```

# 箭头函数

* 创建和调用：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
        let fun = (n)=>{
            return n*n;
        }
        let fun1 = fun(2);
        console.log(fun1);
    </script>
</head>
<body>
</body>
</html>
```

* 简写：与lambda表达式类似
  * 一个参数可以省略()
  * 函数体只有一个语句可以省略{}
  * 只有一个语句return需要被省略
* 特点：
  * this指针是静态的，始终指向函数声明时下的this.
  * 不能作为构造函数实例化对象.
  * 不能调用arguments