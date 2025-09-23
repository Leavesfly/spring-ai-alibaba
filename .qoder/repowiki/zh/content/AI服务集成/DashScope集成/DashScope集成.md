# DashScope集成

<cite>
**本文档引用的文件**
- [ConditionalOnDashScopeEnabled.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/ConditionalOnDashScopeEnabled.java)
- [DashScopeConnectionUtils.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeConnectionUtils.java)
- [DashScopeParentProperties.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeParentProperties.java)
- [DashScopeChatAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeChatAutoConfiguration.java)
- [DashScopeEmbeddingAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeEmbeddingAutoConfiguration.java)
- [DashScopeRerankAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeRerankAutoConfiguration.java)
- [DashScopeAudioSpeechAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeAudioSpeechAutoConfiguration.java)
- [DashScopeAudioTranscriptionAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeAudioTranscriptionAutoConfiguration.java)
- [DashScopeApi.java](file://spring-ai-alibaba-core/src/main/java/com/alibaba/cloud/ai/dashscope/api/DashScopeApi.java)
- [DashScopeApiConstants.java](file://spring-ai-alibaba-core/src/main/java/com/alibaba/cloud/ai/dashscope/common/DashScopeApiConstants.java)
</cite>

## 目录
1. [简介](#简介)
2. [自动配置机制](#自动配置机制)
3. [连接配置与认证](#连接配置与认证)
4. [API封装层次结构](#api封装层次结构)
5. [请求响应处理流程](#请求响应处理流程)
6. [错误恢复策略](#错误恢复策略)
7. [异步调用与流式响应](#异步调用与流式响应)
8. [最佳实践](#最佳实践)

## 简介

本文档详细介绍了Spring AI Alibaba框架如何与阿里云DashScope服务进行集成。文档涵盖了自动配置机制、连接配置参数、API封装层次结构、请求/响应处理流程、错误恢复策略以及异步调用和流式响应的支持。通过本文档，开发者可以深入了解如何在Spring Boot应用中集成和使用DashScope服务。

## 自动配置机制

### ConditionalOnDashScopeEnabled条件注解

`ConditionalOnDashScopeEnabled`是一个自定义的条件注解，用于控制DashScope相关组件的自动配置。该注解基于Spring Boot的`@ConditionalOnProperty`实现，检查系统属性`spring.ai.dashscope.enabled`的值。如果该属性未设置或值为`true`，则条件满足，相关的自动配置类将被加载。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@ConditionalOnProperty(value = "spring.ai.dashscope.enabled", matchIfMissing = true)
public @interface ConditionalOnDashScopeEnabled {
}
```

**Section sources**
- [ConditionalOnDashScopeEnabled.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/ConditionalOnDashScopeEnabled.java#L1-L35)

### 自动配置类

DashScope的自动配置类使用`@ConditionalOnDashScopeEnabled`注解来确保只有在启用DashScope时才加载。这些配置类包括：

- `DashScopeChatAutoConfiguration`：聊天模型的自动配置
- `DashScopeEmbeddingAutoConfiguration`：嵌入模型的自动配置
- `DashScopeRerankAutoConfiguration`：重排序模型的自动配置
- `DashScopeAudioSpeechAutoConfiguration`：语音合成模型的自动配置
- `DashScopeAudioTranscriptionAutoConfiguration`：语音转录模型的自动配置

这些配置类通过`@EnableConfigurationProperties`注解启用相应的配置属性，并通过`@Bean`注解定义相应的模型实例。

**Section sources**
- [DashScopeChatAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeChatAutoConfiguration.java#L1-L143)
- [DashScopeEmbeddingAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeEmbeddingAutoConfiguration.java#L1-L100)
- [DashScopeRerankAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeRerankAutoConfiguration.java#L1-L83)
- [DashScopeAudioSpeechAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeAudioSpeechAutoConfiguration.java#L1-L73)
- [DashScopeAudioTranscriptionAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeAudioTranscriptionAutoConfiguration.java#L1-L89)

## 连接配置与认证

### 连接配置参数

DashScope的连接配置参数通过`DashScopeConnectionProperties`类进行管理。主要配置参数包括：

- `baseUrl`：DashScope API的基础URL
- `apiKey`：API密钥
- `workspaceId`：工作区ID
- `readTimeout`：读取超时时间

这些参数可以通过`application.properties`或`application.yml`文件进行配置，也可以通过环境变量设置。

### 认证机制

DashScope的认证机制基于API密钥和工作区ID。API密钥通过HTTP请求头中的`Authorization`字段传递，格式为`Bearer <apiKey>`。工作区ID通过`DashScope-Workspace`请求头传递。

```java
if (StringUtils.hasText(workSpaceId)) {
    this.headers.add(DashScopeApiConstants.HEADER_WORK_SPACE_ID, workSpaceId);
}

Consumer<HttpHeaders> finalHeaders = h -> {
    if (!(apiKey instanceof NoopApiKey)) {
        h.setBearerAuth(apiKey.getValue());
    }
    h.setContentType(MediaType.APPLICATION_JSON);
    h.addAll(headers);
};
```

**Section sources**
- [DashScopeApi.java](file://spring-ai-alibaba-core/src/main/java/com/alibaba/cloud/ai/dashscope/api/DashScopeApi.java#L118-L164)
- [DashScopeApiConstants.java](file://spring-ai-alibaba-core/src/main/java/com/alibaba/cloud/ai/dashscope/common/DashScopeApiConstants.java)

### DashScopeConnectionUtils连接工具

`DashScopeConnectionUtils`类提供了连接属性解析的工具方法。该方法从通用配置和模型特定配置中解析出最终的连接属性，优先使用模型特定配置。

```java
public static @NotNull ResolvedConnectionProperties resolveConnectionProperties(
        DashScopeParentProperties commonProperties, DashScopeParentProperties modelProperties, String modelType) {

    String baseUrl = StringUtils.hasText(modelProperties.getBaseUrl()) ? modelProperties.getBaseUrl()
            : commonProperties.getBaseUrl();
    String apiKey = StringUtils.hasText(modelProperties.getApiKey()) ? modelProperties.getApiKey()
            : commonProperties.getApiKey();
    String workspaceId = StringUtils.hasText(modelProperties.getWorkspaceId()) ? modelProperties.getWorkspaceId()
            : commonProperties.getWorkspaceId();

    Map<String, List<String>> connectionHeaders = new HashMap<>();
    if (StringUtils.hasText(workspaceId)) {
        connectionHeaders.put("DashScope-Workspace", List.of(workspaceId));
    }

    // Get apikey from system env.
    if (Objects.isNull(apiKey)) {
        if (Objects.nonNull(System.getenv(DashScopeApiConstants.AI_DASHSCOPE_API_KEY))) {
            apiKey = System.getenv(DashScopeApiConstants.AI_DASHSCOPE_API_KEY);
        }
    }

    Assert.hasText(baseUrl,
            "DashScope base URL must be set.  Use the connection property: spring.ai.dashscope.base-url or spring.ai.dashscope."
                    + modelType + ".base-url property.");
    Assert.hasText(apiKey,
            "DashScope API key must be set. Use the connection property: spring.ai.dashscope.api-key or spring.ai.dashscope."
                    + modelType + ".api-key property.");

    return new ResolvedConnectionProperties(baseUrl, apiKey, workspaceId,
            CollectionUtils.toMultiValueMap(connectionHeaders));
}
```

**Section sources**
- [DashScopeConnectionUtils.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeConnectionUtils.java#L1-L75)
- [DashScopeParentProperties.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeParentProperties.java#L1-L62)

## API封装层次结构

### 聊天功能模块

`DashScopeChatModel`类封装了聊天功能，支持多种聊天模型如`qwen-max`、`qwen-plus`等。通过`DashScopeChatAutoConfiguration`自动配置类，开发者可以轻松地在Spring Boot应用中使用聊天功能。

```java
@Bean
@ConditionalOnMissingBean
public DashScopeChatModel dashscopeChatModel(
        RetryTemplate retryTemplate,
        ToolCallingManager toolCallingManager,
        DashScopeChatProperties chatProperties,
        ResponseErrorHandler responseErrorHandler,
        DashScopeConnectionProperties commonProperties,
        ObjectProvider<ObservationRegistry> observationRegistry,
        ObjectProvider<WebClient.Builder> webClientBuilderProvider,
        ObjectProvider<RestClient.Builder> restClientBuilderProvider,
        ObjectProvider<ChatModelObservationConvention> observationConvention,
        ObjectProvider<ToolExecutionEligibilityPredicate> dashscopeToolExecutionEligibilityPredicate
) {

    var dashscopeApi = dashscopeChatApi(
            commonProperties,
            chatProperties,
            restClientBuilderProvider.getIfAvailable(RestClient::builder),
            webClientBuilderProvider.getIfAvailable(WebClient::builder),
            responseErrorHandler,
            "chat"
    );

    var dashscopeModel = DashScopeChatModel.builder()
            .dashScopeApi(dashscopeApi)
            .retryTemplate(retryTemplate)
            .toolCallingManager(toolCallingManager)
            .defaultOptions(chatProperties.getOptions())
            .observationRegistry(observationRegistry.getIfUnique(() -> ObservationRegistry.NOOP))
            .toolExecutionEligibilityPredicate(
                    dashscopeToolExecutionEligibilityPredicate.getIfUnique(DefaultToolExecutionEligibilityPredicate::new))
            .build();

    observationConvention.ifAvailable(dashscopeModel::setObservationConvention);

    return dashscopeModel;
}
```

**Section sources**
- [DashScopeChatAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeChatAutoConfiguration.java#L1-L143)

### 嵌入功能模块

`DashScopeEmbeddingModel`类封装了嵌入功能，支持生成文本的向量表示。通过`DashScopeEmbeddingAutoConfiguration`自动配置类，开发者可以轻松地在Spring Boot应用中使用嵌入功能。

```java
@Bean
@ConditionalOnMissingBean
public DashScopeEmbeddingModel dashscopeEmbeddingModel(DashScopeConnectionProperties commonProperties,
        DashScopeEmbeddingProperties embeddingProperties,
        ObjectProvider<WebClient.Builder> webClientBuilderProvider,
        ObjectProvider<RestClient.Builder> restClientBuilderProvider, RetryTemplate retryTemplate,
        ResponseErrorHandler responseErrorHandler, ObjectProvider<ObservationRegistry> observationRegistry,
        ObjectProvider<EmbeddingModelObservationConvention> observationConvention) {

    var dashScopeApi = dashscopeEmbeddingApi(commonProperties, embeddingProperties,
            restClientBuilderProvider.getIfAvailable(RestClient::builder),
            webClientBuilderProvider.getIfAvailable(WebClient::builder), responseErrorHandler);

    var embeddingModel = new DashScopeEmbeddingModel(dashScopeApi, embeddingProperties.getMetadataMode(),
            embeddingProperties.getOptions(), retryTemplate,
            observationRegistry.getIfUnique(() -> ObservationRegistry.NOOP));

    observationConvention.ifAvailable(embeddingModel::setObservationConvention);

    return embeddingModel;
}
```

**Section sources**
- [DashScopeEmbeddingAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeEmbeddingAutoConfiguration.java#L1-L100)

### 重排序功能模块

`DashScopeRerankModel`类封装了重排序功能，支持对搜索结果进行重新排序。通过`DashScopeRerankAutoConfiguration`自动配置类，开发者可以轻松地在Spring Boot应用中使用重排序功能。

```java
@Bean
@ConditionalOnMissingBean
public DashScopeRerankModel dashscopeRerankModel(DashScopeConnectionProperties commonProperties,
        DashScopeRerankProperties rerankProperties, ObjectProvider<Builder> restClientBuilderProvider,
        ObjectProvider<WebClient.Builder> webClientBuilderProvider, RetryTemplate retryTemplate,
        ResponseErrorHandler responseErrorHandler) {

    ResolvedConnectionProperties resolved = resolveConnectionProperties(commonProperties, rerankProperties,
            "rerank");

    var dashScopeApi = DashScopeApi.builder()
        .apiKey(resolved.apiKey())
        .headers(resolved.headers())
        .baseUrl(resolved.baseUrl())
        .webClientBuilder(webClientBuilderProvider.getIfAvailable(WebClient::builder))
        .workSpaceId(resolved.workspaceId())
        .restClientBuilder(restClientBuilderProvider.getIfAvailable(RestClient::builder))
        .responseErrorHandler(responseErrorHandler)
        .build();

    return new DashScopeRerankModel(dashScopeApi, rerankProperties.getOptions(), retryTemplate);
}
```

**Section sources**
- [DashScopeRerankAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeRerankAutoConfiguration.java#L1-L83)

### 语音合成功能模块

`DashScopeAudioSpeechModel`类封装了语音合成功能，支持将文本转换为语音。通过`DashScopeAudioSpeechAutoConfiguration`自动配置类，开发者可以轻松地在Spring Boot应用中使用语音合成功能。

```java
@Bean
@ConditionalOnMissingBean
public DashScopeAudioSpeechModel dashScopeSpeechSynthesisModel(RetryTemplate retryTemplate,
        DashScopeConnectionProperties commonProperties, DashScopeAudioSpeechSynthesisProperties speechProperties) {

    var dashScopeSpeechSynthesisApi = dashScopeSpeechSynthesisApi(commonProperties, speechProperties);

    return new DashScopeAudioSpeechModel(dashScopeSpeechSynthesisApi, speechProperties.getOptions(), retryTemplate);
}
```

**Section sources**
- [DashScopeAudioSpeechAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeAudioSpeechAutoConfiguration.java#L1-L73)

### 语音转录功能模块

`DashScopeAudioTranscriptionModel`类封装了语音转录功能，支持将语音转换为文本。通过`DashScopeAudioTranscriptionAutoConfiguration`自动配置类，开发者可以轻松地在Spring Boot应用中使用语音转录功能。

```java
@Bean
@ConditionalOnMissingBean
public DashScopeAudioTranscriptionModel dashScopeAudioTranscriptionModel(
        DashScopeConnectionProperties commonProperties,
        DashScopeAudioTranscriptionProperties audioTranscriptionProperties,
        ObjectProvider<RestClient.Builder> restClientBuilderProvider, ResponseErrorHandler responseErrorHandle
) {

    ResolvedConnectionProperties resolved = resolveConnectionProperties(commonProperties,
            audioTranscriptionProperties, "audio.transcription");

    var dashScopeAudioTranscriptionApi = DashScopeAudioTranscriptionApi.builder()
        .baseUrl(resolved.baseUrl())
        .apiKey(new SimpleApiKey(resolved.apiKey()))
        .model(audioTranscriptionProperties.getOptions().getModel())
        .workSpaceId(resolved.workspaceId())
        .restClientBuilder(restClientBuilderProvider.getIfAvailable(RestClient::builder))
        .headers(resolved.headers())
        .responseErrorHandler(responseErrorHandle)
        .build();

    return new DashScopeAudioTranscriptionModel(dashScopeAudioTranscriptionApi,
            audioTranscriptionProperties.getOptions());
}
```

**Section sources**
- [DashScopeAudioTranscriptionAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeAudioTranscriptionAutoConfiguration.java#L1-L89)

## 请求响应处理流程

### 请求构建

请求构建过程中，`DashScopeApi`类负责创建HTTP请求。通过`RestClient`和`WebClient`构建器，可以灵活地配置请求的URL、头部信息、请求体等。

```java
public DashScopeApi(
        String baseUrl,
        ApiKey apiKey,
        MultiValueMap<String, String> header,
        String completionsPath,
        String embeddingsPath,
        String workSpaceId,
        RestClient.Builder restClientBuilder,
        WebClient.Builder webClientBuilder,
        ResponseErrorHandler responseErrorHandler
) {

    this.baseUrl = baseUrl;
    this.apiKey = apiKey;
    this.headers = header;
    this.completionsPath = completionsPath;
    this.embeddingsPath = embeddingsPath;
    this.responseErrorHandler = responseErrorHandler;

    if (StringUtils.hasText(workSpaceId)) {
        this.headers.add(DashScopeApiConstants.HEADER_WORK_SPACE_ID, workSpaceId);
    }

    Consumer<HttpHeaders> finalHeaders = h -> {
        if (!(apiKey instanceof NoopApiKey)) {
            h.setBearerAuth(apiKey.getValue());
        }
        h.setContentType(MediaType.APPLICATION_JSON);
        h.addAll(headers);
    };
}
```

**Section sources**
- [DashScopeApi.java](file://spring-ai-alibaba-core/src/main/java/com/alibaba/cloud/ai/dashscope/api/DashScopeApi.java#L118-L164)

### 响应处理

响应处理过程中，`ResponseErrorHandler`负责处理HTTP响应中的错误。通过`RetryTemplate`，可以实现请求的重试机制，提高系统的容错能力。

```java
@ImportAutoConfiguration(classes = { SpringAiRetryAutoConfiguration.class, RestClientAutoConfiguration.class,
        WebClientAutoConfiguration.class })
```

**Section sources**
- [DashScopeChatAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeChatAutoConfiguration.java#L1-L143)

## 错误恢复策略

### 重试机制

通过`SpringAiRetryAutoConfiguration`，框架提供了内置的重试机制。`RetryTemplate`可以在请求失败时自动重试，直到成功或达到最大重试次数。

```java
@ImportAutoConfiguration(classes = { SpringAiRetryAutoConfiguration.class, RestClientAutoConfiguration.class,
        WebClientAutoConfiguration.class })
```

**Section sources**
- [DashScopeChatAutoConfiguration.java](file://auto-configurations/spring-ai-alibaba-autoconfigure-dashscope/src/main/java/com/alibaba/cloud/ai/autoconfigure/dashscope/DashScopeChatAutoConfiguration.java#L1-L143)

### 降级策略

在请求失败且重试无效的情况下，可以配置降级策略，返回默认值或执行备用逻辑，确保系统的稳定性和可用性。

## 异步调用与流式响应

### 异步调用

通过`WebClient`，可以实现异步调用，提高系统的响应速度和吞吐量。异步调用适用于长时间运行的任务，如大文件上传、复杂计算等。

### 流式响应

流式响应允许客户端逐步接收响应数据，而不是等待整个响应完成。这对于实时数据传输（如视频流、音频流）非常有用。

```java
public Flux<DashScopeAgentResponse> stream(DashScopeAgentRequest request) {
    String uri = "/api/v1/apps/" + request.appId() + "/completion";
    return webClient.post()
        .uri(uri)
        .body(Mono.just(request), DashScopeAgentResponse.class)
        .retrieve()
        .bodyToFlux(DashScopeAgentResponse.class)
        .handle((data, sink) -> {
            sink.next(data);
        });
}
```

**Section sources**
- [DashScopeAgentApi.java](file://spring-ai-alibaba-core/src/main/java/com/alibaba/cloud/ai/dashscope/api/DashScopeAgentApi.java#L70-L97)

## 最佳实践

### 性能优化

- 使用异步调用和流式响应，提高系统的响应速度和吞吐量。
- 配置合理的重试机制，避免因网络波动导致的请求失败。
- 使用缓存机制，减少重复请求，提高性能。

### 配额管理

- 监控API调用次数，避免超出配额限制。
- 配置合理的请求频率，避免触发限流机制。
- 使用批处理请求，减少请求次数，提高效率。

### 降级策略

- 在请求失败时，返回默认值或执行备用逻辑，确保系统的稳定性和可用性。
- 配置熔断机制，当错误率超过阈值时，自动切换到降级模式，避免雪崩效应。