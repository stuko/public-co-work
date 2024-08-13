# SSL Proxy

'''
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
    ProxyPass / https://49.247.7.108:8443/             # 목적지 SSL 주소
    ProxyPassReverse / https://49.247.7.108:8443/      # 목적지 SSL 주소

    ErrorLog "logs/domain-error_log"
    CustomLog "logs/domain-access_log" common
</VirtualHost>


'''