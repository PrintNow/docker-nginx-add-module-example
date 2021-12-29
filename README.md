# Docker Nginx 添加自定义的模块例子

- 官方文档说明：https://github.com/nginxinc/docker-nginx/tree/master/modules
- 详细说明请看这里：https://nowtime.cc/docker/1636.html

已经打好的镜像（包含 `nginx-http-concat` 模块）：https://hub.docker.com/r/shine09/nginx
> 拉取镜像：`docker pull shine09/nginx:1.21.4-with-http-concat`

## 配置说明

**docker-compose.yaml**

```yaml
version: "3.3"
services:
  web:
    build:
      context: ./modules/
      args:
        # 如果要添加多个模块，用空格隔开即可
        ENABLED_MODULES: http-concat
    # 自定义镜像名字，可以自己修改
    image: shine09/nginx:1.21.4-with-http-concat
```

**Dockerfile**

> 主要我在 `Dockerfile` 里面最后一步的时候添加了：
>
> `sed -i '1i\load_module modules/ngx_http_concat_module.so;' /etc/nginx/nginx.conf`
>
> 意思是导入 build 是产生的 `so` 库文件，这样 `nginx-http-concat` 就添加进 Nginx 了

## 构建

> 注意：需要确保自己"网络畅通"，才能成功构建

```shell
docker-compose build
```

## 杂七杂八

```shell
➜  docker-nginx-add-module-example git:(master) ✗ docker history shine09/nginx:1.21.4-with-http-concat
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
ef2dfafcb724   3 minutes ago   RUN /bin/sh -c set -ex     && apt update    …   1.25MB    buildkit.dockerfile.v0
<missing>      3 minutes ago   COPY /tmp/packages /tmp/packages # buildkit     211kB     buildkit.dockerfile.v0
<missing>      7 days ago      /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B        
<missing>      7 days ago      /bin/sh -c #(nop)  STOPSIGNAL SIGQUIT           0B        
<missing>      7 days ago      /bin/sh -c #(nop)  EXPOSE 80                    0B        
<missing>      7 days ago      /bin/sh -c #(nop)  ENTRYPOINT ["/docker-entr…   0B        
<missing>      7 days ago      /bin/sh -c #(nop) COPY file:09a214a3e07c919a…   4.61kB    
<missing>      7 days ago      /bin/sh -c #(nop) COPY file:0fd5fca330dcd6a7…   1.04kB    
<missing>      7 days ago      /bin/sh -c #(nop) COPY file:0b866ff3fc1ef5b0…   1.96kB    
<missing>      7 days ago      /bin/sh -c #(nop) COPY file:65504f71f5855ca0…   1.2kB     
<missing>      7 days ago      /bin/sh -c set -x     && addgroup --system -…   61.1MB    
<missing>      7 days ago      /bin/sh -c #(nop)  ENV PKG_RELEASE=1~bullseye   0B        
<missing>      7 days ago      /bin/sh -c #(nop)  ENV NJS_VERSION=0.7.0        0B        
<missing>      7 days ago      /bin/sh -c #(nop)  ENV NGINX_VERSION=1.21.4     0B        
<missing>      7 days ago      /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B        
<missing>      7 days ago      /bin/sh -c #(nop)  CMD ["bash"]                 0B        
<missing>      7 days ago      /bin/sh -c #(nop) ADD file:09675d11695f65c55…   80.4MB 
```