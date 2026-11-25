## workbuddy官网

https://www.workbuddy.cn/

## 自动签到

### WorkBuddy2API    (自定义docker版本)

项目地址：[`linguo2625469/workbuddy2api-panel`](https://github.com/linguo2625469/workbuddy2api-panel)，**第三方开源网关**，把 WorkBuddy 网页端会话转成**OpenAI 兼容 API 接口**，可以给 ChatBot、OneAPI、LangChain 等直接调用，附带 Web 管理面板

⚠️ 注意：**非腾讯官方项目**，仅个人开源工具，有风控、账号封禁风险，生产谨慎使用。

#### docker-compose构建（推荐服务器部署）

```bash
# 1. 克隆
git clone https://github.com/linguo2625469/workbuddy2api-panel.git
cd workbuddy2api-panel

# 2. 准备配置（compose 挂载此文件，缺失会导致容器启动失败）
cp config.example.json config.json
#    建议编辑 config.json 设置 api_key（或留空由程序自动生成随机密钥）

# 3. 启动（首次会构建镜像，约 1-2 分钟）
docker compose up -d --build

# 4. 健康检查（无可用账号时返回 503）
curl -s http://localhost:7863/healthz
# {"healthy":0,"total":0,"service":"workbuddy2api"}
```

启动后打开 **`http://localhost:7863/panel/`**，用面板「添加账号」完成登录（见下节）。

常用运维命令：

```bash
docker compose logs -f          # 跟踪日志
docker compose restart          # 重启
docker compose down             # 停止并移除容器（数据在 ./auths 与 ./data，不受影响）
```

