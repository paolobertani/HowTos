##How to get & install Let's Encrypt certificates with Acme PHP


&nbsp;


**[Acme PHP website](https://acmephp.github.io)**

**[Documentation](https://acmephp.github.io/documentation/)**

&nbsp;

###Setup


Install Acme PHP as per their documentation into `~/LetsEncrypt`

```
cd
mkdir LetsEncrypt
cd LetsEncrypt

php -r "copy('https://github.com/acmephp/acmephp/releases/download/1.2.0/acmephp.phar', 'acmephp.phar');"
php -r "copy('https://github.com/acmephp/acmephp/releases/download/1.2.0/acmephp.phar.pubkey', 'acmephp.phar.pubkey');"
php acmephp.phar --version
```

&nbsp;


Do not create a configuration file, follow the long way and [run the commands one by one](https://acmephp.github.io/documentation/getting-started/2-obtain-certificate-hard.html)

&nbsp;


Obtain an **account key**

```
php acmephp.phar register paolo.bertani@kalei.it
```

All Acme PHP files and certs are stored into `~/.acmephp`

The above command creates a default configuration file into `~/.acmephp/acmephp.conf`

Edit the configuration file enabling email notification / monitoring.

&nbsp;

### Getting and installing certificate

The website must be up and running on **http** (redirects to **https** in case a certificate is already present are allowed) to expose the token used to prove the ownership of the domain.

```
cd ~/www/example.com
mkdir .well-known
mkdir .well-known/acme-challenge
cd .well-known/acme-challenge
```

In order to allow access to `.well-known` but deny access to all other `.` directories and files **nginx** need this enrty in the configuration file:

```
# Deny all attempts to access hidden files such as .htaccess, .htpasswd, .DS_Store (Mac).

location ~ /\.(?!well-known).*
{
    log_not_found off;
    deny all;
}
```

Don't forget `sudo nginx -s reload` if changes were made.

Get the tokens to expose

```
php acmephp.phar authorize example.com www.example.com www.other-domain.com
```

for each token / domain

```
echo '<token>' > <filename>
```

Ask the server to check the proof

```
php acmephp.phar check example.com www.example.com www.other-domain.com
```

Get the certificate

```
php acmephp.phar request example.com -a www.example.com -a www.other-domain.com
```

Leave the certificate's files in their location.

Make symlinks from `/usr/local/nginx/ssl/example.com/` to the certificates.

The certificate is **fullchain**

The private key is **key.private**

```
pinaxo.com.crt -> /Users/administrator/.acmephp/master/certs/pinaxo.com/public/fullchain.pem
pinaxo.com.key -> /Users/administrator/.acmephp/master/certs/pinaxo.com/private/key.private.pem
```

Properly configure **nginx**

```
server
{
    listen 443 ssl;

    ssl_certificate /usr/local/nginx/ssl/pinaxo.com/pinaxo.com.crt;
    ssl_certificate_key /usr/local/nginx/ssl/pinaxo.com/pinaxo.com.key;
    ssl_prefer_server_ciphers on;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    server_name www.pinaxo.com;

    [...]
```

&nbsp;

### List the certificates

```
cd ~/LetsEncrypt
php acmephp.phar status
```

&nbsp;

### Renew the certificates

Certificates last **90 days**

Re-issuing

```
cd ~/LetsEncrypt
php acmephp.phar request example.com www.example.com www.other-domain.com
```

will renew the certificates.

The command can be issued everyday, certificates request will actually happen **only one week** before expire date.

&nbsp;
---

 



 


``