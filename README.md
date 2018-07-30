HTML5 编辑器的设计总结

---

# 参考的示例总结

## https://github.com/answershuto/H5

### 特点

前端采用了 vue + vuex

### 存储数据结构

```js
{
  "_id": {
    "$oid": "5b5ddd6269799f8454b49835"
  },
  "userName": "admin",
  "workName": "test",
  "designInfos": {
    "pages": [{
        "backgroundColor": "#FFFFFF",
        "image": [],
        "text": [{
          "animationStyle": {
            "animation-timing-function": "ease",
            "animation-duration": "1s",
            "animation-name": "fadeInLeft"
          },
          "style": {
            "padding": "5px",
            "line-height": "242%",
            "background-color": "transparent",
            "font-size": "235%",
            "color": "black",
            "top": "29%",
            "left": "0%"
          },
          "text": "这是第一页",
          "id": "design_1"
        }],
        "id": "page_0"
      },
      {
        "backgroundColor": "#FFFFFF",
        "image": [],
        "text": [{
          "animationStyle": {
            "animation-timing-function": "ease",
            "animation-duration": "1s",
            "animation-name": "fadeInLeft"
          },
          "style": {
            "padding": "0px",
            "line-height": "100%",
            "background-color": "transparent",
            "font-size": "100%",
            "color": "black",
            "top": "10%",
            "left": "12%"
          },
          "text": "这是第二页",
          "id": "design_3"
        }],
        "id": "page_1"
      }
    ],
    "currentBackgroundColor": "#FFFFFF",
    "currentPage": "page_0",
    "currentElementType": "text",
    "currentElement": "",
    "music": "",
    "pageId": 2,
    "id": 4
  },
  "date": {
    "$date": "2018-07-29T15:29:38.087Z"
  },
  "__v": 0
}
```

## https://github.com/postor/html5-generator

### 特点

编辑设计的通用化

### 存储数据结构

```js
{
  "_id": {
    "$oid": "5b5de5648ec7e21c8024fff1"
  },
  "email": "123@qq.com",
  "name": null,
  "title": "新的项目01",
  "desc": null,
  "keywords": null,
  "tags": null,
  "css": ".page1{}\n\r.e0{top:9.86%;left:29.34%;width:20%;height:20%;font-size:12px;border-color:#000;border-style:solid;border-width:0px;background-color:transparent;font-family:serif;align-items:center;justify-content:center;}\n\r.t0{opacity:1;transition:all 0.6s ease 0s;-webkit-transition:all 0.6s ease 0s;}\n.p0 .t0{transform:translate(0,0) rotate(0deg) scale(1);-webkit-transform:translate(0,0) rotate(0deg) scale(1);opacity:1;}\n\r.p0 .fadein0{-webkit-animation:fadein0 0.6s ease 0s normal 1 ;animation:fadein0 0.6s ease 0s normal 1 ;}\n\r\n\r@-webkit-keyframes fadein0{0%{}100%{opacity:1;}},\n\r@keyframes fadein0{0%{}100%{opacity:1;}}",
  "javascript": null,
  "additionalScriptCount": null,
  "additionalScripts": null,
  "createdTime": "2018-07-29T16:03:48.834Z",
  "modifiedTime": {
    "$date": "2018-07-29T16:05:46.714Z"
  },
  "pageCount": null,
  "pageHtmls": [
    "<div class=\"f t0\"><div class=\"e e0 fadein0\">第一页的测试</div></div>"
  ],
  "items": {
    "0": {
      "id": 0,
      "name": "未命名0",
      "text": "第一页的测试",
      "type": "text",
      "style": {
        "top": 9.86,
        "left": 29.34,
        "width": 20,
        "height": 20,
        "fontSize": 12,
        "borderColor": "#000",
        "borderStyle": "solid",
        "borderWidth": 0,
        "backgroundColorTransparent": true,
        "fontFamilyBackup": "serif",
        "alignItems": "center",
        "justifyContent": "center"
      },
      "animate": {
        "enabled": true,
        "type": "fadeIn",
        "offsetX": 0,
        "offsetY": 0,
        "rotate": 0,
        "scale": 1,
        "delay": 0,
        "duration": 0.6,
        "timingFunction": "ease",
        "iterationCount": 1,
        "opacity": 1
      },
      "transition": {
        "enabled": true,
        "offsetX": 0,
        "offsetY": 0,
        "rotate": 0,
        "scale": 1,
        "delay": 0,
        "duration": 0.6,
        "timingFunction": "ease",
        "opacity": 1
      }
    }
  },
  "curId": 1,
  "pages": [{
    "items": [
      0
    ]
  }],
  "links": [],
  "currentResolution": "iphone6",
  "id": "5b5de5648ec7e21c8024fff1"
}
```

# 我们的设计

## 需要满足的功能

- 分辨率的设置；包含横竖屏的切换
- 部件的列表框；主要解决可快速选择部件的问题，类比 ps 中的图层功能
- 编辑的历史记录；用于编辑的撤销和恢复撤销
- 预览；
- 发布；
- 生成模板；
- 部件的旋转和自由拖拽；
- 整体的背景音乐；

## 实现的方法概述

### 基本原则

每一个编辑的部件都是一个对象；

现在必须有的两个的基本类：页面和页面中组件

每个组件的含有通用编辑框，包含通用的属性，如：位置、尺寸、动画、互动功能的选择、自定义脚本和拖拽旋转的实现；所有组件都是通过其含有哪些特殊属性来进行区分；

### 数据结构

```json
{
  "id": "5b5de5648ec7e21c8024fff1",
  "name": null,
  "title": "新的项目 01",
  "desc": null,
  "keywords": null,
  "tags": null,
  "createdTime": "2018-07-29T16:03:48.834Z",
  "modifiedTime": {
    "$date": "2018-07-29T16:05:46.714Z"
  },
  "pageCount": null,
  "pageHtmls": [
    "<div class=\"f t0\"><div class=\"e e0 fadein0\">第一页的测试</div></div>"
  ],
  "links": [],
  "assets": {
    "images": [],
    "fonts": [],
    "audio": [],
    "video": [],
    "pdf": []
  },
  "backgroudMusic": {
    "autoplay": true,
    "volum": 0.1,
    "src": ""
  },
  "items": {
    "0": {
      "id": 0,
      "name": "未命名 0",
      "text": "第一页的测试",
      "type": "text",
      "style": {
        "top": 9.86,
        "left": 29.34,
        "width": 20,
        "height": 20,
        "fontSize": 12,
        "borderColor": "#000",
        "borderStyle": "solid",
        "borderWidth": 0,
        "backgroundColorTransparent": true,
        "fontFamilyBackup": "serif",
        "alignItems": "center",
        "justifyContent": "center"
      },
      "animate": {
        "enabled": true,
        "type": "fadeIn",
        "offsetX": 0,
        "offsetY": 0,
        "rotate": 0,
        "scale": 1,
        "delay": 0,
        "duration": 0.6,
        "timingFunction": "ease",
        "iterationCount": 1,
        "opacity": 1
      },
      "transition": {
        "enabled": true,
        "offsetX": 0,
        "offsetY": 0,
        "rotate": 0,
        "scale": 1,
        "delay": 0,
        "duration": 0.6,
        "timingFunction": "ease",
        "opacity": 1
      },
      "additionalScripts": null
    }
  },
  "curId": 1,
  "pages": [
    {
      "backgroundColor": "#FFFFFF",
      "order": 0,
      "id": "page_0",
      "animation": "default",
      "items": [0]
    }
  ],
  "currentResolution": "iphone6"
}
```

### todo

- 历史记录的保存格式
- 预览的实现方式
- 终端的解析原则
