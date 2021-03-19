# jasper_reports

Modify .bashrc
```
export AWS_ACCESS_KEY_ID=XXXXXXXXXX
export AWS_SECRET_ACCESS_KEY=XXXXX
export AWS_REGION=us-east
```

jasper


Haproxy Config
```
global
  log         127.0.0.1 syslog
  maxconn     1000
  user        haproxy
  group       haproxy
  daemon
  tune.ssl.default-dh-param 4096
  ssl-default-bind-options no-sslv3 no-tls-tickets
  ssl-default-bind-ciphers EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH


defaults
  log  global
  mode  http
  option  httplog
  option  dontlognull
  option  http-server-close
  option  forwardfor except 127.0.0.0/8
  option  redispatch
  option  contstats
  retries  3
  timeout  http-request 10s
  timeout  queue 1m
  timeout  connect 10s
  timeout  client 1m
  timeout  server 1m
  timeout  check 10s

###########################################
#
# HAProxy Stats page
#
###########################################
listen stats
  bind *:9090
  mode  http
  maxconn  10
  stats  enable
  stats  hide-version
  stats  realm Haproxy\ Statistics
  stats  uri /
  stats  auth admin:GPSc0ntr0l1

###########################################
#
# Front end for all
#
###########################################
frontend ALL
  bind   *:80
  bind   *:443 ssl crt /etc/haproxy/certs/dudewhereismy.mx.pem crt /etc/haproxy/certs/dudewhereismy.com.mx.pem

  mode   http
######

# Define hosts
  acl host_rs hdr(host) -i reports.dudewhereismy.mx
  acl host_prs hdr(host) -i reports.dudewhereismy.com.mx
  acl is_root path -i /
redirect scheme https code 301 if !{ ssl_fc }
  redirect code 301 location /jasperserver-pro if is_root

# Direct hosts to backend
  use_backend rs if host_rs
  use_backend rs if host_prs

###########################################
#
# Back end for foo
#
###########################################

backend rs
  #http-response add-header 
  #rspadd Access-Control-Allow-Origin:\ *
  #rspadd Access-Control-Max-Age:\ 31536000
  balance         roundrobin
  server          sun1 127.0.0.1:8080 check

```
