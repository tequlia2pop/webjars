# WebJars

WebJars 是打包为 JAR（Java Archive） 文件的客户端 Web 库（比如 jQuery 和 Bootstrap）。

* 在基于 JVM 的 Web 应用程序中显式地和容易地管理客户端依赖
* 使用基于 JVM 的构建工具（比如 Maven，Gradle，sbt，...）下载客户端依赖
* 了解你正在使用的客户端依赖
* 传递依赖性会自动解析并可选择通过 RequireJS 加载
* 部署在 [Maven Central](http://search.maven.org/) 上
* 公共 CDN，由 [JSDELIVR](http://www.jsdelivr.com/) 慷慨提供

简单的说，WebJars 可以将客户端 Web 库（比如 jQuery 和 Bootstrap）打包成 JAR，然后使用基于 JVM 的构建工具（比如 Maven 和 Gradle）来管理客户端依赖。

WebJars 有三种风格：

* NPM WebJars

	* Instant creation & deployment
	* On-demand releases by anyone
	* Artifact contents mirror NPM package

* Bower WebJars

	* Instant creation & deployment
	* On-demand releases by anyone
	* Artifact contents mirror Bower package

* 经典的 Webjars
	* 手动打包和部署
	* 由 WebJars 团队按需提供
	* 人工创建 RequireJS 配置

## Servlet 3

以 bootstrap 为例，先看一下它对应的 WebJar 的包结构：

````
bootstrap-3.1.0.jar
    └─ META-INF
        └─ resources
            └─ webjars
                └─ bootstrap
                    └─ 3.1.0
                        └─ css
							└─ bootstrap.min.css
````

对于任何兼容 Servlet 3 的容器，`WEB-INF/lib` 目录中的 JAR 中的 `META-INF/resources` 目录中的所有内容都会自动作为静态资源并公开。换句话说，`WEB-INF/lib/{\*.jar}/META-INF/resources` 目录中的所有内容都可以被直接访问。所以，位于 `WEB-INF/lib` 目录中的 WebJar 会自动作为静态资源使用。这样我们就可以通过请求 http://localhost:8080/webjars/bootstrap/3.1.0/css/bootstrap.min.css 直接访问对应的资源文件了。

**Maven 示例**

1. 在 `pom.xml` 文件中添加 WebJar 依赖，如：

	````xml
	<dependencies>
	    <dependency>
	        <groupId>org.webjars</groupId>
	        <artifactId>bootstrap</artifactId>
	        <version>3.1.0</version>
	    </dependency>
	</dependencies>
	````

2. 引用 WebJar 资源，如：

	````html
	<link rel='stylesheet' href='/webjars/bootstrap/3.1.0/css/bootstrap.min.css'>
	````

## Spring MVC

通过 WebJars，Spring MVC 可以使用 `ResourceHandler` 方便地定位 JAR 文件的静态资产。

**Maven 示例**

配置 Spring 将 `/webjars` 的请求映射到 CLASSPATH 中所有 JAR 的 `/META-INF/resources/webjars` 目录。

````java
@Configuration
@EnableWebMvc
public class WebConfig extends WebMvcConfigurerAdapter {

  @Override
  public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/webjars/**").addResourceLocations("classpath:/META-INF/resources/webjars/");
	// 在 Servlet 3 容器中，可以简化为：
	// registry.addResourceHandler("/webjars/**").addResourceLocations("/webjars/");
  }

}
````

通过以上配置，就成功添加了一个 `PathResourceResolver`。该解析器的作用是将 URL 为 `/webjars/**` 的请求映射到 `classpath:/META-INF/resources/webjars/`。比如请求 `http://localhost:8080/webjars/bootstrap/3.1.0/css/bootstrap.min` 时， Spring MVC 会查找路径为 `classpath:/META-INF/resources/webjars/bootstrap/3.1.0/css/bootstrap.min` 的资源文件。