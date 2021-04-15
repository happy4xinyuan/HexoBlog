---
title: Todo-List 
category: Application
tag: [Front-End,JQuery]
thumbnail: /img/todo.png
---

>  一个简单的利用JQuery实现+CSS修饰的静态网页备忘录. [成品](https://happy4xinyuan.github.io/todolist_js/)

<!--more-->

### 技术重点

* JQuery对click, keypress事件的监听
* 复习bootstrap与css

### 技术难点

* onclick只能对已经存在的元素监听

  解决方案:

  ```js
  //$("li").on("click",function(){ //尽管使用on,但是还是没法应用到新的li中,因为$(li)绑定行为一开始就执行了,此时只有三个li.
  $("ul").on("click","li",function(){ //而绑定ul之后, 在click时,再去确认click是其中的哪个li,则可以解决该问题.即$()中的元素一定一开始就要存在,因此此类问题绑定其父节点即可
  	$(this).toggleClass("finished");
  })
  ```

* span元素存在于ul元素中, 点击span会触发ul的监听执行函数

  解决方案: e.stopPropagation(); 中断回调传递

  ```js
  //remove evert when click X before it
  $("ul").on("click","span",function(e){
  	$(this).parent().fadeOut(function(){
  		$(this).remove(); //此处使用this,而不是parent,因为已经在fadeOut中,此时this已经指向parent
  	}); //parent()方法返回父元素
  	e.stopPropagation(); //span处于li,ul,body中,则事件e可能导致其他回调函数,终止传播函数stopPropagation由jQuery提供
  })
  
  ```

* JS代码对html内容的影响

  解决方案:$("ul").append()函数, 为ul增加item

  ```js
  $("input").on("keypress",function(e){
  	if(e.which == 13){
  		let newevent = $(this).val();
  		 $(this).val(""); //清空输入栏
  		$("ul").append("<li><span><i class=\"fa fa-trash\" aria-hidden=\"true\"></i> </span>"+newevent+"</li>");
  	}
  
  })
  ```

* 如何设计hover滑动淡出淡入特效, 需要结合css和JQuery

  ```css
  span{
      
  	transition: 0.3s linear;
  	height: 35px;
  	width: 0;
  	opacity: 0;
  	/*设置能见度,优化显示*/
  }
  /*有意思的写法 表示hover的li里面的span*/
  li:hover span{
  	width: 35px;
  	opacity: 1.0;
  }
  
  ```


* 关于fadeout之后remove的研究

  ```js
  //effect 部分
  $("button").on("click",function(){
  	$("div").fadeOut(1000,function(){//fade的同时执行函数
  		//fade只是隐藏显示,在html中仍然存在
  		//$(this).remove 从html中删除
  	});
  	$("div").remove();//不妥,参考setInterval事件队列,fade未执行完成就删除了
  	
  })
  ```

  