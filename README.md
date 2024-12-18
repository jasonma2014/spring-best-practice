这是一个使用 Spring Boot 开发人员的最佳实践指南。它包含了一些最佳实践，以帮助你编写高质量的代码，并提高应用程序的性能和可维护性。

````markdown
# Spring Boot 开发人员最佳实践

本教程来源于：Spring Boot Best Practices For Developers 👨‍💻 (Youtube/JavaTechie) 和 Spring Boot Best Practices for Developers (Medium/RaviYasas)

这是一份最佳实践指南，包括一些你可以使用的提示，以改进你的 Spring Boot 应用程序并提高其效率。

## 目录

- 开始
  - 0. 特别提及
  - 1. 适当的打包风格
  - 2. 使用 Spring Boot 启动器
  - 3. 使用适当的依赖版本
  - 4. 使用 Lombok
  - 5. 控制器仅用于路由
  - 6. 服务用于业务逻辑
  - 7. 使用 Lombok 进行构造函数注入
  - 8. 使用 Slf4j 日志记录
  - 9. 对类、方法、变量和其他属性使用有意义的单词
  - 10. Bean 验证
  - 11. 自定义异常处理
  - 12. 使用自定义响应对象
  - 13. 使用设计模式
  - 14. 使用 yml 而不是 properties
  - 15. 加密或外部化敏感信息
  - 16. 编写 E2E 单元测试用例并覆盖
  - 17. 使用 Optional 避免 NPE
  - 18. 使用集合框架的最佳实践
  - 19. 使用缓存
  - 20. 使用分页
  - 21. 移除不必要的代码、变量、方法
  - 22. 使用注释
  - 23. 使用统一的代码格式化风格
  - 24. 使用 SonarLint
  - 25. 保持简单！

## 开始

### 先决条件

- Git
- Java 8
- Maven

### 运行应用程序

打开你的终端或命令提示符。

克隆仓库：

```bash
git clone https://github.com/arsy786/springboot-best-practices.git
```
````

导航到克隆的仓库的根目录：

```bash
cd springboot-best-practices
```

运行以下 Maven 命令以构建并启动服务：

```bash
# For Maven
mvn spring-boot:run

# For Maven Wrapper (自动使用正确的Maven版本)
./mvnw spring-boot:run
```

应用程序现在应该在 localhost:9191 上运行。

### 数据库配置

通过编辑 src/main/resources 中的 application.yml 来配置你的 MySQL 数据库连接：

```yaml
# DataSource 配置
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/javatechie
    username: root
    password: root
```

将 url 替换为你数据库的 JDBC URL。
根据需要更新用户名和密码。

注意：要使用不同的 SQL 数据库，请在 pom.xml 中包含适当的数据库驱动程序，并相应地更新 application.yml 配置。

### 0. 特别提及

- 为每个环境创建 application.yml。例如：application-dev.yml, application-prod.yml ...
- 使用一个库来映射 DTOs，例如 MapStruct。
- 使用软删除。
- 使用环境变量以避免硬编码。

### 1. 适当的打包风格

你可以用有意义的打包来构建你的应用程序。
适当的打包将有助于轻松理解代码和应用程序的流程。
你可以将所有的控制器放入一个单独的包，服务放入一个单独的包，工具类放入一个单独的包...等等。这种风格在小型微服务中非常方便。
如果你正在处理一个庞大的代码库，可以使用基于功能的方法。
你可以根据你的需求决定采用哪种。

基于类型：

```plaintext
type-based-packaging
```

基于功能：

```plaintext
feature-based-packaging
```

### 2. 使用 Spring Boot 启动器

我们可以非常容易地使用启动器依赖项，而不需要逐个添加单个依赖项。这些启动器依赖项已经捆绑了所需的依赖项。
可以通过 Spring Initializr 轻松添加这些依赖项。
例如，如果我们添加了 spring-boot-starter-web 依赖项，默认情况下它捆绑了 jackson, spring-core, spring-mvc, 和 spring-boot-starter-tomcat 依赖项。
因此，我们不需要单独添加依赖项。
这也有助于我们避免版本不匹配。

### 3. 使用适当的依赖版本

总是建议使用最新的稳定 GA（通用可用性）版本。
有时它可能因 Java 版本、服务器版本、应用程序类型等而有所不同。
不要使用同一包的不同版本，并始终使用<properties>来指定版本，如果有多个依赖项。例如：

```xml
<dependency-versions>
    <!-- 依赖版本定义 -->
</dependency-versions>
```

Spring Boot 启动器将由父版本定义其版本，这将自动配置所有外部库以使用相同的版本！例如：

```xml
<dependency-versions-sb-parent>
    <!-- 依赖版本定义 -->
</dependency-versions-sb-parent>
```

### 4. 使用 Lombok

Lombok 是一个 Java 库，用于减少样板代码，并允许你使用其注解编写干净的代码。
例如，你可能需要在实体、请求/响应对象、DTOs 等类中使用大量的代码来编写 getter 和 setter。
但如果你使用 Lombok，它只需要一行代码，你可以根据需要使用@Data、@Getter 或@Setter。
你也可以使用 Lombok 的日志注解。推荐使用@Slf4j。

### 5. 控制器仅用于路由

控制器专用于路由。
它是无状态的，并且是单例的。
DispatcherServlet 将检查控制器上的@RequestMapping。
控制器是请求的最终目标，然后请求将交给服务层并由服务层处理。
业务逻辑不应该在控制器中。

代码实现示例请查看：ProductController.java 或 TeamController.java

### 6. 服务用于业务逻辑

完整的业务逻辑在这里进行，包括验证、缓存等。
服务与持久层通信并接收结果。
服务也是单例的。

代码实现示例请查看：ProductService.java 或 TeamService.java

### 7. 使用 Lombok 进行构造函数注入

当我们谈论依赖注入时，有两种类型。
一种是“构造函数注入”，另一种是“setter 注入”。除此之外，你还可以使用非常流行的@Autowired 注解进行“字段注入”。
但我们强烈推荐使用构造函数注入而不是其他类型。因为它允许应用程序在初始化时初始化所有必需的依赖项。
这对于单元测试非常有用。
重要的是，我们可以使用 Lombok 的@RequiredArgsConstructor 注解来使用构造函数注入。
代码实现示例请查看：ProductController.java 或 ProductService.java

### 8. 使用 Slf4j 日志记录

日志记录非常重要。
如果你的应用程序在生产中出现问题，日志记录是找出根本原因的唯一方法。
因此，在添加日志记录器、日志消息类型、日志级别和日志消息时应该仔细考虑。
不要使用 System.out.print()
推荐使用 Slf4j 和 logback，这是 Spring Boot 中默认的日志框架。
总是使用 slf4j {}并避免在日志消息中使用字符串插值。因为字符串插值会消耗更多的内存。
你可以使用 Lombok @Slf4j 注解非常容易地创建一个日志记录器。
如果你处于微服务环境中，可以使用 ELK 堆栈。
代码实现示例请查看：ProductController.java 和 ProductService.java 或 TeamController.java 和 TeamService.java 和 ApiExceptionHandler.java

### 9. 对类、方法、变量和其他属性使用有意义的单词

总是使用适当的、有意义的和可搜索的命名约定，以及适当的大小写。
通常，我们在声明类、变量和常量时使用名词或短语。例如：String firstName, const isValid
你可以为函数和方法使用动词和带有形容词的短语。例如：readFile(), sendData()
避免使用缩写变量名和意向揭示名称。例如：int i; String getExUsr;
如果你有意义地使用这些，可以减少声明注释行。因为它有有意义的名称，新开发人员可以通过阅读代码轻松理解。
案例

我们可以选择许多不同的大小写风格，如上所示。
但是，我们必须确定哪种大小写专用于哪种变量，并保持一致性。最常见的标准：
类 = PascalCase
方法 & 变量 = camelCase
常量 = SCREAMING_SNAKE_CASE
数据库相关字段 = snake_case

### 10. Bean 验证

应用于 DTOs。
使用@NotBlank、@Min、@Max 等注解，并为每个添加消息。
在控制器 POST 请求方法属性中使用@Valid 以验证 DTO bean 验证注解。
注意：使用 javax.persistence.*中的注解为模型/实体层添加约束。
注意：使用 javax.validation.constraints.*中的注解为 DTO 层添加验证。

代码实现示例请查看：Team.java 和 TeamDTO.java 或 ProductRequestDTO.java

### 11. 自定义异常处理

在处理大型企业级应用程序时，这一点非常重要。
除了一般异常外，我们可能有一些场景需要识别一些特定的错误案例。
可以使用@ControllerAdvice 创建异常顾问，我们可以创建具有有意义详细信息的单独异常。
这将使识别和调试未来的错误变得更加容易。
代码实现示例请查看：FCMS 异常文件夹或此异常文件夹与此处理程序文件夹或不同项目异常文件夹

### 12. 使用自定义响应对象

可以使用自定义响应对象返回一个具有特定数据的对象，如 HTTP 状态码、API 代码、消息等。
代码实现示例请查看：APIResponse.java

### 13. 使用设计模式

知道何时何地使用
哪种模式。
Builder 和 Singleton 最常用于 Spring Boot 应用程序中。
支持材料

使用 Lombok 的@Builder 注解（Baeldung/EricGoebelcker）
Spring 框架中的设计模式（Baeldung/JustinAlbano）

### 14. 使用 yml 而不是 properties

减少键前缀的重复/重复。
更易读。
此外，使用注释在配置文件中分隔哪些设置属于哪个功能。
代码实现示例请查看：application.yml

### 15. 加密或外部化敏感信息

加密所有密码（永远不要以明文形式存储）。
将此信息移出代码库（Vault、git 服务器、云配置等）。
支持材料

使用 Spring Boot 进行 Bcrypt 密码加密（Youtube/ProgrammingWithBasar）

### 16. 编写 E2E 单元测试用例并覆盖

这是为了验证所有功能和 API 按预期工作。
我们为什么要模拟 Repository/Service？我们不希望测试数据保存到数据库，因此模拟方法的行为可以帮助我们绕过数据库交互。
测试所有端点/方法。
测试每个端点的正面和负面场景。包括边缘情况！
运行代码并覆盖允许我们识别哪些代码部分已经或没有被测试。
目标是 100%覆盖！
代码实现示例请查看：所有 FCMS 测试或 TeamMockMvcIT.java 或 ProductServiceApplicationTests.java

### 17. 使用 Optional 避免 NPE

在 Java 8 中，为了避免 NullPointerException，可以使用 java.util 包中的 Optional。
可以使用：

```code
Optional.ofNullable(object);

// 或者

productRepository.findById(productId).orElseThrow(
() -> new ProductNotFoundException("product not found with id: " + productId))
```

### 18. 使用集合框架的最佳实践

为你的数据集使用适当的集合（数据结构）。
使用 forEach 与 Java 8 功能，避免使用旧的 for 循环。
Java 8 前后比较：
// Java 8 之前

```code
Map<String, List<ProductResponseDTO>> productsMap = new HashMap<>();
List<String> productTypes = Arrays.asList("Electronics", "fashion", "Kitchen");//1 次迭代来自 DB

List<Product> productList = productRepository.findAll(); //2 次

for (String type : productTypes) {
List<ProductResponseDTO> productResponseDTOList = new ArrayList<>();
for (Product product : productList) {
if (type.equals(product.getProductType())) {
productResponseDTOList.add(ValueMapper.convertToDTO(product));
}
productsMap.put(type, productResponseDTOList);
}
}
return productsMap;
```

// Java 8 之后

```code
Map<String, List<ProductResponseDTO>> productsMap = productRepository.findAll().stream()
.map(ValueMapper::convertToDTO)
.filter(productResponseDTO -> productResponseDTO.getProductType() != null)
.collect(Collectors.groupingBy(ProductResponseDTO::getProductType));
return productsMap;
```

函数式编程对性能和可读性更好。
使用接口类型而不是实现。
使用 isEmpty()而不是 size()以提高可读性。
不要返回 null 值，你可以返回一个空集合。

### 19. 使用缓存

缓存是谈论应用程序性能时另一个重要因素。
它减少了你的请求从应用程序到数据库的往返（如果没有检测到更改）。
默认情况下，Spring Boot 提供 ConcurrentHashMap 进行缓存，你可以通过@EnableCaching 注解实现这一点。
如果你对默认缓存不满意，可以使用 Redis、Hazelcast 或任何其他分布式缓存实现。
Redis 和 Hazelcast 是内存缓存方法。你也可以使用数据库缓存实现。

### 20. 使用分页

如果你有很多记录，使用分页会很有用，会让我们更容易地查看数据，并且会提高应用程序的性能。
如果你使用 Spring Data JPA，PagingAndSortingRepository 使得使用分页变得非常容易，而且几乎不费吹灰之力。

### 21. 移除不必要的代码、变量、方法

未使用的变量声明会占用一些内存。
移除未使用的方法、类、导入、变量等，因为它会影响应用程序的性能。
尽量避免嵌套循环。你可以使用 Java 8 Streams 代替。
支持材料

使用 Java Streams API 进行函数式编程（Youtube/Amigoscode）

### 22. 使用注释

注释是一个好习惯，除非你滥用它。
不要对每件事都发表评论。相反，你可以使用有意义的单词为类、函数、方法、变量...等编写描述性代码。
删除注释代码、误导性注释和故事型注释。
你可以使用注释来警告和解释第一眼难以理解的内容。
方法注释示例：
/\*\* 此方法将根据 ID 从数据库中获取产品

- @param productId
- @return 来自数据库的产品响应
  \*/

### 23. 使用统一的代码格式化风格

有一个一致和统一的代码格式化方式。
使用空格和/或返回以有序和可读的方式。
在 IDE 中使用 CMD+OPTION+L 对高亮代码进行代码重新格式化！
如下所示，两种不同的代码格式化方式。为了避免差异，你或你的团队应该有一个共同的编码格式。
代码格式化

### 24. 使用 SonarLint

这非常有用，可以识别小错误和最佳实践，以避免不必要的错误和代码质量问题。
你可以将插件安装到你最喜欢的 IDE 中。

### 25. 保持简单！

总是尝试编写可读的、易于理解的和简单的代码！
