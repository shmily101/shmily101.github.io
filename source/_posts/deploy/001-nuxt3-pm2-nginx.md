---
title: Nuxt3 应用部署全流程
date: 2025-01-15 18:41:23
tags: 
  - nginx
  - nuxt3
  - pm2
categories:
  - deploy
---

# 前言

最近需要把 nuxt3 的项目部署到云服务器上并绑定域名，使用的是 nuxt3 的 ssr 部署，记录一下部署的全流程。

# ssr 部署和静态部署的区别

静态部署 spa（比如 vue），就是将项目打包后的 html, css, js 文件直接上传到服务器里，直接进行访问，很简单。但是这样部署的项目对 seo 不友好，在页面没有加载之前，浏览器拿不到页面内容，没办法让搜索引擎爬取到。

nuxt 支持 ssr，这样部署后所有的 dom 元素都能被检索到，因为 html, css, js 文件是由服务器组合完再直接把 html 文件传输给浏览器的。所以我们部署 ssr 的时候需要启动一个这样的服务。

# ssr 部署步骤

## nuxt 项目打包

执行

```
nuxt build
```
对项目进行打包

打包后的产物默认会在 `.output` 文件夹下。

{% asset_img package.png %}

## 服务器环境配置 & 文件上传

服务器需要安装好 `node`、`npm`以及`nginx`，安装方式不赘述了，可以参考文末附的文章地址；

之后需要将 `.output` 文件夹上传到服务器中，我用的是腾讯云，可以直接在 orcaterm 里面上传文件+终端操作，还挺方便的，我这里选择的是上传到`/root`目录（后面发现尽量不要上传到这里）。

{% asset_img output.png %}

## 服务启动

因为咱们是 ssr 渲染，所以必须保持一个服务端的运行。正常可以直接在服务器中执行 `nuxt start` 给他跑起来，但是这样服务并不能持续运行，关闭终端窗口之后项目也终止运行了。为了持续保存任务，我们需要 pm2 来实现进程管理。

### pm2 安装

```
npm install pm2 -g
#或者
pnpm install pm2 -g
```

### 通过 pm2 启动+保存应用

[PM2 操作参考：PM2中文网](https://pm2.fenxianglu.cn/docs/start)

对于 nuxt 应用，pm2 通过 `ecosystem.config.js` 文件来启动。我们在根目录创建这个文件，并写入配置：

```
module.exports = {
  apps: [
    {
      name: 'lingnovatech', 
      exec_mode: 'cluster',
      instances: 'max', 
      port: '3000',
      script: './.output/server/index.mjs',
      args: 'start',
      error_file: './err.log', // 错误日志存放地址
      out_file: './out.log', // 输出日志存放地址
      env: {
        HOST: "0.0.0.0"
      }
    }
  ]
}
```

error_file 和 out_file 可以定义错误日志和输出日志的存放地址，对于找到部署中出现的错误非常有帮助！env 可以定义环境变量，我代码中的环境变量一直没生效，只能每次在 shell 里先 export 一下，在这里配置后就方便多了。

至于这个`HOST: "0.0.0.0"`的配置，是因为之前 pm2 直接启动后默认只监听了 `127.0.0.1` 这个地址，只监听 `127.0.0.1` 时，外部无法访问。

定义完`ecosystem.config.js` 文件后，执行

```
pm2 start ecosystem.config.cjs
```

即可启动项目。如果遇到问题，在 err.log 里查找并解决。

如果项目启动，访问服务器公网ip + 端口号没问题，这时候还需要保存服务，让进程一直存在。执行：

```
pm2 save
```

## 配置 nginx

执行到这一步，我们服务已经部署完了，但目前也还只能访问公网ip + 端口号。我们可以通过 nginx 来做代理转发，实现通过域名直接访问。

nginx 的所有安装+配置步骤可以参考这篇文章，非常详细：[如何在CVM上安装Nginx](https://cloud.tencent.com/developer/article/1160474)

但要注意，文章中需要我们创建一个 `index.html` 入口文件，且配置里需要指向这个 html 入口文件。我们是不需要这一步的，这里是 spa 的配置方法，我们只需要使用 nginx 做转发，将我们的域名代理转发到我们上一步启动的服务上，即公网ip+端口号上面。（域名解析等等需要提前在腾讯云配好，这部分就不详细展开了）

所以我们的配置文件长这样即可：

```
server {
    listen       80;
    listen       [::]:80;
    server_name  lingnovatech.com www.lingnovatech.com;

    # 静态资源（nuxt3 打包在 .output/public 下）
    location /_nuxt/ {
        alias /root/lingnovatech/.output/public/_nuxt/;
        access_log off;
        expires 7d;
    }
    location /img/ {
        alias /root/lingnovatech/.output/public/img/;
        access_log off;
        expires 7d;
    }
    location /svg/ {
        alias /root/lingnovatech/.output/public/svg/;
        access_log off;
        expires 7d;
    }
    # 其他静态资源目录，可按需添加
    # location /favicon.ico { alias /root/lingnovatech/.output/public/favicon.ico; }

    # 其余全部转发给 nuxt3 服务
    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /proxy/ {
        proxy_pass http://127.0.0.1:3000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

这里对静态资源也做了代理，是因为如果不代理的话，通过 nginx 转发的一切请求都会代理到了 `http://lingnovatech.com:3000/home`，这会导致静态资源（如 `/_nuxt/*` 和 `/img/*`）也被错误地代理到 `/home` 路径，实际 Nuxt3 服务并没有 `/home/_nuxt/xxx` 这样的路径，所以返回 404。

所以我采用静态资源（如 /_nuxt/、/img/ 等）直接由 nginx 本地读取，其余请求反向代理给 Nuxt3 服务的方法，并设置缓存，可以解决静态资源 404 问题，同时动态路由和 API 仍然由 Nuxt3 服务处理。

{% note no-icon %}
补充另一个问题：静态资源做了直接读取本地的处理后，进入页面还是所有静态资源报错 403。实际上这是由于文件和目录权限导致的，一般 nginx 以 www-data、nginx 或 nobody 用户运行，我的的静态资源目录 `/root/lingnovatech/.output/public` 默认只有 root 用户有权限，nginx 的普通运行用户没有读取权限，导致 403，需要给所有用户赋予读取权限。这也是我前面说最好不要放在 `/root`文件夹下，生产环境建议将静态资源目录挪到非 /root 目录（如 /srv/ 或 /var/www/），避免直接暴露 root 目录，提升安全性。
{% endnote %}

做完这一步，访问 `lingnovatech.com`或者`www.lingnovatech.com`就可以正常访问到官网啦✌️。