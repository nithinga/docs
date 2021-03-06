# FAQ

## Bug Reporting and Feature Requests
**Report bugs directly on [GitHub Issues](https://github.com/directus/directus/issues/new) – request and vote for new features on [FeatHub](http://feathub.com/directus/directus). For all security related issues, please chat with us directly through [getdirectus.com](http://getdirectus.com/)**

## Manually Resetting User Passwords
[This section](https://github.com/directus/docs/blob/master/04-developer/06-schema-guide.md#manually-setting-passwords) of the Docs explains how to update a password with a SQL command.


## Server Error: Automatically populating $http_raw_post_data is deprecated
Within PHP 5.6.x `$HTTP_RAW_POST_DATA` is deprecated, but sometimes isn't on individual installs ([php bug #66763](https://bugs.php.net/bug.php?id=66763))

To solve this add/use/update this on your php.ini
`always_populate_raw_post_data = -1`

## Server Error Occurred!
If you get "Server error occurred!" the first time you try to login, it likely means that you misconfigured Apache2. Try adding *AllowOverride All* into your virtualHost.

## Internal Server Error
This error can be anything, but when you are using Directus for the first time and you are able to see the installation page or the login page, but as soon as you try to log in see this error, it might be that your hosting provider doesn't allow to use `Option SymLinksIfOwnerMatch` which can be solve by whether make your hosting allow you to use or simple remove that line in `api/.htaccess`. Make sure this is the issue by checking the apache logs, try looking for something like: `Option SymLinksIfOwnerMatch not allowed here`

## MySQL Strict Mode
Directus does not fully support Strict Mode due to limitations with the PDO and MySQL Drivers. Please disable MySQL Strict Mode before installation.

You can check to see if your database is in strict mode with the following queries:
```
SELECT @@GLOBAL.sql_mode;
SELECT @@SESSION.sql_mode;
SHOW VARIABLES LIKE 'sql_mode';
```

## Enabling `mod_rewrite`

#### Unix
1. In the terminal run `a2enmod rewrite`
2. Restart *apache2* with `/etc/init.d/apache2 restart` or `service apache2 restart`

#### Windows (WAMP)
1. `wamp tray icon > apache > apache module > rewrite_module`

#### Mac (MAMP PRO)
1. Click on `modules` tab.
2. Look for and check `rewrite_module`.

### `mod_rewrite` is enabled and still not working
1. Go to your apache configuration (httpd.conf or apache.conf)
2. Look for the `<Directory>` directive that matches the Directus path.

    Ex:
    ```
    <Directory "/var/www/html">
        AllowOverride All
        Order allow,deny
        Allow from all
    </Directory>
    ```
3. Add `AllowOverride All`, If `AllowOverride None` exists change `None` to `All` to allow `.htaccess` files.

### `mod_rewrite` is enabled and still getting 404 error.

If you are using VirtualDocumentRoot `RewriteBase` needs to be set.

1. Go to `/directus/path/.htaccess` and add `RewriteBase /` just below `RewriteEngine On`.
2. Go to `/directus/path/api/.htaccess` and add `RewriteBase /api` just below `RewriteEngine On`.

### Expecting 'OPEN_ENDBLOCK', got 'EOF'

Getting this error is more likely you are using PageSpeed module, which means it's rewriting the templates html files, making them corrupted as valid Handlebars template.

The solution is to disallowing PageSpeed to rewrite the html files in `app` directory.

See the solution for Apache [here](https://github.com/directus/directus/blob/master/.htaccess) and NGINX [here](https://github.com/directus/directus-vagrant/blob/master/config/nginx/pagespeed.conf).

Apache:
```
ModPagespeedDisallow "*/app/**/*.html"
```

NGINX:
```
pagespeed Disallow */app/**/*.html;
```

*AWS Lightsail has PageSpeed enabled by default.*
