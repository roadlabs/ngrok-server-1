ngrok-server
============

Building the binaries
---------------------

    docker run -it -v /tmp/bin:/ngrok/bin \
        -e CA_CERT="`awk 1 ORS='\\n' <CA_CERT_FILE>`" \
        tutum/ngrok

Server and client binaries will be available in `/tmp/bin` on the host.

If you want to get other platform version, you can run /bin/bash command and switch to /ngrok directory and launch following commands.

    GOOS=windows GOARCH=amd64 make release-client
    GOOS=darwin GOARCH=amd64 make release-client
    GOOS=linux GOARCH=arm make release-client

Running the server
------------------

    docker run -d --net host \
        -e TLS_CERT="`awk 1 ORS='\\n' <TLS_CERT_FILE>`" \
        -e TLS_KEY="`awk 1 ORS='\\n' <TLS_KEY_FILE>`" \
        -e CA_CERT="`awk 1 ORS='\\n' <CA_CERT_FILE>`" \
        -e DOMAIN="your.domain" \
        -v /tmp/bin:/ngrok/bin \
        tutum/ngrok

If you want run it behind a nginx based proxy, you can add some port options.

    docker run -d -p 8080:80 -p 4443:4443 \
        -e TLS_CERT="`awk 1 ORS='\\n' <TLS_CERT_FILE>`" \
        -e TLS_KEY="`awk 1 ORS='\\n' <TLS_KEY_FILE>`" \
        -e CA_CERT="`awk 1 ORS='\\n' <CA_CERT_FILE>`" \
        -e DOMAIN="your.domain" \
        -v /tmp/bin:/ngrok/bin \
        tutum/ngrok


Environment Variables
---------------------

    TLS_CERT        TLS cert file for setting up tls connection
    TLS_KEY         TLS key file for setting up tls connection
    CA_CERT         CA cert file for compiling ngrok
    DOMAIN          domain name that ngrok running on
    TUNNEL_ADDR     address that ngrok server's control channel listens to, ":4443" by default
    HTTP_ADDR       address that ngrok server's http tunnel listents to, ":80 by default"
    HTTPS_ADDR      address that ngrok server's https tunnel listents to, ":80 by default"

Client configuration
--------------------

    cat >~/ngrok.cfg <<EOL
    server_addr: "your.domain:4443"
    trust_host_root_certs: false
    EOL

Running Client
--------------

    ./ngrok -config ngrok.cfg -subdomain your_subdomain port_number
