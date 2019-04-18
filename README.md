# 平台手游SDK接入说明(iOS v 1.1.0)

## 目录

* [1.前言说明] (#1)
* [2.SDK接入操作步骤] (#2)
* [2.1 Demo下载] (#2.1)
* [2.2 Demo接入流程] (#2.2)
* [2.3 登录、注册模块] (#2.3)
* [2.4 充值模块] (#2.4)
* [5 角色] (#5)
* [6 聊天上报] (#6.1)
* [6 正式版接入] (#6)
* [7 竖屏游戏须知] (#7)


---

<h3 id="1">1.前言说明</h3>

* 1.1面向读者

本文档主要面向需要接入平台平台SDK的游戏开发商的开发人员。


* 1.2读者所需技能

读者需有IOS程序开发背景，掌握java和Objective-C 程序开发。


* 1.3开发环境要求

SDK：ios 8.0或以上版本


* 1.4文档术语

game_id：平台提供的游戏id

channel_id : 推广的渠道id

ResultUrl：接入支付的回调通知接口地址，服务器将在后台用get方式请求该通知地址，需http://格式的完整路径，不允许加?id=123这类自定义参数。该页面处理成功必须输出“success”字符串。

total_fee：支付金额参数。单位元

goods：商品名称，可空

goods_desc：商品描述，可空

game\_order_sn：订单号，不为空

game\_api_url：异步通知订单接口，不为空


<h3 id="2">2.SDK接入操作步骤</h3>

* 为了便于联运合作方的接入，我们提供了SDK接入demo。通过本demo,联运合作方可以参考demo的代码完成接入

<h3 id="2.1"> 2.1 Demo下载</h3>

<br>

* [Demo下载]需要先登录账号

<h3 id="2.2">2.2 Demo接入流程</h3>

* Bundle id 格式: com.qisu.g7 (末尾的7为game_id)

* 导入demo中的KXYXGameSDK.framework、KXYXBundle.bundle、Masonry、AliPay，这四个文件夹

![文件夹.png](http://upload-images.jianshu.io/upload_images/1899979-010a660a119e952c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 根据支付宝需求导入以下依赖库

![alipay.png](http://upload-images.jianshu.io/upload_images/1899979-fc3207013bfe17f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 注意： 如果是Xcode 7.0之后的版本，需要添加libc++.tbd、libz.tbd；

* Build Phases -> Complie Soueces中来自Masonry的文件后面添加 -fobjc-arc ，适配arc mrc混编

![rac.png](http://upload-images.jianshu.io/upload_images/1899979-3d8b8fd7c57ad7a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 如果遇到 Cannot use "@throw" with objective-c exceptions disabled  或 Cannot use '@try' with Objective-C exceptions disabled 设置Enable Objective-c Exceptions为yes 如下图


![enable_objective_exceptions.png](http://upload-images.jianshu.io/upload_images/1899979-9f52afa11d78c7aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 在taegets->settings->header search path中添加支付宝SDK的路径

![header.png](http://upload-images.jianshu.io/upload_images/1899979-310fb6956aeb854f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 添加白名单

![白名单.png](http://upload-images.jianshu.io/upload_images/1899979-4fe604e8958cddfa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<h3 id="2.3">2.3 登录、注册模块</h3>

* 登录/注册用户在调用SDK之前强制用户需要填写配置文件，填写两个字段GAMEID和CHANNELID（如不知请询问对接人员）

![GAMEID](http://upload-images.jianshu.io/upload_images/1899979-a407f67358ba4b45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 这是用户注册所必须的两个参数，用户注册时便从配置文件读取这两个参数进行请求。

* 调用

导入#import “KXYXSDKManager.h"

```objc

- (void)login:(id)currentVC
success:(void (^)(void))successBlock
failure:(void (^)(NSString*))failBlock;

//方法，获取返回值，详情参见以下Demo；

/**
* 登录
*/
[[KXYXSDKManager sharedVOneSDK] login:self success:^{
//登录成功后的操作
} failure:^(NSString * str) {
//登录失败后的操作
}];
```


* 获取用户信息

```objc
//用户名
[KXYXSDKManager getUserName];
//用户昵称
[KXYXSDKManager getUserNickname];
//用户头像
[KXYXSDKManager getUserHaadimage];
```

* 退出登录

用户退出登录后会清除本地登录信息，并实现退出登录回调[KXYXSDKManager sharedVOneSDK].logout()。清除应用缓存可能会导致登录信息失效，需要重新登录。

```objc
[KXYXSDKManager sharedVOneSDK].logout = ^(){
//退出登录回调
};
```

* 登录状态

```objc

BOOL isLogin = [[KXYXSDKManager sharedVOneSDK] isLogin];
if (isLogin) {
//登录
} else {
//未登录
}

```


<h3 id="2.4">2.4 充值模块</h3>

* 在info.plist中添加 URL Schemes 

```objc
<key>CFBundleURLTypes</key>
<array>
<dict>
<key>CFBundleIdentifier</key>
<string></string>
<key>CFBundleTypeRole</key>
<string>Editor</string>
<key>CFBundleURLName</key>
<string>alipay</string>
<key>CFBundleURLSchemes</key>
<array>
<string>comKXYXSDK.g1</string>
</array>
</dict>
<dict>
<key>CFBundleTypeRole</key>
<string>Editor</string>
<key>CFBundleURLName</key>
<string>wechat</string>
<key>CFBundleURLSchemes</key>
<array>
<string>come.KXYX.xmrk.g1</string>
</array>
</dict>
</array>
```

* 注意URL Schemes 末尾需要根据GAMEID来修改 比如GAMEID = 7 comKXYXSDK.g7

![URLschemes.png](http://upload-images.jianshu.io/upload_images/1899979-8aa0dd5f2661bdd3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 支付接口

```objc
/**
*  调用支付界面
*
*  @param currentVC        发起调用的界面
*  @param payNum           支付金额
*  @param goods            商品名称(可以不填)
*  @param goods_desc       商品描述(可以不填)
*  @param game_order_sn    游戏方订单号(充值进游戏必填, 充值到平台不用填)
*  @param game_api_url     游戏方异步通知订单结果(充值进游戏必填, 充值到平台不用填)
*  @param game_server_name 充值区服的名称(充值进游戏必填, 充值到平台不用填)
*  @param game_server_id   充值区服的id(充值进游戏必填, 充值到平台不用填)
*  @param role_name        充值角色名(充值进游戏必填, 充值到平台不用填)
*  @param role_id          充值角色id(充值进游戏必填, 充值到平台不用填)
*  @param ext              拓展参数
*  @param successBlock     支付成功
*  @param failBlock        支付失败
*
*/
- (void)toPay:(id)currentVC
pay:(NSInteger)payNum
goods:(NSString *)goods
goods_desc:(NSString *)goods_desc
game_order_sn:(NSString *)game_order_sn
game_api_url:(NSString *)game_api_url
game_server_name:(NSString *)game_server_name
game_server_id:(NSString *)game_server_id
role_name:(NSString *)role_name
role_id:(NSString *)role_id
ext:(NSString *)ext
success:(void (^)(void))successBlock
failure:(void (^)(NSInteger))failBlock;

//example
[[KXYXSDKManager sharedVOneSDK] toPay:self
pay:self.textField.text.intValue
goods:@"青龙偃月刀"
goods_desc:@"关羽蜀黍"
game_order_sn:[NSString stringWithFormat:@"%@",[NSDate dateWithTimeIntervalSinceNow:0]]
game_api_url:@"111"
game_server_name:self.role_server_name.text
game_server_id:self.role_server_id.text
role_name:self.role_name.text
role_id:self.role_id.text
ext:@"2012333232" success:^{

NSLog(@"成功");


} failure:^(NSInteger failcode) {

NSLog(@"失败==%ld", (long)failcode);

}];

```




<h3 id="5">5 角色</h3>

* 注意：区服名称和区服id都传创角时的数据（尤其合区过后调用此接口的时候）
* 角色创建上报接口

```objc
/**
角色升级上报接口

@param gameId 游戏id
@param server_name 区服名称
@param server_id 区服id
@param role_name 角色名
@param role_id 角色id
@param level 等级
@param create_role_time 角色创建时间 时间戳
@param completion 上报回调 error: 0->成功, 其他失败  info:信息描述
*/
- (void)upgradeWithGameId:(NSInteger)gameId
server_name:(NSString *)server_name
server_id:(NSString *)server_id
role_name:(NSString *)role_name
role_id:(NSString *)role_id
level:(NSInteger)level
create_role_time:(NSTimeInterval)create_role_time
completion:(void (^)(NSInteger error, NSString *info))completion;
```



<h3 id="6.1">5 聊天上报</h3>

* 参考角色创建上报接口
* 注意：区服名称和区服id都传创角时的数据（尤其合区过后调用此接口的时候）

<h3 id="6">6 正式版接入 越狱版请忽略</h3>

* 1.拷贝原有项目，移除Alipay文件夹及相关内容
* 2.将[正式版framework]文件夹里面的内容拖到越狱版项目替换即可
* 3.内购产品id格式 Bundle identifier + RMB 例如：一个6块钱的产品（com.qisu.g7.6）

<h3 id="7">7 竖屏游戏须知</h3>

* 调用sdk之前需要配置sdk支持方向

```objc
[KXYXSDKManager sharedVOneSDK].orientation = UIInterfaceOrientationMaskPortrait;
```
