# 原生js弹幕效果
- **思路**
----
1. 获取弹幕value，生成弹幕后要清除input里面的value，为下一次生成弹幕做准备
2. 两个时间 keydown  click
3. 触发后生成弹幕span,添加到父盒子中appendChild()
4. 初始化span的top和left
5. 使用setInterval(,1000/60)来让弹幕移动
6. 当span移除屏幕时，让其消失，即offsetLeft+offsetWidth<=0时，this.parentNode.removeChild()
---
```html
    <style type="text/css" media="screen">
        * {
            margin: 0px;
            padding: 0px
        }

        html, body {
            height: 100%
        }

        body {
            overflow: hidden;
            background-color: #FFF;
            text-align: center;
        }

        .flex-column {
            display: flex;
            flex-direction: column;
            justify-content: space-between;align-items: center;
        }

        .flex-row {
            display: flex;
            flex-direction: row;
            justify-content: center;
            align-items: center;
        }

        .wrap {
            overflow: hidden;
            width: 60%;
            height: 500px;
            margin: 1px auto;
            padding: 20px;
            background-color: transparent;
            box-shadow: 0 0 9px #222;
            border-radius: 20px;
        }

        .wrap .box {
            position: relative;
            width: 100%;
            height: 90%;
            background-color: #000000;
            border-radius: 10px
        }

        .wrap .box span {
            position: absolute;
            top: 10px;
            left: 20px;
            display: block;
            /* padding: 10px; */
            width: 300px;
            color: #336688;
            text-align: left
        }

        .wrap .send {
            display: flex;
            width: 100%;
            height: 10%;
            background-color: #000000;
            border-radius: 8px
        }

        .wrap .send input {
            width: 40%;
            height: 60%;
            border: 0;
            outline: 0;
            border-radius: 5px 0px 0px 5px;
            box-shadow: 0px 0px 5px #d9d9d9;
            text-indent: 1em
        }

        .wrap .send .send-btn {
            width: 100px;
            height: 60%;
            background-color: #fe943b;
            color: #FFF;
            text-align: center;
            border-radius: 0px 5px 5px 0px;
            line-height: 30px;
            cursor: pointer;
        }

        .wrap .send .send-btn:hover {
            background-color: #4cacdc
        }
    </style>

    <body>
    <div class="wrap flex-column">
        <div class="box">
            <video src="movie.mp4" width="100%" height="100%" controls></video>
        </div>
        <div class="send flex-row">
            <input type="text" class="con" placeholder="弹幕发送[]~(^v^)~*">
            <div class="send-btn">发送</div>
        </div>
    </div>
    </body>
```
---
```javascript
    let oBox = document.querySelectorAll('.box')[0]
    let con = document.querySelectorAll('.con')[0]
    let sendBtn = document.querySelectorAll('.send-btn')[0]
    let boxWidth = oBox.offsetWidth
    let boxHeight = oBox.offsetHeight
    let danHeight = boxHeight*0.7
    let message =''
    sendBtn.onclick = send
    con.onkeydown = function(e){
        if(e.keyCode === 13){
            send()
        }
    }
    function send(){
        message = con.value
        creatEle(message)
        con.value = ''
    }
    function creatEle(message){
        let span = document.createElement('span')
        span.innerHTML = message
        oBox.appendChild(span)
        span.style.left = boxWidth + 'px'
        span.style.top = Math.random()*danHeight + 'px'
        span.style.color = '#' + (~~(Math.random() * (1 << 24))).toString(16)
        trans(span)
    }
    function trans(ele){
        let timer = setInterval(function(){
            ele.style.left = ele.offsetLeft - 1 + 'px'
            if(ele.offsetLeft + ele.offsetWidth<=0){
                clearInterval(timer)
                ele.parentNode.removeChild(ele)
            }
        },1000/60)
    }
    </script>
```
# 觉得不够好 就面向对象来一次
```javascript
    <script>
        let oBox = document.querySelectorAll('.box')[0]
        let con = document.querySelectorAll('.con')[0]
        let sendBtn = document.querySelectorAll('.send-btn')[0]
        let boxWidth = oBox.offsetWidth
        let boxHeight = oBox.offsetHeight
        let danHeight = boxHeight*0.7 //弹幕盒子出现的高度范围
        class Barrage {
            constructor(oBox,con,boxWidth,danHeight){
                this.oBox = oBox
                this.con = con //弹幕输入框
                this.message = ''
                this.boxWidth = boxWidth
                this.danHeight = danHeight
            }
            send(){
                this.message = this.con.value
                this.creatEle(this.message)
                this.con.value = ''
            }
            creatEle(message){
                let span = document.createElement('span')
                span.innerHTML = message
                this.oBox.appendChild(span)
                //初始化各类参数
                span.style.left = boxWidth + 'px'
                span.style.top = Math.random()*this.danHeight + 'px'
                span.style.color = '#' + (~~(Math.random() * (1 << 24))).toString(16)
                this.trans(span)
            }
            trans(ele){
                let timer = setInterval(function(){
                    ele.style.left = ele.offsetLeft - 1 + 'px'
                    if(ele.offsetLeft + ele.offsetWidth<=0){
                        clearInterval(timer)
                        ele.parentNode.removeChild(ele)
                    }
                },1000/60)
            }
        }  
        let barrage = new Barrage(oBox,con,boxWidth,boxHeight)  
        sendBtn.onclick = function(){
            barrage.send()
        }
        
        con.onkeydown = function(e){
            if(e.keyCode === 13){
                barrage.send()
            }
        }
    </script>
```

