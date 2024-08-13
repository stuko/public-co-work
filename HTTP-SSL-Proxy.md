아파치 설정 
===============

HTTP SSL Proxy
---------------

### ssl.conf 설정

> 아파치의 ssl.conf 파일에 public/private 키를 설정 하는 경우

```
<VirtualHost domain.com:443>                             # 도메인

    # General setup for the virtual host, inherited from global configuration

    DocumentRoot /opt/public_html                         # 웹 루트
    <Directory "/opt/public_html>                         # 웹 루트 
       AllowOverride None
       Order allow,deny
       Allow from all
       Require all granted
    </Directory>


    ServerName  domain.com:443                           # 도메인:port
    RewriteEngine On
    
    # Use separate log files for the SSL virtual host; note that LogLevel
    # is not inherited from httpd.conf.
    # 로그 설정
    ErrorLog logs/ssl_error_log
    TransferLog logs/ssl_access_log
    LogLevel warn

    ##########################################
    # 로그 설정
    ##########################################
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite HIGH:MEDIUM:!aNULL:!MD5:!SEED:!IDEA
    #SSLCipherSuite RC4-SHA:AES128-SHA:HIGH:MEDIUM:!aNULL:!MD5
    #SSLHonorCipherOrder on

    ##########################################
    # 공개키 인증서 설정
    ##########################################
    SSLCertificateFile /etc/httpd/ssl/ssl.crt
    ##########################################
    # 개인키 설정
    ##########################################
    SSLCertificateKeyFile /etc/httpd/ssl/ssl.key
    ##########################################
    # 인증서 체인 
    ##########################################
    SSLCertificateChainFile /etc/httpd/ssl/chain_ssl.crt
    ##########################################
    # 인증서 검증 
    ##########################################
    #SSLVerifyClient require
    #SSLVerifyDepth  10
    <Files ~ "\.(cgi|shtml|phtml|php3?)$">
        SSLOptions +StdEnvVars
    </Files>
    <Directory "/var/www/cgi-bin">
        SSLOptions +StdEnvVars
    </Directory>

    BrowserMatch "MSIE [2-5]" \
             nokeepalive ssl-unclean-shutdown \
             downgrade-1.0 force-response-1.0

    CustomLog logs/ssl_request_log \
              "%t %h %{SSL_PROTOCOL}x %{SSL_CIPHER}x \"%r\" %b"

    
    
    ##########################################
    # HTTPS -> HTTPS Proxy 한 경우
    ##########################################
    ProxyRequests Off
    ProxyPreserveHost On
    SSLProxyEngine On
    SSLProxyVerify none
    SSLProxyCheckPeerCN off
    SSLProxyCheckPeerName off
    SSLProxyCheckPeerExpire off
    ProxyPass / https://target.site/
    ProxyPassReverse / https://target.site/

</VirtualHost>

```


### httpd.conf 설정 파일의 VirstHost 설정

> 아파치에서 원격 웹서버와 SSL 통신을 하도록 설정하는 경우


    <VirtualHost *:80>
        ServerAdmin webmaster@mail.com            # 관리자 메일 주소
        DocumentRoot /opt/public_html             # 웹 루트
        <Directory "/opt/public_html">            # 웹 루트 
        AllowOverride None
        Order allow,deny
        Allow from all
        Require all granted
        </Directory>
        ServerName domain.com                      # 도메인 주소
        ServerAlias host.domain.com                # virtual host name + 도메인 주소
        ProxyRequests Off
        ProxyPreserveHost On

        SSLProxyEngine On
        SSLProxyVerify none
        SSLProxyCheckPeerCN off
        SSLProxyCheckPeerName off
        SSLProxyCheckPeerExpire off
        ProxyPass / https://target.web.site/             # 목적지 SSL 주소
        ProxyPassReverse / https://target.web.site/      # 목적지 SSL 주소

        ErrorLog "logs/domain-error_log"
        CustomLog "logs/domain-access_log" common
    </VirtualHost>


