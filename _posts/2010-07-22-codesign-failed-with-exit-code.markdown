---
title: Xcode编译时找不到/usr/bin/codesign
layout: post
category: Tech
tags: [Xcode, iOS]
---

今天使用开发者证书往iPhone上编译安装应用的时候, 出现签名错误, 提示 /usr/bin/codesign 脚本找不到了.

>/Users/marshluca/Library/Developer/Xcode/DerivedData/Share-bsvxmdeugbnodxbqbrbztujklqkz/ArchiveIntermediates/Share/InstallationBuildProductsLocation/Applications/Share.app: object file format invalid or unsuitable
>Command /usr/bin/codesign failed with exit code 1

突然想起来刚升级了 iPhone SDK, 升级之后又把旧版本的 SDK 卸载了, 猜测可能是卸载的时候, /usr/bin/codesign 脚本也被移除了.

在stackoverflow上看到了有人碰到了跟我类似的[问题](http://stackoverflow.com/questions/4842717/iphone-codesign-object-file-format-invalid-or-unsuitable), 在下面找到了解决办法:

	sudo mv /usr/bin/codesign_allocate /usr/bin/codesign_allocate_old
	sudo ln -s /Developer/Platforms/iPhoneOS.platform/Developer/usr/bin/codesign_allocate /usr/bin
 