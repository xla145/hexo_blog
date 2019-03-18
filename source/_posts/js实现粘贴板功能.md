---
title: js实现粘贴板功能
date: 2019-02-22 10:28:47
tags:
- JavaScript
- 浏览器兼容性
---
js实现粘贴板方式有document.exeCommad，window.clipboardData的setData方法，还有使用ZeroClipboard插件

# 1. 通过document提供的exeCommad方法实现粘贴板功能 
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    <input type="button" onClick='copyUrls(this)' value="方式二">
    
    <script language="javascript">
        function copyUrls(that) {
            let textArea = document.createElement("textarea")
            textArea.style.position = 'fixed'
            textArea.style.top = 0
            textArea.style.left = 0
            textArea.style.width = '2em'
            textArea.style.height = '2em'
            textArea.style.padding = 0
            textArea.style.border = 'none'
            textArea.style.outline = 'none'
            textArea.style.boxShadow = 'none'
            textArea.style.background = 'transparent'
            textArea.value = that.value
            document.body.appendChild(textArea)
            textArea.select()
            try {
                document.execCommand('copy')
            } catch (err) {
                this.throwError('不能使用这种方法复制内容'+err.toString())
            }
            document.body.removeChild(textArea)
        }
    </script>
    </body>
    </html>

优点不需要引用外部插件

* google浏览器（兼容）
* IE浏览器（兼容）
* 火狐（兼容） ！

exeCommad 还有很多方法，待续

# 2. window.clipboardData的setData方法

* google浏览器（不兼容）
* IE浏览器（兼容）
* 火狐（不兼容）
    <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>Title</title>
        </head>
        <body>
    
        <input type="button" onClick='copyUrl()' value="方式一">
        <script language="javascript">
            function copyUrl() {
                var clipBoardContent="test";
                window.clipboardData.setData("Text",clipBoardContent);
                alert("复制成功!");
            }
        </script>
        </body>
        </html>

# 3. 通过插件clipboard方式实现 https://github.com/zenorocha/clipboard.js

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <script src="clipboard.min.js"></script>
    </head>
    <body>
    
    <input type="button" onClick='copyUrls3(this)' value="方式三" class="btn">
    
    <script language="javascript">
        function copyUrls3(that) {
            var clipboard = new ClipboardJS('.btn', {
                text: function() {
                    return that.value;
                }
            });
    
            clipboard.on('success', function(e) {
                console.log(e);
            });
    
            clipboard.on('error', function(e) {
                console.log(e);
            });
        }
    
    </script>
    </body>
    </html>

* google浏览器（不兼容）
* IE浏览器（兼容）
* 火狐（不兼容）

后续加上各个版本的优缺点

