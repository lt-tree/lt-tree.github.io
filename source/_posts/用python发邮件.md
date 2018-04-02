---
title: 用python发邮件
date: 2016-11-04 20:51:35
tags: 想就做
---

One Step

用python来发邮件

<!-- more -->
<br/>
恩，
学了python就不要浪费，
公司要求每天都要写工作日志，
每次都要:
1. 打开浏览器
2. 选择收藏夹里的邮箱
3. 输入账号密码登陆
4. 选择收件人
5. 填写邮件抬头
6. 填写当前日期，还要把鼠标移下去看看
7. 写工作日志
8. 点击发送

很麻烦啊，
写个脚本，

1. 打开脚本
2. 写你的工作日志（注意，只需要写日志，不需要写时间，title，收件人等）
3. 编译

惊！
这难道就是传说中的 —— One Step ？！！

<br/>
唠叨完了，该整干货了。


		import smtplib
		from email.header import Header
		from email.mime.text import MIMEText
		from datetime import datetime

		sender = ''  							# 此处填写发件人邮箱
		password = ''							# 此处填写发件人邮箱密码   	=>注释1
		receiver = ''							# 此处填写收件人邮箱
		smtp_server = ''						# 此处填写smtp服务器地址
		
		DATE_FORMATE = '%Y-%m-%d %A'			# 日期的格式				=> 注释2
		title = ''								# 此处填写邮件标题

		def initialContent():
			now = datetime.now()
			return now.strftime(DATE_FORMATE)

		def sendEmail(cont):
			content = initialContent() + '\n' + cont

			try:
				msg = MIMEText(content, 'plain', 'utf-8') 					
				msg['Subject'] = Header(title, 'utf-8')				# => 注释3
				msg['From'] = sender + ' <' + sender + '>'			# => 注释3
				msg['To'] = receiver								# => 注释3

				server = smtplib.SMTP(smtp_server, 25) 				# 连接服务器，默认端口均为25
				server.set_debuglevel(1)  							# 选择是否开启Debug，可以得到与服务端的反馈信息
				server.login(sender, password)
				server.sendmail(sender, [receiver], msg.as_string())
				server.quit()
		
				print('send email success')
			except smtplib.SMTPException:
				priint('send email fail')


关于上述代码的一些注释：
- 注释1:
关于邮箱密码，有些密码需要填写客户端授权码，比如163，当然，要用SMPT发邮件，需要将发信箱的SMTP服务打开。
- 注释2:
日期的格式，更详细的可参考官方文档：http://python.usyiyi.cn/translate/python_352/library/datetime.html#datetime.datetime
- 注释3:
这里的From、To、Subject是非常必要的

**还有，最好不要用163邮箱测试发邮件，会出现很多乱七八糟的问题（并非是你代码的问题）。**
<br/>
上面代码是用来发邮件的核心代码，
写日志的地方加在里面是很不友好的行为，
所以，
再单独建一个新的友好的地方来写日志。


		import imp 
		tool = imp.load_source('sendEmail', 'url')				# 导入核心模块，url 填写你上面核心代码的位置

		content = """
		在这里来写你的工作日志，
		支持多行。
		"""

		tool.sendEmail(content.strip()) 						# strip将首尾空白字符删除

<br/>
这样就方便很多了。
最后，
这个只是初步版本，
后期可以添加很多你想要的东西，
比如多个联系人，一些判断。
我因为自己用，所以没有加那些东西。



















