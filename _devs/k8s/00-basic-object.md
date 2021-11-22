---
title: "Kubernetes 기본 Object"
permalink: /devs/k8s/00-basic-object
excerpt: "How to quickly install and setup Minimal Mistakes for use with GitHub Pages."
last_modified_at: 2021-06-07T08:48:05-04:00
redirect_from:
  - /theme-setup/
toc: true
---

- 모든 오브젝트 목록
	```
	kubectl api-resources
	```
  
1. Pod
  - 컨테이너 애플리케이션의 기본 단위
	- 한개의 Pod에는 한개 이상의 컨테이너가 존재할 수 있다.
	- 오프젝스 생성 명령
		```
		# 오프젝트 생성 명령
		kubectl apply -f {yaml 파일}
		
		# 리소스 자세히 보기
		kubectl describe pods {pod 이름}
		
		# 컨테이너 내부에 직접 들어감
		kubectl exec -it my-nginx-pod bash
		
		# 컨테이너 로그 확인
		kubectl logs my-nginx-pod
		
		# 오프젝트 제거
		kubectl delete -f {yaml file}
		kubectl deleto pod my-nginx-pod
		
		# 클러스터 내부에서 테스트용 Pod를 생성해서 임시로 사용 하기 위한 스크림트
		kubectl run -i --tty --rm debug --image=alicek106/ubuntu:curl --restart=Never bash
		```
	- gb
		* a
		

2. Service
