---
title: SpringBoot参数下划线转驼峰
cover: https://images.xianhu.wang/22053002.jpg
tags:
  - 参数转换 
categories:
  - 程序员的"春天"
---

# SpringBoot参数下划线转驼峰

{% note red 'fas fa-fan' disabled %} ## 前言{% endnote %}

{% note red  'fas -fa-fan' flat%} 
一般在我们项目中，客户可能会通过接口层面传递下划线参数，然后我们在代码中一般封装的实体都是驼峰。这种情况下如果针对客户的参数进行处理，则会获取不到参数，无法得到预期结果。遇到这种问题时，我们可以使用下面的这种方式，针对下划线参数进行转换处理。
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 示例{% endnote %}
## 示例1
{% span red, postman调用 %}
{% image https://images.xianhu.wang/20220530220724.png %}
```java
/**
 * @author xianhu.wang
 * @date 2022/5/25 10:17 下午
 */
@Data
@ToString
public class PeopleDTO {

    /*用户名称*/
    private String userName;
    /*用户地址*/
    private String userAddress;
    /*用户年龄*/
    private Integer userAge;

}
```
```java
/**
 * @author xianhu.wang
 * @date 2022年05月25日 5:23 下午
 */
@RestController
public class TestController extends AbstractController {

    private static final Logger logger = LoggerFactory.getLogger(TestController.class);

    @RequestMapping("/query")
    public String query( PeopleDTO peopleDTO) {

        logger.info("current user info is={}", peopleDTO);
        return "success";
    }
}

// current user info is=PeopleDTO(userName=null, userAddress=null, userAge=null)
```

## 示例2
{% span red, postman调用 采用下划线转驼峰注解 %}
{% image https://images.xianhu.wang/20220530220724.png %}

```java

/**
 * @author xianhu.wang
 * @date 2022/5/25 10:17 下午
 */
@Data
@ToString
@ParamConversion
public class PeopleDTO {
   /*用户名称*/
    private String userName;
    /*用户地址*/
    private String userAddress;
    /*用户年龄*/
    private Integer userAge;
}
```
```java
/**
 * @author xianhu.wang
 * @date 2022年05月25日 5:23 下午
 */
@RestController
public class TestController extends AbstractController {

    private static final Logger logger = LoggerFactory.getLogger(TestController.class);

    @RequestMapping("/query")
    public String query( PeopleDTO peopleDTO) {

        logger.info("current user info is={}", peopleDTO);
        return "success";
    }
}

// current user info is=PeopleDTO(userName=user, userAddress=hz, userAge=15)
```
{% p cyan, 可以看到采用自定义注解@ParamConversion之后 我们传入的下划线数据已经被成功转为驼峰 %}

{% note red 'fas fa-fan' disabled %} ## 注解封装{% endnote %}

```java

第一步：
/**
 * @author xianhu.wang
 * @date 2022年05月25日 5:20 下午
 */
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    /**
     * 增加参数解析
     * @param resolvers
     */
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {

        resolvers.add(new ArgumentResolver());

    }
}


第二步：定义注解
/**
 * @Description parameter conversion
 * @Author xianhu.wang
 * @Data 2022/4/26 5:16 下午
 */
@Documented
@Target(value = ElementType.TYPE)
@Retention(value = RetentionPolicy.RUNTIME)
public @interface ParamConversion {
}

第三步：实现HandlerMethodArgumentResolver接口
public class ArgumentResolver implements HandlerMethodArgumentResolver {
    /**
     * 匹配下划线的格式
     */
    private static Pattern pattern = Pattern.compile("_(\\w)");

    private static String underLineToCamel(String source) {
        Matcher matcher = pattern.matcher(source);
        StringBuffer sb = new StringBuffer();
        while (matcher.find()) {
            matcher.appendReplacement(sb, matcher.group(1).toUpperCase());
        }
        matcher.appendTail(sb);
        return sb.toString();
    }

    @Override
    public boolean supportsParameter(MethodParameter methodParameter) {
        Class<?> parameterType = methodParameter.getParameterType();
        ParamConversion annotation = parameterType.getAnnotation(ParamConversion.class);
        return annotation != null;

    }

    @Override
    public Object resolveArgument(MethodParameter methodParameter, ModelAndViewContainer modelAndViewContainer,
                                  NativeWebRequest nativeWebRequest, WebDataBinderFactory webDataBinderFactory) throws Exception {
        return handleParameterNames(methodParameter, nativeWebRequest);
    }

    private Object handleParameterNames(MethodParameter parameter, NativeWebRequest webRequest) {
        Object obj = BeanUtils.instantiate(parameter.getParameterType());
        BeanWrapper wrapper = PropertyAccessorFactory.forBeanPropertyAccess(obj);
        Iterator<String> paramNames = webRequest.getParameterNames();
        while (paramNames.hasNext()) {
            String paramName = paramNames.next();
            Object o = webRequest.getParameter(paramName);
            try {
                wrapper.setPropertyValue(underLineToCamel(paramName), o);
            } catch (BeansException e) {
                e.printStackTrace();
            }
        }
        return obj;
    }
}

```