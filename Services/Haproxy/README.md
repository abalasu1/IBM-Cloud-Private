# External HA Proxy load balancing

## Nodeport
- kubectl apply -f nginx.yaml

- Set up haproxy.cfg to load balance across servers and restart haproxy (systemctl restart haproxy)

- access url: http://<haproxy-ip>:32514

## Ingress - no host, default backend
- Assuming steps outlines under Nodeport are done, kubectl apply -f ingress-1.yaml

- Set up haproxy.cfg to load balance across servers and restart haproxy (systemctl restart haproxy)

- access url: http://9.20.194.145:8080

## Ingress - no host, multiple backends
- Assuming steps outlines under Nodeport are done, kubectl apply -f ingress-2.yaml

- Set up haproxy.cfg to load balance across servers and restart haproxy (systemctl restart haproxy)

- access url: http://9.20.194.145:8080/nginx

## Ingress - host with multiple backends
- Assuming steps outlines under Nodeport are done, kubectl apply -f ingress-3.yaml

- Set up haproxy.cfg to load balance across servers and restart haproxy (systemctl restart haproxy)

- map hostname to ip address of haproxy server

- access url: http://nginx.io:8081/nginx