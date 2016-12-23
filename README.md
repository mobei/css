##CSS 总结
第一时间还是要查手册啊 [手册地址](http://www.css88.com/book/css/) 
#### CSS 常用样式及规范
  - 命名使用小写，多个单词用横线分开，尽量不使用简写，确保语义明确
  - 尽量不使用行内样式
  - 尽量用字体替代图片，如果需要使用图片，优先使用背景图片
  - 样式尽可能的采取全局控制，尽量少在局部重写太多的css规则；
  - 每个模块或者页面的样式继承父元素样式基础上，要有自己的命名空间，方便以后添加
  - 文件结构尽量按照项目的页面（`templates`）目录结构来创建，保证结构清晰，层次分明
  - 尽量避免定义或重写`tag`的或者全局的样式，例如：

    ```css
    label {
      color: #fff;
    }
    .pull-right {
      position: absolute;
    }
    ```

  - 项目中尽量多针对一些全局使用的组件做统一风格，比如按钮，链接，文字大小等，保证项目整体风格统一
  - 适当增加一些css3动画，提升用户体验

#### 项目中常用的全局css样式
- 见 `./sass/common.scss`（基于sass）	

#### css3
- 见 `./css3/doc.md`
#### IE兼容和css Hack（只列举一些项目中用到的）
   整理了一些基于css3的常用浏览器的hack，见`.css3/hack.css`
- 属性级：一般使用`*`来识别IE6、IE7，`-`识别IE6，`\9`识别IE6到IE10，也是用的最普遍的一个，`\0`识别IE8到IE10，一般来说IE9以上的不太需要hack。例如：

	```css
	el {
		*color: #000 /*IE6和IE7识别*/
		－color: #fff /*IE6识别*/
		color: #111\9 /*IE6-10*/
		color: #222\9\0 /*IE8-10*/
	}
	```
	
- 条件注释法：写在html页面中，一般用在需要为IE即按需加载时，例如：

	```vbscript-html
		<!--[if IE]>
		这段文字只在IE浏览器显示
		<![endif]-->
		
		<!--[if !IE]>
		这段文字只在非IE浏览器显示
		<![endif]-->
		
		<!--[if IE 6]>
		这段文字只在IE6浏览器显示
		<![endif]-->
		
		<!--[if ! IE 8]>
		这段文字在非IE8浏览器显示
		<![endif]-->
		
		<!--[if gte IE 6]>
		这段文字只在IE6以上(包括)版本IE浏览器显示
		<![endif]-->
		
		<!--[if lt IE 9]>
	      <script src="../common/js/lt-ie9.js"></script>
	    <![endif]-->
	```
- 选择器级，目前项目中用的比较少，可能一般不怎么用。例如：

	```
	*body: {
		font-size: 14px;
	}
	```

- css3特性：常用的有`opacity`，`text-shadow`，`box-shadow`，`boder-radius`，动画。`opacity`可以用`filter`实现，剩下的低版本IE能不用就不用吧，个人认为阴影，圆角这些效果在IE下显示没太大必要，即使实现了也没有太好的效果，没有不会影响什么，能正常显示内容就好，另外实现起来麻烦，性价比不高。当然，如果非要实现的话也可以，使用`ie-css3.htc`，可以实现，不过不能支持动画，具体使用方法网上查找，项目到目前为止没有使用过。

- 字体：有一次遇到问题，字体图标（iconfont）不能在IE8里面显示，然后通过一系列复杂的调试（IE8调试起来...），发现样式正常，元素正常，就是不显示，但是只要编辑一下元素或者重新加载一下字体文件就好了，虽然不知道是不是这样，但是，感觉应该就是字体文件直接从页面引入，当打开页面开始渲染的时候字体文件还没有加载进来，而当文件载入后，浏览器已经不会重新渲染了，所以导致无法显示，于是想了一招，**延迟加载**，尝试了下，代码：

	```javascript
	 common.tipsNewMessage.init();
	 var linkTag = $('<link rel="stylesheet" href="fonts/iconfont.css" />');
     $.getScript('./js/chat-agent.js', function() {
         $('head').append(linkTag);
     });
     setTimeout(function() { //万恶的IE...
	     $('head').append(linkTag);
	 }, 5000);
	```
没想到还真解决了。查问题期间找到一篇文章，也是同样的问题，很有帮助，可以参考：[文章地址](http://www.tuicool.com/articles/2EFRJf6)
- placeholder：IE8不支持placeholder，这个比较好处理，直接用插件就行，有一个`jquery.placeholder.js`的插件，使用方式简单，就引入这个插件就行，然后正常使用placeholder，实现原理其实比较简单，就是如果不支持placeholder，就把placeholder当作一个普通属性`attr`，然后拿到值，在用jquery处理一下，用一个绝对定位的`span`元素显示那个值，看起来和真的一样。网上还有另一种解决方案，就是把placeholder的值当成一个默认值，就是相当于`input`的默认的`value`就是那个值，但是这种方法可能会带来问题，如果是表单的话，可能会把这个只当作真的value，需要特殊处理。
- max-height：IE8及以下不支持`max-height`，网络上有解决方案：
	
	```css
	/*这样就可以让ie实现max-height的效果*/
	.div{ 
		max-height: 100px; 
		_height: expression(
			this.scrollHeight > 100 ? "100px" : "auto"
		); 
		overflow-y: auto; 
	} 
	```
	
	我并没有采取这个方法，而是很简单很暴力的，使用css hack直接让在IE下就显示固定宽度了，毕竟height是可以的。
- 百分比：IE8（其他版本没研究过）里面的百分比有问题，有时候无效或者计算不准确，这是因为高度取决于父元素，需要对该元素所有的父元素设置高度100%
- IE自带的input删除功能：这个东西比较恶心，不知道为什么要这样设计。。。去掉就好，`input::-ms-clear { display: none;}`
- 由于系统只兼容到IE8，所以大部分的兼容性都是针对IE8，有时候IE9、11，偶尔有firefox和safair，大部分的兼容都只做最简单的兼容，即低版本的一些浏览器保证功能正常使用，剩下的一些UI什么的可以不考虑。**所以总结的一些都只是在项目中遇到的，使用过的方法，也有可能有一些不准确的或者错误的，只是一个总结，仅供参考。**
- websocket/strophe/flash：这个其实不算是css的范围了，不过因为是处理IE兼容性的，就放在这里。项目主要业务时即时通讯，所以需要长链接，使用xmpp通信，前端使用第三方库strophe（貌似只有这么一个比较成熟好用的）,strophe支持两种连接方式，一种是了*轮询*，也是我们早期使用的方式，这种方式有几个缺点：
	- 耗资源，轮询么，大家都明白
	- 不稳定，这也是最致命的原因，链接经常断开，即时做了断线重连也不能保证稳定性，倒是丢失消息等一些很严重的问题
	- 不及时，同样是因为轮训，有些需要用心跳带的消息就不会即时到达，对用户来讲有延迟，体验不好
	
   另外一种就是websocket，为了切换到这种方式，当然也因为其他原因，比如最大在线人数，后端更换了服务器框架（之前的框架是开源的`openfire`，后来切换为`ejabberd`），这就是大概背景，这里面只介绍兼容，IM相关的以后放在其他地方慢慢整理。

   基于以上背景，开始说兼容。不知是IE，可能有些低版本的其他浏览器，或者因为某些原因不能支持websocket的浏览器，这些不能支持的我们在系统里面做了链接方式兼容，如果不能支持，退而求其次，就切换到之前的轮训方式。当然了，对IE来说，你爱用什么方式都无所谓，我都不支持。。。于是，疯狂的IE兼容行动开始了（因为这个是客户端，所以要兼顾大部分浏览器），好在，网络上也有专门研究过的人，有些方案，可以直接拿来用，我就搬个砖，尝试过几个方案之后就只剩下IE8了，对付IE8一般就一招，就是flash，有别人写好的，我不熟悉flash，所以不明白原理，总之就是需要好多个文件，[这地址基本包含了所有的解决方案](http://code.stanziq.com/strophe)，需要单独把这个插件加入strophe，还要在服务器端处理跨域问题，添加`crossdomain.xml`文件。写到这理发现这个说起来很多很大，准备写到其他地方，专门总结。
   
   如果项目要兼容到IE8，那么基本上就脱离不了flash，比如上传组件，音频播放等等，兼容方面都使用了flash，但是有时候客户的电脑不一安装了或者是不是最新版的flash（吃过很大亏，搞了很久，发现原来是没有flash），这时候可以用条件注释法来判断是不是IE8，然后在判断有没有flash，然后做一些提示，让用户去安装：

	```javascript
	if (ifIE(9, "lte") && hasFlash()) {
	   $('.tip-bar').show();
	}
	
	function hasFlash() {
		var hasFlash = false;
	    try {
		    if (document.all) {
		        var swf = new ActiveXObject('ShockwaveFlash.ShockwaveFlash');
	            if (swf) {
		            hasFlash = true;
	            }
	        }
	    } catch (e) {}
	        return hasFlash;
	    }
	    
	function ifIE(version, operator) {
		var el = document.createElement("b");
	    el.innerHTML = "<!--[if " + (operator || "") + " IE " + (version || "") + "]><i></i><![endif]-->";
	    return el.getElementsByTagName("i").length === 1;
	}
	```

- 暂时能想到的就这么多，以后有新的想法或者新的心得会继续加上