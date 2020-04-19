*转载请标明原创地址：*http://www.robotqi.cn/2020/04/14/%E6%89%8B%E6%8A%8A%E6%89%8B%E6%95%99%E5%AD%A6%E6%90%AD%E5%BB%BAhexo%E5%8D%9A%E5%AE%A2/

*本篇文章共计2100词，预计阅读时间4min*



### HEXO介绍

<img src="手把手教学搭建hexo博客\image-20200414222922417.png" alt="image-20200414230512791" style="zoom:100%;" />

> Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 [Markdown](http://daringfireball.net/projects/markdown/)（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

简而言之，[hexo](https://hexo.io/zh-cn/)是一个静态博客的框架，基于nodejs开发，其能进行页面渲染，还能上传部署。



### 本地搭建hexo

搭建一个hexo博客非常简单，只需寥寥几分钟而已
1. ###### 下载软件
    1. 下载[nodejs](http://nodejs.cn/)，进行本地安装，使用`node -v`和`npm -v`确认
    2. 安装hexo，使用`npm install -g hexo-cli`，使用`hexo -v`确认
2. ###### 启动博客
    1. 创建博客目录，比如这里选择`g:/project/blog`，此时blog目录就作为hexo的工作目录，其博客内容的创作、渲染与部署都将在此完成。
    2. 进入博客目录并初始化博客目录，`cd g:/project/blog`，`hexo init`
    3. 启动博客，`hexo server`，默认在本地4000端口启动，此时即可在本地访问，比如`http://localhost:4000`，默认会有一篇Hello World博客  

<img src="手把手教学搭建hexo博客\image-20200414230512791.png" alt="image-20200414230512791" style="zoom:25%;" />
    

3. ###### 创建新博客
   
   1. 新建一篇博客，`hexo new "我的第一篇博客"`，即可在当前目录的子目录./source/_posts下看到新生成的md文件，打开文件即可撰写博客
   2. 清理并生成html文件，`hexo clean`，`hexo generate`
   3. 启动博客，`hexo server`
   4. 此时访问`http://localhost:4000`即可看到新建的hexo博客

到此一个简单的本地博客就搭建完成了，it's so easy!



### 部署到github

由于本地部署，博客只能在本地访问，hexo支持部署到github page，从而能够随时随地访问博客，github page更多详情请戳[官方介绍](https://pages.github.com/)
1. ###### 安装git
   
    1. 安装git，使用`git --version`确认
    2. 安装hexo的git插件，`npm install --save hexo-deployer-git`
2. ###### 部署git
   
    1. 在github中新建一个repo，其名称格式必须是`用户名.github.io`，比如`qirangit.github.io`
    2. 进入blog目录，修改配置文件，`notepad _config.yml`，修改底部的deploy参数，将type部署方式改成git，将repo名称改为github的repo地址，将branch推送分支改为master，比如

```yaml
deploy:
  type: git
  repo: https://github.com/qirangit/qirangit.github.io
  branch: master
```
 3. ###### 部署博客
    
    1. 生成并部署，`hexo clean`，`hexo deploy`
    2. 访问repo地址的即可访问博客，比如`https://qirangit.github.io/`
    3. 博客部署的原理简单来说，hexo会将md文件渲染到.deploy_git目录下，然后将该repo推送到github的repo中去，此时就能通过github page进行访问

<img src="手把手教学搭建hexo博客\image-20200414232109572.png" alt="image-20200414232109572" style="zoom:35%;" />

到此，部署到github上的博客就搭建完成了，it's so easy!!



### 部署到云服务器
由于部署到github上无法被百度检索，另外受到某墙的影响，其体验感略逊一筹，所以可以部署到云服务器，并通过域名进行访问，这样就真正搭建一个独立的技术博客网站
1. ###### 实现原理  

   ​		本地通过`hexo generate`将md文件渲染成静态的html页面，然后通过`hexo deploy`触发[免密](https://blog.csdn.net/jeikerxiao/article/details/84105529)ssh登陆服务器，进而将静态页面推送到云服务器的git仓库中，然后服务器再通过钩子git-hooks 将静态页面同步到网站的根目录下，进而实现博客的自动部署

2. ###### 获取云服务器和域名

   1. 这里选择使用阿里云购买云服务器，对于个人博客购买ECS即可，阿里云性能优异并且稳定，推荐使用
   <img src="手把手教学搭建hexo博客\image-20200417231959695.png" alt="image-20200417231959695" style="zoom:25%;" />
   2. 阿里云默认限制80端口访问，解除阿里云的访问限制，手动添加安全组为端口和ip放行，具体操作是进入控制台创建安全组进行解除，里面需要将端口范围改成`1/65535`，授权对象改为`0.0.0.0/0`
   <img src="手把手教学搭建hexo博客\image-20200418134332806.png" alt="image-20200418134332806" style="zoom:33%;" />
    3. 解除linux服务器的80端口限制，`/sbin/iptables -I INPUT -p tcp –-dport 80 -j ACCEPT`
    4. 申请域名，可以到[阿里云域名官网](https://wanwang.aliyun.com/?spm=5176.12825654.eofdhaal5.9.3dbd2c4aX7zKKn&aly_as=gcvNx1HZH)购买自己所需的域名，并且需要实名认证，另外还要配置DNS解析

3. ###### 安装nginx

   1. 这里选择使用nginx作为http服务器，来访问博客内容。
   2. 选择使用docker安装nginx，更为方便快捷，`docker pull nginx`
   3. 检查云服务器，启动nginx，`docker run -d --name mynginx -p 80:80 -v /home/www/hexo:/home/www/hexo nginx`，这里数据卷目录用于存放博客项目内容
   4. 此时通过访问该服务器的80端口，就能够看到默认的nginx欢迎界面

<img src="手把手教学搭建hexo博客\image-20200418134916633.png" alt="image-20200418134916633" style="zoom:30%;" />

4. ###### 配置nginx
   
   1. 创建服务器中的博客部署目录，比如`mkdir /home/www/hexo`(若前面启动docker容器已经创建不用新建)
   2. 为了让Nginx访问博客内容，首先进入容器，`docker exec -it mynginx /bin/bash`
   3. 修改nginx配置文件，`vim /etc/nginx/conf.d/default.conf`，修改域名server_name，默认是`localhost`，此处更改为自己的域名，比如`www.robotqi.cn`，如果没有域名，先写公网ip；修改location下的root，替换为服务器博客目录，`/home/www/hexo`
5. ###### 配置免密登录服务器
   
   1. 为了实现本地博客的自动部署，需要设置ssh免密登录服务器。原理类似git免密登录服务器，是在本地生成公钥，然后通过ftp工具传递到服务器中，然后将其内容复制到authorized_keys文件中，这样就能实现ssh免密登录服务器，也可以参考[文章](https://blog.csdn.net/jeikerxiao/article/details/84105529)
   2. 在服务器的用户根目录下创建.ssh文件夹，这里直接选择使用root目录，所以是`cd /root`，`mkdir .ssh`
   3. 通过ftp工具比如WinSCP将本地的id_rsa.pub文件传递到服务器的.ssh目录下，并且将其内容copy到authorized_keys文件中，`cp id_rsa.pub authorized_keys`
   4. 确保设置了正确的SELinux上下文，`restorecon -Rv ~/.ssh`
   5. 验证ssh免密登录，此时在本地使用ssh连接服务器不会提示输入密码，`ssh -v root@xxx.xxx.xxx.xxx`(服务器公网IP)

<img src="手把手教学搭建hexo博客\image-20200418141305801.png" alt="image-20200418141305801" style="zoom:30%;" />


6. ###### 配置hook
   
    1. 安装git，`apt-get install git`，使用`git --version`确认
    2. 在服务器中新建git仓库，比如root下的hexo目录，`cd /root`，`git init --bare hexo.git`
    3. 配置钩子文件，`vim /root/hexo.git/hooks/post-receive`，写入内容` git --work-tree=/home/www/hexo --git-dir=/root/hexo.git checkout -f`，将`/root/hexo.git`文件强行checkout到`/home/www/hexo`目录下，可参考[文章](https://blog.csdn.net/swatyb/article/details/52050846)
    4. 重启云服务器，使上述配置生效，注意以上操作的权限。
    
2. ###### 修改本地部署参数

    1. 修改本地blog目录下`_config.yml`的deploy参数，将其repo地址改成云服务器端的repo地址
```
deploy:
  type: git
  repo: root@xx.xx.xx.xx:/root/hexo.git
  branch: master
```
​		2. 然后使用git自动部署，`hexo-deployer-git --save`

8. ###### 访问个人技术博客网站
    1. 本地使用hexo进行生成和部署，`hexo clean`，`hexo generate`和`hexo deploy`
    2. 访问个人博客网站，`http://www.robotqi.cn`，如果没有申请域名，就使用ip地址直连访问
       <img src="手把手教学搭建hexo博客\image-20200418182539943.png" alt="image-20200418182539943" style="zoom:25%;" />

至此，完成了服务器的博客部署，云服务部署博客就是这么简单，it's so easy!!!



### Reference
 1. [Win10下Hexo博客搭建教程，及阿里云服务器部署实战](https://mp.weixin.qq.com/s/JTTUYJTvtdT6X2fvLUBFZg)
 2. [hexo史上最全搭建教程](https://blog.csdn.net/sinat_37781304/article/details/82729029)
 3. [hexo官网](https://hexo.io/zh-cn/)
 4. [hexo插入图片](https://blog.csdn.net/zhangyu4863/article/details/81008511)

