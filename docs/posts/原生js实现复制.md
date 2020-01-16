---
title: 原生js实现复制
tag: 
---
```
const container = document.createElement('textarea');
container.innerHTML = JSON.stringify(
  respJSON,
  null,
  2
);
document.body.appendChild(container);
container.select();
document.execCommand('copy');
document.body.removeChild(container);
```

新建一个输入框，将要复制的文本作为其value，选中，调用document.execCommand('copy'),然后移除输入框。

还可以使用cut, paste执行剪切和粘贴

