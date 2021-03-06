---
title: TreeGraph
order: 2
---

TreeGraph是G6专门为树图场景打造的图。TreeGraph继承自Graph。`G6.TreeGraph`与`G6.Graph`最大的区别就是数据结构和内置布局计算。主要出于以下考虑：

- 数据结构：树图的数据一般是嵌套结构，边的数据隐含在嵌套结构中，并不会特意指定edge。此布局要求数据中一个节点需要有`id`和`children`两个数据项，最精简的数据结构如下所示：

```javascript
const data = {
  id: 'root',
  children: [
    {
      id: 'subTree1',
      children: [...]
    }, 
    {
      id: 'subTree2',
      children: [...]           
    }
  ]
};
```

- 布局特殊性：
  - 树图的布局算法一般是不改变源数据的，而是重新生成一份数据，将源数据作为新数据的一个属性。如果每次都需要做次遍历转换数据到节点和边的数据增加了用户的实现复杂度。
  - 树图的每次新增/删除/展开/收缩节点，都需要重新计算布局。遍历一份结构化数据对应到图上每个节点去做更新操作，也很麻烦。


## 初始化

### G6.TreeGraph

**参数**

| 名称 | 类型 | 默认值 | 描述 |
| --- | --- | --- | --- |
| layout | object | null | **3.0.4 版本开始支持树布局算法配置**。3.0.4 版本之前是 function 形式。建议开发者使用配置形式，操作粒度更细。 |
| animate | boolean | true | 默认打开重布局动画开关。 |


**用法**

```javascript
const treeGraph = new G6.TreeGraph({
	container: 'mountNode',
  width: 800,
  height: 600,
  modes: {
    default: [{
      type: 'collapse-expand',
      onChange(item, collapsed) {
        const icon = item.get('group').findByClassName('collapse-icon');
        if (collapsed) {
          icon.attr('symbol', EXPAND_ICON);
        } else {
          icon.attr('symbol', COLLAPSE_ICON);
        }
      }
    }, 'drag-canvas', 'zoom-canvas']
  },
  layout: {
    type: 'dendrogram',
    direction: 'LR', // H / V / LR / RL / TB / BT
    nodeSep: 50,
    rankSep: 100,
    radial: true
  }
})
```


## layout配置项
layout目前支持dendrogram、compactBox、mindmap和indeted四种布局方式。


### 通用配置项

| 名称 | 类型 | 默认值 | 描述 |
| --- | --- | --- | --- |
| type | string | dendrogram | 布局类型，支持dendrogram、compactBox、mindmap和indeted。 |
| direction | string | LR | 布局方向，有 `LR` , `RL` , `TB` , `BT` , `H` , `V` 可选。<br />L: 左； R: 右； T: 上； B：下； H: 垂直； V: 水平。 |
| getChildren | Function |  | 返回当前节点的所有子节点 |

⚠️**注意：**当`type=indeted`时，`direction`只能取LR、RL和H这三个值。


### dendrogram

**dendrogram示意图**

使用`dendrogram`方式布局时，`direction`取不同值时的效果如下所示。

| LR | RL | H |
| --- | --- | --- |
| <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*zX7tSLqBvwcAAAAAAAAAAABkARQnAQ' width='230' height='100'> | <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*qVbeR4oq4lYAAAAAAAAAAABkARQnAQ' width='230' height='100'> | <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*OHetRqedHOkAAAAAAAAAAABkARQnAQ' width='230' height='100'> |

| TB | BT | V |
| --- | --- | --- |
| <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*P_OETZsj17cAAAAAAAAAAABkARQnAQ' width='230' height='100'> | <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*n6sFS57g424AAAAAAAAAAABkARQnAQ' width='230' height='100'> | <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*CyVbQ5q_0_cAAAAAAAAAAABkARQnAQ' width='230' height='100'> |


**dendrogram配置项**

| 名称 | 类型 | 默认值 | 描述 |
| --- | --- | --- | --- |
| nodeSep | number | 20 | 同层次节点之间的间距 |
| rankSep | number | 200 | 相邻层级节点之间的间距 |
| nodeSize | number | 20 | 节点大小 |
| subTreeSep | number | 10 | 子树之间的间距 |
| isHorizontal | boolean | true | 是否是水平方向，默认为水平方向 |


### compactBox

**compactBox示意图**

使用`compactBox`方式布局时，`direction`取不同值时的效果如下所示。

| LR | RL | H |
| --- | --- | --- |
| <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*z-ESRoHTpvIAAAAAAAAAAABkARQnAQ' width='230' height='100'> | <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*AHfiQ7IfWJwAAAAAAAAAAABkARQnAQ' width='230' height='100'> | <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*Ygp0RaTxjp8AAAAAAAAAAABkARQnAQ' width='230' height='100'> |

| TB | BT | V |
| --- | --- | --- |
| <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*sj6qSqrBvpIAAAAAAAAAAABkARQnAQ' width='230' height='100'> | <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*4tDzSpYiq-cAAAAAAAAAAABkARQnAQ' width='230' height='100'> | <img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*Mj0WSaTKzSsAAAAAAAAAAABkARQnAQ' width='230' height='100'> |


**compactBox配置项**

| 名称 | 类型 | 默认值 | 描述 |
| --- | --- | --- | --- |
| getId | Function |  | 指定节点ID |
| getHeight | Function | 36 | 指定节点高度 |
| getWidth | Function | 18 | 指定节点宽度 |
| getVGap | Function | 18 | 指定节点之间的垂直间距 |
| getHGap | Function | 18 | 指定节点之间的水平间距 |

⚠️**注意：**使用getWidth、getHeight、getVGap和getHGap指定节点的宽高及间距后，并不会改变节点的大小，具体原理如下所示：
```javascript
/*
   * Gaps: filling space between nodes
   * (x, y) ----------------------
   * |            vgap            |
   * |    --------------------    h
   * | h |                    |   e
   * | g |                    |   i
   * | a |                    |   g
   * | p |                    |   h
   * |   ---------------------    t
   * |                            |
   *  -----------width------------
   */
```

**🦁以上原理同样适用于indented和mindmap布局。**


### indented

**indented示意图**

使用`indented`方式布局时，`direction`取不同值时的效果如下所示。

| LR | RL | H |
| --- | --- | --- |
| <img src='https://intranetproxy.alipay.com/skylark/lark/0/2019/png/178530/1560910055783-3783faed-29f0-4e34-9076-df951aa6ea10.png#align=left&display=inline&percent=0&size=0&status=done' width='230' height='100'> | <img src='https://intranetproxy.alipay.com/skylark/lark/0/2019/png/178530/1560910055615-54aaca32-7de4-471e-8600-611854094b90.png#align=left&display=inline&percent=0&size=0&status=done' width='230' height='100'> | <img src='https://intranetproxy.alipay.com/skylark/lark/0/2019/png/178530/1560910055676-86d316d8-9487-4b3d-99a4-27b4a8c091c0.png#align=left&display=inline&percent=0&size=0&status=done' width='230' height='100'> |


**indented配置项**

| 名称 | 类型 | 默认值 | 描述 |
| --- | --- | --- | --- |
| indent | number | 20 | 与直接父节点的缩进值 |
| getVGap | Function | 18 | 指定节点之间的垂直间距 |
| getHeight | Function | 36 | 指定节点的高度 |


### mindmap

**mindmap示意图**

<img src='https://gw.alipayobjects.com/mdn/rms_f8c6a0/afts/img/A*J1l5RofvbP0AAAAAAAAAAABkARQnAQ' width='750'>

**mindmap配置项**

| 名称 | 类型 | 默认值 | 描述 |
| --- | --- | --- | --- |
| getId | Function |  | 指定节点ID |
| getHeight | Function | 36 | 指定节点高度 |
| getWidth | Function | 18 | 指定节点宽度 |
| getSide | Function |  | 指定当前节点在主题的左边 (left) 还是右边 (right) |
| getSubTreeSep | Function | 0 | 指定子节点之间的高度间隔 |
| getVGap | Function | 18 | 指定节点的垂直间距 |
| getHGap | Function | 18 | 指定节点的水平间距 |


## 更新

### addChild(data, parent)
在指定的父节点下添加子树。

**参数**

| 名称 | 类型 | 是否必选 | 描述 |
| --- | --- | --- | --- |
| data | object | true | 子树的数据 |
| parent | Node | string | true | 父节点或父节点ID |


**用法**

```javascript
const data = {
  id: 'sub1',
  children: [
    {
      id: 'subTree1',
      children: [...]
    }, 
    {
      id: 'subTree2',
      children: [...]           
    }
  ]
};

treeGraph.addChild(data, 'root')
```

### updateChild(data, parent)
更新数据，差量更新子树。


**参数**

| 名称 | 类型 | 是否必选 | 描述 |
| --- | --- | --- | --- |
| data | object | true | 子树的数据 |
| parent | Node | string | false | 父节点或父节点ID |

⚠️**注意：**当parent参数为空时，则全量更新。

**用法**

```javascript
const data = {
  id: 'sub1',
  children: [
    {
      id: 'subTree1',
      children: [...]
    }, 
    {
      id: 'subTree2',
      children: [...]           
    }
  ]
};

treeGraph.updateChild(data, 'root')
```

### removeChild(id)
删除指定的子树。


**参数**

| 名称 | 类型 | 是否必选 | 描述 |
| --- | --- | --- | --- |
| id | string | true | 要删除的子树的ID |


**用法**

```javascript
treeGraph.removeChild('sub')
```


## 布局

### changeLayout(layout)
更改并应用指定的布局。


**参数**

| 名称 | 类型 | 是否必选 | 描述 |
| --- | --- | --- | --- |
| layout | object | false | 指定的布局配置，如不传，则不做变更 |


**用法**

```javascript
const layout = {
	type: 'mindmap',
  dirction: 'H',
  getSubTreeSep: () => 20,
  getVGap: () => 25,
  getHeight: () => 30,
  getWidth: () => 30
}
treeGraph.changeLayout(layout)
```


### refreshLayout(fitView)
数据变更后，重新布局，刷新视图，并更新到画布。


**参数**

| 名称 | 类型 | 是否必选 | 描述 |
| --- | --- | --- | --- |
| fitView | boolean | false | 更新布局后，是否需要自适应窗口 |


**用法**

```javascript
treeGraph.refreshLayout(true)
```


## 查找

### findDataById(id, target)
根据指定的ID获取对应的源数据。


**参数**

| 名称 | 类型 | 是否必选 | 描述 |
| --- | --- | --- | --- |
| id | string | true | 指定的元素ID |
| target | object | false | 从指定的节点开始查找，为空时从根节点开始查找 |


**返回值**

- 返回值类型：object；
- 返回值为查找到的节点的源数据。


**用法**

```javascript
const target = {
	id: 'sub1',
  children: [...]
}

// 从target节点开始查找sub1.1节点
const subData = treeGraph.findDataById('sub1.1', target)
  
// 从根节点开始查找sub1.1节点
const subData = treeGraph.findDataById('sub1.1')
```
