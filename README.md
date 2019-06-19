# copytext

h5实现一键复制到粘贴板 兼容ios

## 实现原理

采用`document.execCommand('copy')`来实现复制到粘贴板功能

复制必须是选中input框的文字内容，然后执行`document.execCommand('copy')`命令实现复制功能。
初步实现方案
```
const input = document.querySelector('#copy-input');
    if (input) {
      input.value = text;
      if (document.execCommand('copy')) {
        input.select();
        document.execCommand('copy');
        input.blur();
        alert('已复制到粘贴板');
      }
    }

```

## 兼容性问题

1. input 输入框不能`hidden`或者`display: none`;
 如果需要隐藏输入框可以使用定位脱离文档流，然后移除屏幕

```
#copy-input{
position: absolute;
left: -1000px;
z-index: -1000;
}
```
2.ios下不能执行document.execCommand('copy')

在ios设备下``alert(document.execCommand('copy'))``一直返回``false``
查阅相关资料发现ios下input不支持``input.select();``

> 因此拷贝的文字必须存在，不能为空字符串，不然也不会执行复制空字符串的功能

参考这篇博客实现了ios下复制的功能 [https://blog.csdn.net/VLilyV/article/details/77523591][1]

主要是使用``textbox.createTextRange``方法选中输入框的文字
```
// input自带的select()方法在苹果端无法进行选择，所以需要自己去写一个类似的方法
// 选择文本。createTextRange(setSelectionRange)是input方法
function selectText(textbox, startIndex, stopIndex) {
  if (textbox.createTextRange) {//ie
    const range = textbox.createTextRange();
    range.collapse(true);
    range.moveStart('character', startIndex);//起始光标
    range.moveEnd('character', stopIndex - startIndex);//结束光标
    range.select();//不兼容苹果
  } else {//firefox/chrome
    textbox.setSelectionRange(startIndex, stopIndex);
    textbox.focus();
  }
}
```
3.ios设备上复制会触发键盘弹出事件

给input加上``readOnly``只读属性

# 代码
踩完以上的坑，总结的代码如下
git地址 [https://github.com/zhaosheng808/copytext][2]

  [1]: https://blog.csdn.net/VLilyV/article/details/77523591
  [2]: https://github.com/zhaosheng808/copytext