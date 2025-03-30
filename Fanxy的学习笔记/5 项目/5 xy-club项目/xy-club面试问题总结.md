# 1. 登录鉴权大致流程【Gateway的结合 以及和微信的结合】

## 1.2. 框架和流程图

权限认证框架 - `satoken`

satoken 十分轻量，功能齐全，学习成本低。可以快速接入。另一方面就是复杂性考虑：Sa-Token 是一个轻量级 Java 权限认证框架，主要解决：登录认证、权限认证、单点登录、`OAuth2.0`、分布式Session会话、微服务网关鉴权 等一系列权限相关问题。正好符合我们的微服务分布式项目的场景。

无需实现任何接口，无需创建任何配置文件，只需要一句静态代码的调用，便可以完成会话登录认证。

在`app`、小程序等前后端分离场景中，一般是没有 Cookie 这一功能的。所以我们就引入 token 的这个概念。拆解出来主要是两步，一步是登录后，后端返回 token，另一部是前端请求带着 token，将 token 放到 header 里面。实现了 token 的传递之后，token 的生成过程，可以包含各种信息，比如用户的用户名，相关的权限，都可以包含在里面，这样一个 token 就可以帮助我们带来很多信息，鉴权等功能也就非常容易做了，同时还解决了 cookie 问题。

gateway 的全局异常处理主要需要我们实现一个接口`ErrorWebExceptionHandler`，实现其中的 handle 方法，在方法内，我们能获取到其中 request 与 response，webhandler 会帮助我们拦住所有异常的情况。然后我们可以在里面做拦截的进一步处理，更改状态码，状态错误信息等等。最后通过 response 可以将其返回出去。

![image.png](https://cdn.nlark.com/yuque/0/2024/png/29413969/1707878867889-2fd212f5-4932-49c6-8361-1568dbec3755.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_28%2Ctext_57uP5YW46bih57-F%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fformat%2Cwebp)

## 1.2. 权限和角色与用户绑定在redis

`auth模块`的`UserController`的注册方法添加了对相同用户名用户注册的检测，以及后续的放入 `redis`  具体当前用户角色对应的所有权限列表，默认会把用户的权限列表和角色列表都存入 `redis` ，使用 `String数据结构` key 为 `PREFIX + user_name` 其中 `user_name` 是对接微信小程序获得的 `open_id`。

```java
@Component
public class StpInterfaceImpl implements StpInterface {

    @Resource
    private RedisUtil redisUtil;

    // 权限前缀
    private static final String AUTH_PERMISSION_PREFIX = "auth.permission";
    // 权限角色前缀
    private static final String AUTH_ROLE_PREFIX = "auth.role";

    @Override
    public List<String> getPermissionList(Object loginId, String loginType) {
        // 返回此 loginId 拥有的权限列表
        return getAuth(loginId.toString(), AUTH_PERMISSION_PREFIX);
    }

    @Override
    public List<String> getRoleList(Object loginId, String loginType) {
        // 返回此 loginId 拥有的角色列表
        return getAuth(loginId.toString(), AUTH_ROLE_PREFIX);
    }
    private List<String> getAuth(String loginId, String prefix) {
        String authKey = redisUtil.buildKey(prefix, loginId.toString());
        String authValue = redisUtil.get(authKey);
        if (StringUtils.isBlank(authValue)) {
            return Collections.emptyList();
        }
        List<String> authList = new LinkedList<>();
        if (AUTH_ROLE_PREFIX.equals(prefix)) {
            List<AuthRole> roleList =
                    new Gson().fromJson(authValue, new TypeToken<List<AuthRole>>(){}.getType());
            authList = roleList.stream().map(AuthRole::getRoleKey).collect(Collectors.toList());
        } else if (AUTH_PERMISSION_PREFIX.equals(prefix)) {
            List<AuthPermission> permissionList =
                    new Gson().fromJson(authValue, new TypeToken<List<AuthPermission>>(){}.getType());
            authList = permissionList.stream().map(AuthPermission::getPermissionKey).collect(Collectors.toList());
        }
        return authList;
    }
}
```

对接公众号主要是希望用某新的唯一的 `openId`，来作为唯一的标识，也方便用户的登录。

整体流程主要是用户扫公众号码。然后发一条消息：验证码。我们通过 `api` 回复一个随机的码。存入 `redis`

`redis` 的主要结构，就是 `openId` 加验证码。用户在验证码框输入之后，点击登录，进入我们的注册模块，同时关联角色和权限。就实现了网关的统一鉴权。用户就可以进行操作，用户可以根据个人的 `openId` 来维护个人信息。用户登录成功之后，返回 token，前端的所有请求都带着 token 就可以访问。

从服务涉及上，我们开一个新服务，专门用于对接某信的 `api` 和微信的消息的回调。

## 1.3. 公众号的事件处理

工厂+策略模式，创建工厂类，事件处理`Handler`类，不同事件的枚举类【可以通过`事件的类型字符串` 获取对应枚举的`value`值的静态方法，为了方便工厂类通过`@Resource` 注入的`List<WxChatMsgHandler> wxChatMsgHandlerList` 的 **`实现类列表`**，通过实现 `InitializingBean` 重写的 `afterPropertiesSet` 方法注入内部的哈希表，其中 `key` 就是消息类型的字符串值，`value` 就是对应的实现类，然后就能在 `Controller` 直接根据多态直接对消息处理】 

```java
@Component
@Slf4j
public class ReceiveTextMsgHandler implements WxChatMsgHandler {

    private static final String KEY_WORD = "验证码";

    private static final String LOGIN_PREFIX = "loginCode";

    @Resource
    private RedisUtil redisUtil;

    @Override
    public WxChatMsgTypeEnum getMsgType() {
        return WxChatMsgTypeEnum.TEXT_MSG;
    }

    @Override
    public String dealMsg(Map<String, String> messageMap) {
        log.info("接收到文本消息事件");
        String content = messageMap.get("Content");
        if (!KEY_WORD.equals(content)) {
            return "";
        }
        String fromUserName = messageMap.get("FromUserName");
        String toUserName = messageMap.get("ToUserName");

        Random random = new Random();
        int num = random.nextInt(1000);
        String numKey = redisUtil.buildKey(LOGIN_PREFIX, String.valueOf(num));
        redisUtil.setNx(numKey, fromUserName, 5L, TimeUnit.MINUTES);
        String numContent = "您当前的验证码是：" + num + "！ 5分钟内有效";
        String replyContent = "<xml>\n" +
                "  <ToUserName><![CDATA[" + fromUserName + "]]></ToUserName>\n" +
                "  <FromUserName><![CDATA[" + toUserName + "]]></FromUserName>\n" +
                "  <CreateTime>12345678</CreateTime>\n" +
                "  <MsgType><![CDATA[text]]></MsgType>\n" +
                "  <Content><![CDATA[" + numContent + "]]></Content>\n" +
                "</xml>";

        return replyContent;
    }
}
```



## 1.4. 打通登录接口

`auth模块的userController` 创建接口，需要传入验证码。

```java
    /**
     * 用户登录
     */
    @RequestMapping("doLogin")
    public Result<SaTokenInfo> doLogin(@RequestParam("validCode") String validCode) {
        try {
            Preconditions.checkArgument(!StringUtils.isBlank(validCode), "验证码不能为空!");
            return Result.ok(authUserDomainService.doLogin(validCode));
        } catch (Exception e) {
            log.error("UserController.doLogin.error:{}", e.getMessage(), e);
            return Result.fail("用户登录失败");
        }
    }
```

`authUserDomainService 具体实现方法` 

```java
    // 登录账号前缀
    private static final String LOGIN_PREFIX = "loginCode";

	@Override
    public SaTokenInfo doLogin(String validCode) {
        String loginKey = redisUtil.buildKey(LOGIN_PREFIX, validCode);
        String openId = redisUtil.get(loginKey);
        if (StringUtils.isBlank(openId)) {
            return null;
        }
        AuthUserBO authUserBO = new AuthUserBO();
        authUserBO.setUserName(openId);
        if(!this.register(authUserBO)) {
            throw new RunTimeException("登录失败!");
        }
        StpUtil.login(openId);
        SaTokenInfo tokenInfo = StpUtil.getTokenInfo();
        return tokenInfo;
    }
```



# 2. 你是如何监听用户发给公众号的消息的？

主要是对接公众号的回调消息平台配置。重点主要分为三步：填写服务器配置，验证服务器地址的有效性，依据接口文档实现业务逻辑。

在公众号配置界面填写服务器地址（`URL`）、`Token`和 `EncodingAESKey`，其中URL是开发者用来接收微信消息和事件的接口URL。Token可由开发者可以任意填写，用作生成签名（该Token会和接口URL中包含的Token进行比对，从而验证安全性）。EncodingAESKey由开发者手动填写或随机生成，将用作消息体加解密密钥。

![image-20240905223255379](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\5 项目\5 xy-club项目\xy-club面试问题总结.assets\image-20240905223255379.png)

验证消息的确来自微信服务器：某信服务器将发送GET请求到填写的服务器地址URL上，GET请求携带参数有`签名`，`随机数`，`时间戳` ，`随机字符串`之类的，后台服务要通过一样的加密形式来进行校验。

依据接口文档实现业务逻辑：验证URL有效性成功后即接入生效，用户每次向公众号发送消息，开发者填写的服务器配置URL将得到微信服务器推送过来的消息和事件，开发者可以依据自身业务逻辑进行响应，如回复消息。

这部分需要自己通过`java` 实现，官网只给了 `PHP实例` 。

# 3. 回调消息的验证校验是如何做的？

开发者通过检验`signature`对请求进行校验。若确认此次GET请求来自微信服务器，请原样返回`echostr`参数内容，则接入生效，成为开发者成功，否则接入失败。加密/校验流程如下：

1）将token、timestamp、nonce三个参数进行字典序排序

2）将三个参数字符串拼接成一个字符串进行`sha1`加密

3）开发者获得加密后的字符串可与signature对比，标识该请求来源于微信

# 4. 