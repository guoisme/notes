# sass笔记
## 定义变量与值
```
$bgcolor: #f7f7f7;
$textcolor: #333;
$fontsize: 13px;
body {
  background-color: $bgcolor;
  color: $textcolor;
  font-size: $fontsize;
}
```
## 嵌套规则与属性
```
nav{
  margin:0;
  li{
    display: inline-block;
  }
  text: {
    align: center;
    transform: lowercase;
  }
}

```
转为css
```
nav{margin:0;text-align:center;text-transform:lowercase;}
nav li{display:inline-block;}
```
## 导入文件
```
@import "reset";//导入reset.scss
```
## @mixin 与 @include
引入重复使用的样式
```
@mixin repeat-text{
  color:#666;
  font-size:12px;
  font-family:Thoma;
}
.selector{
  @include repeat-text;
}
```
### 向混入传递变量
```
@mixin bordered($color:blue, $width:1px) {
  border: $width solid $color;
}
.selecor{
  @include bordered(#f7f7f7);
}
```
### 参数不确定
```
@mixin box-shadow($shadows...) {
  -moz-box-shadow: $shadows;
  -webkit-box-shadow: $shadows;
  box-shadow: $shadows;
}
.shadows {
  @include box-shadow(0px 4px 5px #666, 2px 6px 10px #999);
}
```
### 浏览器前缀使用混入
```
@mixin transform($property){
  -webkit-transform: $property;
  -ms-transform: $property;
  transform: $property;
}
.myBox {
  @include transform(rotate(20deg));
}
```
### @extend 与 继承
继承另一个选择器的样式
```
.button{
  padding:10px 15px;
  font-size:16px;
  text-align:center;
}
.danger-button{
  @extend .button;
  border:1px solid red;
  color:red;
}
```