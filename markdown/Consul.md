![Consul logo](images/consul-logo.png)
# Consul

!SUB
## Consul - Service Discovery

The platform uses [consul](http://www.consul.io), [registrator](http://gliderlabs.com/registrator/latest/user/quickstart/#quickstart) and [consul template](https://github.com/hashicorp/consul-template) for service discovery and default HTTP router.  

Unfamiliar with these components? Do the workshop [Service discovery with Consul](http://nauts.io/workshop-docker-consul) first.

!SUB
## Why Consul instead of etcd?
Consul provides a very non-intrusive way of integrating dynamic service discovery with well-known infrastructure components.

- DNS interface 
- developer utilities
    - Consul Web UI
    - Consul template 
    - Consul env 
