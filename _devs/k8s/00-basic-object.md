---
title: "Kubernetes 기본 Object"
permalink: /devs/k8s/00-basic-object
last_modified_at: 2021-11-22
redirect_from:
  - /theme-setup/
toc: true
---

- command
	```
	# 모든 오브젝트 목록
	kubectl api-resources
	
	# 모드 리소스 제거
	kubectl delete deployment,pod,rs --all
	```

1. **Pod**
	- 컨테이너 애플리케이션의 기본 단위
	- 한개의 Pod에는 한개 이상의 컨테이너가 존재할 수 있다.
	- Pod 내부의 컨테이너들은 같은 네트워크 네임스페이와 같은 리눅스 네임스페이스를 공유해서 사욯한다.
	- sample yaml
		```
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
	- command
		```
		# 오브젝트 목록
		kubectl get pods -o wide
		kubectl get pods --show-labels	# 라벨도 표시
		kubectl get pods -l app	# 라벨의 key가 "app"인 Pods  
		kubectl get pods -l app=my-nginx-pods-label	# 라벨의 key가 "app"이고 value가 "my-nginx-pods-label"인 Pods
		
		# 오프젝트 생성 명령
		kubectl apply -f {yaml 파일}
		
		# 리소스 자세히 보기
		kubectl describe pods {pod 이름}
		
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
		```
	- sidecar container : Pod에 정의된 부가적인 컨테이넝
		

2. **Replica set**
	- 역할 : 정해진 수의 동일한 Pod이 항상 실행되도록 관리한다. 노드 장애 등의 이유로 Pod을 사용할 수 없다면 다른 노드에서 Pod을 다시 생성한다.
	- replicaset-nginx.yaml
		```
		apiVersion: apps/v1
		kind: ReplicaSet
		metadata:
		  name: replicaset-nginx
		spec:
		  replicas: 3
		  selector:
			matchLabels:
			  app: my-nginx-pods-label
		  template:
			metadata:
			  name: my-nginx-pod
			  labels: 
				app: my-nginx-pods-label
			spec:
			  containers:
			  - name: nginx
				image: nginx:latest
				ports:
				- containerPort: 80
		```
	- replicaset-nginx-match-expression.yaml
		```
		apiVersion: apps/v1
		kind: ReplicaSet
		metadata:
		  name: replicaset-nginx
		spec:
		  replicas: 3
		  selector:
			matchLabels:
			  app: our-nginx-pods-label
			matchExpressions:
			  - key: app2
				values:
				  - my-nginx-pods-label
				  - your-nginx-pods-label
				operator: In
		  template:
			metadata:
			  name: my-nginx-pod
			  labels:
				app: our-nginx-pods-label
				app2: my-nginx-pods-label
			spec:
			  containers:
			  - name: nginx
				image: nginx:latest
				ports:
				- containerPort: 80
		```
		
3. **Deployment**
	- 역할 : Deployment를 생서하면 ReplicaSet이 생성되고 ReplicaSet이 Pod를 생성함.
	애플리케이션을 업데이트할 때 ReplicaSet의 변경 사항을 저장하는 revision을 남겨 롤벡을 가능하게 함.
	무중단 서비스를 위한 Pod의 롤링 업데이트 전략을 지정할 수 있음.
	- deployment-nginx.yaml
		```
		apiVersion: apps/v1
		kind: Deployment
		metadata:
		  name: my-nginx-deployment
		spec:
		  replicas: 3
		  selector:
			matchLabels:
			  app: my-nginx
		  template:
			metadata:
			  name: my-nginx-pod
			  labels:
				app: my-nginx
			spec:
			  containers:
			  - name: nginx
				image: nginx:1.10
				ports:
				- containerPort: 80
		```
	- command
		```
		# 이전 정보를 revision으로서 보전 (최신 k8s 버전에서는 --tecord 옵션이 기본 설정임)
		kubectl apply -f {yaml 파일} --record
		
		# 이미지 변경 명령
		kubectl set image deployment my-nginx-deployment nginx=nginx:1.11 --record
		
		# revision 정보 확인
		kubectl rollout history deployment my-nginx-deployment
		
		# 이전 버전의 ReplicaSet으로 되돌리고 싶은 경우
		kubectl rollout undo deployment my-nginx-deployment --to-revision=1
		```

4. **Service**
	- 역할 : Pod를 연결하고 외부에 노출
	고유한 도메인 이름을 부여한다.
	load balance 기능을 수행한다.
	크랄우드 플랫폼의 로드 밸런서, 클러스터 노드의 포트 등을 통해 Pod를 외부로 노출한다.
