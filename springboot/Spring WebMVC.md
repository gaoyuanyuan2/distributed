### Spring Web MVC

#### 主要内容
<br>Spring Web MVC介绍:整体介绍Spring Web MVC框架设计思想、功能特性、以及插播式实现
<br>Spring Web MVC实战:详细说明DispatcherServlet、@Controller 和@RequestMapping的基本原理、@RequestParam 、@RequestBody 和@ResponseBody使用方式、以及它们之间关系
<br>映射处理:介绍DispatcherServlet与RequestMappingHandlerMapping之间的交互原理，HandlerInterceptor 的职责以及使用
<br>异常处理:介绍DispatcherServlet中执行过程中,如何优雅并且高效地处理异常的逻辑,如归类处理以及提供友好的交互界面等Thymeleaf
<br>视图技术:介绍新-代视图技术Thymeleaf ,包括其使用场景、实际应用以及技术优势
<br>视图解析:介绍Spring Web MVC视图解析的过程和原理、以及内容协调视图处理器的使用场景
<br>国际化:利用Locale技术,实现视图内容的国际化

#### MVC
<br>M : Model
<br>V : View
<br>C : Controller -> DispatcherServlet
<br>Front Controller = DispatcherServlet
<br>Application Controller = @Controller or Controller
<br>ServletContextListener -> ContextLoaderListener -> Root WebApplicationContext
<br>DispatcherServlet -> Servlet WebApplicationContext
<br>Services => @Service
<br>Repositories => @Repository
												  
####  请求映射
<br>Servlet / 和 /*
<br>ServletContext path = /servlet-demo
<br>URI : /servlet-demo/IndexServlet
<br>DispatcherServlet < FrameworkServlet < HttpServletBean < HttpServlet
<br>自动装配 : org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration
<br>ServletContext path = "" or "/"
<br>Request URI = ServletContex path + @RequestMapping("")/ @GetMapping()
<br>当前例子：
<br>Request URI = "" + "" = "" -> RestDemoController#index()
<br>Request URI : "" 或者 "/"
<br>HandlerMapping ，寻找Request URI，匹配的 Handler ：
	<br>Handler：处理的方法，当然这是一种实例
	<br>整体流程：Request -> Handler -> 执行结果 -> 返回（REST） -> 普通的文本	
	<br>请求处理映射：RequestMappingHandlerMapping -> @RequestMapping Handler Mapping	
	<br>拦截器：HandlerInterceptor 可以理解 Handler 到底是什么
			<br>处理顺序：preHandle(true) -> Handler: HandlerMethod 执行(Method#invoke) -> postHandle -> afterCompletion
					  preHandle(false)
<br><br>Spring Web MVC 的配置 Bean ：WebMvcProperties
<br>Spring Boot 允许通过 application.properties 去定义一下配置，配置外部化
<br>WebMvcProperties 配置前缀：spring.mvc
<br>spring.mvc.servlet 

#### 异常处理
<br>1. 传统的Servlet web.xml 错误页面
* 优点：统一处理，业界标准
* 不足：灵活度不够，只能定义 web.xml文件里面
<br><error-page> 处理逻辑：
 * 处理状态码 <error-code>
 * 处理异常类型 <exception-type>
 * 处理服务：<location>
<br><br>2. Spring Web MVC 异常处理
 * @ExceptionHandler
    * 优点：易于理解，尤其是全局异常处理
    * 不足：很难完全掌握所有的异常类型
 * @RestControllerAdvice = @ControllerAdvice + @ResponseBody
 * @ControllerAdvice 专门拦截（AOP） @Controller
<br><br>3.  Spring Boot 错误处理页面
 * 实现 ErrorPageRegistrar
    * 状态码：比较通用，不需要理解Spring WebMVC 异常体系
    * 不足：页面处理的路径必须固定
 * 注册 ErrorPage 对象
 * 实现 ErrorPage 对象中的Path 路径Web服务

#### 视图技术
<br>View
<br><br>1. render 方法
<br>处理页面渲染的逻辑，例如：Velocity、JSP、Thymeleaf
<br><br>2. ViewResolver
<br>View Resolver = 页面 + 解析器 -> resolveViewName 寻找合适/对应 View 对象
<br>RequestURI-> RequestMappingHandlerMapping ->
<br>HandleMethod -> return "viewName" ->
<br>完整的页面名称 = prefix + "viewName" + suffix 
-> ViewResolver -> View -> render -> HTML
<br>Spring Boot 解析完整的页面路径：
<br>spring.view.prefix + HandlerMethod return + spring.view.suffix
<br><br>3. ContentNegotiationViewResolver
<br>用于处理多个ViewResolver：JSP、Velocity、Thymeleaf
<br>当所有的ViewResover 配置完成时，他们的order 默认值一样，所以先来先服务（List）
<br>当他们定义自己的order，通过order 来倒序排列
<br><br>4.  Thymeleaf
<br>自动装配类：ThymeleafAutoConfiguration
<br>配置项前缀：spring.thymeleaf
<br>模板寻找前缀：spring.thymeleaf.prefix
<br>模板寻找后缀：spring.thymeleaf.suffix
<br>代码示例：/thymeleaf/index.htm
<br>prefix: /thymeleaf/
<br>return value : index
<br>suffix: .htm
#### 国际化（i18n）
<br>Locale
`LocaleContextHolder`

​	








