

# 说明

> 本接口采用的是[smms](https://sm.ms/)提供的图床接口



```shell
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UploadImageController extends Controller
{
    public function upload(Request $request)
    {
        $file = $request->file('image');
        if ($file == null) {
            return response()->json(['code' => '501', 'message' => '文件不存在']);
        }
        $file_name = uniqid() . $request->file('image')->getClientOriginalName();//设置唯一的上传图片
        $path = public_path('/');//设置上传路径
        $absolute_path_file = $path . '/' . $file_name;//图片全路径,绝对路径
        $file->move($path, $file_name);//转移文件到public目录下
        //判断文件是否存在
        if (!file_exists($absolute_path_file)) {
            return response()->json([
                'msg' => '文件没有上传成功',
                'data' => [],
                'code' => '5000'
            ]);
        }
        $data = [
            'smfile' => new \CURLFile(realpath($file_name), '', 'a.jpg'),
            'format' => 'json'
        ];
        $headers = array(
            'Content-Type:multipart/form-data',
            "Authorization-Type:{$this->getApiToken()}",
            'Transfer-Encoding:Content-Length:' . (filesize($absolute_path_file) + 198)
        );
        $url = config('smms.url') . 'upload';
        $ret = json_decode($this->request_post($url, $data, $headers), true);
        unlink($absolute_path_file); //上传完成之后删除临时文件
        if ($ret['code'] == '200' || $ret['code'] == 'success') {
            return response()->json(['code' => '200', 'message' => '上传成功', 'data' => $ret]);
        }
        return response()->json(['code' => '501', 'message' => '上传失败', 'data' => $ret]);
    }

    /**
     * curl请求
     * @param string $url 地址
     * @param array $post_data 请求参数
     * @param array $post_header header 值
     * @return bool|string
     */
    public function request_post($url = '', $post_data = array(), $post_header = array())
    {
        if (empty($url) || empty($post_data)) {
            return false;
        }
        $postUrl = $url;
        $curlPost = $post_data;
        $headers = $post_header;
        $ch = curl_init();//初始化curl
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE); //禁止 cURL 验证对等证书
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE); //是否检测服务器的域名与证书上的是否一致
        curl_setopt($ch, CURLOPT_URL, $postUrl);//抓取指定网页
        if ($headers != []) {
            curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
        }
        curl_setopt($ch, CURLOPT_HEADER, 0);//设置header param:1 f
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);//要求结果为字符串且输出到屏幕上
        curl_setopt($ch, CURLOPT_POST, 1);//post提交方式
        curl_setopt($ch, CURLOPT_POSTFIELDS, $curlPost);//提交的参数
        $data = curl_exec($ch);//运行curl
        curl_close($ch);
        return $data;
    }

    /**
     * 获取自己的apiToken
     */
    public function getApiToken()
    {
        $url = config('smms.url') . 'token';
        $data = [
            'username' => config('smms.api_access.username'),
            'password' => config('smms.api_access.password'),
        ];
        $ret = json_decode($this->request_post($url, $data), true);
        if ($ret['code'] == 'success') {
            return $ret['data']['token'];
        }
        return false;
    }

}

```

