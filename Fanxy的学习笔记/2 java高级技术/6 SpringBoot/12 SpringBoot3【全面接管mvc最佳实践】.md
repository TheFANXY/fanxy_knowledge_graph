# 12. SpringBoot3【全面接管mvc最佳实践】

[雷神神级源码讲解，很适合作为复习springboot以及SpringMVC的复习串联讲解视频](https://www.bilibili.com/video/BV1Es4y1q7Bf/?p=49&spm_id_from=pageDriver&vd_source=da8c316450987e3173a62ba5ea9acd61)

>● SpringBoot 默认配置好了 SpringMVC 的所有常用特性。
>● 如果我们需要全面接管SpringMVC的所有配置并 **禁用默认配置**，仅需要编写一个`WebMvcConfigurer`配置类，并标注 `@EnableWebMvc `即可【于配置类标注而并非启动类】
>● 全手动模式
>○ `@EnableWebMvc` : 禁用默认配置
>○ **`WebMvcConfigurer组件`** ：定义MVC的底层行为

## 12.1. WebMvcAutoConfiguration 到底自动配置了哪些规则

> SpringMVC自动配置场景给我们配置了如下所有 **默认行为**

1. `WebMvcAutoConfigurationweb` 场景的自动配置类

   1.1. 支持`RESTful` 的 `filter`：`HiddenHttpMethodFilter`

   1.2. 支持`非POST请求`，请求体携带数据：`FormContentFilter`

   1.3. 导入 **`EnableWebMvcConfiguration`(被1.4的类使用`@Import`导入)** ：

   1.3.1. `RequestMappingHandlerAdapter`

   1.3.2. `WelcomePageHandlerMapping`： **欢迎页功能** 支持（模板引擎目录、静态资源目录放index.html），项目访问 `/` 就默认展示这个页面

   1.3.3. `RequestMappingHandlerMapping`：找每个请求由谁处理的映射关系

   1.3.4. `ExceptionHandlerExceptionResolver`：默认的异常解析器

   1.3.5. `LocaleResolver`：国际化解析器

   1.3.6. `ThemeResolver`：主题解析器

   1.3.7. `FlashMapManager`：临时数据共享

   1.3.8. `FormattingConversionService`： 数据格式化 、类型转化——> 可以在配置文件 `spring.mvc.format.xxxx` 进行配置

   1.3.9. `Validator`： 数据校验`JSR303`提供的数据校验功能

   1.3.10. `WebBindingInitializer`：请求参数的封装与绑定，需要用到上面的格式化器和数据校验器进行参数封装和绑定

   1.3.11. `ContentNegotiationManager`：内容协商管理器

   1.4. **`WebMvcAutoConfigurationAdapter`** 配置生效，它是一个`WebMvcConfigurer`（**实现了接口**），定义mvc底层组件

   1.4.1. 定义好 <font color="bb000">**WebMvcConfigurer** **底层组件默认功能；所有功能详见列表**</font>

   1.4.2. **视图解析器**：`InternalResourceViewResolver`，**可以完成内部跳转的功能**

   1.4.3. **视图解析器**：`BeanNameViewResolver`,**视图名（controller方法的返回值字符串）** 就是组件名，我们自己定义一个视图实现MVC的`View`接口，然后加上`@Controller`注解，然后重写渲染`render`方法，就通过`BeanName`的方式做了一个视图，或者说页面，以前我们还需要利用模板引擎，通过`Thymeleaf`，获取Controller返回的字符串，拼接前后缀。**我们通过上述新方法，可以不需要模板引擎干预自定义逻辑视图，自定义异常白页就是这么做的**

   1.4.4. **内容协商解析器**：`ContentNegotiatingViewResolver`

   1.4.5. **请求上下文过滤器**：`RequestContextFilter`: **任意位置直接获取当前请求（响应也可以获取）**，此前我们如果是`Controller`中，可以直接从形参写`HttpServletRequest request`，通过形参获取当前请求，但是业务层想要去使用，就不能这么获取，需要自己写形参传过去，**每个业务层的每个方法都这么操作很冗余**，我们可以在任意位置通过`RequestContextHolder.getRequestAttributes()`获取请求上下文过滤器内部封装请求和响应的`ServletRequestAttributes`类（**需要强转成这个类，要不然默认获取父类`RequestAttributes`类），然后直接通过这个容器的`get方法`获取请求和响应**

   1.4.6. **静态资源链规则**

   1.4.7. `ProblemDetailsExceptionHandler`：**错误详情，新特性**
     1.4.7.1. **SpringMVC内部场景异常被它捕获：其实就是之前异常处理讲解MVC默认能否处理异常的第三环，不能处理再交给Springboot**
   1.5. 定义了MVC默认的底层行为: `WebMvcConfigurer`


## 12.2. @EnableWebMvc 禁用默认行为

1. `@EnableWebMvc`给容器中导入 `DelegatingWebMvcConfiguration`组件，
       他是 `WebMvcConfigurationSupport`(**实现了这个接口**)
2. `WebMvcAutoConfiguration`有一个核心的条件注解, `@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)`，容器中没有`WebMvcConfigurationSupport`，`WebMvcAutoConfiguration`才生效.
3. `@EnableWebMvc` 导入 `WebMvcConfigurationSupport` 导致 `WebMvcAutoConfiguration` 失效。导致禁用了默认行为

>● `@EnableWebMVC` 禁用了 Mvc的自动配置
>● `WebMvcConfigurer` 定义SpringMVC底层组件的功能类

## 12.3. WebMvcConfigurer 功能

> 定义扩展SpringMVC底层功能

| 提供方法                             | 核心参数                                    | 功能                                                         | 默认                                                         |
| ------------------------------------ | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| addFormatters                        | FormatterRegistry                           | **格式化器**：支持属性上`@NumberFormat`和`@DatetimeFormat`的数据类型转换 | GenericConversionService                                     |
| getValidator                         | 无                                          | **数据校验**：校验 Controller 上使用`@Valid`标注的参数合法性。需要导入`starter-validator` | 无                                                           |
| `  addInterceptors `                 | InterceptorRegistry                         | **拦截器**：拦截收到的所有请求                               | 无                                                           |
| `  configureContentNegotiation     ` | ContentNegotiationConfigurer                | **内容协商**：支持多种数据格式返回。需要配合支持这种类型的`HttpMessageConverter` | 支持 json                                                    |
| `  configureMessageConverters  `     | List`<HttpMessageConverter<?>>  `           | **消息转换器**：标注`@ResponseBody`的返回值会利用`MessageConverter`直接写出去 | 8 个，支持`byte`，`string`, `multipart`, `resource`，`json ` |
| addViewControllers                   | ViewControllerRegistry                      | **视图映射**：直接将请求路径与物理视图映射。用于无 java 业务逻辑的直接视图页渲染 | 无`<mvc:view-controller>   `                                 |
| configureViewResolvers               | ViewResolverRegistry                        | **视图解析器**：逻辑视图转为物理视图                         | ViewResolverComposite                                        |
| addResourceHandlers                  | ResourceHandlerRegistry                     | **静态资源处理**：静态资源路径映射、缓存控制                 | ResourceHandlerRegistry                                      |
| configureDefaultServletHandling      | DefaultServletHandlerConfigurer             | **默认 Servlet**：可以覆盖 Tomcat 的`DefaultServlet`。让`DispatcherServlet`拦截  `/` | 无                                                           |
| configurePathMatch                   | PathMatchConfigurer                         | **路径匹配**：自定义 URL 路径匹配。可以自动为所有路径加上指定前缀，比如` /api  ` ,这就是我们第一节学的，老版路径匹配使用了`ant`匹配规则，[详见第一节](https://www.fanxy.cloud/archives/springboot-7) | 无                                                           |
| `configureAsyncSupport   `           | AsyncSupportConfigurer                      | **异步支持**                                                 | `TaskExecutionAutoConfiguration`                             |
| addCorsMappings                      | CorsRegistry                                | **跨域**                                                     | 无                                                           |
| addArgumentResolvers                 | List `<HandlerMethodArgumentResolver>   `   | **参数解析器**                                               | mvc 默认提供                                                 |
| addReturnValueHandlers               | List `<HandlerMethodReturnValueHandler>   ` | **返回值解析器**                                             | mvc 默认提供                                                 |
| configureHandlerExceptionResolvers   | List `<HandlerExceptionResolver>   `        | **异常处理器**                                               | 默认 3 个 ExceptionHandlerExceptionResolver，ResponseStatusExceptionResolver，DefaultHandlerExceptionResolver |
| getMessageCodesResolver              | 无                                          | **消息码解析器**：国际化使用                                 | 无                                                           |


## 12.4. 最佳实践

> SpringBoot 已经默认配置好了 **Web开发** 场景常用功能。我们直接使用即可。



### 12.4.1. 三种方式

| 方式                                     | 用法                                                         |                             | 效果                                                      |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------- | --------------------------------------------------------- |
| **全自动**                               | 直接编写控制器逻辑                                           |                             | 全部使用 **自动配置默认效果**                             |
| **<font color="#dd000">手自一体</font>** | `@Configuration` + 配置 **`WebMvcConfigurer`** + **`配置 WebMvcRegistrations`** | **`不要标注@EnableWebMvc`** | **保留自动配置效果手动** **设置部分功能** 定义MVC底层组件 |
| **全手动**                               | `@Configuration` + 配置 **`WebMvcConfigurer`**               | **`标注 @EnableWebMvc`**    | **禁用自动配置效果**  **全手动设置**                      |

总结：
**给容器中写一个配置类** `@Configuration` **实现** **`WebMvcConfigurer`接口**，**但是不要标注** **`@EnableWebMvc`注解**，**实现手自一体的效果。**



### 12.4.2. 两种模式 

1、**<font color="#bb000">前后分离模式：</font>** `@RestController` 响应JSON数据
2、**<font color="#bb000">前后不分离模式：</font>**`@Controller` + `Thymeleaf` 模板引擎