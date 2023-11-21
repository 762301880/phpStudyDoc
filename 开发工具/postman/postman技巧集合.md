## 提交json数据的时候不支持注释

> **postman**开发的时候最不爽的一个地方就是提交json数据的时候写了注释无法过滤，后端也接收不到，时间长了
>
> 调试结构自己都不知道这个接口参数是干嘛的，很难受 参数所以百度查询了一下解决方案

**参考资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 参考博客 | [link](https://learnku.com/articles/66387) [link](https://github.com/fkei/JSON.minify/blob/master/minify.json.js) [link](https://doc.houdunren.com/soft/9%20postman.html#postman) |

**Pre-req.**中添加代码

```shell
//  去除json参数注释方法
GlobalJsonMinify = function (json) {

    var tokenizer = /"|(\/\*)|(\*\/)|(\/\/)|\n|\r|\[|]/g,
        in_string = false,
        in_multiline_comment = false,
        in_singleline_comment = false,
        tmp, tmp2, new_str = [], ns = 0, from = 0, lc, rc,
        prevFrom
    ;

    tokenizer.lastIndex = 0;

    while ( tmp = tokenizer.exec(json) ) {
        lc = RegExp.leftContext;
        rc = RegExp.rightContext;
        if (!in_multiline_comment && !in_singleline_comment) {
            tmp2 = lc.substring(from);
            if (!in_string) {
                tmp2 = tmp2.replace(/(\n|\r|\s)*/g,"");
            }
            new_str[ns++] = tmp2;
        }
        prevFrom = from;
        from = tokenizer.lastIndex;

        // found a " character, and we're not currently in
        // a comment? check for previous `\` escaping immediately
        // leftward adjacent to this match
        if (tmp[0] === "\"" && !in_multiline_comment && !in_singleline_comment) {
            // limit left-context matching to only go back
            // to the position of the last token match
            //
            // see: https://github.com/getify/JSON.minify/issues/64
            lc.lastIndex = prevFrom;

            // perform leftward adjacent escaping match
            tmp2 = lc.match(/(\\)*$/);
            // start of string with ", or unescaped " character found to end string?
            if (!in_string || !tmp2 || (tmp2[0].length % 2) === 0) {
                in_string = !in_string;
            }
            from--; // include " character in next catch
            rc = json.substring(from);
        }
        else if (tmp[0] === "/*" && !in_string && !in_multiline_comment && !in_singleline_comment) {
            in_multiline_comment = true;
        }
        else if (tmp[0] === "*/" && !in_string && in_multiline_comment && !in_singleline_comment) {
            in_multiline_comment = false;
        }
        else if (tmp[0] === "//" && !in_string && !in_multiline_comment && !in_singleline_comment) {
            in_singleline_comment = true;
        }
        else if ((tmp[0] === "\n" || tmp[0] === "\r") && !in_string && !in_multiline_comment && in_singleline_comment) {
            in_singleline_comment = false;
        }
        else if (!in_multiline_comment && !in_singleline_comment && !(/\n|\r|\s/.test(tmp[0]))) {
            new_str[ns++] = tmp[0];
        }
    }
    new_str[ns++] = rc;
    return new_str.join("");
};

pm.request.body.raw = GlobalJsonMinify(pm.request.body.raw)
```

## postman添加cookie

> 有这样一种场景 不分离的项目要如何调试接口呢  

**参考**

| 名称     | 地址                                           |
| -------- | ---------------------------------------------- |
| 参考博客 | [link](https://zhuanlan.zhihu.com/p/583171655) |

![image-20230828165936809](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230828165936809.png)

## **设置变量**

```shell
// 响应结果
const response = JSON.parse(responseBody)
//在控制台打印结果（在postman软件底部 Console 标签查看）
console.log(response);
 //储存到环境变量token
 pm.environment.set("api_token",response.data.token);
```

![image-20230828171011218](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230828171011218.png)

## 发送ajax请求

**资料**

| 名称 | 地址                                                  |
| ---- | ----------------------------------------------------- |
| 博客 | [link](https://www.cnblogs.com/jcydd/p/11454571.html) |

> 请在**Headers**中添加      X-Requested-With**:**XMLHttpRequest

![image-20230829152700546](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230829152700546.png)