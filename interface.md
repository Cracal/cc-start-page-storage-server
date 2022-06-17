# 远程存储API接口文档

需要使用远程存储请按照如下接口文档实现，然后在[CC起始页](https://cracal.cn)中配置APIs接口和认证信息即可使用远程存储。

- 跨域: 需要用户开发的服务器进行跨域
- APIs：
  - 默认的请求方法：
    | 方法                      | 行为         |
    | ------------------------- | ------------ |
    | *<font color=green>GET</font>*    | 获取数据     |
    | *<font color=green>POST</font>*   | 保存数据     |
    | *<font color=green>DELETE</font>* | 删除数据     |
    | *<font color=green>PUT</font>*    | 获取认证信息 |
  - 可以输入多个URL，使用英文“**<font color=green>,</font>**”隔开
  - 可以指定URL的用途：
    - 各用途标识之间除了在标识有值时需要在值的后面用英文“<font color=green>-</font>”表示结束外，其他情况不需要分隔符；和URL的分隔符使用英文“**<font color=green>::</font>**”
    - 用途类型表：

        | 类型标识                                                                                          | 含义                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
        | ------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
        | a                                                                                                 | authorization 表示URL用于获取认证信息,默认的请求方法是<font color=orange>PUT</font>。                                                                                                                                                                                                                                                                                                                                                                                                                   |
        | u<font color=green>[(<\|>\|=\|<=\|>=n<font color=orangered>(Bytes\|KB\|MB\|GB)</font>)]</font>    | u:upload 表示URL用于数据的上传。默认的请求方法是<font color=orange>POST</font><br><、>、=、... 比较符。<br>n:size 数值，表示存储数据大小。<br>Bytes、KB、MB、GB size单位，不区分大小写<br>例如：<br>u<=10KB::url 表示url存储数据小于或等于10KB的数据。                                                                                                                                                                                                                                                  |
        | g                                                                                                 | get 表示URL用于获取数据。默认的请求方法是<font color=orange>GET</font>。                                                                                                                                                                                                                                                                                                                                                                                                                                |
        | G                                                                                                 | 单独指定get 'all-keys'，用于获取存储是所有数据的键名。不指定G时标识了g的URL应该完成该功能，否则切换存储方式时会丢失数据。                                                                                                                                                                                                                                                                                                                                                                       |
        | d                                                                                                 | delete 标识URL用于删除数据。默认的请求方法是<font color=orange>DELETE</font>。                                                                                                                                                                                                                                                                                                                                                                                                                          |
        | D                                                                                                 | 单独指定delete 'all-keys'，用于删除存储的所有数据。不指定时标识了d的URL应该完成该功能，否则无法删除全部数据。                                                                                                                                                                                                                                                                                                                                                                                   |
        | m=<font color=orange>(GET\|POST\|DELETE\|PUT)</font>                                              | method 标识URL请求时使用的方法，为了不与标识关键字冲突，建议只使用大写。<br><font color=green>规定m要紧紧跟在a、u、g或d后面，跟谁就为谁设置请求方法。</font>                                                                                                                                                                                                                                                                                                                                    |
        | p=(j\|x)                                                                                          | parameter type 请求参数类型<br>j:json<br>x或者不指定*p*:x-www-form-urlencoded。<br><font color=pink>规定同m</font>                                                                                                                                                                                                                                                                                                                                                                              |
        | V=(n\|h\|p\|hp)\[authName\]                                                                       | verify 指定URL的<font color=pink>认证方式</font>（请求头或请求参数）和携带认证的<font color=pink>参数名</font>。<br>n:无需认证。<br>h:header 表明认证信息只在请求头中。<br>p:parameter 表明认证信息只在请求参数中。<br>hp 表明认证信息同时存在于请求头和请求参数中。<br>authName 可选，不填写表示使用默认的名称“Authorization”<br><font color=pink>规定同m</font>                                                                                                                                               |
        | k=keyName                                                                                         | 更改默认参数名key为keyName<br><font color=pink>规定同m</font>                                                                                                                                                                                                                                                                                                                                                                                                                                   |
        | v=valueName                                                                                       | 更改默认参数名value为valueName<br><font color=pink>规定同m</font><br>只对上传数据生效。                                                                                                                                                                                                                                                                                                                                                                                                         |
        | r=<font color=yellow>(stringData\|{code=[codeName.successCode].msg=msgName.data=dataName})</font> | response 指定URL响应参数样式。<br>当<font color=yellow>r=stringData</font>时，表明对应的URL请求响应返回的是一个字符串<br>当<font color=yellow>r=\{...\}</font>时，表明对应的URL请求响应返回的是json数据，其中必须指定的是<font color=brown>code字段</font>，其次是<font color=pink>data字段</font><br><font color=pink>codeName</font>指明code字段名称<br><font color=pink>successCode</font>指明请求成功返回的code值<br><font color=pink>msgName</font>指定提示消息字段名称<font color=pink>dataName</font>指定data字段名称<br><font color=pink>规定同m</font> |

    - 标识之间的关系:
      - 所有标识之间都相容
      - 如果只使用一个URL来标明a、u、g、d的功能，那么如果其中一个标识指定了m并且和其他的标识冲突（即url一样并且请求方法也一样了），那么冲突的标识也需要<font color=orangered>指定m</font>或者<font color=orangered>使用另外的URL</font>来消除冲突。a、u、g、d组合的子集同理
      - V、k、v对a没有意义
    - URLs的输入样例：
      - `augd::https://cracal.cn/api/do-all` <font size=1 color=#8e8e8e>表明该URL用于获取认证信息、上传存储数据、获取存储数据、删除存储数据。服务器根据不同的<font size=2 color=yellow>*请求方法*</font>来区分不同的功能</font>
      - `https://cracal.cn/api/do-all` <font size=1 color=#8e8e8e>当只有一个URL时，可以省略标识符。这种情况等价于上面的情况</font>
      - `am=POST-um=PUT-gd::https://cracal.cn/api/do-all` <font size=1 color=#8e8e8e>表明该URL用于获取认证信息、上传存储数据、获取存储数据、删除存储数据。服务器根据不同的<font size=2 color=yellow>*请求方法*</font>来区分不同的功能。<font color=red>在此URL中，希望在<font color=green>$\underline{获取认证信息}$</font>时使用POST,而<font color=green>$\underline{上传存储数据}$</font>默认的请求方法就是POST，两者冲突，所以需要为u也指定m,g和d因为没有冲突，所有不需要指定m</font></font>
      - `a::https://cracal.cn/api/auth`**<font color=red>,</font>** `ugd::https://cracal.cn/api/data` <font size=1 color=#8e8e8e>指定<font color=green>a</font>的URL用于获取操作存储数据时所需的认证信息，指定<font color=green>u,g,d</font>的URL用于操作存储数据的上传、获取和删除</font>
      - `am=POST::https://cracal.cn/api/auth`**<font color=red>,</font>** `ugd::https://cracal.cn/api/data` <font size=1 color=#8e8e8e>指定<font color=green>a</font>的URL用于获取操作存储数据时所需的认证信息，并且标明了该URL使用<font color=orange>POST</font>方法；指定<font color=green>u,g,d</font>的URL用于操作存储数据的上传、获取和删除。虽然a和u都使用POST，但是两者使用不同的URL，不会冲突，因此不需要额外给u指定POST之外的请求方法。<font color=red>注意：使用<font color=orange>","</font>隔开的两个URL被认为是不同的URL，因此“`am=POST::https://cracal.cn/api/save, um=POSTgd::https:://cracal.cn/api/save`”在程序验证上<font color=orange>不认为相同</font>，但是实际上a和u是<font color=yellow>相同的</font>，如果后端服务器没有对两者做额外处理是会出错的，因此这类错误需要用户手动排除</font></font>
      - `ar={code=[myCode.0].data=myData.msg=myMsg}::https://cracal.cn/api/all` <font size=1 color=#8e8e8e>该URL用于获取操作存储数据时所需的认证信息。并且请求返回的json样式如下：</font>

        ```javascript
        {
            myCode: 状态,
            myData: 数据,
            myMsg:  消息
        }
        // myCode如果等于0被认为请求成功
        ```

- 认证:
  - 可以填写多个凭证，用英文“**<font color=green>,</font>**”隔开
  - 不填写标识时系统根据输入顺序确认用途：
    - 只有一个输入时，被认为该认证信息永久生效，在请求数据时请求头<font color=orange>Authorization</font>将会携带该认证信息
    - 有两个输入时，第一个数据被认为是<font color=orange>username</font>，第二个数据被认为是<font color=orange>password</font>。这两个数据不直接应用在请求数据中，而是会被当成APIs中标识a的URL的请求参数，参数类型默认是<font color=yellow>x-www-form-urlencoded</font>；当标识a的URL返回时会携带一个token，请求数据的URL在请求时会使用该token(默认携带在请求头Authorization中)
    - 如果需要输入3个及以上的参数，必须使用标识符
  - 使用标识:
    - 标识列表:

        | 标识      | 含义                                                                                  |
        | --------- | ------------------------------------------------------------------------------------- |
        | k=keyName | key 指定参数名称。<br>keyName可以是你需要的任何合法名称，根据你后端服务器的需要确定。 |

    - 认证使用样例：
      - `xxxxxxxxxxxxxxxxxxxx` 只有一个输入，表示这是一个长期有效的认证,如果APIs中<font color=brown>没有指定a</font>，那么它将会被请求数据（上传、获取和删除）接口在请求时<font color=pink>当做认证凭证一起提交给后端服务器</font>；如果APIs中<font color=yellow>指定了a</font>，那么它<font color=pink>只会被请求认证的接口使用</font>，而请求数据的接口使用的是请求认证的接口返回的token。
      - `xxx, xxx` 两个输入，第一个被认为是<font color=orangered>username</font>,被参数名username携带；第二个被认为是<font color=orangered>password</font>，被参数名password携带。如果APIs中<font color=brown>没有指定a</font>，这些输入不会被分割成username和password，而是一起被当成一个认证凭证，这个错误要手动排除；如果APIs中<font color=yellow>指定了a</font>，那么这些输入会被当成请求认证接口的请求参数提交给后端服务器。
      - `k=id::xxx, k=pass::xxx` 同上，只是参数名username被改成id，参数名password被改成pass。
      - `k=id::xxx, k=pass::xxx, k=extraData::xxx` 同上，但在请求时会额外增加一个名为extraData的参数

## 默认接口文档

以下是没有特别定制的默认情况下的接口文档。可根据需要进行按照上述所说进行修改。

### 1. 保存数据
  
- 请求地址: 用户自定义
- 请求方法: POST
- 请求头:

| 参数名        | 参数类型 | 描述                              |
| ------------- | -------- | --------------------------------- |
| Content-Type  | string   | application/x-www-form-urlencoded |
| Authorization | string   | 认证信息                          |

- 请求参数:

| 参数名 | 参数类型 | 描述 |
| ------ | -------- | ---- |
| key    | string   | 键名 |
| value  | string   | 值   |

- 响应参数:

| 参数名 | 参数类型 | 描述          |
| ------ | -------- | ------------- |
| code   | int      | 成功时返回200 |
| msg    | string   | 描述信息      |
| data   | object   | 忽略          |

### 2. 获取数据
  
- 请求地址: 用户自定义
- 请求方法: GET
- 请求头:

| 参数名        | 参数类型 | 描述                              |
| ------------- | -------- | --------------------------------- |
| Content-Type  | string   | application/x-www-form-urlencoded |
| Authorization | string   | 认证信息                          |

- 请求参数:

| 参数名 | 参数类型 | 描述                                                       |
| ------ | -------- | ---------------------------------------------------------- |
| key    | string   | 键名,<br>当key=='all-keys'时请返回包含所有键名的字符串数组 |

- 响应参数:

| 参数名 | 参数类型           | 描述                                                                                   |
| ------ | ------------------ | -------------------------------------------------------------------------------------- |
| code   | int                | 成功时返回200                                                                          |
| msg    | string             | 描述信息                                                                               |
| data   | string \| string[] | 根据请求参数key获取的对应的value值,<br>当key=='all-keys'时返回包含所有键名的字符串数组 |

### 3. 删除数据
  
- 请求地址: 用户自定义
- 请求方法: DELETE
- 请求头:

| 参数名        | 参数类型 | 描述                              |
| ------------- | -------- | --------------------------------- |
| Content-Type  | string   | application/x-www-form-urlencoded |
| Authorization | string   | 认证信息                          |

- 请求参数:

| 参数名 | 参数类型 | 描述                                       |
| ------ | -------- | ------------------------------------------ |
| key    | string   | 键名,<br>当key=='all-keys'时请删除所有数据 |

- 响应参数:

| 参数名 | 参数类型 | 描述          |
| ------ | -------- | ------------- |
| code   | int      | 成功时返回200 |
| msg    | string   | 描述信息      |
| data   | object   | 忽略          |

### 4. 请求认证
  
- 请求地址: 用户自定义
- 请求方法: PUT
- 请求头:

| 参数名       | 参数类型 | 描述                              |
| ------------ | -------- | --------------------------------- |
| Content-Type | string   | application/x-www-form-urlencoded |

- 请求参数:

| 参数名 | 参数类型 | 描述                                             |
| ------ | -------- | ------------------------------------------------ |
| token  | string   | 验证信息，服务器根据这个参数决定是否返回认证信息 |

- 响应参数:

| 参数名 | 参数类型 | 描述          |
| ------ | -------- | ------------- |
| code   | int      | 成功时返回200 |
| msg    | string   | 描述信息      |
| data   | string   | 认证信息      |

### 以下是使用Spring Boot实现的简单默认接口示例

为了方便，所有代码都写在同一个类中。

```java
@RestController
@RequestMapping("/api/save")
public class CCSaveController {
    private Map<String, String> data = new HashMap<>();
    private String auth;

    @PostMapping
    public Result<Boolean> postData(@RequestParam String key, @RequestParam String value, HttpServletRequest request) {
        if (check(request.getHeader("Authorization"))) {
            data.put(key, value);
            return Result.success(200, "成功", true);
        }
        return Result.failed();
    }

    @GetMapping
    public Result<Object> getData(@RequestParam(value = "key") String key, HttpServletRequest request) {
        if (check(request.getHeader("Authorization"))) {
            Object value = null;
            if ("all-keys".equalsIgnoreCase(key))
                value = data.keySet().toArray(new String[data.keySet().size()]);
            else
                value = data.get(key);
            return Result.success(200, "成功", value);
        }
        return Result.failed();
    }

    @DeleteMapping
    public Result<Boolean> delData(@RequestParam(value = "key") String key, HttpServletRequest request) {
        if (check(request.getHeader("Authorization"))) {
            if ("all-keys".equalsIgnoreCase(key))
                data.clear();
            else
                data.remove(key);
            return Result.success(200, "成功", true);
        }
        return Result.failed();
    }

    @PutMapping
    public Result<String> getToken(@RequestParam(required = false) String token) {
        if ("ok".equals(token)){
            this.auth = UUID.randomUUID().toString();
            return Result.success(200, "成功", this.auth);
        }else return Result.failed();
    }

    private boolean check(String token) {
        // 编写验证逻辑
        if (this.auth != null)
            return this.auth.equals(token);
        return false;
    }
}
```

示例对应的配置:

![Result类部分实现](https://cracal.cn/d/img/conf1.png)

## 指定了用途标识的接口文档示例

为了能够演示用到更多的用途标识符，接口故意写的比较奇怪。

### a. 保存数据
  
- 请求地址: 用户自定义
- 请求方法: POST
- 请求头:

| 参数名       | 参数类型 | 描述             |
| ------------ | -------- | ---------------- |
| Content-Type | string   | application/json |
| Token        | string   | 认证信息         |

- 请求参数:

| 参数名 | 参数类型 | 描述 |
| ------ | -------- | ---- |
| key    | string   | 键名 |
| value  | string   | 值   |

- 响应参数:

| 参数名 | 参数类型 | 描述          |
| ------ | -------- | ------------- |
| code   | int      | 成功时返回200 |
| msg    | string   | 描述信息      |
| data   | object   | 忽略          |

### b. 获取数据
  
- 请求地址: 用户自定义
- 请求方法: GET
- 请求头:

| 参数名       | 参数类型 | 描述                              |
| ------------ | -------- | --------------------------------- |
| Content-Type | string   | application/x-www-form-urlencoded |
| Token        | string   | 认证信息                          |

- 请求参数:

| 参数名 | 参数类型 | 描述 |
| ------ | -------- | ---- |
| name   | string   | 键名 |

- 响应参数:
  - string data 直接返回value

### c. 获取键名数组
  
- 请求地址: 用户自定义
- 请求方法: POST
- 请求头:

| 参数名       | 参数类型 | 描述                              |
| ------------ | -------- | --------------------------------- |
| Content-Type | string   | application/x-www-form-urlencoded |
| Token        | string   | 认证信息                          |

- 请求参数:

| 参数名 | 参数类型 | 描述                                             |
| ------ | -------- | ------------------------------------------------ |
| name   | string   | 键名(对于获取键名数组来说，name永远为'all-keys') |

- 响应参数:

| 参数名 | 参数类型 | 描述          |
| ------ | -------- | ------------- |
| code   | int      | 成功时返回200 |
| msg    | string   | 描述信息      |
| data   | string[] | 键名数组      |

### d. 删除数据
  
- 请求地址: 用户自定义
- 请求方法: DELETE
- 请求头:

| 参数名       | 参数类型 | 描述                              |
| ------------ | -------- | --------------------------------- |
| Content-Type | string   | application/x-www-form-urlencoded |
| Token        | string   | 认证信息                          |

- 请求参数:

| 参数名 | 参数类型 | 描述                                       |
| ------ | -------- | ------------------------------------------ |
| key    | string   | 键名,<br>当key=='all-keys'时请删除所有数据 |

- 响应参数:

| 参数名 | 参数类型 | 描述          |
| ------ | -------- | ------------- |
| code   | int      | 成功时返回200 |
| msg    | string   | 描述信息      |
| data   | object   | 忽略          |

### e. 请求认证
  
- 请求地址: 用户自定义
- 请求方法: PUT
- 请求头:

| 参数名       | 参数类型 | 描述             |
| ------------ | -------- | ---------------- |
| Content-Type | string   | application/json |

- 请求参数:

| 参数名   | 参数类型 | 描述             |
| -------- | -------- | ---------------- |
| username | string   | 验证信息1,用户名 |
| password | string   | 验证信息2,密码   |

- 响应参数:

| 参数名   | 参数类型 | 描述          |
| -------- | -------- | ------------- |
| authCode | int      | 成功时返回100 |
| message  | string   | 描述信息      |
| token    | string   | 认证信息      |

### 代码实现

```java
@RestController
@RequestMapping("/api/save")
public class CCSaveController {
    private Map<String, String> data = new HashMap<>();
    private String auth;

    @PostMapping
    public Result<Boolean> postData(@RequestBody Map<String, String> kv, HttpServletRequest request) {
        if (check(request.getHeader("Token"))) {
            String k = kv.get("key");
            String v = kv.get("value");
            if (k != null) {
                data.put(k, v);
                return Result.success(200, "成功", true);
            }
        }
        return Result.failed();
    }

    @GetMapping
    public String getData(@RequestParam String name, HttpServletRequest request) {
        if (check(request.getHeader("Token")))
            return data.get(name);
        return null;
    }

    @PostMapping("/get-keys")
    public Result<String[]> getKeys(HttpServletRequest request) {
        if (check(request.getHeader("Token"))) {
            String[] value = data.keySet().toArray(new String[data.keySet().size()]);
            return Result.success(200, "成功", value);
        }
        return Result.failed();
    }

    @DeleteMapping
    public Result<Boolean> delData(@RequestParam String key, HttpServletRequest request) {
        if (check(request.getHeader("Token"))) {
            if ("all-keys".equalsIgnoreCase(key))
                data.clear();
            else
                data.remove(key);
            return Result.success(200, "成功", true);
        }
        return Result.failed();
    }

    @PutMapping
    public Map<String, Object> getToken(@RequestBody Map<String, String> params) {
        String username = params.get("username");
        String password = params.get("password");
        Map<String, Object> res = new HashMap<>();
        if ("user1".equals(username) && "password1".equals(password)) {
            this.auth = UUID.randomUUID().toString();
            res.put("authCode", 100);
            res.put("message", "请求认证成功");
            res.put("token", this.auth);
            return res;
        } else {
            res.put("authCode", 101);
            res.put("message", "请求认证失败");
            return res;
        }
    }

    private boolean check(String token) {
        // 编写验证逻辑
        if (this.auth != null)
            return this.auth.equals(token);
        return false;
    }
}
```

使用的配置信息如下:

> APIs: `aV=hToken-p=j-r={code=[authCode.100].msg=message.data=token}-uV=hToken-p=j-gV=hToken-k=name-r=stringData-dV=hToken::http://localhost:8080/api/save, GV=hToken-m=POST::http://localhost:8080/api/save/get-keys`
>
>
>认证：user1, password1

## 其他代码

跨越配置

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    ...
    // 上面两个例子用到的跨越配置
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowCredentials(true)
                // 第一个例子和第二个例子分别使用到的携带认证信息的请求头需要在这里允许
                .allowedHeaders("Authorization", "Token", "Origin", "X-Requested-With", "Content-Type",
                        "Accept")
                // 用到的请求方法需要允许
                .allowedMethods("OPTION", "GET", "POST", "PUT", "DELETE")
                .allowedOrigins("*");
    }
    ...
}
```

示例中使用到的Result类部分代码

```java
@Data
public class Result<E> {
    private Integer code;
    private String msg;
    private E data;
    public Result(Integer code, String msg, E data){
        this.code = code;
        this.msg = msg;
        this.data = data;
    }

    public static <E> Result<E> success(Integer code, String msg, E data){
        return new Result<E>(code, msg, data);
    }

    public static <E> Result<E> success(E data){
        return success(200, "成功", data);
    }

    public static <E> Result<E> success(){
        return success(null);
    }
    ...
}
```

为了传输大数据（比如传输一张10MB的图片），需要服务器进行配置，如下是Spring Boot(tomcat)的配置示例。

```yaml
server:
   tomcat:
      max-http-form-post-size: -1 # -1表示没有限制
```
