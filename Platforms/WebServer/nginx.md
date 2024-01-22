---
title: NGINX
draft: false
---


# HTTPS Server

To configure an HTTPS server, the ssl parameter must be enabled on listening sockets in the server block, and the locations of the server certificate and private key files should be specified:

TLS, or transport layer security, and its predecessor SSL, which stands for secure sockets layer, are web protocols used to wrap normal traffic in a protected, encrypted wrapper. The SSL certificate is publicly shared with anyone requesting the content. It can be used to decrypt the content signed by the associated SSL key.

Using this technology, servers can send traffic safely between the server and clients without the possibility of the messages being intercepted by outside parties. The certificate system also assists users in verifying the identity of the sites that they are connecting with.

The server certificate is a public entity. It is sent to every client that connects to the server. The private key is a secure entity and should be stored in a file with restricted access, however, it must be readable by nginx’s master process. The private key may alternately be stored in the same file as the certificate:

```
server {
    listen              443 ssl;
    server_name         www.example.com;
    ssl_certificate     www.example.com.crt;
    ssl_certificate_key www.example.com.key;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    ...
}
```

The directives ssl_protocols and ssl_ciphers can be used to limit connections to include only the strong versions and ciphers of SSL/TLS. By default nginx uses “ssl_protocols TLSv1 TLSv1.1 TLSv1.2” and “ssl_ciphers HIGH:!aNULL:!MD5”, so configuring them explicitly is generally not needed but they can change in future

## HTTPS server optimization

SSL operations (mainly SSL handshake) consume extra CPU resources. There are two ways to minimize the number of these operations per client:

1. enabling keepalive connections to send several requests via one connection
2. to reuse SSL session parameters to avoid SSL handshakes for parallel and subsequent connections.

The sessions are stored in an SSL session cache shared between workers and configured by the ssl_session_cache directive. One megabyte of the cache contains about 4000 sessions. The default cache timeout is 5 minutes. It can be increased by using the ssl_session_timeout directive.

## SSL certificate chains

Some browsers may complain about a certificate signed by a well-known certificate authority, while other browsers may accept the certificate without issues. This occurs because the issuing authority has signed the server certificate using an intermediate certificate that is not present in the certificate base of well-known trusted certificate authorities which is distributed with a particular browser. In this case the authority provides a bundle of chained certificates which should be concatenated to the signed server certificate. The server certificate must appear before the chained certificates in the combined file:

```
cat www.example.com.crt bundle.crt > www.example.com.chained.crt
```

The resulting file should be used in the ssl_certificate directive:
If the server certificate and the bundle have been concatenated in the wrong order, nginx will fail to start and will display the error message:

```
SSL_CTX_use_PrivateKey_file(" ... /www.example.com.key") failed
   (SSL: error:0B080074:x509 certificate routines:
    X509_check_private_key:key values mismatch)
```

because nginx has tried to use the private key with the bundle’s first certificate instead of the server certificate.

Browsers usually store intermediate certificates which they receive and which are signed by trusted authorities, so actively used browsers may already have the required intermediate certificates and may not complain about a certificate sent without a chained bundle. To ensure the server sends the complete certificate chain, the openssl command-line utility may be used, for example:

```
openssl s_client -connect www.godaddy.com:443
```

### An SSL certificate with several names

There are other ways that allow sharing a single IP address between several HTTPS servers. However, all of them have their drawbacks. One way is to use a certificate with several names in the SubjectAltName certificate field, for example, www.example.com and www.example.org. However, the SubjectAltName field length is limited.

Another way is to use a certificate with a wildcard name, for example, *.example.org. A wildcard certificate secures all subdomains of the specified domain, but only on one level. This certificate matches www.example.org, but does not match example.org and www.sub.example.org. These two methods can also be combined. A certificate may contain exact and wildcard names in the SubjectAltName field, for example, example.org and*.example.org.

It is better to place a certificate file with several names and its private key file at the http level of configuration to inherit their single memory copy in all servers:

```
worker_processes auto;

http {
    ssl_session_cache   shared:SSL:10m;
    ssl_session_timeout 10m;

    ssl_certificate     common.crt;
    ssl_certificate_key common.key;

    server {
        listen              443 ssl;
        server_name         www.example.com;
        keepalive_timeout   70;

        # ssl_certificate     www.example.com.crt;
        # ssl_certificate_key www.example.com.key;
        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers         HIGH:!aNULL:!MD5;

    }
    server {
        listen          443 ssl;
        server_name     www.example.org;
        # ssl_certificate www.example.org.crt;
        ...
    }
```

### Server Name Indication

A more generic solution for running several HTTPS servers on a single IP address is TLS Server Name Indication extension (SNI, RFC 6066), which allows a browser to pass a requested server name during the SSL handshake and, therefore, the server will know which certificate it should use for the connection. SNI is currently supported by most modern browsers, though may not be used by some old or special clients.

Only domain names can be passed in SNI, however some browsers may erroneously pass an IP address of the server as its name if a request includes literal IP address. One should not rely on this.
In order to use SNI in nginx, it must be supported in both the OpenSSL library with which the nginx binary has been built as well as the library to which it is being dynamically linked at run time. OpenSSL supports SNI since 0.9.8f version if it was built with config option “--enable-tlsext”. Since OpenSSL 0.9.8j this option is enabled by default. If nginx was built with SNI support, then nginx will show this when run with the “-V” switch:

```
# nginx -V
nginx version: nginx/1.18.0
built by gcc 8.3.1 20191121 (Red Hat 8.3.1-5) (GCC)
built with OpenSSL 1.1.1g FIPS  21 Apr 2020
TLS SNI support enabled
configure arguments: --prefix=/usr/share/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --http-client-body-temp-path=/var/lib/nginx/tmp/client_body --http-proxy-temp-path=/var/lib/nginx/tmp/proxy --http-fastcgi-temp-path=/var/lib/nginx/tmp/fastcgi --http-uwsgi-temp-path=/var/lib/nginx/tmp/uwsgi --http-scgi-temp-path=/var/lib/nginx/tmp/scgi --pid-path=/run/nginx.pid --lock-path=/run/lock/subsys/nginx --user=nginx --group=nginx --with-file-aio --with-ipv6 --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-stream_ssl_preread_module --with-http_addition_module --with-http_xslt_module=dynamic --with-http_image_filter_module=dynamic --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_degradation_module --with-http_slice_module --with-http_stub_status_module --with-http_perl_module=dynamic --with-http_auth_request_module --with-mail=dynamic --with-mail_ssl_module --with-pcre --with-pcre-jit --with-stream=dynamic --with-stream_ssl_module --with-debug --with-cc-opt='-O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS -fexceptions -fstack-protector-strong -grecord-gcc-switches -specs=/usr/lib/rpm/redhat/redhat-hardened-cc1 -specs=/usr/lib/rpm/redhat/redhat-annobin-cc1 -m64 -mtune=generic -fasynchronous-unwind-tables -fstack-clash-protection -fcf-protection' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -specs=/usr/lib/rpm/redhat/redhat-hardened-ld -Wl,-E'
```

However, if the SNI-enabled nginx is linked dynamically to an OpenSSL library without SNI support, nginx displays the warning:

```
nginx was built with SNI support, however, now it is linked
dynamically to an OpenSSL library which has no tlsext support,
therefore SNI is not available
```

### Self Signed SSL Certificate

> Note: A self-signed certificate will encrypt communication between your server and any clients. However, because it is not signed by any of the trusted certificate authorities included with web browsers, users cannot use the certificate to validate the identity of your server automatically.

> A self-signed certificate may be appropriate if you do not have a domain name associated with your server and for instances where the encrypted web interface is not user-facing. If you do have a domain name, in many cases it is better to use a CA-signed certificate.

#### Creating SSL Certificate

The /etc/ssl/certs directory, is being used to hold the public certificate, and /etc/ssl/private directory to hold the private key file. Since the secrecy of this key is essential for security, lock down the permissions on /etc/ssl/private to prevent unauthorized access:

create a self-signed key and certificate pair with OpenSSL in a single command by typing:

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
```

- req: This subcommand specifies that we want to use X.509 certificate signing request (CSR) management. The “X.509” is a public key infrastructure standard that SSL and TLS adheres to for its key and certificate management. We want to create a new X.509 cert, so we are using this subcommand.
- -x509: This further modifies the previous subcommand by telling the utility that we want to make a self-signed certificate instead of generating a certificate signing request, as would normally happen.
- -nodes: This tells OpenSSL to skip the option to secure our certificate with a passphrase. We need Nginx to be able to read the file, without user intervention, when the server starts up. A passphrase would prevent this from happening because we would have to enter it after every restart.

While we are using OpenSSL, we should also create a strong Diffie-Hellman group, which is used in negotiating Perfect Forward Secrecy with clients.

We can do this by typing:

```
openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

This may take a few minutes, but when it’s done you will have a strong DH group at /etc/ssl/certs/dhparam.pem that we can use in our configuration.

