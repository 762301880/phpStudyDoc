# 资料

| 名称          | 地址                           |
| ------------- | ------------------------------ |
| vitepress官网 | [link](https://vitepress.dev/) |

# 搭建

## 🧱 第一步：初始化项目

在你电脑随便找个地方：

```shell
npm create vitepress@latest
```

然后按提示走。

##  第二步：把你现有内容搬进去

你的目录可以直接变成这样：

```
docs/
├─ laravel/
├─ linux/
├─ mysql/
├─ swoole/
├─ thinkphp/
├─ 开发工具/
└─ README.md
```

👉 每个文件夹里的 `.md` 文件直接丢进去就行（不用改）

##  第三步：配置导航栏（关键）

编辑：

```
docs/.vitepress/config.js
```

### 推荐自动化配置导航

#### 安装插件

```shell
npm install vitepress-sidebar -D
```

### 配置config.ts

```shell
import { defineConfig } from 'vitepress'
import { generateSidebar } from 'vitepress-sidebar'

// ✅ 自动分组 + 折叠（不需要 index.md）
function groupSidebar(sidebar: any[]) {
    const result: any[] = []

    sidebar.forEach(item => {
        // 1️⃣ 已经是分组（有 items）
        if (item.items && item.items.length) {
            result.push({
                ...item,
                collapsed: true
            })
            return
        }

        // 2️⃣ 普通页面（有 link）
        if (item.link) {
            const parts = item.link.split('/')
            const folder = parts[1] || 'root'

            let group = result.find(g => g.text === folder)

            if (!group) {
                group = {
                    text: folder,
                    collapsed: true,
                    items: []
                }
                result.push(group)
            }

            group.items.push(item)
        }
    })

    return result
}

// 原始 sidebar
const rawSidebar = generateSidebar({
    documentRootPath: 'docs'
})

export default defineConfig({
    title: "姚留洋的技术博客",

    markdown: {
        lineNumbers: true,

        config(md) {
            const defaultFence = md.renderer.rules.fence

            md.renderer.rules.fence = (tokens, idx, options, env, self) => {
                const token = tokens[idx]
                let lang = (token.info || '').trim().toLowerCase()

                // ✅ 语言映射（关键）
                const langMap: Record<string, string> = {
                    'mysql': 'sql',
                    'postgres': 'sql',
                    'shell': 'bash',
                    'sh': 'bash',
                    'zsh': 'bash',
                    'env': 'bash',
                    'ini': 'bash',
                    'conf': 'bash',
                    'nginx': 'bash',
                    'docker': 'dockerfile',
                    'yml': 'yaml',
                    'php+html': 'php'
                }

                if (langMap[lang]) {
                    token.info = langMap[lang]
                }

                try {
                    return defaultFence(tokens, idx, options, env, self)
                } catch (e) {
                    // ✅ 兜底防炸
                    token.info = 'text'
                    return defaultFence(tokens, idx, options, env, self)
                }
            }
        }
    },

    themeConfig: {
        sidebar: groupSidebar(rawSidebar)
    }
})
```

## ▶️ 第四步：运行预览

```shell
npm install   # 安装依赖
npm run dev   # 运行项目 
```

打开浏览器：

```
http://localhost:5173
```

你就能看到博客了 🎉

# 拓展

##  配置docker启动

**docker-compose.yml**

```shell
version: '3'

services:
  vitepress:
    image: node:20-alpine
    container_name: vitepress-blog
    working_dir: /app
    volumes:
      - ./:/app
    ports:
      - "5173:5173"
    command: sh -c "rm -rf node_modules package-lock.json && npm install && npm run dev -- --host 0.0.0.0"
```

