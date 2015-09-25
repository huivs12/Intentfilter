/**
 * 启动Activity的方式有两种：显示调用，隐式调用
 * <p/>
 * 显示调用：指定要打开的Activity的包名及类名
 * <p/>
 * 隐式调用：需Intent能够匹配目标组件的IntentFilter中的过滤信息。
 * 这些过滤信息包括：action,category,data。这些信息可以存在多个，只要有一个Intent同时匹配Action,Category,
 * Data才算完全匹配并成功启动目标Activity，另外，一个Activity中可以有多个intent-filter，一个Intent
 * 只要成功匹配任何一组的intent-filter即可成功启动对应的Activity。
 * <p/>
 * Action: 系统已经帮我们预定了一些Action，我们也可以自己去定义，在一个Intent中必须指定的一个Action，并且这个
 * Action中的字符串必须与IntentFilter中的定义的Action相匹配，在IntentFilter中可定义多个Action，但
 * 我们只需要匹配其中一条即可。
 * 注：Action的字符串是区分大小写的。
 * Category: 系统也已经帮我们预定了一些Category，我们也可自定义，与Action不同的是，在Intent中可以有Category，
 * 也可以没有，但是如果有Category，不管有几个，都必须与intent-filter中category的任意一条匹配。否则
 * 无法启动对应的Activity。
 * 为什么可以在Intent不设置Category?
 * 因为系统用startActivity或startActivityForResult的时候会默认给Intent加上"android.intent.category.DEFAULT"
 * 这个Category,所以值得注意的就是：为了我们能够正常隐式调用，必须在intent-filter中指定"android.intent.category.DEFAULT"
 * 的category。
 * Data: 如果intent-filter中定义了data，那Intent中必须要指定可匹配的Data。
 * Data由两个部分组成：mimeType和URI
 * mimeType: 指的是媒体类型，比如image/jpeg,vido/*等
 * URI:
 * URI的结构
 * <scheme>://<host>:<port>/[<path>|<pathPrefix>|<pathPattern>]
 * 例：
 * content://com.example.project:200/folder/subfolder/etc
 * <p/>
 * Scheme: URI的模式，比如http,file,content等，如果URI没有指定scheme，那整个URI的其它参数无效，意味着整个URI无效。
 * Host: URI的主机名，比如www.baidu.com，如果Host没有指定，那整个URI的其它参数无效，意味着整个URI无效。
 * Port：URI的端口号
 * Path,PathPrefix和PathPattern这三个参数都是表示路径信息的，其中path表示完整路径，pathPattern也表示完整路径，但是
 * 它可以包含通配符"*"，"*"表示0到多个任意字符。值得注意的是：由于正则表达式的规范，如果想表示真实的字符串，那么"*"要
 * 写成"\\*"，"\"要写成"\\\\";pathPrefix表示路径的前缀信息。
 * <p/>
 * 匹配规则：Intent必须含有Data数据，并且Data数据能够完全匹配intent-filter中的某一个data。
 * <p/>
 * 当在intent-filter指定一个mimeType为image/*时，Intent中mimeType属性必须也为image/*才会匹配，虽然没有指定URI，但是
 * 却有默认值，默认值为content和file。
 * <p/>
 * 如果要为Intent指定完整的Data，必须要调用setDataAndType方法，不能先调用setData再调用setType，因为在源代码中可以了解
 * 到在setData和setType中都会将对方法的对象设置为空：
 * 如：
 * public Intent setData(Uri data){
 * mData = data;
 * mType = null;
 * return this;
 * }
 * <p/>
 * Intent-filter的匹配规则对于Service和BroadcastReceiver也是同样的道理，不过系统对Service的建议是尽量使用显示调用方式来启动
 * <p/>
 * 另外通过隐式方式启动一个Activity的时候，可以先做一个判断，看是否有Activity能够匹配我们的隐式Intent，如果不做判断就有可能出现
 * 错误。判断的方法有两种：
 * 1. 采用PackageManager的resolveActivity
 * 2. Intent的resolveActivity方法
 * 如果它们找不到匹配的Activity就会返回null，我们通过判断返回值就可以规避错误。
 * <p/>
 * 另外，PackagetManager还提供了queryIntentActivities方法，这个方法和resolveActivity方法不同的是：它不是返回最值匹配的Activity
 * 信息，还是返回所有成功匹配的Activity信息。
 * <p/>
 * 调用这两个方法时的第二个参数要注意：这个传入MATCH_DEFAULT_ONLY标记位，这个标记位是匹配那些在intent-filter中声明
 * <category android:name="android.intent.category.DEFAULT"/>这个category的Activity。只要上述两个方法不返因null
 * 那么就可以成功匹配。如果不用这个标记位的话，就可以把intent-filter中category不含DEFAULT的那些Activity匹配出来，从而
 * 导致startActivity可能失败，因为不含有DEFAULT这个category的Activity是无法接收隐式的Intent的。
 */
