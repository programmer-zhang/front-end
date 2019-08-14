> 此文件主要留存日常代码书写过程中的 微信相关开发 相关的报错与解决方案，部分方案参考自网络，如有侵权，请联系chinajnzhang@hotmail.com删除

### 微信小程序开发数据传输限制
* 报错内容 `invokeWebviewMethod 数据传输长度为 xxxxx 已经超过最大长度 1048576`
* 原因：wx:for 渲染的数据长度过长,每次setData的时候传入的更新数据太多
* 解决方法：
	* 后台的数据接口传值简化
	* 前端优化数据，只保留需要的数据

### 微信安全域名的坑
* [微信网页授权官方文档](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140842)
* 具体的微信安全域名配置就不说了，大家都知道的，也可自行百度
* 略微提几点
	* 微信安全域名配置前需要在该域名根目录下放入微信官方的配置文件，并且可以访问到。
	* 域名配置仅配置域名就可以，不需要加上传输协议名称。
	* 大家可根据需要直接配置一级或者二级域名，避免以后重复添加和修改，微信安全域名同一个微信公众号仅可配置三个微信安全域名，每月仅支持修改三次。

### 微信开发踩过的那些坑
* invalid URL domain（域名绑定失败或者域名不存在）
	* 检查后台是否设置：右上角公众号名称--功能设置--JS接口安全域名
	* 检查代码里的appid和公众号后台的id是否一致
	* 一级域名，非80端口需要带端口号
* permission denied（接口权限问题）
	* 有的接口需要认证之后才可以使用
* invalid signature（大部分微信分享失效是因为这个报错）
	* 微信js文件未引入或者引入错误
		* 看传输协议，HTTPS引入外部HTTP文件有问题，将微信的JS文件传输协议改成HTTPS，该文件支持HTTPS传输
	* 生成签名的url(使用jssdk的页面地址，这个页面地址可以在浏览器访问)，包含“？”号后面的所有参数，不包含“#”号后面的值
		* 官方提供的解决方案 使用`encodeURIComponent(location.href.split('#')[0])即可`
	   * 未知原因会造成页面请求的url被encode，需要多加一层encode
* debug模式下config:OK但是分享不成功的奇葩原因
	* 客户端6.7.2及JSSDK 1.4.0以上版本将开始更新新的微信分享接口
	* [微信分享接口API更新](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115)
	* 在按照微信指引使用JSSDK 1.4.0之后，将微信分享接口调整为`wx.updateAppMessageShareData、updateTimelineShareData`失效
	* 不知道是微信的原因还是什么原因，使用旧版的分享接口后完全没问题

### 浏览器端模拟微信打开

* UA-Android
Mozilla/5.0 (Linux; U; Android 4.1.2; zh-cn; Chitanda/Akari) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 Mobile Safari/534.30 MicroMessenger/6.0.0.58_r884092.501 NetType/WIFI

* UA-ios
Mozilla/5.0 (iPhone; CPU iPhone OS 8_0 like Mac OS X) AppleWebKit/600.1.4 (KHTML, like Gecko) Mobile/12A365 MicroMessenger/5.4.1 NetType/WIFI

### 关于微信中的localStorage及使用cookie的解决方案
* 微信环境其实是个webview组件而已，并不是真正意义上的什么内置浏览器。
* 安卓版微信直接调用系统浏览器内核，它是用chrome改造做的一套WKwebView,概念上类似是一套组建, iOS则是调用safari。所以把微信内置的第三方网页看成是在整个浏览器环境下的想法是错误的。
* 微信内置浏览器中的localstorge是可以用的，但是会有兼容性问题，并且问题比较严重，会有部分机型存储不了localstorge，或者微信关闭及页面退出的时候会被清空
* 网上有开发提供了一种使用cookie代替localstorge的方案

```
//设置cookie
function setCookie(c_name,value,expiredays) {
    var exdate = new Date()
    exdate.setDate(exdate.getDate()+expiredays)
    document.cookie = c_name+ "=" + escape(value) + ((expiredays==null) ? "" : ";expires=" + exdate.toGMTString())
}

//取回cookie
function getCookie(c_name) {
    if (document.cookie.length>0) {
        c_start=document.cookie.indexOf(c_name + "=")
        if (c_start!=-1) { 
            c_start=c_start + c_name.length+1 
            c_end=document.cookie.indexOf(";",c_start)
            if (c_end==-1) {
                c_end=document.cookie.length
            }
            return unescape(document.cookie.substring(c_start,c_end))
        } 
    }
    return ""
}
```

### 微信小程序 scroll-view 左右横向滑动没有效果（无法滑动）
* 解决方式
	1. scroll-view 中的需要滑动的元素不可以用 float 浮动
	2. scroll-view 中的包裹需要滑动的元素的大盒子用 `display: flex;` 是没有作用的
	3. scroll-view 中的需要滑动的元素要用 `dislay: inline-block;` 进行元素的横向编排
	4. 包裹 scroll-view 的大盒子有明确的宽和加上样式 `overflow: hidden; white-space:  nowrap;`

```
// HTML
<scroll-view class="play-item-scroll" scroll-x="true">
	<block wx:for="{{activityLog.leave}}" wx:for-item="item" wx:for-index="index" wx:key="index">
	  <view class="scroll-content">
	    <span class="scroll-item-img" style="background-image: url({{item.userInfo.avatarUrl || '/assets/img/user-active.png'}});"></span>
	    <span class="scroll-item-name">{{item.realName || '错误'}}</span>
	  </view>
	</block>
</scroll-view>
// CSS
.play-item-scroll {
    float: left;
    width: 86%;
    height: 80px;
    overflow:hidden;
    white-space:nowrap;
}
.play-item-scroll .scroll-content{
    display: inline-block;
    position: relative;
    height: 80px;
    width: 60px;
    padding-top: 10px;
    box-sizing: border-box;
    text-align: center;
    overflow: hidden;
}
.scroll-item-img {
    display: inline-block;
    position: absolute;
    left: 50%;
    margin-left: -15px;
    width: 30px;
    height: 30px;
    border-radius: 50%;
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center;
}
.scroll-item-name {
    width: 100%;
    text-align: center;
    display: block;
}
```

### 微信小程序 wx.showModal content 文字换行
* 本身并没有换行，需要通过换行符进行实现
* 注：在微信开发者工具中不一定能看到效果，需要在真机中才能看到

```
wx.showModal({
  title: '提交成功',
  content: '足球真正的魅力不是输赢，而是有它的地方就有兄弟姐妹。\r\n很多年后，当我们老得只能坐在场边，你会发现最怀念的不是踢足球，而是陪你踢球的那群人。',
  showCancel: false,
  confirmText: '前往首页',
  success: function(res) {
    if (res.confirm) {
      wx.navigateTo({
        url: '/pages/index/index'
      })
    }
  }
})
```

### 微信号

```
一般大家现在都在用微信做生意，微信却限制大家使用第三方软件加粉和日常营销，一个微信号每天最多主动加不能超过30人，所以我们要批量化的申请微信号，又因为微信的算法很强大，他判断你在做营销就会封号，所以有一个养号的概念，既把微信号养成普通人在用的号。这过程中，很多前辈花了很多钱和时间与微信做斗争。每一次微信大规模封号，都导致营销人血本无归。作为达摩院，我们希望同学少走网路，在互联网营销的道路上走的更远，赚的更多。
提示：技术解释看不懂直接跳到怎么做部分
1.技术解析封号的四个因素
硬件设备、微信帐号ID、IP、行为。
硬件：指微信号登录的设备，有几个关键标识：MAC、IMEI、序列号等等；
IP:就是设备联网时被分配的网络地址。流量IP与WIFI的IP还是有不同的。流量基于基站，WIFI基于路由器的发射模块，微信是可以分辨的；
行为：就是从上次登陆时间和地理位置来判断是否移动速度异常，另外还有被举报、被自动检测到等等情形。
这四个方面是基于微信的运行日志，微信必会采集的10条信息:
（1）你的手机的MAC地址；
（2）上次登录的时间；
（3）手机的IMEI；
（4）手机号码；
（5）手机型号；
（6）手机基带芯片序列号；
（7）当前手机系统的用户（有没有ROOT）；
（8）手机的家里WIFI路由器的地址，IP，上次登录的时间；
（9）地理位置；
（10）通信录的电话本信息。
微信既然收集以上10条信息，那么对于违规账号的判断依据，也必然跟这10条收集的信息有关。微信是通过监测号子的行为，来判断是否将一个ID或者IP关进小黑屋。
封ID和设备的情况较多，因为一个IP下往往有很多用户，不可能因为一两个ID违规，就将整个IP下的设备都封掉，所以封ID是首选，其次严重违规了，会封设备。所以经常有这种情况，就是一个机子上，某个微信的功能被限制了，再上其他号，也一样被限制；
封IP的情况也不是没有，就是一个IP下有大量违规ID，那这个IP就很危险。最常见的就是模拟器养号的，不死就都活，一死一片。封IP的时候一般也会连ID也一起封了。
如果规避风险
1、MAC：
一个MAC地址，最好不要登陆多个号；登过黑号的MAC，也不要再登陆新号，以免“感染”，所以建议咱们群的伙伴最好用另外一个手机注册和登录。
2、上次登陆的时间：
这个我觉得没太所谓，只要别有“上一分钟在北京，下一分钟到了广东”的情况出现，就没什么问题。
3、IMEI：
这个跟MAC一样处理。
4、手机号码：
号码最好设置成微信号注册的号码，另外别用移动的号码，却跑着联通的流量；170的虚拟卡也是分移动联通电信的，不懂的找度娘。
5、手机型号：
什么品牌出货量大，就选什么品牌！将自己的号子埋没进人海中去！最好是三星，国
际品牌，路子广！
6、手机基带芯片序列号：
交给软件搞定。
7、ROOT与否：
无所谓。ROOT过的机子检测更严一点，伪装好一点便是。
8、WIFI路由器的地址，IP，上次登录的时间：
换地址请拨电源重插。建议一个wifi的IP下不要超过五个微信号，否则会引起封号几率大大提升。
9、定位：
咱们群里没有大规模做群控的，所以这个无所谓，建议大家出门的时候也带着这个小号。当初我们公司批量养号的时候，就每天让一个人背着一个书包，然后放一书包手机去做公交车满郑州的跑。
10、通信录，短信等：
对于养号的人来说，这一项比较重要，所以建议养号的手机里面可以存几个号码，并且让微信有访问权限。
11、傍大腿：
购买理财通，购买公益等，偶尔付出一点还是有回报的。
2.微信养号这么做？
微信注册号准备阶段
了解了相关的封号机制，那么我们开始注册微信号，准备两个东西吧：
1、新的手机。还是建议用新的手机，配置不用太好，除非以后咱们微信上的粉丝数量、微信群数量、信息数量多的话可以考虑升级配置；当然你用闲置旧手机也可以的。
2、 一个开通流量的手机卡。切记，不要去买微信号！那些微信号都是批量注册的，很容易被封，我们就吃过这方面的亏。现在不都是无限流量套餐吗，开个副卡就OK。
养号期（15天）
新注册的账号就像新生儿一样，一定要有清白的出生记录，执行下最基本的操作就可以了，保持安静，等待成长。
1、用手机号注册新微信号，每天正常登陆微信。
2、完善账号信息（头像，签名，微信号，地区，性别等）。
3、适当添加若干好友。
4、被动加若干好友。
5、15天内不要使用摇一摇，附近加好友功能，微信会屏蔽的信息。
6、关注公众号，并转发文章。
7、加入微信群，并在群里打招呼，保持少量群内互动。
8、提高更新朋友圈频率，增加点赞与评论次数。
9、主动找好友聊天，有问必答，对好友进行标签分组。
10,、增加主动添加好友数量，但每天不要超过20个。
11、微信账号存1-2元红包。
12、点开朋友圈发布消息，不要连续发，时间间隔2小时，发布内容与生活相关的，不要涉及推销，广告违禁消息。
13、关注微信公众号，微信官
```