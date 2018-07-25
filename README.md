# ubuntu_1404_lnmp 跨平台环境搭建说明

## 软件依赖

- Windows 
  - VirtualBox-5.2.12-122591-Win.exe
  - vagrant_2.1.2_x86_64.msi
  - xshell5_wm_5.0.1332.exe
  
  百度网盘下载: 链接:https://pan.baidu.com/s/18zNHQTJkRHpR3b3dHMmeXQ  密码:887x
  
- Mac
  - VirtualBox-5.2.12-123759-OSX.dmg
  - vagrant_2.1.2_x86_64.dmg
  
  百度网盘下载： 链接:https://pan.baidu.com/s/14or08UfUNvVibFTjzpxw_w  密码:yap9
  
- ubuntu_1404_lnmp_public.box 镜像

	百度网盘下载：链接:https://pan.baidu.com/s/1F7gdKvUiwDvh59luGhBAxw  密码:274n
	
## 创建目录示例(windows)

- vagrant 环境目录 d:/vagrant

```
|--vagrant
|  |--ubuntu_1604_lnmp_public
|  |  |--dev 开发环境nginx配置
|  |  |  |--test.conf  测试站nginx配置
|  |  |  |--xhprof.test.conf  性能分析nginx配置
|  |  |--logs 相关日志目录
|  |  |  |--nginx
|  |  |  |--php
|  |  |--www 【应用程序代码目录】
|  |  |  |--test 【测试项目】
|  |  |  |--xhgui  【PHP性能分析项目】
|  |  |--.vagrant vagrant信息存储
|  |  |--start_run.bat
|  |  |--Vagrantfile
```

##  部署说明
- step1: 获取配置信息（压缩包 或 git clone 任选其一）
     - 压缩包：解压ubuntu_1404_lnmp_public.zip 到 d:/vagrant/ubuntu_1404_lnmp_public
     - git clone: git clone git@github.com:aweb/ubuntu_1404_lnmp.git d:/vagrant/ubuntu_1404_lnmp_public

- Step2: 百度网盘获取下载相关文件
	- ubuntu_1404_lnmp.box 镜像
	- vagrant、virtualbox、xshell 相关软件并安装

- setp3: 切换到 d:/vagrant/ubuntu_1604_lnmp_public, 选择一下任意方式：
	  - 下载ubuntu_1404_lnmp.box 镜像安装
	  
	  ```
	  // 从文件添加镜像到box
	  vagrant box add ubuntu_1404_lnmp ubuntu_1404_lnmp.box
	  // 创建并启动虚拟机
	  vagrant up 
	  ```
	  
	  - 从vagrant cloud 在线安装
	  
	  ```
	  // 取消Vagrantfile 以下两行注释
	    #config.vm.box = "aweb/ubuntu_1404_lnmp"
       #config.vm.box_version = "1.0"
	  // 创建并启动虚拟机
	  vagrant up 
	  ```
	  
- step4: 将start_run.bat 添加快捷方式到桌面并添加到开机启动（win + R 输入 shell:startup，将快捷方式添加到文件夹）

- step5: Done 

## 演示说明
- http://localhost:8100 【测试站】
- http://localhost:8101 【php性能分析系统】


# ubuntu_1404_lnmp 环境使用说明

## 链接到ubuntu服务器
    - 地址：127.0.0.1 
    - 端口:2222 
    - 用户名：vagrant 
    - 密码: vagrant
    
## 相关软件与服务
- Mysql
- Redis
- Memcached
- Mongodb
- Php-cli
- Php-fpm
- Composer
- Php-xdebug
- Php-xhprof
- Golang
- Nginx
- Nodejs
- Npm

## 服务管理
- nginx

``` 
sudo service nginx start|stop|restart|reload|force-reload|status|configtest|rotate|upgrade
```
- mysql

``` 
sudo service mysql start|stop|restart|reload|force-reload|status
```
- php-fpm

``` 
sudo service php-fpm force-reload|reload|restart|start|status|stop  
```
……

## php代码无入侵性能分析
### nginx 配置添加以下内容
```shell
fastcgi_param PHP_VALUE "auto_prepend_file=/home/www/xhgui/external/header.php";
```

### xhgui 配置
```shell
// D:\vagrant\ubuntu_1404_lnmp_public\www\xhgui\config\config.php 配置
    'profiler.enable' => function() {
        //  监控规则过滤nzing
        $xh_global = true;
        $xh_conf_urls = array(
            "/test/",
        );
        $host = $_SERVER['HTTP_HOST'];
        if (!in_array($host, ['localhost'])) {
            return false;
        }
        if ($_SERVER["REQUEST_URI"] == '/favicon.ico') {
            return false;
        }
        if ($xh_global === true ) {
            return true;
        } else {
            $xh_request_uri = isset($_SERVER["REQUEST_URI"]) ? $_SERVER["REQUEST_URI"] : "";
            $arr_xh_cur_url = explode("?", $xh_request_uri);
            $xh_cur_url = $arr_xh_cur_url[0];
            if(in_array($xh_cur_url, $xh_conf_urls)){
                return true;
            }俄s3e
        }
        return false;
       //rand(1, 100) === 42;
    },
```
- xhgui https://github.com/perftools/xhgui
- xhgui中文 https://github.com/laynefyc/xhgui-branch