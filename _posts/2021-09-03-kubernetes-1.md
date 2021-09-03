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
