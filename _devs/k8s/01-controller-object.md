---
title: "Kubernetes Controller Object"
permalink: /devs/k8s/01-controller-object
last_modified_at: 2021-11-30
redirect_from:
  - /theme-setup/
toc: true
---

## Controller가 하는 역할
- Auto Healing
	- Pod 또는 Node에 문제가 생기면 새로운 Pod를 생성
- Software Update
	- 새로우 버전의 Pod을 생성
- Auto Scaling
	- 리소스를 초과하는 경우 추가 Pod을 생성
- Job
	- 일시적인 작업 실행

## ReplicaSet
- 역할
	- 정해진 수의 동일한 Pod이 항상 실행되도록 관리한다. 
	- 노드 장애 등의 이유로 Pod을 사용할 수 없다면 다른 노드에서 Pod을 다시 생성한다.
- <details><summary>replicaset-nginx.yaml</summary><div markdown="1">
	```yaml
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
  </div></details>
- <details><summary>replicaset-nginx-match-expression.yaml</summary><div markdown="1">
	```yaml
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
  </div></details>

## Deployment
- 역할
	- Deployment를 생서하면 ReplicaSet이 생성되고 ReplicaSet이 Pod를 생성함.
	- 애플리케이션을 업데이트할 때 ReplicaSet의 변경 사항을 저장하는 revision을 남겨 롤벡을 가능하게 함.
	- 무중단 서비스를 위한 Pod의 롤링 업데이트 전략을 지정할 수 있음.
- <details><summary>deployment-nginx.yaml</summary><div markdown="1">
	```yaml
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
  </div></details>
- <details><summary>command</summary><div markdown="1">
	```bash
	# 이전 정보를 revision으로서 보전 (최신 k8s 버전에서는 --tecord 옵션이 기본 설정임)
	kubectl apply -f {yaml 파일} --record
	
	# deployment 의 Pod 개수 변경
	kubectl scale --replicas=1 deployment {deployment 이름}
	
	# 이미지 변경 명령
	kubectl set image deployment my-nginx-deployment nginx=nginx:1.11 --record
	
	# revision 정보 확인
	kubectl rollout history deployment my-nginx-deployment
	
	# 이전 버전의 ReplicaSet으로 되돌리고 싶은 경우
	kubectl rollout undo deployment my-nginx-deployment --to-revision=1
	```
  </div></details>

## DaemonSet, Job, CronJob

### DaemonSet
	- ReplicaSet과 달리 Node의 자원과 상관 없이 각 Node에 하나씩 생성됨
	- Node에 하나를 초과한 Pod을 만들수는 없지만 특정 Node에 Pod를 안 만들수는 있음
	- 사용 목적
		- Performance : Prometheous와 같은 성능 모니터링 툴
		- Logging : Fluentd와 같은 로그 수집 툴
		- Storage : GlusterFS와 같은 툴을 사용하여 Node를 Network file 시스템으로 사용 하는 경우
### Job
	- ReplicaSet 으로 만든 Pod와 달리 프로세스가 일을 하지 않으면 Pod가 종료(지원지지는 않음)
### CronJob
	- Job을 주기적인 시간에 생성되고
	- 사용 용도
		- Backup : 데이터 벡업
		- Checking : 버전 체크
		- Messaging : 메시지 발송

