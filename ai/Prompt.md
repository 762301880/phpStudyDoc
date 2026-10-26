## Prompt 是什么

**Prompt（提示词）**：你发给大模型的所有文字信息，用来告诉大模型：**它是谁、要做什么、有什么限制、输入是什么**。

大模型本身没有 “主观想法”，它只会根据你发给它的文本，按概率续写文字；**Prompt 就是你给模型下达的指令**。

在 Chat 消息结构里（就是你 `$messages` 数组），分为两类：

1. **System Prompt（系统提示词）**：角色、固定规则、全局约束，一次性告诉模型，作用贯穿本轮对话。
2. **User Prompt（用户提示词）**：用户输入的问题、需求，动态变化。

> 还有一个 Assistant，是模型上一轮返回的回答，多轮对话时放进去维持记忆。

```php
$messages = [
    ['role' => 'system', 'content' => $systemPrompt], // System Prompt
    ['role' => 'user', 'content' => $userMsg],        // User Prompt
];
```

**项目中使用**

```php
$systemPrompt = <<<PROMPT
你是文档问答助手。严格遵守下面全部规则：
1. 你只能使用 <context> 标签内的参考资料回答用户问题。
2. 如果 <context> 中不存在相关信息，直接回复：抱歉，知识库中没有找到相关内容。严禁编造信息、禁止猜测。
3. 回答简洁，优先引用原文要点，不要额外发挥。
4. 不要输出思考过程，直接给出答案。

<context>
{$context}
</context>
PROMPT;
```

