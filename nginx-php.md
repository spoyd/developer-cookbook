# Install and Configure NGINX and PHP
This document provides instructions on how to use chocolately
to setup an NGINX/PHP7 development environment on your windows
machine.

## Pre-requisites
  * chocolately is installed

## Install NGINX and PHP
```
choco install nginx-service php
```

## Configure PHP as a Service with NSSM
Open an elevated powershell and type the following:
```
nssm install php-cgi C:\tools\php72\php-cgi.exe
nssm set php-cgi Description "PHP FastCGI server"
nssm set php-cgi AppDirectory  C:\tools\php72
nssm set php-cgi AppParameters "-c C:\tools\php72 -b 127.0.0.1:9000"
```

## Configure PHP for Development

1. Save the current `c:\tools\php72\php.ini` file.
1. Copy the `c:\tools\php72\php.ini-development` file to `c:\tools\php72\php.ini`.
1. Open the `c:\tools\php72\php.ini` file in a text editor.
1. Uncomment the line: `extension_dir = "ext"`

## Configure NGINX to Communicate with PHP

1. Open the `c:\tools\nginx\conf.d\server.default.file` file in a text editor.
1. Uncomment these lines:
    ```
   	location ~ \.php$ {
	    root           html;
	    fastcgi_pass   127.0.0.1:9000;
	    fastcgi_index  index.php;
	    fastcgi_param  SCRIPT_FILENAME  /script$fastcgi_script_name;
	    include        fastcgi_params;
	}
    ```
1. Change the text `/script` in the `SCRIPT_FILENAME` to `$document_root`. It should now look like this:
    ```
   	location ~ \.php$ {
	    root           html;
	    fastcgi_pass   127.0.0.1:9000;
	    fastcgi_index  index.php;
	    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
	    include        fastcgi_params;
	}
    ```

## Restart Services
```
nssm restart php-cgi
nssm restart nginx
```

## Test the Configuration

1. Create a text file `info.php` in `c:\tools\nginx\html`
1. The file contents should be:
    ```
	<?php phpinfo();
	```
1. Open in the browser:
[http://localhost/info.php](http://localhost/info.php)
1. You should now see the typical PHP Info Page
