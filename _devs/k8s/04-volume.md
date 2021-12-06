---
title: "Volume"
permalink: /devs/k8s/04-volume
last_modified_at: 2021-12-06
redirect_from:
  - /theme-setup/
toc: true
---

## Volume Network Service

### Internal Network
- kubernetes
	- hostPath
	- local
- On-Premise Solution
	- storageOS
	- ceph
	- ClusterFS
- NFS
{: .notice--info}

### External Network
Cloud Storage
	- AWS
	- Google Cloud Platform
	- Microsoft Azure
{: .notice--info}

	
## Dynamic Provisioning
- 사용자가 PVC를 만들면 PV를 만들고 실제로 Volume과 연결해 주는 기능
- Dynamic Provisioning 을 지원하는 Storage solution인 설치되어 있어야 함.
{: .notice--info}

### StorageClass
- storageClassName 에 만들어 놓은 StorageClass의 name을 넣으면 PV를 만듬
- defalut StorageClass를 만들어 놓으면 storageClassName을 생략한 경우 사용해서 PV를 만듬
{: .notice--info}

## PV

### Status
- Available : 최초 생성 되었을때
- Released : PVC와 연결이 끊어진 경우
- Bound : PVC와 연결되었을때
- Failed : PV와 실제 데이터간의 문제가 발생한 경우.
{: .notice--info}

### Reclaim Policy
- Retain : default , 데이터 보존, 재사용 불가, PVC가 삭제되면 PV의 상태가 released가 됨
- Recycle (deprecated) : 데이터 삭제, 재사용 가능
- Delete : storageClass 사용시 default, volume에 따라 데이터 삭제됨, 재사용 불강, PVC를 지우면 PV도 지원짐.
{: .notice--info}