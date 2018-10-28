DMX Admin Documentation
=======================

How to close public ports with iptables
---------------------------------------

1. Drop incoming requests on the port your DMX instances is listening on (e.g. port 8080) on all interfaces but localhost:

.. code:: bash

    iptables -A INPUT ! -s 127.0.0.1 -p tcp --destination-port 8080 -j REJECT

2. Check (from a remote client machine) if external accedd to jetty is securely blocked:

.. code:: bash

    nmap deepamehta.yourdomain.tld -p 8080

should return

Starting Nmap 5.00 ( http://nmap.org ) at yyyy-mm-dd hh:mm CEST
Interesting ports on deepamehta.yourdomain.tld (xxx.xxx.xxx.xxx):
PORT     STATE    SERVICE
8080/tcp filtered http-proxy

Nmap done: 1 IP address (1 host up) scanned in 0.53 seconds

.. warning:: Only use this tool to check your own server!


Configuring Apache2 as SSL Proxy
-----------------------------------------

If you want to secure your DeepaMehta server e.g. on a public webserver, you should secure it via SSL/TLS. Even though DeepaMehta does support this features natively, you can also use an Apache2 webserver and run it as an SSL proxy. Just follow these steps:

1. Install apache2 webserver

.. code::

    aptitude install apache2

2. Load the required modules

.. code::

    a2enmod ssl rewrite proxy proxy_http

3. Make sure that virtual hosts is enabled for ssl somewhere in your apache2 config.

.. code:: bash

    NameVirtualHost *:443

4. Create file deepamehta-ssl in /etc/apache2/sites-available:

.. code:: bash

    <VirtualHost *:443>
        ServerName deepamehta.yourdomain.tld
        ServerAdmin postmaster@yourdomain.tld
        DefaultType text/html

        SSLEngine On
        SSLCertificateFile /etc/apache2/ssl/deepamehta.yourdomain.tld.pem

        ErrorLog /var/log/apache2/deepamehta-ssl-error.log
        CustomLog /var/log/apache2/deepamehta-ssl-access.log combined

        RewriteEngine On 
        RewriteRule ^/?$ http://localhost:8080/de.deepamehta.webclient [P] 
        RewriteLog /var/log/apache2/deepamehta-ssl-rewrite.log    

        ProxyRequests Off
        ProxyPreserveHost Off
        AllowEncodedSlashes NoDecode

        <Proxy *>
            Order deny,allow
            Allow from all
        </Proxy>

        <Location />
            Order allow,deny
            Allow from all
            ProxyPass http://localhost:8080/ nocanon
            ProxyPassReverse http://localhost:8080/
        </Location>

     </VirtualHost>

5. Enable the site and restart the webserver.

.. code::

    a2ensite deepamehta-ssl.conf  
    systemctl restart apache2  

That's it! You can now securely connect to https://deepamehta.your.domain.tld. 
