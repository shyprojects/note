# HTML常用标签

## 最简单的一些~

~~~html
<!--标题标签-->
<h1></h1>
<h2></h2>
...
<h6></h6>

<!--段落标签-->
<p></p>

<!--换行标签-->
<br>
~~~

* 文本格式化标签

| 语义   | 标签                         |
| ------ | ---------------------------- |
| 加粗   | <strong></strong>或者<b></b> |
| 倾斜   | <em></em>或者<i></i>         |
| 删除线 | <del></del>或者<s></s>       |
| 下划线 | <ins></ins>或者<u></u>       |

~~~html
<!--div标签-->
<div></div>
<!--span标签-->
<span></span>
~~~

## 图像标签

## 表格标签

~~~html
<!--<table>为整个表格，<th>代表表头，<tr>代表每一行，<td>代表每一个格子。-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <table>
        <tr>    <th>姓名</th> <th>年龄</th> <th>职业</th>    </tr>
        <tr>    <td>Tom</td> <td>21</td> <td>程序员</td>    </tr>
        <tr>    <td>Jerry</td> <td>23</td> <td>医生</td>    </tr>
    </table>
</body>
</html>
~~~

## 表单标签

表单由三部分组成：表单域、表单控件、提示信息。

* 表单域

~~~html
<!--三个属性：action、method、name，此阶段不需要掌握-->
<form action="url地址" method="提交方式" name="表单域名称">
    
<!--此处为表单元素控件-->
    
</form>
~~~

* 提示信息：直接写文字即可。

* 表单元素

  * input输入表单元素

  ~~~html
  <form>
      <!--input的type元素必须有-->
      <input type="test">
  	</input>
  </form>
  ~~~

  | type属性值 | 描述                                                         |
  | ---------- | ------------------------------------------------------------ |
  | button     | 定义可点击按钮(多数情况下通过JS启动脚本)。                   |
  | checkbox   | 定义复选框。                                                 |
  | file       | 定义输入字段和"浏览"按钮，提供文件上传。                     |
  | hidden     | 定义隐藏的输入字段。                                         |
  | image      | 定义图像形式的提交按钮。                                     |
  | password   | 定义密码字段，字段字符会被掩盖。                             |
  | radio      | 定义单选按钮。                                               |
  | reset      | 定义重置按钮，清楚表单中所有数据。                           |
  | submit     | 定义提交按钮，提交后会把表单数据发送到服务器。               |
  | test       | 定义单行的输入字段，用户可以在其中输入文本，默认宽度文20个字符。 |

  input其他两个属性：

  * name：定义单选按钮时，一组选项的name值应该相同。
  * value：设置一个默认值，主要是后台使用。
  * checked：对于单选按钮和复选框设置一个默认值。
  * maxlength：设置最大长度。

