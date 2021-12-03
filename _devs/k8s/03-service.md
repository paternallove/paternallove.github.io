---
title: "Service"
permalink: /devs/k8s/03-service
last_modified_at: 2021-12-01
redirect_from:
  - /theme-setup/
toc: true
---

**FQDN (Fully Qualified Domain Name)**
호스트와 도메인을 함께 명시하여 전체 경로를 모두 표기하는 것
{: .notice--info}

## Headless Service
- Pod에 직접 접근 가능한 고유 IP를 부여한다.
- Headless Service를 생성하는 방법은 ClusterIP: None인 서비스를 생성하는 것이다.
- <details><summary>svc_headless.yaml</summary><div markdown="1">
	```yaml
	apiVersion: v1
	kind: Service
	metadata:
	  name: headless-app
	  labels:
		app: headless-app
	spec:
	  ports:
	  - port: 80
		name: headless-app-port
	  clusterIP: None
	  selector:
		app: headless-app
	```
	</div></details>

## Endpoint Service
- 일반적으로 서비스를 생성하면 Service의 selector와 Pod에 labels를 이용하여 endpoint가 자동 생성 된다.

## ExternalName Service
- 내부 파드가 외부의 특정 FQDN에 쉽게 접근하기 위한 서비스. 서비스 이름으로 외부 서버에 접근 가능.
- spec.type: ExternalName 으로 선언 하면 된다.
- <details><summary>svc_external.yaml</summary><div markdown="1">
	```yaml
	apiVersion: v1
	kind: Service
	metadata: 
	  name: myapp-extname
	spec:
	  type: ExternalName
	  externalName: www.google.com
	```
	</div></details>