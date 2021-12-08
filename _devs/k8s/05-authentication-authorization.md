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
		- 속성 : name, url, CA
		{: .notice--info}
	- users 항목
		- 속성 : name, crt, key
		{: .notice--info}
	- contexts 항목 : cluster와 users를 정보를 연결
		- 속성 : name, cluster, user
		{: .notice--info}

#### Service Account 를 사용
1. k8s cluster에 Namespace를 만들면 기본적의로 default 이름의 ServiceAccount가 생성 된다.
2. 이 ServiceAccount에는 Secret이 생성되어 있으며 여기에는 CA crt 정보와 token 값이 들어 있음.
3. Pod를 만들면 해당 ServiceAccount와 연결이 되고 token 값을 사용하여 API 서버와 연결이 된다.
4. 결국 token 값을 알게 되면 사용자도 API 서버에 접근 할수 있다.


## Authorization