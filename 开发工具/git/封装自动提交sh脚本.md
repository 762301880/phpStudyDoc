##  最新版

> git_auto_push.sh

```shell
#!/bin/bash
# 定义颜色代码
RED='\033[31m'
GREEN='\033[32m'
YELLOW='\033[33m'
BLUE='\033[34m'
BOLD='\033[1m'
RESET='\033[0m'

# 检查是否有未提交的变更
if [ -z "$(git status --porcelain)" ]; then
    echo -e "${RED}⚠️ 没有检测到代码变更，终止执行${RESET}"
    exit 1
fi

COMMIT_MESSAGE="${1:-change_code_push}"

echo -e "${GREEN}====================== 代码提交流程开始 ======================${RESET}"

# 添加文件到暂存区
echo -e "${BLUE}步骤1/3: 添加所有文件到暂存区...${RESET}"
if ! git add . ; then
    echo -e "${RED}✗ 文件添加失败${RESET}"
    exit 1
fi
echo -e "${GREEN}✓ 文件添加成功${RESET}"

# 提交更改
echo -e "\n${BLUE}步骤2/3: 提交代码变更 [${YELLOW}提交信息: ${BOLD}${COMMIT_MESSAGE}${RESET}${BLUE}]${RESET}"
if ! git commit -m "${COMMIT_MESSAGE}" ; then
    echo -e "${RED}✗ 代码提交失败${RESET}"
    exit 1
fi
echo -e "${GREEN}✓ 代码提交成功${RESET}"

# 推送到主仓库
echo -e "\n${BLUE}步骤3/3: 推送代码到主仓库...${RESET}"
if ! git push ; then
    echo -e "${RED}✗ 主仓库推送失败${RESET}"
    exit 1
fi
echo -e "${GREEN}✓ 代码推送至主仓库成功${RESET}"

# 推送到GitCode镜像仓库
echo -e "\n${BLUE}[附加操作] 推送代码到GitCode镜像仓库...${RESET}"
if ! git push gitcode ; then
    echo -e "${RED}✗ GitCode推送失败${RESET}"
    exit 1
fi
echo -e "${GREEN}✓ 代码推送至GitCode成功${RESET}"

echo -e "\n${GREEN}====================== 所有操作已完成 ======================${RESET}"
echo -e "本次提交信息: ${YELLOW}${BOLD}${COMMIT_MESSAGE}${RESET}"
echo -e "${GREEN}===========================================================${RESET}"

```

## 废弃版

```shell
#
#COMMIT_MESSAGE=""
#if [ -z "$1" ]; then
#    COMMIT_MESSAGE="change_code_push"
#    else
#   COMMIT_MESSAGE=$1
#fi
#echo "开始上传本次代码提交:提交信息为:{$COMMIT_MESSAGE}"
#git add .
#git commit -m $COMMIT_MESSAGE
#git push
#
#echo "-----------------------"
#echo "开始上传本次代码提交到gitcode"
#git push gitcode
#
#echo "上传完毕"
```

