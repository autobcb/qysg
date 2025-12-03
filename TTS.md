
# 轻悦时光朗读引擎规则

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

# 登录类
````
{
    "name" :"", //标题
    "action":"", //执行的js 仅button生效
    "type" :"", //button text  password
};
当type为 0 正常解析发现页，当 type 为 1 则会用 webview 打开 url
````


# 引擎
获取tts音频的请求链接
````
  // tts链接返回方式 ，不可缺少参数
  async function getttsurl(speakText,speechRate){
    return JSON.stringify({
      "method": "get", //请求方式
      "url": "", //请求 url
      "headers": {}, //携带的 header
      "body": "" //如果为 post 可以携带 body 参数
    });
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