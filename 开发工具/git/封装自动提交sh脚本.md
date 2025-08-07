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



## 推送成功后执行远程同步脚本
#echo -e "\n${BLUE}[附加操作] 推送成功，执行远程服务器同步操作...${RESET}"
#
## 请根据你的路径替换为实际的远程脚本路径
#REMOTE_SYNC_SCRIPT="./remote_git_pull.sh"
#if [ -f "$REMOTE_SYNC_SCRIPT" ]; then
#    bash "$REMOTE_SYNC_SCRIPT"
#    if [ $? -ne 0 ]; then
#        echo -e "${RED}✗ 远程同步脚本执行失败${RESET}"
#        exit 1
#    fi
#else
#    echo -e "${RED}✗ 找不到远程同步脚本：${REMOTE_SYNC_SCRIPT}${RESET}"
#    exit 1
#fi





## 推送到GitCode镜像仓库
#echo -e "\n${BLUE}[附加操作] 推送代码到GitCode镜像仓库...${RESET}"
#if ! git push gitcode ; then
#    echo -e "${RED}✗ GitCode推送失败${RESET}"
#    exit 1
#fi
#echo -e "${GREEN}✓ 代码推送至GitCode成功${RESET}"

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

## 补充

### 代码中注释的拉取远程代码ci钩子 

> 由于我的服务器是**2核2G的服务器**  搭建一个jenkins 太占用资源了**大概需要500M 内存左右**  对于我这种穷逼玩家来说
>
> 实在是不够看  还是本地提交再多构建一个脚本把 方便多了
>
> **注意事项:**
>
> 必须把本地密钥**.ssh\id_rsa.pub** 提交到远程服务器的**/root/.ssh/authorized_keys** 并开启 私钥登录 才可以本地无密码执行  [参考](https://gitee.com/yao_liu_yang/phpStudyDoc/blob/main/linux/centOs/linux%E5%88%9B%E5%BB%BA%E6%96%B0%E7%94%A8%E6%88%B7%E5%B9%B6%E9%87%87%E7%94%A8ssh%E5%AF%86%E5%8C%99%E7%99%BB%E5%BD%95.md)

```sh
#remote_git_pull.sh

#!/bin/bash
echo "开始执行代码同步远程服务器钩子：自动同步远程服务器..."

# 远程服务器信息（替换为你的实际信息） 必须要用本地的密钥
# 后期优化 记录失败日志文件
REMOTE_USER=root
REMOTE_HOST=113.45.29.83
REMOTE_REPO_PATH=/work/php_projects/laravel_study

# 通过 SSH 登录远程服务器，执行 git pull
ssh ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_REPO_PATH} && git checkout . && git pull origin develop && docker exec php7.4-fpm supervisorctl restart all"

# 检查命令是否执行成功
if [ $? -eq 0 ]; then
    echo "远程服务器拉取成功！"
else
    echo "远程服务器拉取失败，请手动检查！"
    exit 1
fi
```

