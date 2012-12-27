---
layout: post
title: Apple Push Notification Service
category: iOS
tags: [APNS]
---



苹果的Push Notification服务, 允许用户不打开应用的情况下接受通知消息. 下面是所有部署配置过程。

1.将app注册notification里面, 并从APNS上获取测试机的deviceToken.    

		-(BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions 
		{        
		  [[UIApplication sharedApplication] registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound)];  
		  // other codes here.    
		  return YES;
		}

		-(void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken 
		{
		  NSLog(@"deviceToken: %@", deviceToken);
		}

		-(void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error 
		{
		  NSLog(@"Error in registration. Error: %@", error);
		}

		-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
		{	
		  NSLog(@"收到推送消息 ： %@",[[userInfo objectForKey:@"aps"] objectForKey:@"alert"]);
		  if ([[userInfo objectForKey:@"aps"] objectForKey:@"alert"]!=NULL) {
		    UIAlertView* alert = [[UIAlertView alloc] initWithTitle:@"推送通知"
		                                            message:[[userInfo objectForKey:@"aps"] objectForKey:@"alert"]
													delegate:self
													cancelButtonTitle:@"关闭"
													otherButtonTitles:@"更新状态",nil];
		    [alert show];
		    [alert release];
		  }
		}	

 启动程序，将app注册到通知项后，在console里面找到打印的deviceToken：

    deviceToken: <6974ac11 870e09fa 00e2238e 8cfafc7d 2052e342 182f5b57 fabca445 42b72e1b>


2.生成app在服务端需要的许可证

1）进入Provisioning Portal, 下载Certificates在development下的证书。 

2）找到需要测试的app id,然后enable它在development下的Apple Push Notification service: Development Push SSL Certificate。需要输入1）中的签名证书才可以生成一个aps_developer_identity.cer.

3) 双击aps_developer_identity.cer，会打开系统的key chain. 在My certificates下找到Apple Development Push Services。需要为certificate和它之下的private key各自export出一个.p12文件。(会出现设置密码过程)

4）需要将上面的2个.p12文件转成.pem格式：

    openssl pkcs12 -clcerts -nokeys -out cert.pem -in cert.p12
    openssl pkcs12 -nocerts -out key.pem -in key.p12

5）如果需要对key不进行加密：

    openssl rsa -in key.pem -out key.unencrypted.pem

6）然后就可以合并两个.pem文件, 这个ck.pem就是服务端需要的证书了。

    cat cert.pem key.unencrypted.pem > ck.pem

3.服务端push通知到ANPS. 这里是用php服务端驱动。需要将ck.pem和php脚本放到server上。全部的php代码是：

	<?php
	$deviceToken = '6974ac11 870e09fa 00e2238e 8cfafc7d 2052e342 182f5b57 fabca445 42b72e1b';
	$pass = '123456';   // Passphrase for the private key (ck.pem file)
	// Get the parameters from http get or from command line
	$message = $_GET['message'] or $message = $argv[1] or $message = 'A test message from worldcup';
	$badge = (int)$_GET['badge'] or $badge = (int)$argv[2];
	$sound = $_GET['sound'] or $sound = $argv[3];
	// Construct the notification payload
	$body = array();
	$body['aps'] = array('alert' => $message);
	if ($badge)
	$body['aps']['badge'] = $badge;
	if ($sound)
	$body['aps']['sound'] = $sound;
	/* End of Configurable Items */
	$ctx = stream_context_create();
	stream_context_set_option($ctx, 'ssl', 'local_cert', 'ck.pem');  
	// assume the private key passphase was removed.
	stream_context_set_option($ctx, 'ssl', 'passphrase', $pass);
	// connect to apns
	$fp = stream_socket_client('ssl://gateway.sandbox.push.apple.com:2195', $err, $errstr, 60, STREAM_CLIENT_CONNECT, $ctx);
	if (!$fp) {
	print "Failed to connect $err $errstr\n";
	return;
	}
	else {
	print "Connection OK\n<br/>";
	}
	// send message
	$payload = json_encode($body);
	$msg = chr(0) . pack("n",32) . pack('H*', str_replace(' ', '', $deviceToken)) . pack("n",strlen($payload)) . $payload;
	print "Sending message :" . $payload . "\n";  
	fwrite($fp, $msg);
	fclose($fp);
	?>

4.打开浏览器,地址栏输入:

 http://127.0.0.1/apns/apns.php?message=A%20test%20message%20from%20localhost&badge=2&sound=received5.caf

就会向APNS进行一次推送. 请求成功后服务器的响应是这样的:

	Connection OK
	Sending message :{"aps":{"alert":"A test message from localhost","badge":2,"sound":"received5.caf"}}
