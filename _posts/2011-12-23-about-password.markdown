---
comments: true
date: 2011-12-23 10:21:02
layout: post
slug: about-password
title: 关于密码
wordpress_id: 54
categories:
- 互联网
tags:
- csdn
- python
- 密码
---

CSDN被爆库了，更可怕的是，数据库里密码存的是明文。当天就有现任的CTO解释是因为历史遗留问题，并且在他接手之后还进行了修正。也就是说他上任之后的所有注册用户的密码使用了强加密。很可惜，我是csdn早期用户，我甚至在高中的时候就在dearbook上买过书，也就是说，我的密码明文仍旧在他们的数据库里。果不其然，我最常用的用户名，密码，邮箱就这么泄漏出去了。
然后是持续两天的各种焦头烂额回忆，改密码

其实大家都懒，我很早之前也考虑过类似的风险问题，但是一直没有动手去做，只是在关键账户使用了特殊的长密码。所以说实话，CSDN那些倒霉程序员拖了几年没有处理明文密码的问题，我非常理解。但是，我心里想的是最早设计时决定采用明文密码的人，CSDN的程序员总不会很业余吧？那他是得个怎么恶毒的人？

但是第二天开始越来越多的网站数据库被爆，从被爆的数据库来看，几乎清一色的明文或者是弱加密。或许你会说，其他采用强加密的站数据库是安全的，只是你见不到而已。但是从第二轮披露的网站来看，特别是从他们的用户量来看，不像是会忽略用户安全问题的野鸡小网站啊？我不禁以最坏的可能去揣摩他们的用心，瞬间我就明白了

同时也是在了解这件事的过程中，我知道了利用社工方法攻击其实非常普遍，在圈子里甚至有一个社工库包含了各种没有公开的密码数据库。那个库里面有你的、我的、各种大牛的各种网站的用户名和密码，这次爆出来的只是非常小的一部分。并且，有这么一个千万级的密码字典，那攻击起来将会多么便利？如果我需要csdn账户或者其他什么网站的账户为我做一些事情，只需要拿到一份常用的用户名集合然后用密码12345678去试，我相信拿到几万用户是十分轻松的事情。这事太没有技术含量了，但是又太方便了。

之前一个同学的密码这次也被爆了，另外一个同学成功的登录上了他的支付宝，还好不是网银。都是在网上摸爬滚打十年多的人了，这也给我敲了一记警钟。这次的事实证明，之前的密码管理方式确实存在漏洞。那么，现在是时候该做出些改变了。

1、注册几个邮箱，作为各种论坛注册用。
2、对我需要注册的网站甄别，如果只是一次性需要的网站，使用统一密码和Ring3邮箱注册
3、对于试用之后感觉需要长期使用的服务，使用Ring2邮箱+随机密码注册
4、对于涉及到我隐私和真实身份的服务，使用Ring1邮箱+随机密码注册，并且1～3个月更换密码
5、对于一切涉及到钱的服务，使用Ring0邮箱+16位随机密码
6、大于Ring3的服务停用后，手动删除敏感数据，注册账户（虽然这么做肯定没有意义）

另外对于密码的生成和管理，这两天晚上折腾出来一个脚本，算是练手吧
**注意：其中加密后可能会产生‘\n’的ascii码产生错误换行，这个问题还没有修正**



抛砖引玉了

    
    #!/usr/bin/python
    # -*- coding: utf-8 -*-
    #
    #       version: v0.1
    #        author: yan97ao
    #                magictao[at]gmail.com
    #       license: gpl v3
    #python version: python 2.7
    """
    一个维护密码的小程序
    密码文件格式：
    '='开始行为注释
    每个密码项
    第一行为索引(一般为对应网站的汉语拼音缩写)
    第二行为网站名称(网址)
    第三行为用户名
    第四行为密码
    后续为可扩展字段
    每个密码项以eopi结束(end of passoword item)
    """
    import random,time
    
    pwseed=""
    
    def createpwseed():
    	global pwseed
    	for i in range(10):
    		pwseed += str(i)
    	for i in range(26):
    		pwseed += chr(ord('a') + i)
    		pwseed += chr(ord('a') + i)
    
    def createpassworditem(filename, itemname, key):
    	global pwseed
    	f = open(filename,"a")
    
    	fullname = raw_input("the full name of item>")
    	username = raw_input("username>")
    	lenpw = input("length of passoword>")
    
    	newpw = ""
    	for i in range(lenpw):
    		newpw += pwseed[int(random.random() * len(pwseed))]
    
    	cryptedpw = crypt(newpw,key)
    
    	print "索引:\t\t",itemname
    	print "密码项全名:\t\t",fullname
    	print "用户名:\t\t",username
    	print "生成的密码:\t\t",newpw
    	print "加密后的密码:\t\t",cryptedpw
    
    	f.write("= 生成于"+time.ctime(time.time()) + '\n')
    	f.write(itemname + '\n')
    	f.write(fullname + '\n')
    	f.write(username + '\n')
    	f.write(cryptedpw + '\n')
    	f.write("eopi\n")
    	f.write('\n')
    
    	f.close()
    
    def searchpassworditem(filename, itemname, key):
    	f = open(filename,"r")
    
    	itemname += '\n'
    	result = ""
    	strreaded = ""
    
    	while strreaded != "eof" :
    		strreaded = f.readline()
    		if itemname == strreaded :
    			break
    		else:
    			pass
    
    	result += "索引:\t\t" + strreaded
    	result += "密码项全名:\t\t" + f.readline()
    	result += "用户名:\t\t" + f.readline()
    	result += "密码:\t\t" + decrypt(f.readline(),key)
    
    	print result
    	f.close()
    
    def crypt(strtocrypt, key):
    	strcrypted = ""
    	for i in range(len(strtocrypt)):
    		strcrypted += chr(ord(strtocrypt[i]) + (ord(key[i % len(key)]) % 10))
    	return strcrypted
    
    def decrypt(strtodecrypt, key):
    	strdecrypted = ""
    	for i in range(len(strtodecrypt)):
    		strdecrypted += chr(ord(strtodecrypt[i]) - (ord(key[i % len(key)]) % 10))
    	return strdecrypted
    
    def printhelp():
    	print "用于生成和维护密码的一个小工具"
    	print "create [itemname]\t\t\t 生成以itemname为索引的密码"
    	print "search [itemname]\t\t\t 查找itemname为索引的密码"
    
    def main():
    	global pwseed
    	createpwseed()
    	#filename = raw_input("filename>")
    	filename = "pw.txt"
    	key = raw_input("key>")
    	while 1:
    		cmdstr = raw_input("cmd>")
    		cmdsplit = cmdstr.split()
    		if len(cmdsplit) != 2:
    			printhelp()
    		else:
    			a,b=cmdsplit
    			if a == "create":
    				createpassworditem(filename, b, key)
    			elif a == "search":
    				searchpassworditem(filename, b, key)
    			else:
    				printhelp()
    
    if __name__ == '__main__':
        main()
    
    __revision__ = '0.1'
    #- EOF -
