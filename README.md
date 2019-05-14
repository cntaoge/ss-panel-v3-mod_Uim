# 欢迎使用ss panel v3 mod 再次修改版

## 搭建教程

-------------------------------------------------------------------------------------
前端面板：
安装宝塔 www.bt.cn
配置PHP (宝塔用户)
在禁用函数一栏找到 system proc_open proc_get_status 去除它
在性能调整中，把 PHP 运行模式设置为 静态
在配置修改中 按 Ctrl+F 搜索 display_errors = 改为 Off 后保存

web环境配置好后
<pre>
cd /你的网站目录  例：cd /www/wwwroot/你的网站目录  
yum update
yum install git -y
git clone -b master https://github.com/Anankke/ss-panel-v3-mod_Uim.git tmp && mv tmp/.git . && rm -rf tmp && git reset --hard
chown -R root:root *
chmod -R 755 *
chown -R www:www storage
php composer.phar install
</pre>
移除防跨目录
修改网站目录
/你的网站目录/public;

Example Nginx Configuration伪静态设置
<pre>
location / {
    try_files $uri /index.php$is_args$args;
}
</pre>

配置数据库
登陆数据库
<pre>
mysql -u root -p                           // 这里需要输入密码
mysql>CREATE DATABASE database_name;       // 新建数据库
mysql>use database_name;                   // 选择数据库
mysql>source /网站目录/sql/glzjin_all.sql;  // 导入.sql文件
</pre>
配置数据库访问权限为所有人

配置 sspanel
<pre>
cd /网站目录
cp config/.config.php.example config/.config.php
vi config/.config.php
</pre>
重启Nginx和php
创建管理员并同步用户
<pre>
php xcat createAdmin          //创建管理员
php xcat syncusers            //同步用户
php xcat initQQWry            //下载IP解析库
php xcat resetTraffic         //重置流量
php xcat initdownload         //下载ssr程式
</pre>
设置定时任务
执行 crontab -e命令, 添加以下四条
<pre>
30 22 * * * php /网站目录/xcat sendDiaryMail
0 0 * * * php -n /网站目录/xcat dailyjob
*/1 * * * * php /网站目录/xcat checkjob
*/1 * * * * php /网站目录/xcat syncnode
</pre>
如果需要自动备份，可模仿以下两例，自行添加一条

例1：每20分钟备份1次（若间隔大于60分钟，看例2）：
<pre>
*/20 * * * * php -n /网站目录/xcat backup
</pre>

例2：每20小时备份1次（若间隔大于24小时，自行Google）：
<pre>
0 */20 * * * php -n /网站目录/xcat backup
</pre>

如果需要财务报表，可选添加以下三条
<pre>
5 0 * * * php /网站目录/xcat sendFinanceMail_day
6 0 * * 0 php /网站目录/xcat sendFinanceMail_week
7 0 1 * * php /网站目录/xcat sendFinanceMail_month
</pre>

如果需要检测被墙，添加以下一条
<pre>
*/1 * * * * php /网站目录/xcat detectGFW
</pre>

如果要用到radius，需要添加下面这三条
<pre>
*/1 * * * * php /网站目录/xcat synclogin
*/1 * * * * php /网站目录/xcat syncvpn
*/1 * * * * php -n /网站目录/xcat syncnas
</pre>
-------------------------------------------------------------------
后端一键安装脚本

安装 For CentOS 7 x64
<pre>
yum install wget -y && wget https://raw.githubusercontent.com/SuicidalCat/Airport-toolkit/master/ssr_node_c7.sh && chmod +x ssr_node_c7.sh && ./ssr_node_c7.sh
</pre>

卸载
<pre>
systemctl disable ssr_node && \rm /usr/lib/systemd/system/ssr_node.service && \rm -rf /soft/shadowsocks
</pre>

设置开机启动
<pre>
systemctl enable ssr_node
</pre>

服务启动
<pre>
systemctl start ssr_node
</pre>

服务停止
<pre>
systemctl stop ssr_node
</pre>
----------------------------------------------------------------------
版本升级
cd /www/wwwroot/你网站目录 到站点根目录(保证.git目录存在)执行
<pre>
git fetch --all
git reset --hard origin/master
git pull
</pre>
注意:你会丢失除 .config.php 外目前自己的所有改动，请做好文件备份，如不想丢失改动， 请参考这个文章7.3 Git 工具- 储藏与清理

2.迁移config 执行命令
<pre>
php /网站目录/xcat update
</pre>

迁移/更新之前会自动备份原config为.config.php.bak

迁移/更新完成之后会输出新旧配置之间的差异

3.UI更新后请刷新浏览器缓存，有CDN需要清理CDN内缓存的CSS和JS文件，同时检查并清除nginx缓存
dev需要安装nodejs和npm
出错提示1:Peer reports incompatible or unsupported protocol version.##
原因：Git版本过低

解决方法：

1、安装所需软件包
<pre>
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
yum install gcc perl-ExtUtils-MakeMaker
</pre>
2、下载&安装
<pre>
cd /usr/src
wget https://www.kernel.org/pub/software/scm/git/git-2.7.3.tar.gz
tar xzf git-2.7.3.tar.gz
cd git-2.7.3
make prefix=/usr/local/git all
make prefix=/usr/local/git install
echo "export PATH=$PATH:/usr/local/git/bin" >> /etc/bashrc
source /etc/bashrc
</pre>
3、检查版本：2.X.X版本即可
<pre>
git --version
</pre>
