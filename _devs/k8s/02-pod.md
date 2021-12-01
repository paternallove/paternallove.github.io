---
title: "Pod"
permalink: /devs/k8s/02-pod
last_modified_at: 2021-12-01
redirect_from:
  - /theme-setup/
toc: true
---

## Lifecycle
- Pending > Running > Succeeded > Faild

### 속성
- status
	- phase `Pending, Running, Succeeded, Faild, Unknown`
	- conditions `Initialized, ContainerReady, PodScheduled, Ready`
		- reason `ContainersNotReady, PodCompleted`
	- containerStatuses
		- status `wating, running, termimated`
			- reason `ContainerCreating, CrashLoopBackOff, Error, Completed`
