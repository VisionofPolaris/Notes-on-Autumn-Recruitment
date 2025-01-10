# Spring框架（SpringSecurity-SpringCache）

# SpringSecurity

*spring体系中提供安全的框架。*

## 认证与授权

### 什么是认证和授权？

1. 认证是为了保证身份的合法性，是让系统知道我们是谁，如密码登录、指纹登录等；
2. 授权是用户认证通过之后根据用户的权限来控制用户访问资源的过程。

### 常见的认证方式有哪些？

1. Cookie-Session机制：
    1. 服务端创建session记录用户登录的信息，将sessionID存到cookie；
    2. 客户端再次发起请求，带上cookie信息，由服务端验证。
    
    弊端：
    
    1. 只能用在web场景下，不能用在APP中；
    2. 存在窃取cookie伪造请求的安全风险；
    3. 是有状态方式，后台保存用户信息，内存开销大；
    4. cookie存在跨域问题（ip、端口号、协议需要一致）。
2. JWT令牌无状态认证：
    1. 服务端认证后用算法生成票据返回客户端；
    2. 客户端再次发起请求，携带票据；
    3. 服务端用相同的算法进行验证。
    
    优点：
    
    1. 用json作为数据传输，体积小便于传输；
    2. 不需要在服务器端保存相关信息，节省资源开销；
    3. jwt载荷部分可以存储非敏感的业务信息。

### RBAC权限模型？

RBAC指基于角色的权限控制，再授权过程中，需要知道如何对用户访问的资源进行控制。RBAC模型包括用户、角色、权限和资源。

![image-20210116114510176.png](Spring%E6%A1%86%E6%9E%B6%EF%BC%88SpringSecurity-SpringCache%EF%BC%89%209ddc193070a047b68b02976459f11060/image-20210116114510176.png)

## JWT

### JWT是什么？

- 定义：jwt（json web token）是为了在网络环境中传递声明而执行的一种基于json的开放标准。
- 作用：可以在用户和服务器之间传递安全可靠的信息。
- 特点：紧凑且安全的，适用于前后端无状态的应用场景。

### JWT的组成？

完整的JWT由下面三个部分加.连接得到。

1. 头部header
    1. 描述基本的信息，如数据类型、签名算法等。`{"typ":"JWT","alg":"HS256"}` 
    2. 有BASE64编码处理。
2. 载荷playload
    1. 存放非敏感的有效信息。`{"sub":"1234567890","name":"John Doe","admin":true}`
    2. 由BASE64编码处理。
3. 签证signature
    1. 由三个部分组成：header+playload+secret key。
    2. 再由签名算法加密。

### JWT的使用步骤？

1. 工程引入JWT依赖
    
    ```
    <dependencies>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.9.1</version>
        </dependency>
    </dependencies>
    ```
    
2. **生**成JWT令牌
    
    ```java
    @Test
    public void testGenerate(){
        String compact = Jwts.builder()
            .setId(UUID.randomUUID().toString())//设置唯一标识
            .setSubject("JRZS")//设置主题
            .claim("name", "nineclock")//自定义信息
            .claim("age", 88)//自定义信息
            .setExpiration(new Date())//设置过期时间
            .setIssuedAt(new Date())//令牌签发时间
            .signWith(SignatureAlgorithm.HS256, "itheima")//签名算法, 秘钥
            .compact();
        System.out.println(compact);
    }
    ```
    
3. JWT令牌校验
    
    ```java
    @Test
    public void testVerify(){
        String jwt = "eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI5MzljNjU4MC0yMTQyLTRlOWEtYjcxOC0yNzlmNzRhODVmNDMiLCJzdWIiOiJOSU5FQ0xPQ0siLCJuYW1lIjoibmluZWNsb2NrIiwiYWdlIjo4OCwiaWF0IjoxNjE3MDMxMjUxfQ.J-4kjEgyn-Gkh0ZuivUCevrzDXt0K9bAyF76rn1BfUs";
        Claims claims = Jwts.parser().setSigningKey("itheima").parseClaimsJws(jwt).getBody();
        System.out.println(claims);
    }
    ```
    
    当我们对令牌进行任何部分(header , payload , signature)任何部分进行篡改, 都会造成令牌解析失败 。
    

### 前端保存token的方案有哪些？/JWT存放在哪里？

- LocalStorage：浏览器关了还在本地
- SessionStorage：浏览器关了就没了
- cookie

## SpringSecurity

### SpringSecurity的注解有哪些？

```java
@EnableGlobalMethodSecurity(prePostEnabled = true) //开启SpringSecurity相关注解支持
@PermitAll
@PreAuthorize("hasRole('ADMIN')")
@PreAuthorize("hasAuthority('P5')")
```

### 加密算法有哪些？

可逆的加密算法，可以从密文获取明文：

1. 对称密钥加密：加密解密使用相同的密钥，如DES、AES。
    1. 优点：计算量小，加密效率高
    2. 缺点：安全性较差
2. 非对称密钥加密：公钥加密、私钥解密，如RSA。
    1. 优点：安全性好
    2. 缺点：加密解密花费时间长、速度慢，适用于少量数据

不可逆的加密算法：

1. MD5
2. Bcrypt：加密时在密码中混淆盐值，解密时根据明文密码从密文密码中截取盐值，将盐值与明文密码再加密一次，如果得到的结果与密文密码一样，则认证成功。

### SpringSecurity的认证过程？

SpringSecurity是一个过滤器链，内部包含了各种功能的过滤器。关键的过滤器包括：

- UsernamePasswordAuthenticationFilter用于处理来自表单提交的认证。该表单必须提供对应的用户名和密码，其内部还有登录成功或失败后进行处理的AuthenticationSuccessHandler 和 AuthenticationFailureHandler，这些都可以根据需求做相关改变；
- ExceptionTranslationFilter 能够捕获来自 FilterChain 所有的异常，并进行处理。但是它只会处理两类异常： AuthenticationException 和 AccessDeniedException，其它的异常它会继续抛出；
- FilterSecurityInterceptor负责权限校验的过滤器，使用AccessDecisionManager对当前用户进行授权访问；

![RECTIFY_IMG_20240605_234049.jpg](Spring%E6%A1%86%E6%9E%B6%EF%BC%88SpringSecurity-SpringCache%EF%BC%89%209ddc193070a047b68b02976459f11060/RECTIFY_IMG_20240605_234049.jpg)

- 认证过滤器(UsernamePasswordAuthentionFilter)接收form表单提交的账户、密码信息，并封装成UsernamePasswordAuthenticationToken认证凭对象；
- 认证过滤器调用认证管理器AuthenticationManager进行认证处理；
- 认证管理器通过调用用户详情服务获取用户详情UserDetails；
- 认证管理器通过密码匹配器PasswordEncoder进行匹配，如果密码一致，则将用户相关的权限信息一并封装到Authentication认证对象中；
- 认证过滤器将Authentication认证过滤器放到认证上下文，方便请求从上下文获取认证信息；

# SpringCache

## 环境配置

## 注解

### @Cacheable

- 如果缓存中没有：查询数据库，存储缓存，返回结果；
- 如果缓存中有：直接返回结果。

![aHR0cDovL3d4My5zaW5haW1nLmNuL2xhcmdlLzAwNmVPcVY5bHkxZzFnOXMzMnc5aWozMG9pMDh5Z21rLmpwZw.jpg](Spring%E6%A1%86%E6%9E%B6%EF%BC%88SpringSecurity-SpringCache%EF%BC%89%209ddc193070a047b68b02976459f11060/aHR0cDovL3d4My5zaW5haW1nLmNuL2xhcmdlLzAwNmVPcVY5bHkxZzFnOXMzMnc5aWozMG9pMDh5Z21rLmpwZw.jpg)

`value`也可以用`cacheNames`。

### @CacheEvict

删除缓存的注解。

![aHR0cDovL3d4My5zaW5haW1nLmNuL2xhcmdlLzAwNmVPcVY5bHkxZzFnOXM1bzF5Z2ozMG9vMGUzNDA2LmpwZw.jpg](Spring%E6%A1%86%E6%9E%B6%EF%BC%88SpringSecurity-SpringCache%EF%BC%89%209ddc193070a047b68b02976459f11060/aHR0cDovL3d4My5zaW5haW1nLmNuL2xhcmdlLzAwNmVPcVY5bHkxZzFnOXM1bzF5Z2ozMG9vMGUzNDA2LmpwZw.jpg)

### @CachePut

缓存更新，如果存在对应的缓存，则更新覆盖，不存在则添加。

![aHR0cDovL3d4NC5zaW5haW1nLmNuL2xhcmdlLzAwNmVPcVY5bHkxZzFnOXMwazFnMmozMG9kMDhvdDlrLmpwZw.jpg](Spring%E6%A1%86%E6%9E%B6%EF%BC%88SpringSecurity-SpringCache%EF%BC%89%209ddc193070a047b68b02976459f11060/aHR0cDovL3d4NC5zaW5haW1nLmNuL2xhcmdlLzAwNmVPcVY5bHkxZzFnOXMwazFnMmozMG9kMDhvdDlrLmpwZw.jpg)

### @Caching

重新组合要应用于方法的多个缓存操作。

```java
@Caching(
        cacheable = {
                @Cacheable(key = "#role.rolename")
        },
        put = {
                @CachePut(key = "#role.id"),
                @CachePut(key = "#role.rolecode")
        },
        evict = {
                @CacheEvict(key = "8")
        }
)
```