# Calculator
A simple polyglot microservices application to experiment with and understand features of istio service mesh.
![calculator](calculator.png?raw=true "calculator")

## Getting Started
Clone the project or download to your local file system

### Prerequisites
1. A working kubernetes cluster like IBM Cloud Private (2.1 or latter)
2. Set up kubectl to point to a valid kubernetes cluster
3. Install istio on the cluster (https://istio.io/docs/setup/kubernetes/) 1.0 or latter
4. python, node.js, ruby, go compilers are needed to compile locally

### Customization:
1. Change PROJECT (namespace), DOCKERREPOSITORY(image registry address) and PROXYNODE to match your cluster
configuration

### Deployment
To deploy the entire application:
1) "make init-all" command initializes all the services, ingress and other cluster roles needed
2) "make deploy-all" deploys all the services

To deploy the services one by one:
1) "make init-add" and "make deploy-add" to deploy add service.
2) "make init-subtract" and "make deploy-subtract" to deploy subtract service.
3) "make init-multiply" and "make deploy-multiply" to deploy multiply service.
4) "make init-divide" and "make deploy-divide" to deploy divide service.
5) "make init-compositeop" and "make deploy-compositeop" to deploy compositeop service.
6) "make init-processor" and "make deploy-processor" to deploy processor service.
7) "make init-ui" and "make deploy-ui" to deploy ui for the application.

## Versioning of compositeop service:
There are three versions of the compositeop service deployment. It is the same source code deployed thrice with
each one returning version=v1, v2 & v3 respectively. This is used to test traffic shaping and other istion features.

## Running the tests
1) "make test-processor" to test the services end to end
2) Individual services can be tested easily with "make test-add",  "make test-subtract", "make test-multiply", "make test-divide" & "make test-compositeop"

## Traffic shaping
3) Direct all traffic to v1 of the compositeop service.
a) "make initts1" - Set up the traffic rules
b) "make testts1" - 80% of the traffic goes to v1, 10% to v2 & 10% to v3
c) "make cleants1" - clean up the traffic rules

3) Direct all traffic to v3 of the compositeop service. 
a) "make initts2" - Set up the traffic rules
b) "make testts2" - 80% of the traffic goes to v3, 10% to v1 & 10% to v3
c) "make cleants2" - clean up the traffic rules

3) Direct traffic to v2 based on presence of a http header
a) "make initts3" - Set up the traffic rules
b) "make testts3" - two calls one with the header and another without to demonstrate redirection to traffic
c) "make cleants3" - clean up the traffic rules

4) Traffic mirroring - Mirror calls to a service
a) "make initmirror" - Set up mirroring of calls to compositeop-v1 to compositeop-v2
b) "make testmirror" - Execute test calls to show mirroring, use jaegar ui to see the calls mirrored
c) "make cleanmirror" - clean up the mirror rules

## Testing microservices
1) Introduce a fixed delay of 30s to v1 of compositeop servie
a) "make initfd" - Set up the fixed dealy
b) "make testfd" - all calls take time to execute because of the 30s delay to the compositeop service
c) "make cleanfd" - clean up the virtual service

2) Combine fixed delay and request timeout to demonstrate timeouts for microservice through istio
a) "make initrt" - Set up the fixed dealy & request timeouts
b) "make testrt" - see calls to processor service timeout in 10s because of a fixed delay os 30s
c) "make cleanrt" - clean up the virtual services

3) Fault injection
a) "make initfi" - Set up the http faults with code 500 for all traffic to compositeop-v2
b) "make testfi" - Some calls time out with the error
c) "make cleanfi" - clean up the virtual services

## Handling microservice failures
1) Circuit Breaker - Set up a circuit breaker
a) "make initcb" - Set up circuit breaker for compositeop service
b) "make testreqcb" - Use fortio load testing tool to execute a single call, this should succeed
c) "make testreqscb" - Use fortio to execute multiple calls concurrently and this should cause circuit breaker to be triggered
d) "make infocb" - Query istio proxy to see the pending calls to verify circuit breaker trigger
e) "make cleancb" - clean up the circuit breaker setup

## Monitoring
1) Run "make monitor" on a separate command line to set up all monitoring services. This assumes all istio addons
are installed. 
a) http://localhost:16686 - Jaeger UI to see the tracing
b) http://localhost:7411 - Zipkin UI to see the traces
c) http://localhost:9090 - Prometheus UI to query istio metrics
d) http://localhost:3000 - Grafana UI to see metric dashboards
e) http://localhost:8088/force/forcegraph.html - Istio Service graph UI

## Security
1) Network policies - Use network policies to secure services
a) initnp - Initialize allowed traffic between microservices
b) cleannp - clean up network policies

2) Seucirty Policies - Work in Progress

## Work in Progress
Istio Multicluster, Rate limiting

## Cleanup
a) "make restart-all" - restart all pods within the calculator application

b) "make clean" - Clean up the entire application, namespaces and all services, ingress inside
