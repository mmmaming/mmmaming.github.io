---
layout: default
title: angular-tree基础实现
---
## 用法
在html页面，使用`tree`标签以及data和item-template属性。
`<tree data="vm.tree" item-template="vm.itemTemplate"></tree>`
其中，data是我们的tree所需要展示的数据，item-template是tree要显示的内容。（用户自定义）

data:

```
vm.tree = [{
        name: 'PC游戏',
        id: 1,
        children: [
            {
                name: '角色扮演',
                id: 2,
                children: [
                    {
                        name: '月影传说',
                        id: 3
                    }
                ]
            },

        ]
    }];
```

item-template:用户可以在这里自定义所需要显示的内容，并且增加增删改查方法。

```
vm.itemTemplate = `<div ng-class="{'cur-select' : vm.id === item.id}" ng-click="vm.add(item)" ng-dblclick="vm.edit(item)" ng-show="!item.isEdit">{{item.name}}</div>
                        <input ng-dblclick="vm.edit(item)" type="text" ng-model="item.name" ng-show="item.isEdit">
                        <button ng-click="vm.del(item)" ng-show="vm.id === item.id">删除</button>`;
```

效果：
![tree](https://github.com/mmmaming/angular-tree/blob/master/img/tree.png?raw=true)

嗯，简单的用法就是这样，接下来说一下实现方法。

## 实现

在做东西之前，首先要清楚自己需要的是什么，那么现在需要一个可以用户自定义内容与交互的tree组件，所以此angular-tree组件就应运而生了。

最开始的想法是，直接在使用tree指令的时候，在tree的标签里传入用户自定义的内容，通过transclude去实现，类似于下面这样
```
    <tree data="vm.tree">
        <div ng-transclude>{{item.name}}</div>
    </tree>
```
然而事实上这样并不行，（当然不是在标签里直接写内容不行，而是通过transclude这种方式实现不可行）,于是只能改用通过属性传值的形式，在js中写好我们需要的模板，传到指令中去，在做编译。

在我们的html结构中，通常一个tree的结构都是`<ul>`和`<li>`的互相嵌套去完成，类似这样：

```
<ul>
    <li>
        一级标题
        <ul>
            <li>二级标题</li>
        </ul>
    </li>
    <li>
        一级标题
        <ul>
            <li>二级标题</li>
        </ul>
    </li>
</ul>
```
所以我们需要两个html模板去配合我们做这样的处理。第一个模板负责我们一级标题的内容展示，然后通过repeat循环我们的第二个模板达到显示所有二级标题的效果。

template1

```
<tree-item ng-repeat="treeItem in $ctrl.data" item="treeItem" item-template="$ctrl.itemTemplate"></tree-item>
```

template2

```
<ul>
    <li>
        {{item.name}}
        <i ng-click="$ctrl.showChild()" ng-if="!$ctrl.isLeaf(item)" class="fa" ng-class="{'fa-caret-right': !$ctrl.childShow, 'fa-caret-down': $ctrl.childShow}"></i>
        <i ng-if="!$ctrl.isLeaf(item)" class="fa" ng-class="{'fa-folder': !$ctrl.childShow, 'fa-folder-open': $ctrl.childShow}"></i>
    </li>
    <li ng-show="$ctrl.childShow" ng-if="!$ctrl.isLeaf(item)">
        <tree-item ng-repeat="treeItem in item.children" item="treeItem"  item-template="itemTemplate" ></tree-item>
    </li>
</ul>
```
通过这种模板套模板的方式，可以达到循环显示每一个级别的内容以及深层次的嵌套。
在template2中，我们需要做一些处理，就是增加展开收缩逻辑以及显示子集的逻辑。这两个功能是tree的基本功能，并不需要暴露给用户，所以我们在directive2的内部去实现即可。
我们给每个子节点增加了 childShow属性，并且通过父节点的箭头（`showChild()`）点击来展示和收缩子节点的内容。至于判断是否是末尾节点，则更简单，只需要判断它的children属性是否为空或者不存在即可。
```
vm.showChild = function () {
    vm.childShow = !vm.childShow;
};
vm.isLeaf = function(item) {
    return !item.children || item.children.length === 0;
}
```
至此，一个具备展开收缩功能的tree，就基本完成了。但是用的时候就会发现，我们在item-template中定义的一些方法属性等，完全没有生效，这是为什么呢？
其实原因很简单，是在指令中使用了隔离作用域导致的，指令内部由于切断了与外部scope的联系，在指令外面定义的方法，指令内部并不认识。所以那些方法并没有生效。为了能让指令内部与指令外部建立联系，需要将外部的scope传到指令中，这样在指令外面定义的方法，指令里面也可以使用生效了。

首先在第一层指令的controller中，拿到当前scope的父级scope，也就是`scope.$parent`。并将这个scope传到第二层指令当中，且挂载到第二层指令中scope的原型上，即可达到`scope`的继承。`Object.setPrototypeOf(scope, scope.baseScope);
`

最后，为了能让指令内部认识我们传进去的模板，需要在指令内部去重新编译。

```
// 在li中插入用户指令的模板
 element.find('ul').find('li').append(scope.itemTemplate);
 $compile(element.find('ul').find('li').contents())(scope);
```
第二行代码很重要，为什么不直接编译element.contents()呢？这样做其实是为了防止重复编译，导致我们在指令内部定义的方法会被执行两遍。
最后，再修改一下样式，一个简单的tree就完成了。（[源码戳这里](https://github.com/mmmaming/angular-tree)）用户可以在自己的controller中定义自己想要的东西、交互、效果等，而不需要交给指令内部去实现，比较灵活。当然，不爽的地方就是需要在js里写html的代码。那么有没有办法直接在html标签中去写我们需要的内容呢？当然，不过这种实现方法比较麻烦，在此不做赘述，有兴趣的话可以看这个[github。](https://github.com/dump247/angular.tree)
他的实现方式是通过js去生成dom元素，并且在每个item上`scope.$new()`去创建scope并继承到用户的scope上。一个很6的实现方法。

## 总结
在写tree的过程中，遇到了一些问题，最后也都得到了解决，并且在这个过程中，加深了对指令的理解。平时多写一些这样的小指令，对技能的掌握与巩固还是很有帮助的。最后，感谢@hjzheng ，@fnjoe在此期间提供的支持与帮助。
