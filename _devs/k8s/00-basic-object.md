---
title: "Kubernetes 기본 Object"
permalink: /devs/k8s/00-basic-object
last_modified_at: 2021-11-22
redirect_from:
  - /theme-setup/
toc: true
---

- <details><summary>command</summary><div markdown="1">
	```bash
	# 모든 오브젝트 목록
	kubectl api-resources
	
	# 모드 리소스 제거
	kubectl delete deployment,pod,rs --all
	```
  </div></details>

## Pod
- 컨테이너 애플리케이션의 기본 단위
- 한개의 Pod에는 한개 이상의 컨테이너가 존재할 수 있다.
- Pod 내부의 컨테이너들은 같은 네트워크 네임스페이와 같은 리눅스 네임스페이스를 공유해서 사욯한다.
- <details><summary>nginx-pod.yaml</summary><div markdown="1">
	```yaml
	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-nginx-pod
	spec:
	  containers:
	  - name: my-nginx-container
		image: nginx:latest
		ports:
		- containerPort: 80
		  protocol: TCP

	  - name: ubuntu-sidecar-container
		image: alicek106/rr-test:curl
		command: ["tail"]
		args: ["-f", "/dev/null"] # 포드가 종료되지 않도록 유지합니다
	```
  </div></details>
  
- <details><summary>command</summary><div markdown="1">
	```bash
	# 오브젝트 목록
	kubectl get pods -o wide
	kubectl get pods --show-labels	# 라벨도 표시
	kubectl get pods -l app	# 라벨의 key가 "app"인 Pods  
	kubectl get pods -l app=my-nginx-pods-label	# 라벨의 key가 "app"이고 value가 "my-nginx-pods-label"인 Pods
	
	# 오프젝트 생성 명령
	kubectl apply -f {yaml 파일}
	
	# 리소스 자세히 보기
	kubectl describe pods {pod 이름}
	kubectl get pods {pod 이름} -o yaml
	
	# 오브젝트 수정
	kubectl edit pods {pod 이름}
	
	# Pod 내부에 직접 들어감
	kubectl exec -it {pod 이름} -- bash
	
	# Pod의 특정 컨테이너에 명령을 실행
	kubectl exec -it {pod 이름} -c {컨테이너 이름} -- bash
	
	# Pod 로그 확인
	kubectl logs {pod 이름}
	
	# 오프젝트 제거
	kubectl delete -f {yaml file}
	kubectl deleto pod my-nginx-pod
	
	# 클러스터 내부에서 테스트용 Pod를 생성해서 임시로 사용 하기 위한 스크림트
	kubectl run -i --tty --rm debug --image=alicek106/ubuntu:curl --restart=Never bash
	kubectl run -i --tty --rm debug --image=alicek106/ubuntu:curl --restart=Never -- bash
	```
  </div></details>
  
- sidecar container : Pod에 정의된 부가적인 컨테이넝

## Service
- 역할
	- 고유한 도메인 이름을 부여한다.
	- load balance 기능을 수행한다.
	- Cloud Platform의 로드 밸런서, 클러스터 노드의 포트 등을 통해 Pod를 외부로 노출한다.
- <details><summary>deployment-hostname.yaml</summary><div markdown="1">
	```yaml
	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: hostname-deployment
	spec:
	  replicas: 3
	  selector:
		matchLabels:
		  app: webserver
	  template:
		metadata:
		  name: my-webserver
		  labels:
			app: webserver
		spec:
		  containers:
		  - name: my-webserver
			image: alicek106/rr-test:echo-hostname
			ports:
			- containerPort: 80
	```
  </div></details>
  
- 종류
	- ClusterIP 타입
		- k8s 내부에서만 Pod를 접근할 때 사용. 외부로 Pod을 노출하지 않음
		- 별도의 설정이 없어도 서비스는 연결된 Pod에 대하여 로드 밸런싱을 수행함.
		- <details><summary>hostname-svc-clusterip.yaml</summary><div markdown="1">
			```yaml
			apiVersion: v1
			kind: Service
			metadata:
			  name: hostname-svc-clusterip
			spec:
			  ports:
				- name: web-port
				  port: 8080
				  targetPort: 80
			  selector:
				app: webserver
			  type: ClusterIP
			```
			</div></details>
		- 클러스터 내부에서 서로를 찾아 연결해야 할때는 서비스 이름(hostname-svc-clusterip)과 같은 도메인 이름을 사용하는 것이 일반적
	- NodePort 타입
		- k8s 외부에서 Pod에 접근할 때 사용. port를 cluster의 모든 Node에 동일하게 개방
		- NodePort의 포트범위는 30000~32768 이지만 API 서버의 옵션을 변경하여 범위 설정이 가능하다.
			`--service-node-port-range=30000-35000`
		- **Ingress**(외부요청을 실제로 받아들이는 관문) : NodePort 서비스는 그 자체를 통해서 외부로 제공하기 보다 Ingress 오브젝트에서 간접적으로 사용되는 경우가 많음.
		- <details><summary>hostname-svc-nodeport.yaml</summary><div markdown="1">
			```yaml
			apiVersion: v1
			kind: Service
			metadata:
			  name: hostname-svc-nodeport
			spec:
			  ports:
				- name: web-port
				  port: 8080
				  targetPort: 80
			  selector:
				app: webserver
			  type: NodePort
			```
			</div></details>
		- <details><summary>hostname-svc-nodeport-custom.yaml (node port 설정)</summary><div markdown="1">
			```yaml
			apiVersion: v1
			kind: Service
			metadata:
			  name: hostname-svc-nodeport
			spec:
			  ports:
				- name: web-port
				  port: 8080
				  targetPort: 80
				  nodePort: 31000 # 30000~32768 사이에서 자동 선택됨.
			  selector:
				app: webserver
			  type: NodePort
			```
			</div></details>
		- 특정 클라이언트가 같은 Pod으로 부터 처리되게 하려면 서비스의 설정에 sessionAffinity: ClientIP 를 셋팅
		<details><summary>hostname-svc-nodeport-affinity.yaml</summary><div markdown="1">
			```yaml
			apiVersion: v1
			kind: Service
			metadata:
			  name: hostname-svc-nodeport-affinity
			spec:
			  sessionAffinity: ClientIP
			  ports:
				- name: web-port
				  port: 80
				  targetPort: 80
			  selector:
				app: webserver
			  type: NodePort
			```
			</div></details>
	- LoadBalancer 타입
		- k8s 외부에서 Pod에 접근할 때 사용. AWS, GCP 등과 같은 Cloud Platform 환경에서만 사용. LoadBalancer를 동적으로 프로비저닝해 Pod에 연결.
		- <details><summary>hostname-svc-lb.yaml</summary><div markdown="1">
			```yaml
			apiVersion: v1
			kind: Service
			metadata:
			  name: hostname-svc-lb
			spec:
			  ports:
				- name: web-port
				  port: 80
				  targetPort: 80
			  selector:
				app: webserver
			  type: LoadBalancer
			```
			</div></details>
- <details><summary>command</summary><div markdown="1">
	```bash
	# 서비스와 관련된 endpoint 확인
	kubectl get endpoint
	kubedtl get ep
	```
	</div></details>
		
### 트래픽의 분배를 결정 하는 서비스 속성 : externalTrafficPolicy
- externalTrafficPolicy: Cluster
	- 클러스터의 모든 노드에 랜덤한 포트를 개방
	- 서비스의 기본 설정값
- externalTrafficPolicy: Local
	- Pod이 생성된 노드에서만 Pod로 접근 가능.
	- 추가적인 네트워크 홉이 발생 하지 않음, 전달되는 요청의 클라이언트 IP 가 보존됨.
	- <details><summary>hostname-svc-lb-local.yaml</summary><div markdown="1">
		```yaml
		apiVersion: v1
		kind: Service
		metadata:
		  name: hostname-svc-lb-local
		spec:
		  externalTrafficPolicy: Local
		  ports:
			- name: web-port
			  port: 80
			  targetPort: 80
		  selector:
			app: webserver
		  type: LoadBalancer
		```
		</div></details>
			
### 요청을 외부로 리다이렉트하는 서비스 : ExternalName
- k8s를 외부 시스템과 연동해야 할때 
- <details><summary>external-svc.yaml</summary><div markdown="1">
	```yaml
	# externalname-svc로 요청을 보내면 paternallove.github.io에 접근하게 됨.
	apiVersion: v1
	kind: Service
	metadata:
	  name: externalname-svc
	spec:
	  type: ExternalName
	  externalName: paternallove.github.io
	```
	</div></details>

## Volume
### emptyDir
### hostPath
### PVC / PV

## ConfigMap / Secret
### ConfigMap
### Secret

## Namespace / ResourceQuota / LimitRage
### Namespace
### ResourceQuota
### LimitRage

