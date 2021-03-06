# 生成Android设备唯一识别码

### 更新
- 2019.9.20

Android 10

禁止访问设备ID（imei、build serial），移除getDeviceId（）不再需要read_phone_state权限。

-----

#### UUID
全局唯一标识符,是指在一台机器上生成的数字，它保证对在同一时空中的所有机器都是唯一的。
#### IMEI
IMEI本该最理想的设备ID，具备唯一性，恢复出厂设置不会变化（真正的设备相关）。
然而，获取IMEI需要 READ_PHONE_STATE 权限，估计大家也知道这个权限有多麻烦了。
尤其是Android 6.0以后, 这类权限要动态申请，很多用户可能会选择拒绝授权。
我们看到，有的APP不授权这个权限就无法使用， 这可能会降低用户对APP的好感度。
而且，Android 10.0 将彻底禁止第三方应用获取设备的IMEI, 即使申请了 READ_PHONE_STATE 权限。
#### MEID
是全球唯一的56bit CDMA制式移动终端标识号。标识号会被烧入终端里，并且不能被修改。可用来对CDMA制式移动式设备进行身份识别和跟踪。
#### DEVICE_ID
根据不同的手机设备返回IMEI，MEID或者ESN码。非手机设备无效
#### MAC ADDRESS
可以使用手机WiFi或蓝牙的MAC地址作为设备标识，但是并不推荐这么做，原因有以下两点：

硬件限制：并不是所有的设备都有WiFi和蓝牙硬件，硬件不存在自然也就得不到这一信息。

获取的限制：如果WiFi没有打开过，是无法获取其Mac地址的；而蓝牙是只有在打开的时候才能获取到其Mac地址。
#### ANDROID_ID
Android ID 是获取门槛最低的，不需要任何权限，64bit 的取值范围，唯一性算是很好的了。
但是不足之处也很明显：
1、刷机、root、恢复出厂设置等会使得 Android ID 改变；
2、Android 8.0之后，Android ID的规则发生了变化：
- 对于升级到8.0之前安装的应用，ANDROID_ID会保持不变。如果卸载后重新安装的话，ANDROID_ID将会改变。
- 对于安装在8.0系统的应用来说，ANDROID_ID根据应用签名和用户的不同而不同。

ANDROID_ID的唯一决定于应用签名、用户和设备三者的组合。
两个规则导致的结果就是：
- 第一，如果用户安装APP设备是8.0以下，后来卸载了，升级到8.0之后又重装了应用，Android ID不一样；
- 第二，不同签名的APP，获取到的Android ID不一样。

其中第二点可能对于广告联盟之类的有所影响（如果彼此是用Android ID对比数据的话），所以Google文档中说“请使用Advertising ID”，不过大家都知道，Google的服务在国内用不了。
对Android ID做了约束，对隐私保护起到一定作用，并且用来做APP自己的活跃统计也还是没有问题的。

---

## 最终方法
为了实现在设备上更通用的获取设备唯一标识，我们可以，以ANDROID_ID为基础，在获取失败时,使用UUID的生成策略。
