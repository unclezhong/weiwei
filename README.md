<?php
class WeChat {
    private $_appid;
    private $_appsecret;
    private $_token;
    public function __construct($appid, $appsecret, $token) {
        $this->_appid = $appid;
        $this->_appsecret = $appsecret;
        $this->_token = $token;
    }
    function _request($curl, $https=true, $method='get', $data=null) {
        $ch = curl_init();    //初始化
        curl_setopt($ch, CURLOPT_URL, $curl);    //设置访问的url
        curl_setopt($ch, CURLOPT_HEADER, false);    //设置不需要头信息
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);    //只获取页面内容但不输出
        if($https) {
            curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);    //不做服务器认证
            curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);    //不做客服端认证
        }
        if($method == 'post') {
            curl_setopt($ch, CURLOPT_POST, true);
            curl_setopt($ch, CURLOPT_POSTFIELDS, $data);    //post数组
        }
        $str = curl_exec($ch);    //执行访问，返回结果
        curl_close($ch);    //关闭curl,释放资源
        return $str;
    }
    function getAccesstoken() {
        $file = './accesstoken';
        if(file_exists($file)) {
            $content = file_get_contents($file);
            $content = json_decode($content);
            if(time()-filemtime($file)<$content->expires_in);
            return $content->access_token;
        }
        $content = _request('https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=".$this->_appid."&secret=".$this->_appsecret"'); 
        file_put_contents($file, $content);
        $content = json_decode($content);
        return $content->access_token;
    }
}
?>
