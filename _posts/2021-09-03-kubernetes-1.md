---
layout: post
title:  " kubernetes 개념정리"
date:   2021-09-03
desc: "kubernetes pod"
keywords: "Kubernetes"
categories: [Kubernetes]
tags: [kubernetes]
icon: icon-html
---

Kubernetes Object
====
- Kubernetes Object란 쿠버네티스 시스템에서 영속성을 가지는 오브젝트이다
    - 어떤 컨테이너화된 Application이 동작중인지
    - Application션이 사용할 수 있는 리소스 정의
    - Application의 재구동,업그레이드,에러발생시 어떻게 동작해야 하는지에 대한 명세

- 오브젝트 구성을 위한 필드
    - apiVersion: 해당 오브젝트를 생성하기 위해 사용하는 쿠버네티스 API 버전이 무엇인지
    - kind: 어떤 종류의 오브젝트를 생성하고자 하는지
    - metadata: 오브젝트의 이름,UID,namespace등 오브젝트를 유일하게 구분지어줄 데이터
    - spec: 오브젝트에 대해 어떤 의도하는 상태


Pod
====

- 정의
    - 쿠버네티스에서 사용되는 컨테이너의 관리 단위로 생성/관리/배포 가능한 가장 작은 단위이며 클러스터내에서 실제 어플리케이션으로 구동되는 Object이다.
- 특징
    - Pod 1개에 여러개의 컨테이너를 담을 수 있으며 storage,network를 내부 컨테이너와 공유할 수 있다.
    - Pod는 개별IP를 가지며 Pod내의 컨테이너는 동일한 IP를 갖는다.
    - Pod는 언제든지 Scale Out/in, 삭제,복제가 가능하기 때문에 다른 자체 IP를 갖고 있음에도 불구하고 다른 Pod들과의 통신을 위해 Service Object를 이용해야 한다.

<br>

Pod template
====

``` yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: my-nginx
  name: my-nginx
  namespace: default        
spec:
  containers:
  - image: nginx:1.14.1
    imagePullPolicy: Always
    name: my-nginx
    ports:
    - containerPort: 80
      protocol: TCP
```

<br/>

- 각 명칭에 대한 설명
    - apiVersion
        - v1: 쿠버네티스에서 발행한 첫 release API
        - apps/v1: Deployment, RollingUpdate, ReplicaSet 기능 추가
        - extensions/v1beta: Deployments, DaemonSets, ReplicatSets, Ingress 기능 추가
    - kind
        - Object의 종류
            - pod,deployment,service,daemonset,configMap등
    - metadata
        - name: 해당 오브젝트의 이름
        - labels: 오브젝트의 특성을 식별하는데 사용하지만 코어 시스템에 직접적인 의미는 없음 즉 레이블로 오브젝트의 하위 집합을 선택,구성하는데 사용할 수 있다.
    - namespace: 물리 클러스터를 논리적인 가상클러스터로 분리할 수 있는데 이를 네임스페이스라고 한다.
        - plus) service를 생성하면 DNS엔트리가 생성되는데 {service name}.{namespace name}.svc.cluster.local 이를 통해 IP대신 위와 같은 형식으로 서비스에 접근할 수 있다.

    - spec
        - containers: pod에는 컨테이너가 다수들어갈 수 있음
            - image: pod를 구성하는 이미지의 registry주소 작성
            - imagePullPolicy: image 배포시 기본설정 값으로 배포하려는 이미지가 Node에 존재할 경우 이미지 다운로드를 skip한다
                - Always: 배포시마다 registry에서 이미지를 다운
                - IfNotPresent: Default설정 값으로 노드에 이미지가 존재할경우 이미지 다운로드 skip
                - Never: 배포시 레지스트리에서 이미지를 다운받지 않고 Node에 존재하는 이미지를 사용한다, 단 Node에 해당 이미지가 존재하지 않을 경우 에러 발생
            - ImagePullSecrets: harbor같은 private Registry를 이용하기 위해 다운로드 권한이 저장된 Secret를 생성하여 해당 키를 설정해줘야 한다.
            - ports: 포트 여러개 가능
                - containerPort: 해당 컨테이너의 포트
                - protocol: TCP,UDP등 프로토콜 선택
            - name: 컨테이너의 이름

<br/>
<br/>

Service
====

- 사용이유
    - Pod의 경우 생성될 때 마다 IP가 새로 지정되어 restart때마다 변하기 때문에 고정된 엔드포인트로 호출이 어렵다.
    - 여러 Pod에 같은 어플리케이션을 운용할 경우 이 Pod들 간의 로드벨런싱을 지원해야 하는데 서비스가 이러한 역할을 한다.
    - 지정된 IP로 생성가능,로드벨런싱기능,고유한 DNS이름 소유 가능

<br/>

Service Template
====

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  type: NodePort
  ports:
  - name: http
    port: 8080
    targetPort: 80
    protocol: TCP
  - name: https
    port: 443
    targetPort: 80
    protocol: TCP
  selector:
    run: my-nginx
```
<br/>

![K8S_Service_architecture](https://user-images.githubusercontent.com/37110261/132088938-23e26f8f-2cdc-427b-ba50-3c529f3e8ead.png)

<br/>

``` yaml
spec:
  type: NodePort
```

- service의 유형
    - ClusterIP: 디폴트 설정으로 서비스에 클러스터IP(내부IP)를 할당하기때문에 클러스터 내에서는 이 서비스에 접근 가능하지만 외부에서는 접근 불가
    - NodePort: 모든 노드의 IP와 포트를 통해 접근이 가능해지고 포트는 30000번대를 할당받는다.
    <img width="392" alt="99ACBB4F5B288E161A" src="https://user-images.githubusercontent.com/37110261/132090735-ab10a7d5-e1c5-4623-ba38-dff2ef119403.png">

    - LoadBalancer: 외부IP를 가지고 있는 로드벨런서 할당, 클러스터 외부에서 접근 가능

<br/>

``` yaml
  ports:
  - name: http
    port: 8080
    targetPort: 80
    protocol: TCP
  - name: https
    port: 443
    targetPort: 80
    protocol: TCP
```

- port: 서비스가 외부에 공개하는 포트
- targetPort: 실제 Pod가 서비스되는 포트
- 멀티 포트 기능 지원

<br/>

``` yaml
selector:
    run: my-nginx
```

- Pod중에서 labels값이 run: my-nginx인 Pod쪽으로 서비스 한다.


<br/>

Ingress
====
- Ingress: HTTP(s)기반의 L7 로드벨런싱 기능 제공 컴포넌트
- 사용이유
    - 쿠버네티스에서 서비스는 L4레이어로 TCP단에서 Pod들을 벨런싱하지만 URL path에 따른 서비스 라우팅이 불가능
    - MSA의 경우 쿠버네티스 서비스 하나가 MSA의 서비스로 표현되는 경우가 많으며 하나의 URL로 나타내지는 경우가 많음
    - API 게이트웨이 라는게 있지만 API게이트웨이라는 관리할 항목이 늘어나기 때문에 L7레이어단에서 로드벨런싱을 제공하는 Ingress를 사용

<br/>

![ingress](https://user-images.githubusercontent.com/37110261/132091808-6a1bc025-9e12-46b5-a714-41a0148d2023.png)

<br/>

ex)
- reservation service

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: reservation-svc
spec:
  selector:
    app: reservation
  type: NodePort
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: 8080
```

- vehicle service

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: vehicle-svc
spec:
  selector:
    app: vehicle
  type: NodePort
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: 8080
```

- ingress

``` yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: car-sharing-ingress
spec:
  rules:
  - http:
      paths:
      - path: /reservation/*
        backend:
          service:
            name: reservation-svc
            port:
              number: 80
      - path: /vehicle/*
        backend:
          service:
            name: vehicle-svc
            port:
              number: 80
```


<br/>

Deployment
====

- 정의: k8s에서 어플리케이션의 단위를 관리하는 컨트롤러이며 k8s의 최소 유닛인 Pod에 대한 기준 스펙을 정의한 Object
- k8s에서 Pod들을 각각 독립적으로 배포하기보다 Deployment를 통해 생성하는 것을 권장

- Pod의 생성,복제,삭제등의 라이프사이클 관리

<br/>

<img width="386" alt="kubernetes-deployment-static" src="https://user-images.githubusercontent.com/37110261/132118253-f66e147f-dd02-4517-9245-8eb3eb95b33b.png">

<br/>
<br/>

- Replica의 숫자만큼 Pod수 생성/유지

<br/>

![deployment-diagram-kubernetes](https://user-images.githubusercontent.com/37110261/132117976-105ee4cc-7295-4077-8ff2-8541b9e991c1.gif)

<br/>

- Label Selecotr를 통한 Pod 타겟팅
- Pod를 따로 삭제해도 다시 생성되므로 지울려면 Deployment를 삭제해야 ReplicaSet,Pod가 삭제된다.
<br/>

Deployment template
====
![k8S_Deployment_main](https://user-images.githubusercontent.com/37110261/132118352-869c8a59-3de0-49a7-ba66-a4970429da58.png)