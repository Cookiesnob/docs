<h1><center>居中内容</center></h1>

方法①

使用标签

````html
<center>居中内容</center>  /*将你希望居中的内容包含即可。*/
````

方法②

```html
<div align=center>居中内容</div>    /*使用div标签，将你希望居中的内容包含即可。*/
```

方法③

添加自定义css文件。css文件中加入如下样式

````css
img{

  positon:relative;

  width:80%;

  left:10%;/*left为（img父元素元素的width - img元素自己的width)÷2*/

}
````

这个方法可以使得文档中所有的img元素都居中，缺点是不想居中的图片都居中了。但可结合方法①方法②来调整你不想居中的图片的位置。

