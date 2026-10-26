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

**安装库**

> 注意最低支持**php8.2版本**

```php
composer require openai-php/client
```

