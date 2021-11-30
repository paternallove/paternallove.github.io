---
title: "Kubernetes Controller Object"
permalink: /devs/k8s/01-controller-object
last_modified_at: 2021-11-30
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
### 1. ReplicaSet
### 2. Deployment
### 3. DaemonSet, Job, CronJob

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
