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

### 속성 및 상태
- status
	- phase `Pending, Running, Succeeded, Faild, Unknown`
	- conditions `Initialized, ContainerReady, PodScheduled, Ready`
		- reason `ContainersNotReady, PodCompleted`
	- containerStatuses
		- status `wating, running, termimated`
			- reason `ContainerCreating, CrashLoopBackOff, Error, Completed`
{: .notice--info}

## ReadinessProbe / LivenessProbe

### ReadinessProbe
- App 구동 순간에 트래픽이 발생하여 에러가 발생하는 것을 방지할 수 있다. App이 구동되기 전 까지 서비스와 연결을 안되게 함.

### LivenessProbe
- Pod의 App의 장애 사항을 판단해 Pod를 재실행 줌.

### 속성
- httpGet
	- port
	- host
	- path
	- httpHeader
	- schema
- exec
	- commnad
- tcpSocket
	- port
	- host
- initialDelaySeconds : 최초 probe를 하기전에 딜레이 시간 (default : 0)
- periodSeconds : probe를 체크하는 시간의 간격 (default : 10)
- timeoutSeconds : 이 지정된 시간까지 결과가 와야함 (default : 1)
- succesThreshold : 몇번 성공을 받아야 성공으로 판단 할건지 (default : 1)
- failureThreshold : 몇번 실패를 방아야 실패로 판단 할건지 (default : 3)
{: .notice--info}

## QoS classes
- Pod의 중요도에 따른 Pod를 생성하고 중요하지 않은 Pod를 중지 할수 있다.
- 중요도 : `Guaranteed > Burstable > BestEffort`

- Guaranteed
	- Pod의 모든 container에 request와 limit가 설정 되어 있어야 함.
	- request와 limit에는 memory와 cpu가 모두 설정 되어 있어야 함.
	- 각 container 내에서 memory와 cpu의 request와 limit의 값이 같아야 한다.
	{: .notice--info}
	
- Burstable
	- request와 limit이 설정되어 있지만 memory와 cpu의 request와 limit의 값이 다른 경우.
	- request와 limit중 하나만 설정된 경우.
	- Pod의 container 중에 하나만 완벽하게 설정된 경우.
	- 기타 Guaranteed 와 BestEffort 조건이 아닌 경우.
	- Burstable 상태중 우선 순위
		- OOM Score(App의 메모리 / request 메모리)가 높음 Pod를 먼저 제거함.
	{: .notice--info}
	
- BestEffort
	- Pod의 어떤 Continer 내에도 request와 limit가 모두 설정되어 있지 않은 경우.
	{: .notice--info}


