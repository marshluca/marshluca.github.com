--- 
layout: post
title: PHP中开启curl模块
category: Programming
tags: [PHP, curl]
---


最近在做的iPhone项目需要配置Apple Push Notification Service的服务端环境. 网上找到了一个PHP版本的推送代码, 里面需要加上对公司的API接口回调请求. 

对PHP不太熟, 网上搜了下解决方案, 发现其实挺简单的:

1.安装 php5-curl

	apt-get install php5-curl

2.重启 apache 服务

	/etc/init.d/apache2 restart

3.加入 curl 测试

	$ch = curl_init();
	$timeout = 5;
	curl_setopt ($ch, CURLOPT_URL, 'http://www.baidu.com/')
	curl_setopt ($ch, CURLOPT_RETURNTRANSFER, 1);
	curl_setopt ($ch, CURLOPT_CONNECTTIMEOUT, $timeout);
	$file_contents = curl_exec($ch);
	curl_close($ch);
	echo $file_contents;
