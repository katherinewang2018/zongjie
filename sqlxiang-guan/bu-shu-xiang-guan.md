##  部分在linux系统部署jfinal项目

### 1.部署redis

[https://www.cnblogs.com/zhaoyan001/p/6143170.html](https://www.cnblogs.com/zhaoyan001/p/6143170.html)

对linux系统中的redis设置访问密码

[https://www.cnblogs.com/herry52/p/5997041.html](https://www.cnblogs.com/herry52/p/5997041.html)

### 2.部署zbus

## 在window下部署多个web项目

[http://www.jfinal.com/share/1298](http://www.jfinal.com/share/1298)

使用ngix安装nginx 并配置nginx.conf，转发访问

```
#A项目 config start    
        server {
        listen       443 ssl;
            server_name  www.A.com;

            ssl_certificate      rs\A.pem;
            ssl_certificate_key  rs\A.key;

           # ssl验证相关配置
            ssl_session_timeout  5m;    #缓存有效期
            ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;    #加密算法
            ssl_protocols TLSv1 TLSv1.1 TLSv1.2;    #安全链接可选的加密协议
            ssl_prefer_server_ciphers on;   #使用服务器端的首选算法
            location / {
                 proxy_pass https://www.A.com:7443;
                }
        }    
        #实现http自动跳转到https
        server {  
            listen  80;  
            server_name www.A.com;      
            rewrite ^(.*)$  https://$host$1 permanent;  
        }      

    #A 项目 config end    

    #B项目 config start    
    server {
        listen       443 ssl;
        server_name  www.B.com;

        ssl_certificate      pl\B.pem;
        ssl_certificate_key  pl\B.key;

       # ssl验证相关配置
        ssl_session_timeout  5m;    #缓存有效期
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;    #加密算法
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;    #安全链接可选的加密协议
        ssl_prefer_server_ciphers on;   #使用服务器端的首选算法
        location / {
             proxy_pass https://www.B.com:6443;
            }
        }    

    server {  
        listen  80;  
        server_name www.B.com;      
        rewrite ^(.*)$  https://$host$1 permanent;  
    }      

    #B项目 config end
```

```
#http项目 config start 
     server {
       listen   80;

        server_name  www.D.com;


        location / {
             proxy_pass http://www.D.com:8093;
            }    

        }    
    #http项目 config end 部分配置
```



