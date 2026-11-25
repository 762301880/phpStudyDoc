## 资料

[deepseek api开放平台](https://platform.deepseek.com/usage)

[deepseek api文档](https://api-docs.deepseek.com/zh-cn/)

## [测试调用api-简单测试](https://api-docs.deepseek.com/zh-cn/)

> curl调用

```bash
curl https://api.deepseek.com/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${DEEPSEEK_API_KEY}" \
  -d '{
        "model": "deepseek-flash",
        "messages": [
          {"role": "system", "content": "You are a helpful assistant."},
          {"role": "user", "content": "Hello!"}
        ],
        "thinking": {"type": "enabled"},
        "reasoning_effort": "high",
        "stream": false
      }'
```

## php框架实现ai

### 重点

市面上 PHP AI 岗，**不是让你训练大模型，是做「AI 应用后端工程师」**

PHP 做 AI，定位是：**把大模型能力接入业务系统，做 RAG 知识库、对话机器人、Agent 工具调用、AI 工作流、业务 AI 接口**，而不是底层模型训练（底层基本都是 Python）。

这类岗位 JD 关键词：Laravel、RAG、向量库、流式输出、Prompt 调优、对接各家大模型 API、Dify 二次开发、文档解析、知识库问答。

### PHP AI 开发到底做什么（岗位真实工作）

1. **对接大模型 API**：通义、DeepSeek、Kimi、OpenAI、Ollama 本地模型，封装统一服务，处理多轮对话、流式 SSE 输出、token 计费、限流、超时、错误重试
2. **RAG 检索增强知识库（最核心）**：文档上传、PDF/Word 解析、文本切片、Embedding 向量化、向量库存储、语义召回、重排、把参考片段塞进 prompt 再问答，解决幻觉、私有资料问答
3. **Agent 智能体开发**：函数 / 工具调用，让大模型调用你 PHP 写的业务接口（查订单、查库存、发消息），循环思考执行
4. **业务系统集成**：把 AI 能力嵌入 CRM、商城、SaaS、客服、后台，和现有用户体系、权限、支付打通
5. **AI 平台二次开发**：Dify、FastGPT 等低代码 AI 平台写插件、扩展接口、私有化部署、权限改造
6. **工程侧：日志、计费、缓存、上下文管理、并发、安全、防注入、prompt 隔离**

### PHP 主流 AI 开发库

**openai-php/client**（最经典）：OpenAI 协议 SDK，兼容国内兼容 OpenAI 格式的模型（DeepSeek、硅基流动等）

```php
composer require openai-php/client
```

简单示例：

```php
$client = OpenAI::client('sk-xxx');
$result = $client->chat()->create([
    'model' => 'gpt-3.5-turbo',
    'messages' => [
        ['role'=>'system','content'=>'你是客服'],
        ['role'=>'user','content'=>'怎么退款']
    ]
]);
echo $result->choices[0]->message->content;
```



### **安装库**

> 注意最低支持**php8.2版本**

```php
composer require openai-php/client guzzlehttp/guzzle
```

**测试框架使用laravel12版本**

### 配置 .env

> 密钥在 deepseek 控制台创建，记得充值，否则 402 余额不足DeepSeek

```php
# .env
DEEPSEEK_API_KEY="sk-xxxxxxxxxxxxxxxxxxxx"
DEEPSEEK_BASE_URI=https://api.deepseek.com/v1
DEEPSEEK_CHAT_MODEL=deepseek-flash
```

**配置config**

`项目\config\openai.php`

```php
<?php
return [
    // DeepSeek 对话
    'deepseek' => [
        'api_key' => env('DEEPSEEK_API_KEY'),
        'base_uri' => env('DEEPSEEK_BASE_URI'),
        'model' => env('DEEPSEEK_CHAT_MODEL'),
    ],
    // Embedding 向量服务（独立）
    'embedding' => [
        'api_key' => env('EMBEDDING_API_KEY'),
        'base_uri' => env('EMBEDDING_BASE_URI'),
        'model' => env('EMBEDDING_MODEL'),
    ]
];

```

