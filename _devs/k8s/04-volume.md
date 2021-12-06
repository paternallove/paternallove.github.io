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
kubernetes
	- hostPath
	- local
On-Premise Solution
	- storageOS
	- ceph
	- ClusterFS
NFS
{: .notice--info}
	
### External Network
Cloud Storage
	- AWS
	- Google Cloud Platform
	- Microsoft Azure
{: .notice--info}

	
## Dynamic Provisioning
사용자가 PVC를 만들면 PV를 만들고 실제로 Volume과 연결해 주는 기능

### StorageClass

## PV

### Status
- Available
- Released
- Bound
- Failed

### Policy
- Retain
- Recycle
- Delete