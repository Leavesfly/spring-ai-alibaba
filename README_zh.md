# [Spring AI Alibaba](https://java2ai.com)

[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)
[![CI Status](https://github.com/alibaba/spring-ai-alibaba/workflows/%F0%9F%9B%A0%EF%B8%8F%20Build%20and%20Test/badge.svg)](https://github.com/alibaba/spring-ai-alibaba/actions?query=workflow%3A%22%F0%9F%9B%A0%EF%B8%8F+Build+and+Test%22)
[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/alibaba/spring-ai-alibaba)
[![Maven central](https://maven-badges.herokuapp.com/maven-central/com.alibaba.cloud.ai/spring-ai-alibaba/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.alibaba.cloud.ai/spring-ai-alibaba)
<img alt="gitleaks badge" src="https://img.shields.io/badge/protected%20by-gitleaks-blue">

[English Version](./README.md)

[Spring AI Alibaba](https://java2ai.com) 是一个智能体 AI 框架，用于构建聊天机器人、工作流和多智能体应用程序。

## 核心特性

<p align="center">
    <img src="./docs/imgs/spring-ai-alibaba-architecture.png" alt="architecture" style="max-width: 740px; height: 508px" /> 
</p>

Spring AI Alibaba 提供以下核心能力，帮助开发者快速构建聊天机器人、工作流或多智能体应用：

1. **基于 Graph 的多智能体框架**，通过 Spring AI Alibaba Graph，开发者可以轻松快速构建工作流和多智能体应用程序。Graph 代码可以从 Dify DSL 生成，并以可视化方式进行调试。

2. **企业级 AI 生态系统集成，将智能体从演示带到生产环境。** Spring AI Alibaba 支持与阿里云百炼平台集成，提供 LLM 模型服务和 RAG 知识解决方案；支持与 ARMS 和 Langfuse 等 AI 观测产品的无缝集成；支持企业级 MCP 集成，包括用于 MCP 发现和路由的 Nacos MCP Registry 等。

3. **计划-执行智能体产品和平台。**
   * JManus：基于 Spring AI Alibaba 的 Manus 实现，支持精细计划调整和计划重用。
   * DeepResearch：基于 Spring AI Alibaba 的研究和报告智能体，具有强大的工具支持，如搜索引擎、网络爬虫、Python 和 MCP 服务。

## 快速开始

要快速开始使用 Spring AI Alibaba，请在您的 Java 项目中添加 'spring-ai-alibaba-starter-dashscope' 依赖。

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>com.alibaba.cloud.ai</groupId>
      <artifactId>spring-ai-alibaba-bom</artifactId>
      <version>1.0.0.3</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>

<dependencies>
  <dependency>
    <groupId>com.alibaba.cloud.ai</groupId>
    <artifactId>spring-ai-alibaba-starter-dashscope</artifactId>
  </dependency>
</dependencies>
```

请查看我们官方网站上的[快速开始](https://java2ai.com/docs/1.0.0.3/get-started/chatbot)了解更多详细信息。更多起动器包括 `spring-ai-alibaba-graph-core`、`spring-ai-alibaba-starter-nl2sql`、`spring-ai-alibaba-starter-nacos-mcp-client` 等，请参考官方网站文档。

> 注意！
> 1. 需要 JDK 17+。
> 2. 如果存在任何 `spring-ai` 依赖问题，请参考[FAQ 页面](https://java2ai.com/docs/1.0.0.3/faq)了解如何配置 `spring-milestones` Maven 仓库。

### Playground 和示例

社区开发了一个包含完整前端 UI 和后端实现的 [Playground](https://github.com/springaialibaba/spring-ai-alibaba-examples/tree/main/spring-ai-alibaba-playground) 智能体。Playground 后端使用 Spring AI Alibaba 开发，让用户快速了解所有核心框架功能，如聊天机器人、多轮对话、图像生成、多模态、工具调用、MCP 和 RAG。

<p align="center">
    <img src="./docs/imgs/playground.png" alt="PlayGround" style="max-width: 949px; height: 537px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);" /> 
</p>

您可以[在本地部署 Playground 示例](https://github.com/springaialibaba/spring-ai-alibaba-examples)并通过浏览器访问体验，或者复制源代码并根据自己的业务需求进行调整，以更快地构建自己的 AI 应用程序。

更多示例请参考我们的官方示例仓库：[https://github.com/springaialibaba/spring-ai-alibaba-examples](https://github.com/springaialibaba/spring-ai-alibaba-examples)

## Spring AI Alibaba Graph 多智能体框架

Spring AI Alibaba Graph 使开发者能够实现工作流和多智能体应用编排。其核心设计主要来源于 LangGraph，我们增加了丰富的预构建节点集并简化了 Graph State 定义，使开发者能够更好地与低代码平台集成并编写流行的多智能体模式应用程序。

核心特性：

+ 工作流，内置工作流节点，与主流低代码平台对齐；
+ 多智能体，内置 ReAct Agent、Supervisor 等模式；
+ 原生流式支持；
+ 人机协作，等待人类确认，修改状态并恢复执行；
+ 记忆和持久存储；
+ Graph 状态快照；
+ 嵌套和并行图；
+ PlantUML 和 Mermaid 格式导出。

## 企业级 AI 生态系统集成

为了将智能体从演示带到生产环境，开发者和组织面临许多挑战，从评估、追踪、MCP 集成、提示管理到 token 速率限制等。Spring AI Alibaba 作为服务企业智能体开发的企业解决方案，通过与 Nacos MCP Registry、Higress AI 网关、阿里云 ARMS、阿里云向量存储、阿里云百炼平台等的集成，提供了深度解决方案。

<p align="center">   
    <img src="https://img.alicdn.com/imgextra/i2/O1CN01sON0wZ21yKROGt2SJ_!!6000000007053-2-tps-5440-2928.png" alt="spring-ai-alibaba-architecture" style="max-width: 700px; height: 400px"/> 
</p>

1. **分布式 MCP 发现和代理：** 支持基于 Nacos MCP Registry 的分布式 MCP 服务器发现和负载均衡。通过 Spring AI Alibaba MCP Gateway 和 Higress 零代码更改将 HTTP 和 Dubbo 服务转换为 MCP 服务器；

2. **Higress LLM 模型代理：** Higress 作为 LLM 代理，`spring-ai-starter-model-openai` 适配器可以利用统一的 Higress OpenAI 模型代理 API；

3. **更好且简单的数据集成：**
	- a. 百炼 RAG 集成。利用百炼平台在数据过滤、分块和向量化方面的出色性能，同时使用 Spring AI Alibaba 进行 RAG 检索；
	- b. 百炼 ChatBI 集成。基于百炼 ChatBI 构建的 Spring AI Alibaba Nl2SQL，完全开源，可以基于自然语言查询生成 SQL。

4. **观测和评估平台：** 借助 Spring AI 的 SDK 原生检测，可以通过报告到 OpenTelemetry 兼容平台（如 Langfuse 和阿里云 ARMS）来实现观测和评估。

## 智能体产品和平台

### JManus

Manus 的出现让具有通用智能体自动规划-执行各种任务能力的人们有了无限的想象空间。它预期在解决开放性问题方面表现出色，可以在日常生活、工作和其他场景中有广泛的应用。

JManus 不仅仅是 Spring AI Alibaba 版本的 Manus 复制品，它还被设计为一个平台，可以帮助开发者构建自己针对特定业务场景的精调智能体。企业级智能体的典型特征是确定性，这意味着我们需要定制化的工具和子智能体，以及稳定和确定性的规划和流程。因此，我们希望 JManus 能够成为一个智能体开发平台，让用户能够以最直观和低成本的方式构建自己的特定领域智能体实现。

<p align="center">
    <img src="./docs/imgs/jmanus.png" alt="jmanus" style="max-width: 749px; height: 467px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);" /> 
</p>

### DeepResearch

Spring AI Alibaba DeepResearch 是基于 Spring AI Alibaba Graph 开发的深度研究智能体，包括完整的前端 Web UI（开发中）和后端实现。DeepResearch 可以借助大模型和一系列精心设计的工具（如 Web 搜索、爬虫、Python 脚本引擎等）帮助用户完成各种深度研究报告。

<p align="center">
    <img src="./docs/imgs/deepresearch.png" alt="Deep Research" style="max-width: 770px; height: 850px">
</p>

## 贡献指南

请参考[贡献指南](./CONTRIBUTING.md)了解如何参与 Spring AI Alibaba 的开发。

## 联系我们

* 钉钉群，搜索 `94405033092` 并加入。
* 微信群，扫描下方二维码关注我们。

<img src="./docs/imgs/wechat-account.png" alt="Deep Research" style="max-width: 200px; height: 200px;">

## 致谢

本项目的一些想法和代码受到以下项目的启发或在其基础上重写。非常感谢那些创建和开源这些项目的人。

* [Spring AI](https://github.com/spring-projects/spring-ai)，一个用于开发 AI 应用程序的 Spring 友好 API 和抽象，基于 Apache License 2.0 许可。
* [Langgraph](https://github.com/langchain-ai/langgraph)，一个使用 LLM 构建有状态、多参与者应用程序的库，用于创建智能体和多智能体工作流，基于 MIT 许可证。
* [Langgraph4J](https://github.com/bsorrentino/langgraph4j)，来自 [LangChain AI 项目](https://github.com/langchain-ai)的原始 [LangGraph](https://github.com/langchain-ai/langgraph) 的 Java 移植版本。