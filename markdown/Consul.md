## Consul
![Consul logo](images/consul-logo.png) <!-- .element: class="noborder" -->


!SUB
## Consul - Service Discovery

The platform uses consul, the registrator and consul templates for service discovery and default HTTP router.
Unfamiliar with these components? Do the workshop [Service discovery with Consul](http://nauts.io/workshop-docker-consul) first.
[_consul.io_](http://www.consul.io)


!SUB

### Why Consul instead of etcd?

Consul provides a very non-intrusive way of integrating dynamic service discovery with well-known infrastructure components.

 - DNS interface 
 - developer utilities
     - console
     - consul template 
     - consul env 
