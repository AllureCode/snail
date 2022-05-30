
---
title: SpringBoot Validate校验
cover: https://images.xianhu.wang/052601.jpg
tags:
  - Validate校验 
categories:
  - 程序员的"春天"
---

# Springboot使用Validate校验参数

{% note red 'fas fa-fan' disabled %} ## 前言{% endnote %}

{% note red  'fas -fa-fan' flat%} 

一般在项目中，我们需要进行接口层参数校验，校验的方式有很多种。比如此篇提及到的validate。但是这种校验我觉得仍有不足之处。`因为我们需要写一个全局的异常处理器，然后去捕捉校验失败抛出的异常，从而进行自定义返回结果。此外，这种异常也会打印在我们日志中，会显得十分格格不入。`

还有其他校验方式，例如我们可以写一个intercepter来拦截请求，从而进行参数校验。如果为了代码可拓展，结果清晰。那么在下一篇中我会详细的给出我个人使用的参数校验方式。大家可以进行参考～～不喜勿喷。

{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 用法{% endnote %}
### maven依赖
```java
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>javax.validation</groupId>
            <artifactId>validation-api</artifactId>
            <version>2.0.1.Final</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate.validator</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>6.0.7.Final</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.70</version>
        </dependency>
```
### 代码示例
#### object定义
```java
/**
 * @author xianhu.wang
 * @date 2022/5/25 10:17 下午
 */
@Data
@ToString
public class PeopleDTO {

    @NotNull(message = "用户名称不能为空")
    private String userName;

    @NotNull(message = "用户地址不能为空")
    private String userAddress;

    @Min(value = 18, message = "年龄不得小于18岁")
    private Integer userAge;

}
```
#### controller层
```java
/**
 * @author xianhu.wang
 * @date 2022/5/25 10:17 下午
 */
public class AbstractController {

    private static final Logger logger = LoggerFactory.getLogger(AbstractController.class);

    @Autowired
    protected HttpServletRequest request;

    @Resource
    protected HttpServletResponse response;

    /**
     * 接口层参数校验
     *
     * @param e
     */
    @ExceptionHandler(value = BindException.class)
    public void handleMethodArgumentNotValidException(BindException e) {
        List<ObjectError> allErrors = e.getBindingResult().getAllErrors();
        String message = allErrors.stream().map(DefaultMessageSourceResolvable::getDefaultMessage).collect(Collectors.joining(";"));
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("success", false);
        jsonObject.put("code", "500");
        jsonObject.put("message", message);
        String res = JSON.toJSONString(jsonObject);
        printResult(res);
    }

    /**
     * 接口层参数校验
     *
     * @param e
     */
    @ExceptionHandler(value = MethodArgumentNotValidException.class)
    public void handleMethodArgumentNotValidException(MethodArgumentNotValidException e) {
        List<ObjectError> allErrors = e.getBindingResult().getAllErrors();
        String message = allErrors.stream().map(DefaultMessageSourceResolvable::getDefaultMessage).collect(Collectors.joining(";"));
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("success", false);
        jsonObject.put("code", "500");
        jsonObject.put("message", message);
        String res = JSON.toJSONString(jsonObject);
        printResult(res);
    }

    public void printResult(String result) {
        PrintWriter out = null;
        try {
            response.setCharacterEncoding("UTF-8");
            response.setContentType("application/json");
            out = response.getWriter();
            out.println(result);
        } catch (Exception e) {
            logger.error("print error", e);
        } finally {
            if (out != null) {
                out.flush();
                out.close();
            }
        }
    }
}


/**
 * @author xianhu.wang
 * @date 2022/5/25 10:17 下午
 */
@RestController
public class TestController extends AbstractController {

    private static final Logger logger = LoggerFactory.getLogger(TestController.class);

    @RequestMapping("/query")
    public String query(@Validated PeopleDTO peopleDTO) {

        logger.info("current user info is={}", peopleDTO);
        return "success";
    }
}
```
{% note red 'fas fa-fan' disabled %} ## 输出结果{% endnote %}
{% image https://images.xianhu.wang/052501.png  %}
{% note red 'fas fa-fan' disabled %} ## 总结{% endnote %}

{% note green  'fas -fa-fan' flat%} 
 以上是在springboot中使用validate校验参数的简单用例。如果你想要自定义输出结果，那么可以在JSONObject中封装你自己的返回规则。最后，送给大家一份validate注解速查手册～～
{% endnote %}

{%  folding green, validate注解速查手册 %}
## JSR303定义的校验类型
| 注解                        | 说明                                                         |
| --------------------------- | ------------------------------------------------------------ |
| @Null                       | 验证对象是否为null                                           |
| @NotNull                    | 验证对象是否不为null, 无法查检长度为0的字符串                |
| @NotBlank                   | 检查约束字符串是不是Null还有被Trim的长度是否大于0,只对字符串,且会去掉前后空格. |
| @NotEmpty                   | 检查约束元素是否为NULL或者是EMPTY.                           |
| @AssertTrue                 | 验证 Boolean 对象是否为 true                                 |
| @AssertFalse                | 验证 Boolean 对象是否为 false                                |
| @Size(min=, max=)           | 验证对象（Array,Collection,Map,String）长度是否在给定的范围之内 |
| @Length(min=, max=)         | 验证注解的元素值长度在min和max区间内                         |
| @Past                       | 验证 Date 和 Calendar 对象是否在当前时间之前                 |
| @Future                     | 验证 Date 和 Calendar 对象是否在当前时间之后                 |
| @Pattern                    | 验证 String 对象是否符合正则表达式的规则                     |
| @Min                        | 验证 Number 和 String 对象是否大等于指定的值                 |
| @Max                        | 验证 Number 和 String 对象是否小等于指定的值                 |
| @DecimalMax                 | 被标注的值必须不大于约束中指定的最大值. 这个约束的参数是一个通过BigDecimal定义的最大值的字符串表示.小数存在精度 |
| @DecimalMin                 | 被标注的值必须不小于约束中指定的最小值. 这个约束的参数是一个通过BigDecimal定义的最小值的字符串表示.小数存在精度 |
| @Digits                     | 验证 Number 和 String 的构成是否合法                         |
| @Digits(integer=,fraction=) | 验证字符串是否是符合指定格式的数字，interger指定整数精度，fraction指定小数精度 |
| @Range(min=, max=)          | 验证注解的元素值在最小值和最大值之间                         |
{% endfolding %}
