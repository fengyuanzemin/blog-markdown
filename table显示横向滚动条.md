---
title: table显示横向滚动条
date: 2017-08-07
tags: [HTML, CSS]
---

如果有这样的html结构

<!--more-->

```html
<div className="table-container">
    <table>
        <thead>
            <tr>
            {
                tHead
            }
            </tr>
        </thead>
        <tbody>
        {
            tBody
        }
        </tbody>
    </table>
</div>
```

这样写CSS就能使表格出现横向滚动条

```css
.table-container {
   overflow-x: scroll;
}
th, td {
    padding: 6px;
    min-width: 50px;
}
```

注意，min-width 必不可少，否则表格会堆在一起

