---
title: SpringBoot-优雅参数校验
cover: https://images.xianhu.wang/20220621.jpg
date:  2022-06-21 14:41:10
updated: 2022-06-21 14:43:10
tags:
  - SpringBoot
categories:
  - 程序员的"春天"
---

{% note red 'fas fa-fan' disabled %} ## 前言{% endnote %}
{% note red  'fas -fa-fan' flat%} 
在一般的项目开发中，我们基本都会进行接口层面的参数校验。接口校验的方式也有很多种，例如拦截器实现、过滤器实现前置的校验或者在接口中获取参数之后进行后置的校验。今天给大家分享我自己在项目中使用的一种前置参数校验方法，此方法可以支持大家自定义拓展实现自己的校验逻辑即可。
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 实践 {% endnote %}
{% note red  'fas -fa-fan' flat%} 
我的项目中设计到文本与图像处理，因此接口层面设计文本与图像接口，当然，大家也可以进行自己的接口定义，然后按照我这种方式进行校验处理。
{% endnote %}
### 定义公共参数校验属性
```java
/**
 * @Description request param
 * @Author xianhu.wang
 * @Data 2022/4/25 4:36 下午
 */
public class RequestParam {

    private String seqId;
    private String partnerCode;
    private String appName;
    private String eventId;
    private String eventType;
    private String remark;

    public String getRemark() {
        return remark;
    }

    public void setRemark(String remark) {
        this.remark = remark;
    }

    public String getEventType() {
        return eventType;
    }

    public void setEventType(String eventType) {
        this.eventType = eventType;
    }

    protected Map<String, Object> params = new HashMap<>();

    public String getSeqId() {
        return seqId;
    }

    public void setSeqId(String seqId) {
        this.seqId = seqId;
    }

    public String getPartnerCode() {
        return partnerCode;
    }

    public void setPartnerCode(String partnerCode) {
        this.partnerCode = partnerCode;
        params.put(AnchorConstant.PARTNER_CODE, partnerCode);
    }

    public String getAppName() {
        return appName;
    }

    public void setAppName(String appName) {
        this.appName = appName;
        params.put(AnchorConstant.APP_NAME, appName);
    }

    public String getEventId() {
        return eventId;
    }

    public void setEventId(String eventId) {
        this.eventId = eventId;
        params.put(AnchorConstant.EVENT_ID, eventId);
    }

    public Map<String, Object> getParams() {
        return params;
    }

    public void setParams(Map<String, Object> params) {
        this.params = params;
    }

    public String getParam(String paramName) {
        return params.containsKey(paramName) ? params.get(paramName).toString() : null;
    }
```
### 定义抽象校验类
```java
/**
 * @Description
 * @Author xianhu.wang
 * @Data 2022/4/25 4:44 下午
 */
public abstract class AbstractParamValidate<T extends RequestParam> {

    public static final String REQUEST_PARAM_KEY = "REQUEST_PARAM_KEY";

    protected Logger logger = LoggerFactory.getLogger(AbstractParamValidate.class);

    // monitor service
    protected Registry registry;

    public AbstractParamValidate(Registry registry) {
        this.registry = registry;
    }

    public <T> T getRequestParam(HttpServletRequest request) {
        Object object = request.getAttribute(REQUEST_PARAM_KEY);
        if (null == object) {
            return null;
        }

        return (T) object;
    }

    /**
     * param validate
     *
     * @param request
     * @param response
     * @return
     */
    public boolean validate(HttpServletRequest request, HttpServletResponse response) {

        String seqId = RequestUtil.getSeqId(request);
        String partnerCode = RequestUtil.getPartnerCode(request);
        String secretKey = RequestUtil.getSecretKey(request);
        String eventId = RequestUtil.getEventId(request);
        String appName = RequestUtil.getAppName(request);
        String eventType = RequestUtil.getEventType(request);
        String remark = RequestUtil.getRemark(request);

        T requestParam = createRequestParam();

        requestParam.setSeqId(seqId);
        requestParam.setPartnerCode(partnerCode);
        requestParam.setEventId(eventId);
        requestParam.setAppName(appName);
        requestParam.setEventType(eventType);
        requestParam.setRemark(remark);

        // parameter invalid
        if (StringUtils.isEmpty(seqId)) {
            RespResult respResult = new RespResult(ResponseCode.SEQ_ID_IS_BLANK.getSubCode(), ResponseCode.SEQ_ID_IS_BLANK.getMessage());
            RespResult.printErrorResult(registry, response, respResult);
            return false;
        }
        if (StringUtils.isEmpty(secretKey)) {
            RespResult respResult = new RespResult(ResponseCode.HEAD_SECRET_LEY_BLANK.getSubCode(), ResponseCode.HEAD_SECRET_LEY_BLANK.getMessage());
            RespResult.printErrorResult(registry, response, respResult);
            return false;
        }

        if (StringUtils.isEmpty(partnerCode)) {
            RespResult respResult = new RespResult(ResponseCode.HEAD_ACCESS_KEY_BLANK.getSubCode(), ResponseCode.HEAD_ACCESS_KEY_BLANK.getMessage());
            RespResult.printErrorResult(registry, response, respResult);
            return false;
        }

        boolean result = customizeValidate(requestParam, request, response);

        if (result) {
            Enumeration names = request.getParameterNames();

            if (names != null) {
                while (names.hasMoreElements()) {
                    String paramName = (String) names.nextElement();
                    String paramValue = request.getParameter(paramName);
                    requestParam.getParams().putIfAbsent(paramName, paramValue);
                }
            }

            request.setAttribute(REQUEST_PARAM_KEY, requestParam);
        }

        return result;
    }

    /**
     * create request param
     *
     * @return
     */
    public abstract T createRequestParam();

    /**
     * customize validate
     *
     * @param request
     * @param response
     * @return
     */
    public abstract boolean customizeValidate(T requestParam, HttpServletRequest request, HttpServletResponse response);
}

```

### 文本接口参数校验
```java
/**
 * @Description text param validate
 * @Author xianhu.wang
 * @Data 2022/4/25 5:21 下午
 */
public class TextParamValidate extends AbstractParamValidate<TextParamValidate.TextRequestParam> {
    public TextParamValidate(Registry registry) {
        super(registry);
    }

    @Override
    public TextParamValidate.TextRequestParam createRequestParam() {
        return new TextRequestParam();
    }

    /**
     * expand param validate
     * it is best if you use it.
     *
     * @param requestParam
     * @param request
     * @param response
     * @return
     */
    @Override
    public boolean customizeValidate(TextParamValidate.TextRequestParam requestParam, HttpServletRequest request, HttpServletResponse response) {
      
        String textContent = RequestUtil.getTextContent(request);
        if (StringUtils.isEmpty(textContent)) {
            RespResult respResult = new RespResult(ResponseCode.TEXT_CONTENT_IS_BLACK.getSubCode(), ResponseCode.TEXT_CONTENT_IS_BLACK.getMessage());
            RespResult.printErrorResult(registry, response, respResult);
            return false;
        }
        String exAuditResult = RequestUtil.getExAuditResult(request);
        if (StringUtils.isEmpty(exAuditResult)) {
            RespResult respResult = new RespResult(ResponseCode.EX_AUDIT_RESULT_IS_BLACK.getSubCode(), ResponseCode.EX_AUDIT_RESULT_IS_BLACK.getMessage());
            RespResult.printErrorResult(registry, response, respResult);
            return false;
        }

        // set
        requestParam.setTextContent(textContent);
        requestParam.setExAuditResult(exAuditResult);

        return true;
    }

    /**
     * you can do create your customize validate parameter
     */
    @Data
    @ToString
    public static class TextRequestParam extends RequestParam {
        private String textContent;
        private String exAuditResult;
    }
}
```

### 图像参数校验
```java
/**
 * @Description image param validate
 * @Author xianhu.wang
 * @Data 2022/4/25 5:21 下午
 */
public class ImgParamValidate extends AbstractParamValidate<ImgParamValidate.ImageRequestParam> {

    public ImgParamValidate(Registry registry) {
        super(registry);
    }

    @Override
    public ImgParamValidate.ImageRequestParam createRequestParam() {
        return new ImageRequestParam();
    }

    /**
     * expand param validate
     * it is best if you use it.
     *
     * @param requestParam
     * @param request
     * @param response
     * @return
     */
    @Override
    public boolean customizeValidate(ImgParamValidate.ImageRequestParam requestParam, HttpServletRequest request, HttpServletResponse response) {

        String exAuditResult = RequestUtil.getExAuditResult(request);
        if (StringUtils.isEmpty(exAuditResult)) {
            RespResult respResult = new RespResult(ResponseCode.EX_AUDIT_RESULT_IS_BLACK.getSubCode(), ResponseCode.EX_AUDIT_RESULT_IS_BLACK.getMessage());
            RespResult.printErrorResult(registry, response, respResult);
            return false;
        }

        String exAuditLabel = RequestUtil.getExAuditLabel(request);

        // set
        requestParam.setExAuditLabel(exAuditLabel);
        requestParam.setExAuditResult(exAuditResult);
        return true;
    }


    /**
     * you can do create your customize validate parameter
     */
    @Data
    @ToString
    public static class ImageRequestParam extends RequestParam {
        private String exAuditResult;
        private String exAuditLabel;
    }
}

```
### 参数校验拦截器
```java
/**
 * @Description parameter verification
 * @Author xianhu.wang
 * @Data 2022/4/25 3:41 下午
 */
@Component
public class ParamValidateInterceptor extends HandlerInterceptorAdapter {

    private static final Logger logger = LoggerFactory.getLogger(ParamValidateInterceptor.class);

    // 服务监控打点
    @Autowired
    private Registry registry;

    private Map<String, AbstractParamValidate> maps = new HashMap<>();

    // 这里初始化会将你的请求路径放入map，后续具体使用的时候会通过map取出对应参数
    @PostConstruct
    public void init() {
        maps.put(MappingPath.TEXT_FEEDBACK_V1_PATH, new TextParamValidate(registry));
        maps.put(MappingPath.IMG_FEEDBACK_V1_PATH, new ImgParamValidate(registry));
    }

    public TextParamValidate.TextRequestParam getTextRequestParam(HttpServletRequest request) {
        return (TextParamValidate.TextRequestParam) maps.get(request.getServletPath()).getRequestParam(request);
    }

    public ImgParamValidate.ImageRequestParam getImgRequestParam(HttpServletRequest request) {
        return (ImgParamValidate.ImageRequestParam) maps.get(request.getServletPath()).getRequestParam(request);
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String path = request.getServletPath();

        AbstractParamValidate paramValidate = maps.get(path);
        if (null == paramValidate) {
            logger.warn("path {} can't found ParamValidator", path);
            return true;
        }

        return paramValidate.validate(request, response);
    }
}

```
```java
/**
 * @Description request path mapping
 * @Author xianhu.wang
 * @Data 2022/4/25 3:46 下午
 */
public class MappingPath {
    /**
     * prefix path
     */
    private static final String ROOT_PATH = "/cs";

    /**
     * text  path
     */
    public static final String TEXT_FEEDBACK_V1_PATH = ROOT_PATH + "/text/v1";
    /**
     * img  path
     */
    public static final String IMG_FEEDBACK_V1_PATH = ROOT_PATH + "/img/v1";
}
```
### 统一返回结果工具类
```java
/**
 * @Description
 * @Author xianhu.wang
 * @Data 2022/4/25 6:01 下午
 */
public class RespResult<T> {

    private static final Logger logger = LoggerFactory.getLogger(RespResult.class);

    private Integer reasonCode;
    private String message;
    private boolean success = false;
    private T data;

    public RespResult(Integer code, String message, boolean success, T data) {
        this.reasonCode = code;
        this.message = message;
        this.success = success;
        this.data = data;
    }

    public RespResult(Integer code, String message, boolean success) {
        this.reasonCode = code;
        this.message = message;
        this.success = success;
    }

    public RespResult(Integer code, boolean success) {
        this.reasonCode = code;
        this.success = success;
    }

    public RespResult(Integer code, String message) {
        this.reasonCode = code;
        this.message = message;
    }


    public Integer getReasonCode() {
        return reasonCode;
    }

    public void setReasonCode(Integer reasonCode) {
        this.reasonCode = reasonCode;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public boolean isSuccess() {
        return success;
    }

    public void setSuccess(boolean success) {
        this.success = success;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    /**
     * 成功结果 携带数据
     *
     * @param data
     * @param
     * @return
     */
    public static <T> RespResult<T> success(T data) {
        return new RespResult<T>(ResponseCode.SUCCESS.getSubCode(), "", true, data);
    }

    /**
     * 成功结果 携带消息 携带数据
     *
     * @param
     * @return
     */
    public static <T> RespResult<T> success(String message, T data) {
        return new RespResult<T>(ResponseCode.SUCCESS.getSubCode(), message, true, data);
    }

    /**
     * 成功结果 不携带数据
     *
     * @param
     * @return
     */
    public static <T> RespResult<T> success() {
        return new RespResult<T>(ResponseCode.SUCCESS.getSubCode(), "", true);
    }


    /**
     * 成功结果 不携带数据 携带消息
     *
     * @param
     * @return
     */
    public static <T> RespResult<T> success(String message) {
        return new RespResult<T>(ResponseCode.SUCCESS.getSubCode(), message, true);
    }

    /**
     * 失败结果 携带失败消息
     *
     * @param
     * @return
     */
    public static <T> RespResult<T> failed(Integer code, String msg, T data) {
        return new RespResult<T>(code, msg, false, data);
    }


    public static void printErrorResult(Registry registry, HttpServletResponse response, RespResult r) {
        printResult(response, JSON.toJSONString(r));
    }

    public static void printSuccessResult(Registry registry, HttpServletResponse response, RespResult r) {
        printResult(response, JSON.toJSONString(r));
    }

    /**
     * print result(JSONObject)
     */
    private static void printResult(HttpServletResponse response, String result) {
        PrintWriter out = null;
        try {
            response.setCharacterEncoding("UTF-8");
            response.setContentType("application/json");
            out = response.getWriter();
            out.print(result);
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
```

### 使用示例
```java
/**
 * @Description text controller
 * @Author xianhu.wang
 * @Data 2022/4/25 3:30 下午
 */
@RestController
public class TextTaskController extends AbstractController implements InitializingBean, DisposableBean {

    private static final Logger logger = LoggerFactory.getLogger(TextFeedBackController.class);
    private static final long LAZY_DESTROY_TIME_SEC = 10;
    @Autowired
    private ParamValidateInterceptor paramValidateInterceptor;
    @Autowired
    private TextTaskService textTaskService;

    @RequestMapping(MappingPath.TEXT_FEEDBACK_V1_PATH)
    public RespResult<Object> TextTaskSubmit(HttpServletRequest request){
        long start = System.currentTimeMillis();
        TextParamValidate.TextRequestParam textRequestParam = paramValidateInterceptor.getTextRequestParam(request);
        RespResult<Object> result = textTaskService.submitTask(textRequestParam);
        logger.info("seqId={},text task submit end,cost={}", textRequestParam.getSeqId(), System.currentTimeMillis() - start);
        return result;
    }
    /**
     * @throws Exception
     */
    @Override
    public void afterPropertiesSet() throws Exception {
        logger.warn("TextTaskController OK");
    }

    /**
     * 延迟销毁
     *
     * @throws Exception
     */
    @Override
    public void destroy() throws Exception {
        logger.warn("TextTaskController Lazy Destroy {} Seconds", LAZY_DESTROY_TIME_SEC);
        TimeUnit.SECONDS.sleep(LAZY_DESTROY_TIME_SEC);
        logger.warn("TextTaskController Destroyed");
    }
}
```