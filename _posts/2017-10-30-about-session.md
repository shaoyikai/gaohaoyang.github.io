---
layout: post
title:  "通过session id能删除某个session吗？"
date:   2017-10-30
categories: php
tags: session
excerpt: 关于session贡献
---


[来源：www.codeproject.com](https://www.codeproject.com/questions/414197/problem-in-remove-session-using-session-id)

I have interpretted your question as:- 

You are a admin and you wanted to terminate other user's sessions from your admin panel.

There is no inbuilt mechanism to access other user's session values.

In your code when you do

`Session.Remove(tempCompID);`

its actually removing the session from your session collection not from the other user's

I thought of three options to resolve your issue
  1. we can workaround on this by changing the Session Mode to SQLServer 
	and delete the session id from the corresponding tables
	so once you delete the record from the session table then the user wont be able to continune using the same session id

  2. Create a table where you can store the user name and the active session id
	access this table in admin panel/form
	delete the entry/mark the entry as deleted for a specified session id
	in the page load you can always check the value from this table for the specific session id if it's not available then do
	`Session.Abandon();`\\
	so the current users session will be ended

  3. Create an Application level object to store the Logged In User Id, Application["LoggedUsers"] value needs to be set in Session_Start event in global.asax and remove them in Session_End, store the LoggedIn user as List<sting>. I hope you are already validating session in page load, so add on more line to check the user id is exist in Application["LoggedUsers"] if yes continue else call Session.Abandon(); . Now the main thing is that in your admin form which ever user's session needs to be killed then remove the correpsonding user from the Application["LoggedUsers"].

mark it as solution, if it resolved you issue


关于单点登录：[SSO单点登录三种情况的实现方式详解](http://www.onmpw.com/tm/xwzj/network_145.html)