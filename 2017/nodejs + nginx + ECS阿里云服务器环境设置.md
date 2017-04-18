# nodejs + nginx + ECS阿里云服务器环境设置

标签（空格分隔）： nodejs Nginx ecs

---

## 部署 nodejs
* 基于 CentOS7.2
* 详细步骤：[click](https://help.aliyun.com/document_detail/50775.html)

## 部署 nginx

### 安装
1. 添加Nginx软件库：
`[root@localhost ~]#rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm`
2. 安装Nginx：
`[root@localhost ~]#yum -y install nginx`
3. 设置Nginx服务器自动启动：
`[root@localhost ~]# systemctl enable nginx.service`
4. 启动Nginx并查看Nginx服务状态：
`[root@localhost ~]#systemctl start nginx.service`
`[root@localhost ~]#systemctl status nginx.service`
5.在浏览器里输入 IPV4 地址（公网地址）就能看运行成功页面 ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/50604/cn_zh/1487645593098/image012.png)

### 域名解析
1. 首先你得有自己的域名，比如在万网上买一个
2. 登录域名管理控制台
3. 在域名列表中找到要解析的域名，然后单击 解析 ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/50604/cn_zh/1487649088921/image025.png)
4. 单击 新手引导设置 ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/50604/cn_zh/1487649118241/image026.png)
5. 输入您的 Linux 实例的公网 IP 地址。然后单击 提交 
6. 设置成功，会出现如下信息 ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/50604/cn_zh/1487649171594/image028.png)

### 二级域名

1.每个域名一个文件的写法 
首先打开 nginx域名配置文件，如要绑定域名www.web126.com 则在此目录建一个文件：www.web126.com.conf 然后在此文件中写规则，如： 

    server
    {
        listen 80;
        server_name www.web126.com; #绑定域名
        index index.htm index.html index.php; #默认文件
        root /home/www/web126.com; #网站根目录
        include location.conf; #调用其他规则，也可去除
    }
 
然后重起nginx服务器，域名就绑定成功了。 
2.一个文件多个域名的写法 
一个文件添加多个域名的规则也是一样，只要把上面单个域名重复写下来就ok了，如： 

    server
    {
        listen 80;
        server_name www.web126.com; #绑定域名
        index index.htm index.html index.php; #默认文件
        root /home/www/web126.com; #网站根目录
        include location.conf; #调用其他规则，也可去除
    }
    server
    {
        listen 80;
        server_name msn.web126.com; #绑定域名
        index index.htm index.html index.php; #默认文件
        root /home/www/msn.web126.com; #网站根目录
        include location.conf; #调用其他规则，也可去除
    }

3.使用正则捕获的写法

    server
    {
        listen 80;
        server_name ~^(.+)?\.howtocn\.org$;
        index index.html;
        if ($host = ssdr.info){
            rewrite ^ http://www.ssdr.info permanent;
        }
        root /data/wwwsite/ssdr.info/$1/;
    } 
    
这样访问 `www.ssdr.info` 时 `root` 目录为 `/data/wwwsite/ssdr.info/www/`，`nginx.ssdr.info` 时为 `/data/wwwsite/ssdr.info/nginx/`，以此类推。 
后面 `if` 语句的作用是将 `ssdr.info` 的方位重定向到 `www.ssdr.info` ，这样既解决了网站的主目录访问，又可以增加seo中对 `www.ssdr.info` 的域名权重。

### Tips
1.禁止IP直接访问80端口或者禁止非本站的域名绑定我们的IP
这样的话应该如下处理，放到最前一个server上面即可： 

    server{
        listen 80 default;
        server_name _;
        return 403;
    }

2.修改完配置文件，先运行 `nginx -t` 查看是否有错


#### ps:

* [http://www.infocool.net/kb/Other/201611/217359.html](http://www.infocool.net/kb/Other/201611/217359.html)
- [https://help.aliyun.com/document_detail/50604.html](https://help.aliyun.com/document_detail/50604.html)