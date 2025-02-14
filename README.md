# webserver_appache2

Troubleshooting Appache webserver is not accessible 
the webserver https://servera.exemple.com/x.html
elinks -dump https:// servera.exemple.com/x.html ---> can't acess the webserver.

Step-1: I check it in webserver log.

cd /var/log/httpd 
access_log-20250202
-rw-r--r--. 1 root root 2181 Feb  2 18:05 error_log-20250202 ----> error 403

rw-r--r--. 1 root root   540  Feb acces_log-20250209 ----> error 403

Change the directory from /var/log/httpd to document root /var/www/html/x@servera

rw-------. 1 x x 133 Jan 27 03:04 x.html

change the permision  -rw-r-xr-x. 1  x.html ---> permision is giving but can't access the server

Step-2: Checking with selinux to see

auserach -i -m acv -ts todoy 
tcontext=unconfined_u:object_r:var_t:s0 tclass-file permissive=0
getentforce ---> Enforcing

ls -ltrZ x.html ----> selinux apache propriete

unconfined_u:object_r:var_t:s0 ---> output of command ran above

ls -ltrZd system_u:object_r:httpd_sys_content_t:s0 46 Jan 27 02:37 www/ ----> output of correct selinux content
Changing the content
system_u:object_r:httpd_sys_content_t:s0 46 Jan 27 02:37  www /----> correct content.

Issue resolve.
