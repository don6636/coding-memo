[TOC]

# KEEP ANDROID



## OkHttp

- GET/POST

```java
// Builder()方式可以在获取实例的同时设置一些参数，也可以通过new获取实例
/**
 * GET/POST 相同步骤：
 * 获取OkHttpClient实例
 */
OkHttpClient client = new OkHttpClient.Builder()
    .connectTimeout(5,TimeUnit.SECONDS)
    .build();
/**
 * POST 单独步骤：
 * 实例化请求体
 */
RequestBody body = new FormBody.Builder()
    .add(key1, value1)
    .add(key2, value2)
    .build();

// 构建Request对象
Request request = new Request.Builder()
    .url("http://www.baidu.com/")
    .post(body)//指定请求方式get/post等
    .builder();

// 构建Call对象
Call mCall = client.newCall(request);

/**
 * 同步方式
 * 直接调用mCall对象的execute()
 */
Response response = mCall.execute();
// 返回的数据
String data = response.body().string();
=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
/**
 * 异步方式
 * 调用mCall对象的enqueue(),并实现Callback接口
 */
mCall.enqueue(new Callback(){
    @Override
    public void onFailure(Call call, IOException e) {
        // TODO
    }
    
    @Override
    public void onResponse(Call call, Response response) throws IOException {
        String data = response.body().string();
        // TODO
    }
});
```

***ps:*** `response.body().string()` **只能有效调用一次！**

- 拦截器：分为应用拦截器*(Application interceptors)*和网络拦截器*(Network Interceptors)*

```java
// Build a full stack of interceptors.
List<Interceptor> interceptors = new ArrayList<>();
interceptors.addAll(client.interceptors());
interceptors.add(new RetryAndFollowUpInterceptor(client));
interceptors.add(new BridgeInterceptor(client.cookieJar()));
interceptors.add(new CacheInterceptor(client.internalCache()));
interceptors.add(new ConnectInterceptor(client));
if (!forWebSocket) {
  interceptors.addAll(client.networkInterceptors());
}
interceptors.add(new CallServerInterceptor(forWebSocket));
```

<img src="okhttp/interceptors.png" style="zoom:50%;" />

***Q:*** `java.net.UnknownServiceException: CLEARTEXT communication to <host> not permitted by network security policy`

***R:*** 从API28开始，Android系统默认要求使用加密连接*(https)*。注意Webview同样会受影响。

***A:*** 针对此情况，有3种解决方案：

1. 后端API接口全部改用https请求；
2. targetSdkVersion降到27或以下；
3. 更改默认[网络安全配置](https://developer.android.google.cn/training/articles/security-config)：新建 `../res/xml/network_security_config.xml`，内容如下。并通过manifest文件application节点下的 `android:networkSecurityConfig="@xml/network_security_config"` 属性引用（`android:usesCleartextTraffic="true"`？）。

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true">
        <trust-anchors>
            <certificates src="system" />
        </trust-anchors>
    </base-config>
</network-security-config>
```

- 使用官方定义的状态码，如：`HttpURLConnection.HTTP_OK`





## Retrofit

- 可以添加多种 `addConverterFactory`，但 `GsonConverterFactory` 必须放在最后，否则抛异常。
- baseUrl 要以 **/** 结尾，HTTP请求注解和 *@Url* 注解（可以定义完整url，优先级高于baseUrl）不要以 **/** 开头。



- Retrofit2 URL组合规则：

| BaseUrl                     | 和URL有关的注解值       | 结果                            |
| :-------------------------- | :---------------------- | :------------------------------ |
| http://localhost:8080/path/ | /post                   | http://localhost:8080/post      |
| http://localhost:8080/path/ | post                    | http://localhost:8080/path/post |
| http://localhost:8080/path/ | https://github.com/test | https://github.com/test         |





## RxJava/RxAndroid





## JSON

- `getXxx()`：取值不正确时会抛出异常，如对应key值不存在时会报空指针异常。
- `optXxx()`：取值不正确时会试图进行转化或输出友好值，不会抛出异常，如对应key值不存在时会返回一个空字符串或者自定义的默认值。

```java
/**
 * @param target 已实例化的 {@link JSONObject} 对象
 * @param key    待查询字段对应的 key
 * @return 不为 <i>null<i/>(注意非字符串"null"), 返回查询结果, 否则返回""
 * @see JsonUitl#optString(JSONObject, String, String)
 */
@NonNull
public static String optString(@NonNull JSONObject target, @Nullable String key) {
    return optString(target, key, "");
}

/**
 * @param target   已实例化的 {@link JSONObject} 对象
 * @param key      待查询字段对应的 key
 * @param fallback 查询不存在时的默认值
 * @return 不为 <i>null<i/>(注意非字符串"null"), 返回查询结果, 否则返回fallback {@param fallback}
 * @see JsonUitl#optString(JSONObject, String)
 */
@NonNull
public static String optString(
        @NonNull JSONObject target, @Nullable String key, @NonNull String fallback) {
    return target.isNull(key) ? fallback : target.optString(key, fallback).trim();
}
```



**Gson**

------

- JSON序列化后的key只取决于Java对象的字段名称，并严格区分大小写及符号。
- fromJson只会解析Java bean类中定义的属性，未定义的JSON key不解析。



假设 *Food.class* 是一个Java bean类：

- JSONObject → Java对象　`<T> T fromJson(String json, Class<T> classOfT)`

```java
String jsonObject = "{ \"id\":2, \"name\":\"皮皮虾\", \"price\":13.7 }";
Food food = new Gson().fromJson(jsonObject, Food.class);
```

- JSONArray → Java List　`T fromJson(String json, Type typeOfT)`

```java
String jsonArray = "[{ \"id\":2, \"name\":\"皮皮虾\", \"price\":13.7 }," 
    + "{ \"id\":5, \"name\":\"麻辣香锅\", \"price\":85 }]";
List<Food> list = new Gson().fromJson(jsonArray, 
                                      new TypeToken<List<Food>>(){}.getType());
```

- Java对象 → JSONObject　`String toJson(Object src)`

```java
Food food = new Food(2, "皮皮虾", 13.7);
String jsonObject = new Gson().toJson(food);
```

- Java List → JSONArray　`String toJson(Object src)`

```java
List<Food> list = new ArrayList<>();
list.add(new Food(2, "皮皮虾", 13.7));
list.add(new Food(5, "麻辣香锅", 85));
String jsonArray = new Gson().toJson(list);
```



- `@SerializedName("name")`：指定字段序列化/反序列化时的名称

```java
public class MyClass {
    // 指定(反)序列化键 name
    @SerializedName("name") String a;
    // 指定序列化键 name1，反序列化键可以为 name1/name2/name3 任意
    @SerializedName(value="name1", alternate={"name2", "name3"}) String b;
    // 默认使用字段名称作为(反)序列化键
    String c;

    public MyClass(String a, String b, String c) {
        this.a = a;
        this.b = b;
        this.c = c;
    }
}
```

```java
MyClass target = new MyClass("v1", "v2", "v3");
Gson gson = new Gson();
String json = gson.toJson(target);
System.out.println(json);
// {"name":"v1","name1":"v2","c":"v3"}
```

```java
// 使用 name1/name2/name3 都可以成功反序列化 b 字段
MyClass target = gson.fromJson("{'name1':'v1'}", MyClass.class);
assertEquals("v1", target.b);
target = gson.fromJson("{'name2':'v2'}", MyClass.class);
assertEquals("v2", target.b);
target = gson.fromJson("{'name3':'v3'}", MyClass.class);
assertEquals("v3", target.b);
```



- `@Expose`：标注(反)序列化字段，需配合 `new GsonBuilder().excludeFieldsWithoutExposeAnnotation().create()` 方式使用

```java
public class User {
    @Expose private String firstName;
    @Expose(serialize = false) private String lastName;
    @Expose (serialize = false, deserialize = false) private String emailAddress;
    private String password;
}
/* 若使用 new Gson() 方式创建Gson对象，则 @Expose 注解不生效，会正常(反)序列化User类的所有字段；
    若使用 new GsonBuilder().excludeFieldsWithoutExposeAnnotation().create() 方式创建Gson对象，
    (反)序列化时将只考虑被 @Expose 注解标注的字段，并根据注解的 serialize/deserialize 值(默认均为true)执行特定行为 */
```

***ps:*** 可配合 `@SerializedName` 注解同时定制(反)序列化键。



- `@Since(1.0)`：标注序列化版本，需配合 `new GsonBuilder().setVersion(1.0).create()` 方式使用

```java
public class User {
    private String firstName;
    private String lastName;
    @Since(1.0) private String emailAddress;
    @Since(1.1) private String password;
}
/* 若使用 new Gson() 方式创建Gson对象，则 @Since 注解不生效，会正常(反)序列化User类的所有字段；
    若使用 new GsonBuilder().setVersion(1.0).create() 方式创建Gson对象，
    序列化时将会排除被 @Since 注解标注且值大于version(如此处设置的1.0)的字段 */
```

***ps:*** 被注解的字段只有<u>序列化</u>会受到影响，未被注解的字段不受任何影响，会正常(反)序列化。



- `@Until(1.0)`：标注序列化版本，需配合 `new GsonBuilder().setVersion(1.0).create()` 方式使用

```java
public class User {
    private String firstName;
    private String lastName;
    @Until(1.0) private String emailAddress;
    @Until(1.1) private String password;
}
/* 若使用 new Gson() 方式创建Gson对象，则 @Until 注解不生效，会正常(反)序列化User类的所有字段；
    若使用 new GsonBuilder().setVersion(1.1).create() 方式创建Gson对象，
    序列化时将会排除被 @Until 注解标注且值小于或等于version(如此处设置的1.1)的字段 */
```

***ps:*** 被注解的字段只有<u>序列化</u>会受到影响，未被注解的字段不受任何影响，会正常(反)序列化。可同时注解 `@Since` 和 `@Until`，但要满足条件：**`@Since` ≦ *version* < `@Until`**。



- `@JsonAdapter`



**FastJson**

------

- JSON序列化后的key只取决于getter方法名，与字段名称或setter方法名无关。

  `JSON.toJSONString(Object object)`





## Glide



***issues:***

1. Glide上下文IllegalArgumentException，e.g.

   *You cannot start a load on a null Context.*

   *You cannot start a load for a destroyed activity.*

   *You cannot start a load on a fragment before it is attached.*

   *R*：`Glide.with(context)` 异步加载时，遇到 context入参为null/Activity context已销毁/Fragment依附的Activity已销毁等情况。

   *W*：预先判断，e.g.

   `if (context != null) { Glide.with(context).. }`

   `if (!activity.isDestroyed()) { Glide.with(context).. }`

   `if (fragment != null && fragment.getActivity() != null) { Glide.with(context).. }`





## RabbitMQ

[RabbitMQ简介][基于RabbitMQ的实时消息推送|IBM Developer][^1]





# LINKS

[^1]:RabbitMQ

[基于RabbitMQ的实时消息推送|IBM Developer]: https://developer.ibm.com/zh/technologies/messaging/articles/os-cn-rabbit-mq/#rabbitmq-简介
[终篇：理解并使用RabbitMQ|简书]: https://www.jianshu.com/p/9a3f9e648743
[RabbitMQ总结|简书]: https://www.jianshu.com/p/af7a05c86364

