## Cluster Architecture 
- 쿠버네티스
	- 쿠버네티의 목적은 애플리케이션을 호스팅 하는 것
	- 컨테이너 형태의 자동화된 방식으로 애플리케이션 내의 다른 서비스 간에 응용 프로그램의 많은 인스턴스를 쉽게 배포할 수 있도록 함

- 쿠버네티스 클러스터
	- 일련의 노드로 구성되어 컨테이너 형태로 애플리케이션을 호스팅함
	- 작업자 노드는 선박으로 비유 가능
	- 마스터 노드 : 클러스터 관리를 위해 다른 노드에 관한 정보를 저장하고 계획하고 노드와 컨테이너 등을 모니터링함
	- ETCD : 고가용성 키-값 저장소, 정보를 저장하는 데이터베이스
- 스케줄러 : 컨테이너를 배치할 올바른 노드를 식별함 
- 컨트롤러 
	- Controller-Manager
	- Node-Controller : 노드를 관리함 새 노드를 클러스터에 온보딩하는 역할
	- Replication-Controller
- Kube-Apiserver : 쿠버네티스의 기본 관리 구성요소로 클러스터 내의 모든 작업을 오케스트레이션함
- 런타임 엔진 : 컨테이너를 실행할 수 있는 소프트웨어 (Docker, ContainerD, Rocket 등)
- Kubelet : 클러스터의 각 노드에서 실행되는 에이전트, Kube API 서버의 지시를 수신함. 필요에 따라 노드에
컨테이너를 배포하거나 제거함 
- Kube-proxy : 클러스터 내의 서비스 간 의사소통을 가능하게 도와줌. 컨테이너가 실행될 수 있도록 작업자 노드에 배치

## ETCD 
- 분산 키-값 저장소
	- key-value store
- 새로운 정보를 추가할 때마다 테이블 전체가 영향을 받음
- 키-값 저장소는 문서 또는 페이지 형태로 정보를 저장함, 한 파일을 변경해도 다른 파일에는 영향을 미치지 않음
- 데이터가 복잡해지면 JSON 또는 YANO와 같은 데이터 형식으로 저장할 수 있음

### ETCD 설치 및 작동
(생략)

- 역할 
	- 클러스터에 관한 정보를 저장함 (Nodes, PODs, Configs, Secrets, Accounts, Roles, Bindings, Others)
	- 배포방식 = 수동
	- wget -q -https-only "https://github.com/coreos/etcd/releases/download/v3.3.9/etcd-v3.3.9-linux-amd64.tar.gz"

	- kubeadm
		- kubectl get pods -n kube-system
		- kubectl exec etcd-master -n kube-system etcdctl get / --prefix -keys-only : 쿠버네티스에 저장된 모든 키를 검색

## Kube-Apiserver
- 설치 
	- wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-apiserver
	- kubectl get pods -n kube-system : kubeadmin은 kubeadmin-apiserver를 배포함
	- cat /etc/kubernetes/manifests/kube-apiserver.yaml
	- cat /etc/systemd/system/kube-apiserver.service : 옵션 검사
	ps -aux | grep kube-apiserver : 실행 중인 프로세스 확인

## Kube-Controller Manager
- 쿠버네티스의 다양한 컨트롤러를 관리함
- 컨트롤러는 지속적으로 시스템 내 다양한 구성 요소의 상태를 모니터링함. 전체 시스템을 원하는 기능 상태로 가져오기 위해 노력함
	- 노드 컨트롤러는 상태를 모니터링 하기 위해 Kube API 서버를 통해서 메모를 작성하고 필요한 조치를 취함. 상태를 테스트함
	- 복제 컨트롤러는 상태 모니터링을 담당함. 복제 세트의 수를 확인하고 원하는 수를 보장함. Pod가 죽으면 재생성함
- 쿠버네티스의 다양한 컨트롤러
- 설치 
	- wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-controller-manager
	- kubectl get pods -n kube-system
	- cat /etc/kubernetes/manifests/kube-controller-manager.yaml
	- cat /etc/systemd/system/kube-controller-manager.service
	- ps -aux | grep kube-controller-manager

## Kube Scheduler
- 노드에서 Pod 예약을 담당함. 어떤 파드가 어디로 가는지 결정함
	- 포드에 가장 적합한 노드를 식별함
	- 포드의 프로필에 맞지 않는 노드를 필터링함 (CPU가 충분하지 않은 노드, 파드에 요청한 메모리 리소스)
	- 스케줄러는 노드의 순위를 매김 (우선순위 기능을 사용하여 점수를 할당함 - 무료로 사용할 수 있는 리소스의 양)
- 더 고려해야할 부분
	- Resource Requirements and Limits
	- Taints and Tolerations
	- Node Selectors/ Affinity
- 설치
	- wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-scheduler
	- cat /etc/kubernetes/manifests/kube-scheduler.yaml
	- ps -aux | grep kube-scheduler

## Kubelet
- 노드의 컨테이너 또는 파드, 먼테이너 런타임 엔진을 요청함. 파드를 계속 모니터링함
- 설치
	- kubeadm 사용해서 클러스터를 배포하는 경우, kubelet을 자동으로 배포하지 않음. 항상 수동으로 설치해야함
	- wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubelet
	- ps -aux | grep kubelet

## Kube-proxy
- 파드 네트워크는 클러스터의 모든 노드에 걸친 내부 가상 네트워크
- Kube-proxy는 쿠버네티스 클러스터의 각 노드에서 실행되는 프로세스 새 서비스를 찾아줌. 새 서비스가 생성될 때마다 각 노드에 적절한 
규칙을 만들어 그 서비스로 트래픽을 백엔드 포드로 전달
	- 한가지 방법은 iptables 규칙을 이용하여 클러스터의 각 노드에 iptables 규칙을 만들어 서비스의 IP로 향하는 트래픽을 향하게 합니다.
- 설치
	- wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-proxy
	- kubectl get pods -n kube-system
	- kubectl get daemonset -n kube-system