1、下载

Cygwin

[http://www.cygwin.com/](http://www.cygwin.com/)

setup-x86\_6

4.exe

1.28MB

安装cgywin

![](/assets/cgywin/1.png)

点击下一步

![](/assets/cgywin/2.png)

继续一直点击下一步

![](/assets/cgywin/3.png)

下一步设置下载路径

![](/assets/cgywin/4.png)

![](/assets/cgywin/5.png)

上面的安装目录和下载目录自己设置。下一步选择镜像地址如图，我选的是这个但是当你用的使用就有可能不确定啦，但是不用着急，反正提供了这么多绝对有一个会是可以用的

![](/assets/cgywin/6.png)

如果没有，可以手动add添加

http://mirrors.metapeer.com

或者

http://mirrors.163.com

附录：

[http://mirrors.163.com/.help/cygwin.html](http://mirrors.163.com/.help/cygwin.html)

  


接下来要选择预先安装的软件啦，由于是要运行php环境所以需要安装一下几个软件

gcc-core，gcc-g++

![](/assets/cgywin/7.png)

gdb

![](/assets/cgywin/8.png)

安装php环境

![](/assets/cgywin/9.png)

安装pcre-devel

![](/assets/cgywin/10.png)

安装autoconf

![](/assets/cgywin/11.png)

安装php-json\(这是由于我会用到php的json\_decode\(\)命令，所以需要安装php-json\)

![](/assets/cgywin/12.png)

注意：安装

wget tar gawk bzip2 vim 这四个基本工具是必须的，其他的php扩展可以通过搜索php来安装

![](/assets/cgywin/13.png)

选好安装包后直接下一步，下一步安装

![](/assets/cgywin/14.png)

2、安装php的swoole拓展

下载swoole

[https://pecl.php.net/package/swoole](https://pecl.php.net/package/swoole)

swoole-4.5.

2.tgz

1.42MB

将下载下来的swool拓展复制到Cygwin安装包的home目录下

\(1\):解压swoole拓展包

启动Cygwin：双击Cygwin安装目录下的Cygwin.bat文件

解压swoole拓展包

cd /home/ tar -zxvf swoole-4.5.2.tgz

安装 pcre2 等 相关插件（我们需要安装一个

apt-cyg包管理工具

）

如果已安装

Cygwin，可以直接下载二进制可执行文件

[https://github.com/transcode-open/apt-cyg](https://github.com/transcode-open/apt-cyg)

apt-cyg



13.44KB

将其放在

Cygwin安装目录的bin目录下执行

：

chmod +x /bin/apt-cyg

执行：

apt-cyg --version

查看是否安装成功

apt-cyg

install libpcre2-devel

apt-cyg

install libpcre-devel

\(2\)安装swoole拓展包

\[1\]生成编译文件configure

cd swoole-4.5.2 phpize

\[2\]编译安装swoole

./configure 

&

&

 make 

&

&

 make install

\[3\]在php.ini文件中加入

extension=swoole.so

使用

php -i \| grep php.ini

找到php.ini文件位置

\[4\]:查看swoole拓展是否安装成功

php -m

命令查看swoole是否安装成功

  


![](/assets/cgywin/15.png)

如上图表示php的swoole拓展安装成功

  


编译安装redis扩展

1、下载redis（注意5.x版本扩展可能会有冲突，建议3.x版本）

[https://pecl.php.net/package/redis](https://pecl.php.net/package/redis)

redis-3.1.

6.tgz

195.23KB

2、解压并进入目录

tar -zxvf redis-3.1.6.tgz

cd redis-3.1.6

3、编译安装

phpize

find / -name php-config

./configure --with-php-config=/usr/bin/php-config

make && make install

4、修改php.ini配置加入

extension=redis.so

vim /etc/php.ini

5、php -m 查看是否安装成功

