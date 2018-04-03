# push
设计概要：

工厂模式：可根据配置随时切换第三方推送（本人的所有包库都是工厂模式）

PHP版本：信鸽、个推PHP7以上，友盟5.3以上，如果PHP版本低可自行改一下兼容的，应该只有一两处地方不兼容

目前支持的驱动有：信鸽，友盟、个推

接口规范

目前支持的框架：Yii

不需要依赖框架运行：是
##### 注：友盟已完成测试无误。 信鸽运行测试没有问题，但是否能到达手机未测试。

# 吐槽
在这里我要吐槽一下，个推PHP SDK不知道是哪一位大神写的，可能写得比较早，没用上命名空间，所以得把sdk改名字再加上命名空间，但是不就是一个推送而已吗，40个多个php文件（友盟，信鸽17，18个），庞然大物啊！就说设置一个简单的属性，就写了7个类出来，且套用这些类的方法时还配了n个动态方法去调用，光是改这set_value就改了劳资近一个小时，感觉好像每一个文件都要改，人家别的就改几个文件的命名空间就能用了，看看这个推的技术水平多高啊，难道40个文件每一个文件都交叉关联使用吗？改得老子想死，这藕合高得没边了。我改别人的程序多垃圾的都见过，这么垃圾的还是第一次，而且还是在一个大公司里写的SDK，劳资代码水平这么高还是在一些中小型公司里，人比人真TM没法比。改起来JB吃力，TMDB，大神你不要让我知道你名字，知道老子非得在心里问候你妈一百遍不可，操！我只是想改好命名空间直接调用，第一次对接第三方接口这样生气。妈B，改了劳资两个多小时，像信鸽，友盟的SKDK几分钟就改好了，这个推大神真牛逼！
### 安装
composer require xing.chen/push dev-master

# 使用示例
```php
<?php
//  ***  初始化驱动
// 友盟配置
$configUmeng = [
    'android' => [
        'appKey' => 'appKey',
        'appMasterSecret' => 'appMasterSecret'
    ],
    'IOS' => [
        'appKey' => 'appKey',
        'appMasterSecret' => 'appMasterSecret'  
    ]
];

// 信使配置
$configXinge = [
   'expireTime' => 86400,
    'android' => [
       'accessId' => 'accessId',
       'secret_key' => 'secret_key',
    ],
    'IOS' => [
       'accessId' => 'accessId',
       'secret_key' => 'secret_key',
    ]
];

// 个推配置
$confgGeTui = [
  'AppID' => 'Bfct7mBqA68WgBU3DV7Yq4',
  'AppKey' => '2CWKfTRjZv9EmXPmtu4qs2',
  'MasterSecret' => 'nrFADeO7lg7QmrCdCgy5t6',
];


// ---------------------- YII框架 --------------------

// 友盟
$driveName = 'Umeng';
$configYii = $configUmeng;

// 信鸽
$driveName = 'Xingge';
$configYii = $configXinge;

// 个推
$driveName = 'GeTui';
$configYii = $configXinge;

// 需要先在components设置：
'components'=> [
        'push' => [
            'class' => 'xing\push\Yii',
            'driveName' => $driveName, // 使用哪个推送驱动名称
            // 驱动里的配置
            'config' => $configYii
        ],
];

// 设置标题和消息、自定义参数
$push = Yii::$app->push
        ->setTitle('标题')
        ->setBody('消息正文')
        ->setExtendedData(['a' => 1, 'b' => 2]); // 自定义扩展参数;
        
        
// -------------------- 独立运行：工厂模式 ---------------------

// 设置标题和消息、自定义参数
$push = \xing\push\core\PushFactory::getInstance('Umeng或xinge')::init($configUmeng)
        ->setTitle('标题')
        ->setBody('消息正文')
        ->setExtendedData(['a' => 1, 'b' => 2]);// 自定义扩展参数

// -------------------- 独立运行：友盟 ---------------------
// 设置标题和消息、自定义参数
$push = \xing\push\drive\UmengService::init($configUmeng)
        ->setTitle('标题')
        ->setBody('消息正文')
        ->setExtendedData(['a' => 1, 'b' => 2]); // 自定义扩展参数
        
// -------------------- 独立运行：信鸽 ---------------------
// 设置标题和消息、自定义参数
$push = \xing\push\drive\UmengService::init($config)
        ->setTitle('标题')
        ->setBody('消息正文')
        ->setExtendedData(['a' => 1, 'b' => 2]); // 自定义扩展参数
        
        
//   ***************  发送方法  **********   
# 广播：所有平台
$push->sendAll();
# 广播：安卓
$push->sendAllAndroid();
# 广播：IOS
$push->sendAllIOS();

# 单播：所有平台
$push->sendOne('设备码');
# 单播：安卓
$push->sendOneAndroid('设备码');
# 单播：IOS
$push->sendOneIOS('设备码');

```