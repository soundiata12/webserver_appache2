# webserver_appache2

Step 1: Check Apache Web Server Logs
The first step in troubleshooting was to check the Apache web server logs for any errors.

Navigate to the Apache log directory:

bash
Copy
cd /var/log/httpd
Review the logs for errors:

Logs from February 2nd show a 403 Forbidden error:
bash
Copy
-rw-r--r--. 1 root root 2181 Feb 2 18:05 error_log-20250202
Logs from February 9th also show a 403 Forbidden error:
bash
Copy
-rw-r--r--. 1 root root 540 Feb 9 access_log-20250209
The 403 error suggests that access is being denied due to permission issues.

Step 2: Check File Permissions of x.html
Navigate to the document root directory:

bash
Copy
cd /var/www/html
Check the permissions of x.html:

bash
Copy
ls -l x.html
Output:

bash
Copy
-rw-------. 1 x x 133 Jan 27 03:04 x.html
The file permissions were restrictive, so I updated the permissions to allow read and execute access for others.

Update the file permissions:

bash
Copy
chmod 755 x.html
After changing the permissions, the issue persisted, and the server still could not access the file.

Step 3: Check SELinux Context
Since the issue persisted, I checked the SELinux context for the x.html file.

Check the SELinux status:

bash
Copy
getenforce
Output:

bash
Copy
Enforcing
SELinux was in enforcing mode, which might prevent Apache from accessing the file based on security policies.

Check the SELinux context of x.html:

bash
Copy
ls -ltrZ x.html
Output:

bash
Copy
unconfined_u:object_r:var_t:s0
The SELinux context for x.html was incorrect for web content, which likely caused the access issue.

Check the correct SELinux context for the web server directory:

bash
Copy
ls -ltrZd /var/www/html
Output:

bash
Copy
system_u:object_r:httpd_sys_content_t:s0
The correct context for the web server files should be httpd_sys_content_t, not var_t.

Change the SELinux context for x.html: I updated the SELinux context for the file to the correct one:

bash
Copy
chcon -t httpd_sys_content_t x.html
Step 4: Verify Server Access
After making the necessary changes to file permissions and SELinux context, I tested the server again and found that the issue was resolved.

Resolution:
The 403 Forbidden error was caused by incorrect file permissions and SELinux context.
After updating the file permissions to 755 and changing the SELinux context to httpd_sys_content_t, Apache was able to access the x.html file, and the issue was resolved.
