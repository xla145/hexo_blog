---
title: 使用iframe遇到的问题
date: 2019-03-15 09:58:07
tags:
- HTML
---
# 1：在iframe中使用layer弹框引发的一些问题
1. 弹框后，出现子窗口高度不够，导致显示内容不全
![在这里插入图片描述](https://img.mupaie.com/20190227175949596.png)

使用iframe 代码如下：

	<div class="layui-tab-content" style="min-height: 150px; padding: 0 0 0 0;">
		<div class="layui-tab-item layui-show">
			<iframe src="/main" name="main" scrolling="auto"></iframe>
		</div>
	</div>
出现这个问题的原因主要是 iframe 设置的高度是100%，没有设置具体的高度，导致子窗口没有参考物导致其即使设置了高度也不能讲父元素撑开。出现这种情况目前有二种方式：
* 给iframe 设置样式 overflow: auto;
* 通过 js 获取窗体的高度，减去头部和底部的高度剩下的就是内容的高度，然后给iframe 设置高度，具体代码如下：

		$(window).on('resize', function () {
		        let resizeTimer = null;
		        if (resizeTimer) {
		            clearTimeout(resizeTimer);
		        }
		        resizeTimer = setTimeout(function () {
		            setContentSize();
		        }, 500);
		    }).resize();

	    function setContentSize() {
	        var $content = $('.admin-nav-card .layui-tab-content');
	        $content.height($(this).height() - 147);
	        $content.find('iframe').each(function () {
	            $(this).height($content.height());
	            $(this).width($content.width());
	            $(this).contents().find(".layui-layer-iframe").width($content.width())
	        });
	    }
解释一下，主要是监听窗体调整，获取窗体的高度，减去头部和底部的高度剩下的就是\$content的高度，然后给iframe设置高度，代码中运行了延迟加载，防止函数执行过快。

2. 高度虽然解决了，但是又出现一个问题，当弹窗使用全屏显示的时候，调整窗体宽度，里面内容不会跟着改变，那么问题来了出现这种效果：
![在这里插入图片描述](https://img.mupaie.com/20190227184346806.png)
里面的内容被覆盖了，导致这个原因主要是里面iframe和弹窗内容div的宽度没有跟着改变，那么我们怎么解决呢，想到上一节讲了高度设置，那么我们宽度也跟着设置呀。带着这个想法，我试一下修改iframe的宽度

```
		function setContentSize() {
	        var $content = $('.admin-nav-card .layui-tab-content');
	        $content.height($(this).height() - 147);
	        $content.find('iframe').each(function () {
	            $(this).height($content.height());
	            $(this).width($content.width());
	        });
	    }
   ```
结果还是出现上述那个问题，那到底为啥呢？然后通过F12 模式 调试了一下，原来弹框里面有一个div也设置了宽度，那我跟iframe一起一并修改呢?

```
	function setContentSize() {
        var $content = $('.admin-nav-card .layui-tab-content');
        if ($(this).width > 768) {

        }
        $content.height($(this).height() - 147);
        $content.find('iframe').each(function () {
            $(this).height($content.height());
            $(this).width($content.width());
            $(this).contents().find(".layui-layer-iframe").width($content.width())
        });
    }.
```
尝试了一下果真可以，现在看效果：
![调整窗体前](https://img.mupaie.com/20190227185309289.png)

![调整窗体后](https://img.mupaie.com/20190227185353924.png)
这样终于算解决问题了，有一个前端小伙伴告诉我还有一种方式，就是改变窗体宽度的时候，调用一个layer.full(index)，index 是创建弹窗的编号。这种方式好处就是内部自动根据窗体宽度调整弹窗宽度，但是有个问题我怎么去获取那个index呢？这个问题等以后解决吧，先放放。。。

# 2. 手机端解决iframe 中内容不能左右移动的问题
现在是pc端解决了适配，但是手机端呢，用上述的代码显示的页面不能左右移动。这下就需要Google一把了，带着问题去搜索了一下网上大佬说：先给iframe 套一个div 然后给div设置样式

```
@media screen and (max-width: 768px) {
	/* 主体容器 */
    .admin-nav-card .layui-tab-item {
        -webkit-overflow-scrolling: touch;
        overflow: auto;
    }
 }
 ```
结果试了一次果真可以实现，但是由于手机品牌很多，需要适配就难上加难，由于自己又不是一个前端人员就先放放了，等有时间在跟着上述的问题一起解决。

参考文档：

[子窗口高度不够](https://fly.layui.com/jie/33662/)
[iphone下iframe问题记录](https://www.jianshu.com/p/bd9fbf5a14f0)

