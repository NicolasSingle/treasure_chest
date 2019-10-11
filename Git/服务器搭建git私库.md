#### Git私库

* 登录服务器(Xshell 5)

* 安装git
    ```bash
    git --version // 如无，则安装
    yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
    yum install -y git
    ```
* 创建用户并配置其仓库
    ```bash
    useradd git 增加新用户
    passwd git 设置密码
    su git
    cd /home/git
    mkdir -p projects/blog
    mkdir repos && cd repos
    git init --bare blog.git // 裸库, 工作用仓库, 实现数据共享和同步，不保存文件，只保存历史提交的版本信息
    cd blog.git/hooks // ls 查看git的所有钩子
    vi post-receive // git命令执行完后, 用调用此钩子, 用于checkout工作目录供nginx访问

    ```
    ```bash
    #!/bin/sh
    git --work-tree=/home/git/projects/blog --git-dir=/home/git/repos/blog.git checkout -f
    ```
    ```
    修改权限
    chmod +x post-receive
    exit 退出
    chown -R git:git /home/git/repos/blog.git  // blog.git控制权交给用户git
    ```

* 测试是否可用

    ```
    git clone git@server_ip:/home/git/repos/blog.git
    ```
* ssh (本地电脑操作)
    ```
    ssh-keygen -t rsa // 生成公钥私钥
    ssh-copy-id -i C:/Users/yourname/.ssh/id_rsa.pub git@server_ip // 将公钥copy给服务器

    ssh git@server_ip // 查看能否链接 git bash也会有提示
    ```

* 禁用 git 用户的 shell 登录权限，从而只能用 git clone，git push 等登录
    > 不操作此步也可
    ```
    cat /etc/shells // 查看 git-shell 是否在登录方式里面
    which git-shell // 查看是否安装
    vi /etc/shells
    添加上2步显示出来的路劲，通常在 /usr/bin/git-shell
    ```
    ```
    修改/etc/passwd中的权限
    // 将原来的
    git:x:1000:1000::/home/git:/bin/bash

    // 修改为
    git:x:1000:1000:,,,:/home/git:/usr/bin/git-shell
    ```


- hexo 的 deploy 配置
    ```
    deploy:
        type: git
        repo: git@[此处为你的服务器ip或域名]:/home/git/repos/blog.git
        branch: master
    ```
    - package.json
    ```
    ...
    "scripts": {
        "start": "hexo clean && hexo g && hexo s",
        "deploy": "hexo clean && hexo g -d"
    }
    ...
    ```
    - 运行命令
    > // 一键发布(push代码)后, 配合服务器中git/hooks中的post-receive钩子(push操作完成时执行), 会直接把最新代码checkout到工作目录, 同时nginx中指向此目录即可实现
    ```
    // npm run start 当npm后命令为start时, 可省略run, 其他的都得有
    npm start // 启动服务
    npm run deploy // 自动发布
    ```
- Nginx配置
```
```