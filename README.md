# nginx ssl证书自动续期
1、申请一个zerossl的账号，可以免费申请证书
https://app.zerossl.com/

2、点击Developer——generate创建客户端凭证，记录凭证


3、在需要用到的游戏服务器安装acme.sh实现自动续期
官方文档：https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E
在需要ssl证书的那台游戏服务器安装acme.sh
```
curl https://get.acme.sh | sh
```

4、添加凭据
```
acme.sh --register-account --server zerossl --eab-kid "xxx刚刚申请的kid" --eab-hmac-key "xxxxx刚刚申请的key"
```

5、在需要证书的那个域名的nginx配置文件添加http验证实现自动续期
```
location /.well-known/acme-challenge/ {
        root /data/www/certbot;
        try_files $uri =404;
    }
```

6、生成证书
```
acme.sh --issue -d 你的域名 --webroot /data/www/certbot/
```

7、安装证书
安装证书的时候切记不能用cp或者下载需要使用--install-cert命令
```
acme.sh --install-cert -d 你的域名 --key-file /你放证书的目录/你的域名.key --fullchain-file /你证书目录/你的域名.cer --reloadcmd "nginx -s reload"
如果你需要--reloadcmd "nginx -s reload"起作用需要把nginx加到环境变量
```
