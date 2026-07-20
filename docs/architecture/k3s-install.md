# Install k3s Kubernetes Cluster

## Goal

Docker Compose 기반 환경에서 Kubernetes 기반 플랫폼으로 전환하기 위해 k3s를 설치한다.

이번 단계에서는 Single Node Kubernetes Cluster를 구성하고, Kubernetes Control Plane이 정상적으로 동작하는지 확인한다.

---

## Why k3s?

기존 Docker Compose는 하나의 서버에서 컨테이너를 실행하고 관리하기에는 충분하지만, 다음과 같은 한계가 있다.

- 컨테이너 장애 발생 시 자동 복구 기능 부족
- Replica 관리 어려움
- Rolling Update 미지원
- 선언형(Declarative) 배포 불가능
- 대규모 서비스 운영 한계

Kubernetes는 이러한 문제를 해결하기 위한 컨테이너 오케스트레이션 플랫폼이며,

본 프로젝트는 로봇 플랫폼(On-Premise / Edge 환경)을 목표로 하기 때문에 경량 Kubernetes 배포판인 **k3s**를 선택하였다.

### Why k3s?

- Lightweight Kubernetes
- ARM64 공식 지원
- Edge / Robotics 환경에 적합
- 설치가 매우 간단
- CNCF Certified Kubernetes

---

## Environment

| Item | Value |
|------|------|
| Host | MacBook (Apple Silicon) |
| Virtualization | UTM |
| Guest OS | Ubuntu Server 24.04 ARM64 |
| Kubernetes | k3s |
| Architecture | ARM64 |

---

## Installation

### Install k3s

```bash
curl -sfL https://get.k3s.io | sh -
```

설치가 완료되면 다음 서비스들이 자동으로 구성된다.

- Kubernetes API Server
- Scheduler
- Controller Manager
- CoreDNS
- Local Path Provisioner
- Container Runtime(containerd)

---

## Configure kubectl

매번 `sudo k3s kubectl`을 사용하는 대신 일반 사용자 환경에서 `kubectl`을 사용할 수 있도록 kubeconfig를 설정하였다.

```bash
mkdir -p ~/.kube

sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config

sudo chown $USER:$USER ~/.kube/config

echo 'export KUBECONFIG=$HOME/.kube/config' >> ~/.bashrc

source ~/.bashrc
```

---

## Verification

### Check k3s Service

```bash
sudo systemctl status k3s
```

Expected

```
active (running)
```

---

### Check Kubernetes Node

```bash
kubectl get nodes
```

Expected

```
NAME       STATUS   ROLES                  AGE
ubuntu     Ready    control-plane,master
```

Node가 `Ready` 상태이면 Kubernetes Cluster가 정상적으로 동작하는 것이다.

---

### Check System Pods

```bash
kubectl get pods -A
```

Example

```
kube-system
├── coredns
├── metrics-server
├── local-path-provisioner
└── ...
```

이는 Kubernetes 운영에 필요한 기본 시스템 Pod이며, k3s 설치 시 자동으로 생성된다.

---

## Project Status

```
Mac
    │
SSH
    │
Ubuntu Server
    │
k3s
    │
Kubernetes Cluster
    │
System Pods
```

현재는 Kubernetes 플랫폼만 구축된 상태이며, 아직 사용자 애플리케이션은 배포하지 않았다.

다음 단계에서는 직접 Pod를 생성하고 Kubernetes 리소스를 관리한다.

---

## What I Learned

- Docker와 Kubernetes의 역할 차이
- k3s를 이용한 Kubernetes Cluster 구축 방법
- Kubernetes Control Plane 구성 확인
- kubectl 환경 구성
- Node와 Pod 상태 확인 방법
- Kubernetes 기본 구조 이해

---

## Commands Used

```bash
curl -sfL https://get.k3s.io | sh -

sudo systemctl status k3s

kubectl get nodes

kubectl get pods -A
```

---

## Result

- ✅ Single Node Kubernetes Cluster 구축 완료
- ✅ k3s 서비스 정상 동작
- ✅ Control Plane 정상 실행
- ✅ System Pods 정상 생성
- ✅ kubectl 환경 구성 완료
