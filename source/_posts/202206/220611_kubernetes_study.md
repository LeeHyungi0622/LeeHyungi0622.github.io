---
title: 220611 Kubernetes 스터디 3일차
date: 2022-06-11 11:13:00
tags:
  - Docker
  - Kubernetes
categories:
  - Docker&K8s
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220606_kubernetes.png" alt="Kubernetes">
</div>

<br/>
<br/>

이번 포스팅에는 쿠버네티스 클러스터에서 서비스와 관련된 이론내용을 정리하고, 실습한 내용에 대해서 정리하려고 한다.
저번시간에는 실습을 위한 쿠버네티스 클러스터 환경을 구축하고, Container, Label, NodeSchedule에 대한 부분을 학습하고 실습을 해보았다. 
`컨테이너는` 쿼버네티스 클러스터 내의 namespace 상에 존재하는 pod의 내부에 복수로 존재하며, 각 각의 컨테이너들은 복수 개의 IP 할당이 가능하지만, pod 내에서는 IP 충돌로 인해 동일한 IP의 할당이 불가하다는 것을 이론과 실습을 통해 이해했다. 
`Label`은 각 각의 용도에 따라 Pod에 명기된 label로 Pod들을 묶어서 하나의 서비스를 생성하거나, 또 다른 pod 집합으로 생성하기 위해 사용된다는 것을 이론과 실습을 통해 이해하였다.
마지막으로 `NodeSchedule` 파트에서는 각 각의 Pod들은 Cluster에 연결된 노드들에 할당이 되어야 하는데, 할당하는 방법에 대해서는 직접 Pod를 Node에 할당하는 방법과 스케줄러에 의해 각 각의 노드가 가지고 있는 여유 메모리 공간에 대한 확인으로, 자동 할당되는 방법에 대해서도 이론과 실습을 통해 이해하였다. 

아직 개괄적으로 쿠버네티스에 있는 각 각의 요소들의 개념과 원리에 대해서 이해하는 과정이기 때문에 전체적인 그림 안에서 각 각의 요소가 어떤 기능을 하는지에 대해서 이해하면서 학습을 하고 있다. 
엇그제 Label을 통해서 용도에 따라 pod(들)을 묶어서 하나의 Service로 만들어서 다른 운영자에게 IP와 Port를 제공한다고 배웠다. (`웹 개발자가 웹 화면만 확인하고자 할때 type이 web인 pod들만 service에 연결시켜서 웹 개발자들에게 제공 / 외부에서 원하는 service의 pod들에만 접그하고자 할 때 label이 production으로 지정된 pod들을 묶어서 하나의 service로 생성하고 제공`) 
이 Service에 대해서 이론적으로 학습을 하고 직접 실습을 해 볼 것이다. 

<!-- more -->

## <ins><b>Service in k8s</b></ins>

  쿠버네티스의 Service의 종류로는 크게 기본적으로 `ClusterIP, NodePort, Load Balancer`가 있으며, 순서대로 ClusterIP Service의 성격을 NodePort Service가 상속하고, NodePort Service의 성격을 Load Balancer가 상속을 한다. 

  - ### **[ClusterIP Service]**
    ClusterIP는 가장 기본적인 Service의 형태로, 실제 서비스를 생성할 때 spec > type의 값은 option인데, default가 ClusterIP이다. 

    `service_sample.yml`
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: svc-1
    spec:
      selector: # 이 부분이 Pod와 Service를 연결시키기 위한 부분이다.
        app: pod # 연결하고자 하는 Pod의 이름
      ports:
        - port: 9000 # 9000번 포트로 들어오면, 
          targetPort: 8080 # 8080번 포트로 연결
      type: ClusterIP   # 이 부분은 optional한 부분이며, 기본값은 ClusterIP이다. 
    ``` 

    `pod_sample.yml`
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod-1
      labels: # 이 부분이 Service와 연동시키기 위한 부분이다.
        app: pod # 이 부분이 Service와 연동시키기 위한 부분이다.
    spec:
      containers:
        - name: container
          image: tmkube/app
          ports:
            - containerPort: 8080
    ```

    ClusterIP의 구성을 한 번 이미지 트레이닝 해보자.

    우선 클러스터 내에 IP와 Port가 할당된 서비스 하나와 Pod이 두 개 있다고 가정한다.
    Service에는 기본적으로 자신의 클러스터 IP를 가지고 있으며, 외부로부터 접근이 불가하다.
    (`서비스와 Pod에 할당된 IP와 Port는 클러스터 내부에서 접근할 때만 사용`)
    각 각의 Pod들에도 IP와 Port가 할당되어 있기 때문에 굳이 Service를 연결하지 않고, Pod의 IP와 Port로 접근하면 되지 않나? 라고 생각이 들겠지만, pod의 특징을 이해하면 왜 Pode들을 Service와 연동시켜서 Service를 통해서 Pod들에 접근을 하는지 이해할 수 있다.
    그 이유는 `Pod`는 성능상에 문제가 생기면, Pod를 재성성하고 Pod에 할당되었던 IP를 재할당하게 된다. 따라서 Pod에 할당된 IP는 클러스터 내에서 접근이 가능한 수단이 될 수는 있지만, 신뢰성이 매우 낮다.
    따라서 사용자가 지우지 않으면 사라지지 않는 Service라는 친구를 두고, 그 하위에 관리하고자 하는 Pod들을 연결시켜서 관리하게 된다. 서비스는 트래픽을 분산해서 pod에 전달하는 역할을 하며, `종류도 다양한데, Pod에 접근을 도와주는 방식이 다 다르다.`

    - #### **[ClusterIP Service의 사용 케이스]**
      
      앞서 이미 살펴봤듯이 ClusterIP는 Service에 할당된 IP로, 외부에서 접근이 불가능하다. 따라서 ClusterIP Service를 사용하는 경우는 외부의 접근이 불가능하고, 오직 내부에서만 클러스터에 접근하는 경우에 사용 가능하다.
      예를들면, 클러스터 내부에 접근이 가능한 운영자와 같은 인가된 사람이 클러스터의 dashboard를 관리하거나, 각 pod들의 서비스 상태를 직접 디버깅하는 업무를 하는 경우에 ClusterIP Servicer를 생성해서 사용할 수 있다.

  - ### **[NodePort Service]**
    
    NodePort Service는 용어에서 알 수 있듯이 Node의 Port와 관련있는 Service이다. 앞서 NodePort Service가 ClusterIP Service의 성격을 상속한다고 했는데, 클러스터 내의 구조를 살펴보면, Service에 복수 개의 Pod들이 물려있는 구조로 되어있다. 이전 시간에 NodeSchedule에 대해서 학습을 했을 때 각 각의 Pod는 Node에 할당이 되어야 한다. 따라서 각 각의 Pod들은 특정 Node에 종속이 되어있는데, 이름에서 알 수 있듯이 모든 Node들에 동일한 Port가 할당이 되어있다.

    따라서 외부로부터 특정 노드의 IP와 동일하게 할당된 Port로 접근을 하게 되면, 어느 노드에서 접근을 했던 간에 각 각의 Pod들이 물려있는 Service로 연결이 가능하며, Service에 연결된 Pod에 트래픽이 전달된다.
    단, 만약에 특정 노드의 IP와 Port를 통해 서비스에 연결했을 때, 연결할 때 접근했던 노드를 제외한 다른 노드에 포함된 Pod들에는 Service에서 트래픽을 보낼 수 없게 제한을 할 수 있는데, 아래와 같이 yml파일에서 `externalTrafficPolicy: Local`을 추가해주면 된다.

    `nodeport.yml`

    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: svc-2
    spec:
      selector:
        app: pod
      ports:
        - port: 9000
          targetPort: 8080
          nodePort: 30000 # 30000~32767 범위내의 Port
      type: NodePort # 옵션! 생략이 가능
    ```
    - #### **[NodePort Service의 사용 케이스]**

      NodePort는 물리적인 호스트(Node)의 IP를 통해서 Pod에 접근이 가능하다. 
      대부분의 호스트 IP는 보안적으로 내부망에서만 접근이 가능하도록 네트워크가 구성이 되어있으며, 종종 내부 시스템을 개발하고 외부에서 간단하게 데모를 할 때 네트워크 중계기에 포트 포워딩을 해서 외부에서 내부 시스템으로 연동해서 일시적으로 외부 연동을 해서 사용할 때 사용된다.

- ### **[Load Balancer Service]**
  
  Load Balancer Service는 NodePort Service의 성격을 상속받고, Node Balancer의 구조를 그대로 가진 상태에서 `Load Balancer`가 추가된 형태로 되어있다. Load Balancer는 각 각의 노드에 트래픽을 분산시켜주는 역할을 한다. 
  단, 주의해야 될 점은 Load Balancer에 할당된 IP(외부에서 접근하기 위한 IP)는 개별적으로 k8s를 설치했을 때 기본적으로 생성되지 않는다.
  Load Balancer의 IP를 생성하기 위해서는 Load Balancer 외부 IP지원 플러그인을 설치해야 한다. 예를들어 AWS의 k8s 플랫폼 관련 서비스를 사용하게 되면, 해당 서비스 내에는 자체적으로 Load Balancer 외부 IP 지원 플러그인이 설치가 되어있기 때문에 Load Balancer 서비스 생성시에 자동으로 외부에서 서비스로 접근이 가능한 IP를 할당해준다.

  `load_balancer.yml`
  ```yml
  apiVersion: v1
  kind: Service
  metadata:
    name: svc-3
  spec:
    selector:
      app: pod
    ports:
      - port: 9000
        targetPort: 8080
    type: LoadBalancer # 이렇게 타입만 추가해주면, LoadBalancer service가 생성이 된다.
  ```
    - #### **[LoadBalancer Service의 사용 케이스]**

      Load Balancer 서비스는 실제로 외부에 서비스를 노출시킬 때 사용된다. Load Balancer를 사용하게 되면, 내부 IP가 노출되지 않고, 외부 IP를 사용해서 안정적으로 외부로 서비스를 노출 시킬 수 있다.


