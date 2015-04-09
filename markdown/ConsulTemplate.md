## Consul Template Daemon

!SUB
### Features
* Watching the Consul registry
* Generating configuration files whenever values change
* Using Go templates

https://github.com/hashicorp/consul-template


!SUB
###Example

```
$ consul-template \
  -consul consul.service.consul:8500 \
  -template \
  "/haproxy.ctmpl:/etc/haproxy/haproxy.cfg:service haproxy reload"
```

* watches `consul.service.consul:8500`
* Renders template `/haproxy.ctmpl` to `/etc/haproxy/haproxy.cfg`
* reloads haproxy service


!SUB
### Go Template Language examples

```
##Query all nodes
{{nodes}}

##Query all services named webapp
{{service "webapp"}}

###Iterate over all webapp services, accessing several properties
{{range service "webapp@datacenter"}}
server {{.Name}} {{.Address}}:{{.Port}}{{end}}
```
https://github.com/hashicorp/consul-template#templating-language

!SUB
### Load Balancing
![Consul logo](images/consul-logo.png) <!-- .element: class="noborder" -->
![plus](images/plus.png) <!-- .element: class="noborder" -->
![HAProxy logo](images/haproxy-logo.png) <!-- .element: class="noborder" -->

!SUB 
### Snippet Consul template for HAProxy configuration
```
listen http-in
    bind *:80{{range $index, $service := service "consul-http-router"}}
    server s{{$index}} {{$service.Address}}:{{$service.Port}}{{end}}
```

!SUB
### HTTP Reverse Proxy
![Consul logo](images/consul-logo.png) <!-- .element: class="noborder" -->
![plus](images/plus.png) <!-- .element: class="noborder" -->
![HAProxy logo](images/nginx-logo.png) <!-- .element: class="noborder" -->

!SUB
### Snippet Consul template for NGiNX configuration

```
http {
{{range $index, $service := services}}
    {{range $tag, $services := service $service.Name | byTag}}
       {{if eq "http" $tag}}

           upstream {{$service.Name}} {
                least_conn;
                {{range $services}}server {{.Address}}:{{.Port}} max_fails=3 fail_timeout=60 weight=1;
                {{end}}
	    }
{{end}}{{end}}{{end}}
```

!SUB
### prepackaged images
* HTTP Router Load Balancer image [`cargonauts/consul-http-router-lb`](https://registry.hub.docker.com/u/cargonauts/consul-http-router-lb/) 
* HTTP Router image [`cargonauts/consul-http-router`](https://registry.hub.docker.com/u/cargonauts/consul-http-router/) 

The source can be found on https://github.com/cargonauts

