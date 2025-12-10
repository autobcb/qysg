
# 轻悦时光
一款多平台可用的阅读软件，通过 webview 来运行书源以达到多平台兼容的效果。

# 交流群
qq交流群：519620220

# 书本简介说明
书本简介支持html ，如果需要使用html，请在最前面添加：@html: ，例如：@html: \<p>这是一个段落\</p>    。
如果不加@html:前缀则会直接显示为文本。

# 正文文本说明
正文文本不支持除了img 以外的标签, 图片可以使用img标签例如 \<img src="****">    
img 标签里的src 支持header 如果需要修改header可以这么设置,http://127.0.0.1,{'headers':{'a':'b'}} ,当然这种写法不仅仅支持这也支持封面

# 正文图片解析说明
和文字同行的图片会默认识别为段评，小图显示，其他图片会正常显示。   
例如
````
轻悦时光是最好用的阅读器<img src="a" />,支持多平台<img src="b" />。\r\n <img src="c" />
````
上面ab 两个图片会以段评显示，c正常显示。    
如果想点击支持js请这么写： \<img src="b,{'js':'要运行的js'}"/> img src 必须用双引号包裹，所以后面的json只能用单引号。   
关于图片是支持base64图片的，但如果base64里是svg图片请勿使用%，fluttersvg不支持百分比，轻阅读是用后端转码了，轻悦时光不支持带百分比的svg。

# 图片后面的json说明
图片后面可以接一个json ，目前支持以下几个参数：
- js：要运行的js，只能在书源代码中使用，在阅读器中携带js的图片是可以直接点击运行的
- headers：访问图片时携带的headers，例如{'headers':{'a':'b'}}
- imageDecode：是否需要解密图片，默认是false，如果需要解密图片需要在书源中实现解密函数

# 段评图片使用
如果要使用应用自带的段评图片，请按照如下写法
例如：
````
<img src="dp:1,{'js':'要运行的js'}" />
````
1 为段评数量

# js加密解密
如果你的代码涉及到加密解密可以引用下面的js
````
<script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
````



# 通用 js
通用 js 的函数分前台 webview 可用和源可用，源可用的话只能在书源代码中使用，前台 webview 可用那表示能在登录或者你启动的前台 webview 中使用, 未单独说明的是两种情况下都能用




````
<script>
  var isCookieJar=true;// 不需要CookieJar请修改此处
  class FlutterJSBridge {
    constructor() {
      this.init(); //前台webview 里必须删除这行
    }

    init() {
      if (window.flutter_inappwebview) {
        this.isReady = true;
        this.CookieJar();
      } else {
        window.addEventListener('flutterInAppWebViewPlatformReady', () => {
          this.isReady = true;
          console.log('JSBridge初始化完成');
          this.CookieJar();
        });
      }
    }

    //通知原生页面初始化完成，仅在书源和tts生效，webview请勿使用，只有通知加载成功后才允许运行，否则会一直等待加载成功
    async CookieJar() {
      try {
        await window.flutter_inappwebview.callHandler('CookieJar', isCookieJar);
      } catch (error) {
        console.error('汇报完成准备失败:', error);
      }
    }

    //获取应用编译版本
    async getbuildNumber() {
      try {
        return await window.flutter_inappwebview.callHandler('buildNumber');
      } catch (error) {
        return  0;
      }
    }

    //获取应用版本
    async getversion() {
      try {
        return await window.flutter_inappwebview.callHandler('version');
      } catch (error) {
        return  "0.0.0";
      }
    }

    //获取设备唯一id
    async getDeviceid() {
      try {
        return await window.flutter_inappwebview.callHandler('id');
      } catch (error) {
        return  "";
      }
    }

    //获取设备平台 此处返回 windows、macos、ios、ohos、android
    async getDevice() {
      try {
        return await window.flutter_inappwebview.callHandler('device');
      } catch (error) {
        return  "";
      }
    }

    //输出日志,前台webview请勿使用
    //str 为 String
    async log(str) {
      try {
        return await window.flutter_inappwebview.callHandler('log',str);
      } catch (error) {
        return  false;
      }
    }

   //书源调试时可输出 html 代码到前台
   //type 0 搜索源码 ， 1详情源码 ，2目录源码 ，3正文源码
    //str 为 String
    //type 为int
    async text(type,str) {
      try {
        return await window.flutter_inappwebview.callHandler('text',type,str);
      } catch (error) {
        return  false;
      }
    }

    //toast弹窗
     //str 为 String
    async showToast(str) {
      try {
        return await window.flutter_inappwebview.callHandler('showToast',str);
      } catch (error) {
        return  false;
      }
    }

    //获取默认ua
    async getWebViewUA() {
      try {
        return await window.flutter_inappwebview.callHandler('getWebViewUA');
      } catch (error) {
        return  "";
      }
    }

    //通过url打开外部应用
    //url 为 String
    async openurl(url) {
      try {
        return await window.flutter_inappwebview.callHandler('openurl',url,"");
      } catch (error) {
        return  false;
      }
    }

    //通过url打开外部应用并附带mimeType
    //url 为 String
    //mimeType 为 String
    async openurlwithMimeType(url,mimeType) {
      try {
        return await window.flutter_inappwebview.callHandler('openurl',url,mimeType);
      } catch (error) {
        return  false;
      }
    }

    /**
     * 使用webView访问网络
     * @param html 直接用webView载入的html, 如果html为空直接访问url
     * @param url html内如果有相对路径的资源不传入url访问不了
     * @param js 用来取返回值的js语句, 没有就返回整个源代码
     * @param body 当参数不为空的时候，会以post请求，此时请务必在 header 中带上content-type
     * @param header 请求的header头，此参数必须是json字符串
     * @return 返回js获取的内容
     */
    async webview(url,js,html,body,header) {
      try {
        return await window.flutter_inappwebview.callHandler('webview',url,js,html,body,header,"","");
      } catch (error) {
        return  "";
      }
    }

    /**
     * overrideUrlRegex 为正则表达式
     * 使用方法和上面的一样
     * 但返回的内容为正则到的内容，如果无法正则到则返回 js 获取的内容，如果 js 为空则返回页面 html
     */
    async webViewGetOverrideUrl(url,js,html,body,header,overrideUrlRegex) {
      try {
        return await window.flutter_inappwebview.callHandler('webview',url,js,html,body,header,overrideUrlRegex,"");
      } catch (error) {
        return  "";
      }
    }

    /**
     * 使用webView获取资源url
     * urlregex 为正则表达式
     * 使用方法和上面的一样
     * 但返回的内容为正则到的内容，如果无法正则到则返回 js 获取的内容，如果 js 为空则返回页面 html
     */
    async webViewGetSource(url,js,html,body,header,urlregex) {
      try {
        return await window.flutter_inappwebview.callHandler('webview',url,js,html,body,header,"",urlregex);
      } catch (error) {
        return  "";
      }
    }


    /**
     * 启动前台 webview 访问链接并获取结束时的 html，可用于手工过盾
     * @param url 网址
     * @param title 标题
     * @param header 请求的header头，此参数必须是json字符串
     * @return 返回网页的内容
     */
    async startBrowser(url,title,header) {
      try {
        return await window.flutter_inappwebview.callHandler('startBrowser',url,title,header);
      } catch (error) {
        return  "";
      }
    }
    
     /**
     * 启动前台 webview 并对每次打开的 url 进行拦截
     * @param url 网址
     * @param title 标题
     * @param header 请求的header头，此参数必须是json字符串
     */
    async startBrowserWithShouldOverrideUrlLoading(url,title,header) {
      try {
        return await window.flutter_inappwebview.callHandler('startBrowserWithShouldOverrideUrlLoading',url,title,header);
      } catch (error) {
        return  "";
      }
    }

    //专门为段评设置的半屏显示，不返回任何东西
    async startBrowserDp(url,title) {
      try {
        return await window.flutter_inappwebview.callHandler('startBrowserDp',url,title);
      } catch (error) {
        return  "";
      }
    }

    //仅前台webview可以使用，返回按钮，返回上一个页面
    async back() {
      try {
        return await window.flutter_inappwebview.callHandler('back');
      } catch (error) {
        return  false;
      }
    }

    //将 utf8字符串转到 gbk 并 url 编码
    async utf8ToGbkUrlEncoded(str) {
      try {
        return await window.flutter_inappwebview.callHandler('utf8ToGbkUrlEncoded',str);
      } catch (error) {
        return  "";
      }
    }

    /*
    * @param str为图片链接 
    * @param header 请求的header头，此参数必须是json字符串
    * 此函数是让用户输入图片中的验证码，当链接为空则直接让用户输入验证码
    */
    async getVerificationCode(str,header) {
      try {
        return await window.flutter_inappwebview.callHandler('getVerificationCode',str,header);
      } catch (error) {
        return  "";
      }
    }
    
    //提交内容书本信息 json 后的字符串
    async addbook(book) {
      try {
        return await window.flutter_inappwebview.callHandler('addbook',book);
      } catch (error) {
        return  "";
      }
    }
    
    //utf8 字符串转base64
     async base64encode(str) {
      try {
        return await window.flutter_inappwebview.callHandler('base64encode',str);
      } catch (error) {
        return  "";
      }
    }
    
    //base64 转utf8字符串
    async base64decode(str) {
      try {
        return await window.flutter_inappwebview.callHandler('base64decode',str);
      } catch (error) {
        return  "";
      }
    }
    
    

  }

  //webview请勿使用
  //以下提交的url，headers,body 都必须为字符串,headers必须为json字符串
  //当followRedirects 为 false 时不处理重定向，当为 true 时会自动处理重定向 ，如不明白用途直接用 true 最佳
  // 以下所有参数除当followRedirects外均为 String
  // 如果需要使用http2协议 请在url 前添加 https2:// ，例如 http2://baidu.com
  // 如果https一直被盾拦截 ，可以使用https2协议
  class Http {
    constructor() {}

    /*
     * 通用返回字段
     * method post get 或者 head
     * body 请求返回后的字节的 base64
     * headers  map<String,List<String>> 可通过headers[""]来或者
     * statusCode 状态码
     * statusMessage 
     * data 返回后的字节 格式化后的内容 
     */
    async Get(url,headers,followRedirects) {
      try {
        return await window.flutter_inappwebview.callHandler('http',"get",url,"",JSON.stringify(headers),followRedirects,"");
      } catch (error) {
        return  null;
      }
    }

    async Head(url,headers,followRedirects) {
      try {
        return await window.flutter_inappwebview.callHandler('http',"head",url,"",JSON.stringify(headers),followRedirects,"");
      } catch (error) {
        return  null;
      }
    }

    
    async Post(url,headers,body,contenttype,followRedirects) {
      try {
        return await window.flutter_inappwebview.callHandler('http',"post",url,body,JSON.stringify(headers),followRedirects,contenttype);
      } catch (error) {
        return  null;
      }
    }
  }

  class Cache {
    constructor() {}
    async get(key) {
      try {
        return await window.flutter_inappwebview.callHandler('cache.get',key);
      } catch (error) {
        return  null;
      }
    }

    async set(key,value) {
      try {
        return await window.flutter_inappwebview.callHandler('cache.set',key,value);
      } catch (error) {
        return  null;
      }
    }

    async remove(key) {
      try {
        return await window.flutter_inappwebview.callHandler('cache.remove',key);
      } catch (error) {
        return  null;
      }
    }

    //如果登录为弹窗格式的，里面输入框输入的内容可以通过这个函数获取，默认返回的json格式或者为空，需要自行转换
    async getLoginInfo(){
      return await  this.get("LoginInfo")
    }

    //将修改后的弹窗输入内容报错 ，必须 JSON.stringify，不然会出错
    async putLoginInfo(info){
      return await  this.set("LoginInfo",info)
    }
   
    //获取书本变量 
    async getbookVariable(bookurl){
      return await  this.get(bookurl)
    }
    
    //写入书本变量 
     async setbookVariable(bookurl,value){
      return await  this.set(bookurl,value)
    }
  }

  class Cookie {
    constructor() {}

    //通过url获取当前url的所有cookie
    async get(url) {
      try {
        return await window.flutter_inappwebview.callHandler('cookie.get',url);
      } catch (error) {
        return  null;
      }
    }

    //通过url删除当前url的所有cookie
    async remove(url) {
      try {
        return await window.flutter_inappwebview.callHandler('cookie.remove',url);
      } catch (error) {
        return  null;
      }
    }


    //通过url保存当前url的所有cookie
    async set(url,value) {
      try {
        return await window.flutter_inappwebview.callHandler('cookie.set',url,value);
      } catch (error) {
        return  null;
      }
    }
    
    //设置单独一个cookie
    async setCookie(url,key,value) {
      try {
        return await window.flutter_inappwebview.callHandler('cookie.setcookie',url,key,value);
      } catch (error) {
        return  null;
      }
    }

    //通过 url 获取单个 cookie 的值
    async getCookie(url,value) {
      try {
        return await window.flutter_inappwebview.callHandler('cookie.getCookie',url,value);
      } catch (error) {
        return  null;
      }
    }
  }

  //安全的创建一个 div 解析 html
  function parseHTMLSafely(htmlStr) {
    try {
      // 在函数作用域内创建独立的临时容器
      // 每个调用创建新的jQuery对象，互不影响
      var tempDiv = document.createElement('div');
      tempDiv.innerHTML = htmlStr;
      return $(tempDiv);
    } catch (e) {
      flutterBridge.log("HTML解析错误:"+e.message);
      return $('<div>');
    }
  }

  //parseHTMLSafely 创建的用完后必须删除
  function removeHTMLSafely(tempContainer) {
    try {
      tempContainer.innerHTML = '';
      if (tempContainer.parentNode) {
        tempContainer.parentNode.removeChild(tempContainer);
      }
    } catch (e) {
      flutterBridge.log("HTML移除失败:"+e.message);
    }
  }

  //移除 css js，创建parseHTMLSafely前如果用不上 cssjs 建议移除
  function removeHTMLTags(htmlString) {
    // 移除script标签
    let result = htmlString.replace(/<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi, '');
    // 移除style标签
    result = result.replace(/<style\b[^<]*(?:(?!<\/style>)<[^<]*)*<\/style>/gi, '');
    return result;
  }

</script>
````

# 书本类
````
{
   "bookUrl":"", //书本 url 不可重复
   "name":"",  //书本名称
   "author":"", //作者
   "kind":"", //分类
   "coverUrl":"", //封面
   "intro":"", //简介
   "tocUrl":"", //目录 url 
   "wordCount":"", //字数
   "type":"", //书本类别 0 小说 1 听书 2 漫画
   "latestChapterTitle":"",
}
````
# 目录类
````
{
    "name" :"", //章节标题
    "chapterId":"", //正文 url
    "index" :0, //index
    "isPay":false, //是否需要付费
    "isVip":false, //是否是 vip
    "isVolume":false, //是否是卷名
    "tag":"" //小标题
};
当isvip 为 true ispay 为false时会显示购买按钮
````
# 发现类
````
{
    "title" :"", //标题
    "url":"", //url
    "js":"", //要运行的 js
    "type" :0, 
    "width": 0,
};
url 和 js 只能填一个
当type为 0 正常解析发现页，当 type 为 1 则会用 webview 打开 url,
width 为 0 时则默认处理宽度（1行3个）， 1 是一行2个 ，3 是一行1个
url 和 js 都为空是会按照 width = 3处理
````

# 登录类
````
{
    "name" :"", //标题
    "action":"", //执行的js 仅button生效
    "type" :"", //button text  password
};
当type为 0 正常解析发现页，当 type 为 1 则会用 webview 打开 url
````

# 书源
搜索函数
````
//如果没有分页 请在 page 为 2 及以上返回  "[]"
//当前函数返回书本数组的 json
async function search(key,page) {
}
````
详情页函数
````
//当前函数返回书本的 json
async function info(bookurl) {
}
````
目录页函数
````
//当前函数返回目录数组的 json
async function chapter(tocUrl) {
}
````
正文页函数
````
//提交内容为目录类里的chapterId
//当前函数返回字符串
async function content(url) {
}
````
发现函数
````
//当前函数返回发现数组的 json
async function getfinds() {
}
````
发现搜索函数
````
//如果没有分页 请在 page 为 2 及以上返回  "[]"
//当前函数返回书本数组的 json
async function find(url,page) {
}
````
购买函数
````
//提交内容bookurl为书本url，url为目录的chapterId
// 执行完成后会刷新目录和正文
async function pay(bookurl,url) {
}
````
登录函数
````
//返回http开头的url 或者 登录数组的json
//如果是登录数组的json 用户登录完会执行 await login()
async function getloginurl() {
}
async function login(){

}
````

图片解密函数
````
// url 为图片的url,如果需要传递参数可以在图片后接json字符串，例如：http://127.0.0.1,{'headers':{'a':'b'}}
//图片解密，image 为加密的图片的base64，执行的js必须是字符串所以这参数只能base64转码
//这个函数得返回byteList List<int> ,并且能直接被Uint8List.fromList(byteList)接受
async function imagedecrypt(url,image){
   
}
````

webview 拦截函数
````
// 当调用startBrowserWithShouldOverrideUrlLoading时必须有此函数
// url 为每次打开的 url
// 返回 false 则会取消打开这个网页
async function shouldOverrideUrlLoading(url){
   return true;
}
````