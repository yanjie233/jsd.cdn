## 说明

代码遵循MIT协议，请自部署到云服务器使用，支持vercel一键部署


[![Deploy to Vercel](https://vercel.com/button)](https://vercel.com/import/project?template=https://github.com/rcy1314/jsd.cdn/)

⚠️ 如果vercel流量不够请使用云服务器部署，配置文件在下方

## 预览

![预览](https://jsd.cdn.noisework.cn/gh/rcy1314/tuchuang@main/uPic/1725080730151.png)

## 自部署

云服务器：修改所有指向域名为自己的解析域名并修改配置文件：

```
server {
    listen 80;
    listen 443 ssl http2 ;
    listen [::]:443 ssl http2 ;
    listen [::]:80;
    server_name jsd.cdn.noisework.cn www.jsd.cdn.cn; #更改为你自己的解析域名

    root /www/wwwroot/jsd.cdn.cn; #宝塔默认目录，换成你自己的域名或你主页文件的所在目录
    #CERT-APPLY-CHECK--START
    # 用于SSL证书申请时的文件验证相关配置 -- 请勿删除
    include /www/server/panel/vhost/nginx/well-known/jsd.cdn.conf;
    #CERT-APPLY-CHECK--END

    #SSL-START SSL相关配置，路径改为你自己的文件路径或配置SSl证书覆盖
    #error_page 404/404.html;
    ssl_certificate    /www/server/panel/vhost/cert/jsd.cdn.cn/fullchain.pem;
    ssl_certificate_key    /www/server/panel/vhost/cert/jsd.cdn.cn/privkey.pem;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_ciphers EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    add_header Strict-Transport-Security "max-age=31536000";
    error_page 497  https://$host$request_uri;
    location = / {
        index index.html; 
    }

    location / {
        try_files $uri $uri/ @proxy;
    }

    location @proxy {
        proxy_pass https://cdn.jsdelivr.net;
        proxy_set_header Host cdn.jsdelivr.net;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header REMOTE-HOST $remote_addr;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_http_version 1.1;        #Set Nginx Cache

				#缓存设置，可在下面配置要缓存的文件扩展名
        if ( $uri ~* "\.(gif|png|jpg|css|js|webp)$" ) {
            expires 24h; #缓存时间
        }
        proxy_ignore_headers Set-Cookie Cache-Control expires;
        proxy_cache cache_one;
        proxy_cache_key $host$uri$is_args$args;
        proxy_cache_valid 200 304 301 302 1440m;

        proxy_max_temp_file_size 5m; #为了防止文件过大浪费带宽，可设置返回文件大小限制
        proxy_intercept_errors on;
        error_page 413 /custom_413.html;
    }
     #修改为你自己的文件路径
    location = /custom_413.html {
        root /www/wwwroot/jsd.cdn.cn; 
        internal;
        default_type text/html;
        return 200 '文件超过5MB，不予返回！';
    }

}
```

