# GrafOrg

Custom [Grafana](https://github.com/grafana/grafana) CSS for [Organizr](https://github.com/causefx/Organizr) homepage integration. 

## Custom CSS for Organizr
#### This theme will mess with your Grafana base theme. And it will hide the side menu. Go to https://graforg.domain.com/datasources for settings.

#### It is created purely for use with Organizr.
**NOTE:** When viewing Grafana in Organizr iframe it will follow the Organizr theme. When viewing it outside of Organizr iframe the background will be white ect. 

### Subfilter
As Grafana doesn't have support for custom CSS you can get around that by using [subfilter](http://nginx.org/en/docs/http/ngx_http_sub_module.html) in Nginx.

Create **another** reverse proxy for monitorr and add this:
```nginx
proxy_set_header Accept-Encoding "";
sub_filter
'</head>'
'<link rel="stylesheet" type="text/css" href="https://rawgit.com/gilbN/GrafOrg/master/graforg.css">
</head>';
sub_filter_once on;
```
### Here is a complete example:
```nginx
#GRAFANA CONTAINER 

# REDIRECT HTTP TRAFFIC TO https://[domain.com]
server {
  listen 80;
  server_name graforg.domain.com;
  return 301 https://$server_name$request_uri;
  }

server {
  listen 443 ssl http2;
  server_name graforg.domain.com;

include /config/nginx/ssl.conf;

location / {
  proxy_pass http://192.168.1.34:3000;
  proxy_set_header Accept-Encoding "";
  sub_filter
  '</head>'
  '<link rel="stylesheet" type="text/css" href="https://rawgit.com/gilbN/GrafOrg/master/graforg.css">
  </head>';
  sub_filter_once on;
  proxy_hide_header X-Frame-Options;
  proxy_set_header X-Forwarded-Host $host;
  proxy_set_header X-Forwarded-Server $host;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_http_version 1.1;
  proxy_pass_request_headers on;
  proxy_set_header Connection "keep-alive";
  proxy_store off;
  }
}
```

![](/Screenshots/1.jpg)

![](/Screenshots/2.jpg)
