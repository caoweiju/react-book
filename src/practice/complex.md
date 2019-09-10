# react中的一些细节

## class类的constructor
props和this的问题
````js
    constructor(props) {
        super(props);
        this.state = {date: new Date()};
    }
````


## 事件

onClick的默认第一个参数是 event，需要注意；以及onClick对应函数的this绑定问题；

## props的不变性

## jsx的一些技巧

JSX 类型中使用点语法
在运行时选择类型






