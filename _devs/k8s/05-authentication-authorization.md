---
title: "Authentication & Authorization"
permalink: /devs/k8s/05-authentication-authorization
last_modified_at: 2021-12-03
redirect_from:
  - /theme-setup/
toc: true
---

## Authentication

### k8s API 접근 방법 3가지

#### X509 Client Certs
1. kubeconfig 파일에 CA crt, Clinet crt, Client key 정보를 담아둔다.
2. kubectl에 kubeconfig를 참조하도록 한다.
3. kubectl의 Proxy에 accept-hots를 설정한다.

#### 외부의 kubectl을 통해서 접근
1. 외부 kubectl에 접근하고자 하는 cluster의 kubeconfig 파일이 있어야 한다.
2. kubeconfig 내용
	- clusters 항목
		속성 : name, url, CA
		{: .notice--info}
	- users 항목
		속성 : name, crt, key
		{: .notice--info}

#### 

## Authorization