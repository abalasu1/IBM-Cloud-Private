PROJECT=calculator
DOCKERREPOSITORY=eucluster.icp:8500
DOCKERREPOSITORYREMOTE=eucluster.icp:8500

PROXYNODE=9.20.194.143
PROXY=http://$(PROXYNODE):$(shell kubectl get svc istio-ingressgateway -n istio-system -o json | jq .spec.ports[1].nodePort)
UIPROXY=http://$(PROXYNODE):$(shell kubectl get svc istio-ingressgateway -n istio-system -o json | jq .spec.ports[1].nodePort)

BASICOPADD=$(DOCKERREPOSITORY)/$(PROJECT)/add
BASICOPSUB=$(DOCKERREPOSITORY)/$(PROJECT)/subtract
BASICOPMUL=$(DOCKERREPOSITORY)/$(PROJECT)/multiply
BASICOPDIV=$(DOCKERREPOSITORY)/$(PROJECT)/divide
COMPOSITOP=$(DOCKERREPOSITORY)/$(PROJECT)/compositeop
PROCESSOR=$(DOCKERREPOSITORY)/$(PROJECT)/processor
UI=$(DOCKERREPOSITORY)/$(PROJECT)/ui

MULTICLUSTERREPO=$(DOCKERREPOSITORYREMOTE)/$(PROJECT)/compositeop

FORTIO_POD=$(shell kubectl get pod -n $(PROJECT) -l app=fortio -o jsonpath='{.items[0].metadata.name}')
DEBUG_POD=$(shell kubectl get pod -n $(PROJECT) -l app=curl -o jsonpath='{.items[0].metadata.name}')

init:
	-bash -c 'kubectl create ns $(PROJECT)'	
	-bash -c 'kubectl label namespace $(PROJECT) istio-injection=enabled'
	-sed -e 's~<PROJECT>~$(PROJECT)~g' -e 's~<DOCKERREPOSITORY>~$(DOCKERREPOSITORY)~g' ./imagepolicy.yaml | kubectl apply -f -	
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./debug.yaml | kubectl apply -f -	
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./messaging/redis.yaml | kubectl apply -f -	
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./psp.yaml | kubectl apply -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./ingress.yaml | kubectl apply -f -				
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/fortio-deploy.yaml | kubectl apply -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./destinationrules.yaml | kubectl apply -f -	

clean:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./destinationrules.yaml | kubectl delete -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./messaging/redis.yaml | kubectl delete -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./debug.yaml | kubectl delete -f -	
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/fortio-deploy.yaml | kubectl delete -f -	
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./ingress.yaml | kubectl delete -f -		
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./psp.yaml | kubectl delete -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./imagepolicy.yaml | kubectl delete -f -
	-bash -c 'kubectl delete ns $(PROJECT)'

init-add:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/add/svc.yaml | kubectl apply -f -

build-add:
	docker build -t $(BASICOPADD):v1.0.0 ./basicop/add

push-add:
	docker push $(BASICOPADD):v1.0.0
	
deploy-add: build-add push-add
	-sed -e 's~<CONTAINER>~$(BASICOPADD):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/add/deployment.yaml  | kubectl apply -f -

clean-add:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/add/svc.yaml | kubectl delete -f -
	-sed -e 's~<CONTAINER>~$(BASICOPADD):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/add/deployment.yaml | kubectl delete -f -

	-docker stop $(BASICOPADD):v1.0.0
	-docker rm $(BASICOPADD):v1.0.0
	-docker rmi $(BASICOPADD):v1.0.0	

test-add:
	-bash -c './basicop/add/test.sh $(PROXY)'	

init-subtract:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/subtract/svc.yaml | kubectl apply -f -

build-subtract:
	docker build -t $(BASICOPSUB):v1.0.0 ./basicop/subtract

push-subtract:
	docker push $(BASICOPSUB):v1.0.0
	
deploy-subtract: build-subtract push-subtract
	-sed -e 's~<CONTAINER>~$(BASICOPSUB):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/subtract/deployment.yaml  | kubectl apply -f -

clean-subtract:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/subtract/svc.yaml | kubectl delete -f -
	-sed -e 's~<CONTAINER>~$(BASICOPSUB):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/subtract/deployment.yaml | kubectl delete -f -
	
	-docker stop $(BASICOPSUB):v1.0.0
	-docker rm $(BASICOPSUB):v1.0.0
	-docker rmi $(BASICOPSUB):v1.0.0

test-subtract:
	-bash -c './basicop/subtract/test.sh $(PROXY)'

init-multiply:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/multiply/svc.yaml | kubectl apply -f -

build-multiply:
	docker build -t $(BASICOPMUL):v1.0.0 ./basicop/multiply

push-multiply:
	docker push $(BASICOPMUL):v1.0.0
	
deploy-multiply: build-multiply push-multiply
	-sed -e 's~<CONTAINER>~$(BASICOPMUL):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/multiply/deployment.yaml  | kubectl apply -f -

clean-multiply:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/multiply/svc.yaml | kubectl delete -f -
	-sed -e 's~<CONTAINER>~$(BASICOPMUL):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/multiply/deployment.yaml | kubectl delete -f -
	
	-docker stop $(BASICOPMUL):v1.0.0
	-docker rm $(BASICOPMUL):v1.0.0
	-docker rmi $(BASICOPMUL):v1.0.0

test-multiply:
	-bash -c './basicop/multiply/test.sh $(PROXY)'	

init-divide:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/divide/svc.yaml | kubectl apply -f -

build-divide:
	docker build -t $(BASICOPDIV):v1.0.0 ./basicop/divide

push-divide:
	docker push $(BASICOPDIV):v1.0.0
	
deploy-divide: build-divide push-divide
	-sed -e 's~<CONTAINER>~$(BASICOPDIV):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/divide/deployment.yaml  | kubectl apply -f -

clean-divide:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/divide/svc.yaml | kubectl delete -f -
	-sed -e 's~<CONTAINER>~$(BASICOPDIV):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/divide/deployment.yaml | kubectl delete -f -
	
	-docker stop $(BASICOPDIV):v1.0.0
	-docker rm $(BASICOPDIV):v1.0.0
	-docker rmi $(BASICOPDIV):v1.0.0

test-divide:
	-bash -c './basicop/divide/test.sh $(PROXY)'

init-compositeop:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/svc.yaml | kubectl apply -f -

build-compositeop:
	docker build -t $(COMPOSITOP):v1.0.0 ./compositeop

push-compositeop:
	docker push $(COMPOSITOP):v1.0.0
	
deploy-compositeop: build-compositeop push-compositeop
	-sed -e 's~<CONTAINER>~$(COMPOSITOP):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/deployment-v1.yaml | kubectl apply -f -
	-sed -e 's~<CONTAINER>~$(COMPOSITOP):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/deployment-v2.yaml | kubectl apply -f -
	-sed -e 's~<CONTAINER>~$(COMPOSITOP):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/deployment-v3.yaml | kubectl apply -f -

clean-compositeop:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/svc.yaml | kubectl delete -f -	
	-sed -e 's~<CONTAINER>~$(COMPOSITOP):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/deployment-v1.yaml | kubectl delete -f -
	-sed -e 's~<CONTAINER>~$(COMPOSITOP):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/deployment-v2.yaml | kubectl delete -f -
	-sed -e 's~<CONTAINER>~$(COMPOSITOP):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/deployment-v3.yaml | kubectl delete -f -

	-docker stop $(COMPOSITOP):v1.0.0
	-docker rm $(COMPOSITOP):v1.0.0	
	-docker rmi $(COMPOSITOP):v1.0.0

test-compositeop:
	-bash -c './compositeop/test.sh $(PROXY)'

init-processor:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./processor/svc.yaml | kubectl apply -f -

build-processor:
	docker build -t $(PROCESSOR):v1.0.0 ./processor

push-processor:
	docker push $(PROCESSOR):v1.0.0

deploy-processor: build-processor push-processor
	-sed -e 's~<CONTAINER>~$(PROCESSOR):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./processor/deployment.yaml | kubectl apply -f -

clean-processor:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./processor/svc.yaml | kubectl delete -f -
	-sed -e 's~<CONTAINER>~$(PROCESSOR):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./processor/deployment.yaml | kubectl delete -f -

	-docker stop $(PROCESSOR):v1.0.0
	-docker rm $(PROCESSOR):v1.0.0	
	-docker rmi $(PROCESSOR):v1.0.0

test-processor:
	-bash -c './processor/test.sh $(PROXY)'

init-ui:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./ui/svc.yaml | kubectl apply -f -
	-sed -e 's~<PROXY>~$(UIPROXY)~g' ./ui/src/environments/environment.ts.template > ./ui/src/environments/environment.ts
	-sed -e 's~<PROXY>~$(UIPROXY)~g' ./ui/src/environments/environment.prod.ts.template > ./ui/src/environments/environment.prod.ts	

build-ui:
	docker build -t $(UI):v1.0.0 ./ui

push-ui:
	docker push $(UI):v1.0.0
	
deploy-ui: build-ui push-ui
	-sed -e 's~<CONTAINER>~$(UI):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./ui/deployment.yaml | kubectl apply -f -
	
clean-ui:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./ui/svc.yaml | kubectl delete -f -
	-sed -e 's~<CONTAINER>~$(UI):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./ui/deployment.yaml | kubectl delete -f -

	-docker stop $(UI):v1.0.0
	-docker rm $(UI):v1.0.0
	-docker rmi $(UI):v1.0.0	

init-all: init init-add init-subtract init-multiply init-divide init-compositeop init-processor init-ui
build-all: build-add build-subtract build-multiply build-divide build-compositeop build-processor build-ui
push-all: push-add push-subtract push-multiply push-divide push-compositeop push-processor push-ui
deploy-all: deploy-add deploy-subtract deploy-multiply deploy-divide deploy-compositeop	deploy-processor deploy-ui
clean-all: clean clean-add clean-subtract clean-multiply clean-divide clean-compositeop clean-processor clean-ui
test-all: test-add test-subtract test-multiply test-divide test-compositeop test-processor

initnp:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/add/networkpolicy.yaml | kubectl apply -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/subtract/networkpolicy.yaml | kubectl apply -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/multiply/networkpolicy.yaml | kubectl apply -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/divide/networkpolicy.yaml | kubectl apply -f -			
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/networkpolicy.yaml | kubectl apply -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./processor/networkpolicy.yaml | kubectl apply -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./ui/networkpolicy.yaml | kubectl apply -f -

cleannp:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/add/networkpolicy.yaml | kubectl delete -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/subtract/networkpolicy.yaml | kubectl delete -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/multiply/networkpolicy.yaml | kubectl delete -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./basicop/divide/networkpolicy.yaml | kubectl delete -f -			
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/networkpolicy.yaml | kubectl delete -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./processor/networkpolicy.yaml | kubectl delete -f -	
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./ui/networkpolicy.yaml | kubectl delete -f -	

initts1:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/trafficshifting-1.yaml | kubectl apply -f -

testts1:
	-bash -c './config/testtrafficshifting-1.sh $(PROXY)'

cleants1:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/trafficshifting-1.yaml | kubectl delete -f -

initts2:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/trafficshifting-2.yaml | kubectl apply -f -
	
testts2:	
	-bash -c './config/testtrafficshifting-2.sh $(PROXY)'

cleants2:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/trafficshifting-2.yaml | kubectl delete -f -

initts3:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/trafficshifting-3.yaml | kubectl apply -f -
	
testts3:	
	-bash -c './config/testtrafficshifting-3.sh $(DEBUG_POD) $(PROJECT)'

cleants3:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/trafficshifting-3.yaml | kubectl delete -f -

initfd:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/fixeddelay.yaml | kubectl apply -f -

testfd:
	-bash -c './processor/test.sh $(PROXY)'

cleanfd:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/fixeddelay.yaml | kubectl delete -f -

initrt:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/requesttimeout.yaml | kubectl apply -f -

testrt:
	-bash -c './config/testtimeout.sh $(DEBUG_POD) $(PROJECT)'

cleanrt:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/requesttimeout.yaml | kubectl delete -f -

initfi:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/faultinjection.yaml | kubectl apply -f -	

testfi:
	-bash -c './processor/test.sh $(PROXY)'	

cleanfi:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/faultinjection.yaml | kubectl delete -f -

initmirror:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/trafficmirroring.yaml  | kubectl apply -f -	

testmirror:
	-bash -c './config/testmirroring.sh $(PROXY)'	

cleanmirror:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/trafficmirroring.yaml  | kubectl delete -f -

initcb:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/circuitbreaker.yaml  | kubectl apply -f -	
	
testreqcb:
	-bash -c 'kubectl exec -it $(FORTIO_POD) -n $(PROJECT) -c fortio fortio -- load -curl "http://compositeop/compositeop/eval?expr=5%2B6%2B3%2B4%2B5%2B2%2B3%2B3%2B4%2B2%2B2%2B2%2B2%2B2%2B2-2"'

testreqscb:
	-bash -c 'kubectl exec -it $(FORTIO_POD) -n $(PROJECT) -c fortio fortio -- load -c 200 -qps 100 -n 1000 -loglevel Warning "http://compositeop/compositeop/eval?expr=5%2B6%2B3%2B4%2B5%2B2%2B3%2B3%2B4%2B2%2B2%2B2%2B2%2B2%2B2%2B2%2B5%2B6%2B3%2B4%2B5%2B2%2B3%2B3%2B4%2B2%2B2%2B2%2B2%2B2%2B2%2B2"'
	-bash -c 'kubectl exec -it $(FORTIO_POD) -n $(PROJECT) -c istio-proxy -- sh -c "curl localhost:15000/stats" | grep compositeop | grep pending'

infocb:
	-bash -c 'kubectl exec -it $(FORTIO_POD) -n $(PROJECT) -c istio-proxy  -- sh -c "curl localhost:15000/stats" | grep compositeop | grep pending'

cleancb:	
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/circuitbreaker.yaml  | kubectl delete -f -		

initrl:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/ratelimit.yaml | kubectl apply -f -

testrl:
	-bash -c './compositeop/test.sh $(PROXY)'

cleanrl:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/ratelimit.yaml | kubectl delete -f -

initsp:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/securitypolicy.yaml | kubectl apply -f -

testsp:
	-bash -c './processor/test.sh $(PROXY)'

cleansp:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/securitypolicy.yaml | kubectl delete -f -

init-multicluster:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/traffic-multicluster.yaml | kubectl apply -f -
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/svc.yaml | kubectl apply -f -

build-multicluster:
	docker build -t $(MULTICLUSTERREPO):v1.0.0 ./compositeop

push-multicluster:
	docker push $(MULTICLUSTERREPO):v1.0.0

deploy-multicluster: build-multicluster push-multicluster
	-sed -e 's~<CONTAINER>~$(MULTICLUSTERREPO):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/deployment-v4.yaml | kubectl apply -f -

clean-multicluster:
	-sed -e 's~<PROJECT>~$(PROJECT)~g' ./config/traffic-multicluster.yaml | kubectl delete -f -
	-sed -e 's~<CONTAINER>~$(MULTICLUSTERREPO):v1.0.0~g' -e 's~<PROJECT>~$(PROJECT)~g' ./compositeop/deployment-v4.yaml | kubectl delete -f -

	-docker stop $(MULTICLUSTERREPO):v1.0.0
	-docker rm $(MULTICLUSTERREPO):v1.0.0	
	-docker rmi $(MULTICLUSTERREPO):v1.0.0

JAEGER_POD_NAME=$(shell kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}')
ZIPKIN_POD_NAME=$(shell kubectl get pod -n istio-system -l app=zipkin -o jsonpath='{.items[0].metadata.name}')
SERVICEGRAPH_POD_NAME=$(shell kubectl -n istio-system get pod -l app=servicegraph -o jsonpath='{.items[0].metadata.name}')
GRAFANA_POD_NAME=$(shell kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}')
PROMETHEUS_POD_NAME=$(shell kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 

monitor:
	$(shell kubectl -n istio-system port-forward $(JAEGER_POD_NAME) 16686:16686 & kubectl -n istio-system port-forward $(ZIPKIN_POD_NAME) 7411:7411 & kubectl -n istio-system port-forward $(SERVICEGRAPH_POD_NAME) 8088:8088 & kubectl -n istio-system port-forward $(GRAFANA_POD_NAME) 3000:3000 & kubectl -n istio-system port-forward $(PROMETHEUS_POD_NAME) 9090:9090)

JAEGER_POD_NAME=$(shell kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}')
KIALI_POD_NAME=$(shell kubectl -n istio-system get pod -l app=kiali -o jsonpath='{.items[0].metadata.name}')
GRAFANA_POD_NAME=$(shell kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}')
PROMETHEUS_POD_NAME=$(shell kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}')

monitor-new:
	$(shell kubectl -n istio-system port-forward $(JAEGER_POD_NAME) 16686:16686 & kubectl -n istio-system port-forward $(KIALI_POD_NAME) 20001:20001 & kubectl -n istio-system port-forward $(GRAFANA_POD_NAME) 3000:3000 & kubectl -n istio-system port-forward $(PROMETHEUS_POD_NAME) 9090:9090)


UI_POD_NAME=$(shell kubectl -n $(PROJECT) get pod -l app=ui -o jsonpath='{.items[0].metadata.name}') 

uiaccess:
	$(shell kubectl -n $(PROJECT) port-forward $(UI_POD_NAME) 9080:80)
	
restart-all:
	kubectl delete pods --all -n $(PROJECT)
