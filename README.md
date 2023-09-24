# 基于Excalidraw官方版本16.1，支持中文手写，支持实时协作

## 截图:

![snapshot](./_assets/Snipaste.png)

## 演示网站:

[Live Demo](https://draw.gc-boy.com/)
A live room sample here: [live room](https://draw.gc-boy.com/#room=6e8d4d2a919c1e0dc84c,eFibbJzUwJSzGqo9nshPHQ)

特别感谢[@alswl](https://github.com/alswl)

协作功能需要配合excalidraw-room和excalidraw-storage-backend使用

参考alswl大佬的项目[excalidraw-collaboration](https://github.com/alswl/excalidraw-collaboration)

## Docker构建
1.修改.env.production
  ```
  VITE_APP_HTTP_STORAGE_BACKEND_URL=https://yourdomain/api/v2
  VITE_APP_WS_SERVER_URL=https://yourdomain
  ```
  把yourdomain改成你的域名

2.构建镜像

## 注意事项
1.实时协作需要加密，需要https协议，可以用Nginx反向代理
### 参考配置
```
server {
  server_name yourdomain.com;
  listen 443 ssl http2;
  ssl_certificate /etc/ssl/certs/server.crt;
  ssl_certificate_key /etc/ssl/certs/server.key;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
  listen 80;
  if ($scheme = http) {
    return 301 https://$host:443$request_uri;
  }
  location / {
    proxy_pass http://127.0.0.1:8080;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Host $http_host;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Methods *;
    add_header Access-Control-Allow-Headers *;
    add_header Access-Control-Allow-Credentials true;
    if ($request_method = 'OPTIONS') {
      return 204;
    }
    proxy_redirect http:// https://;
  }
  location /api/ {
    proxy_pass http://127.0.0.1:8081;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Host $http_host;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Methods *;
    add_header Access-Control-Allow-Headers *;
    add_header Access-Control-Allow-Credentials true;
    if ($request_method = 'OPTIONS') {
      return 204;
    }
    proxy_redirect http:// https://;
  }
  location /socket.io/ {
    proxy_pass http://127.0.0.1:8082;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Host $http_host;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Methods *;
    add_header Access-Control-Allow-Headers *;
    add_header Access-Control-Allow-Credentials true;
    if ($request_method = 'OPTIONS') {
      return 204;
    }
    proxy_redirect http:// https://;
  }
}
```

2.协作空间需要保存数据到后端数据库，excalidraw-storage-backend配合redis实现
