### 最简单的部署方式，以nuxtjs为例子
> 首先是需要一台服务器，安装好nodejs服务环境

- 压缩需要上传的文件，如realworld-nuxtjs.zip

- 登录服务器,在root创建realworld-nuxtjs文件夹,mkdir realworld-nuxtjs

- 项目的当前目录，输入scp ./realworld-nuxtjs.zip root@服务器ip地址:/root/realworld-nuxtjs

- 登录服务器,进入realworld-nuxtjs文件夹解压，unzip realworld-nuxtjs.zip

- 项目的启动命令, npm run start即可，公网IP地址加端口号即可访问

- pm2管理，pm2 start npm -- start

- 停止pm2管理，pm2 stop id 