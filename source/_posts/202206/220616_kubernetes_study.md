---
title: 220616 Kubernetes 스터디 4일차
date: 2022-06-16 18:30:00
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

## <ins><b>ClusterIP Service 실습</b></ins>

ClusterIP Service를 생성하고, 지정한 selector가 app:pod를 가지는 pod와 연결을 하였다. 그런 다음에 연결된 app:pod label을 가지는 Pod를 삭제하고, 다시 재생성을 한다. 이렇게 되면, 실제 pod에 할당되어있던 IP는 동적으로 할당이 되어 변경이 되지만, ClusterIP Service는 재생성이 되지 않아, 동일한 Service IP로 Pod에 접근이 가능하다.

## <ins><b>NodePort Servce 실습</b></ins>

실제 Dashboard의 디스커버리 및 로드 밸런싱 하위의 서비스 항목을 가보면, 내부 엔드 포인트가 두 개 생성된 것을 확인할 수 있는데, 상위에 있는 9000번 포트가 클러스터 내에서 접근할때 사용되는 포트이고, 30000번 포트가 각 각의 노드들에 동일하게 할당된 포트 번호이다. 이 포트를 통해 외부에서 클러스터 내부의 Service를 통해 하위 Pod들에 트래픽을 보낼 수 있다.
실습에서는 외부 터미널에서 curl 명령을 통해 서비스에 접근하여 각 pod에 대한 hostname이 출력되는 것을 확인하였다.

<!-- more -->

## <ins><b>LoadBalancer Servce 실습</b></ins>

LoadBalancer service의 경우에는 이전에 이론에서 배웠듯이 외부로 연결되는 IP가 생성되기 위해서는 플러그인이 설치가 되어있어야 한다.

```zsh
$kubectl get service svc-2
```
위 명령을 통해 확인해보면, EXTERNAL-IP가 <none>으로 표기된 것을 확인할 수 있다.

## <ins><b>Object - Volume</b></ins>

  이전에 Kubernetes의 전체 구조를 살펴볼때 pod에 문제가 생겨서 재생성되면, 내부 데이터가 다 날라가기 때문에 pod의 데이터를 별도의 volume에 mount시켜서 저장을 하고 관리해야 한다는 부분에 대해서 배웠다. 이번 세션에서는 이 Volume에 대해서 좀 더 구체적으로 이론적인 부분을 정리해보려고 한다.

  Volume의 종류로는 emptyDir, hostPath, PVC/PV가 있다.

  - ### **(1) emptyDir**

    emptyDir은 Pod 생성시 Pod의 내부에 만들어지고, Pod가 삭제되면 사라진다. 그래서 `일시적 사용 목적으로 사용`이 되며, 복수의 컨테이너 간에 파일을 공유할 때 사용이 된다. 
    만약 Web Server를 서비스하고 있는 Container1과 Backend server를 서비스하는 Conatiner2가 있다고 가정하고, 두 서버간에 파일을 공유해야되는 경우, 두 컨테이너를 하나의 볼륨에 연결시켜놓으면 손쉽게 파일을 공유할 수 있다.

    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod-volume-1
    spec:
      containers:
        - name: container1
          image: tmkube/init
          volumeMounts:
            - name: empty-dir
              # container가 mountPath 경로로 마운트하겠다는 의미이다.
              mountPath: /mount1
        - name: container2
          image: tmkube/init
          volumeMounts:
            - name: empty-dir
              # 만약 경로가 잘못되어도 name에서 empty-dir을 가르키고 있기 때문에 
              # empty-dir volume으로 mount가 된다.
              mountPath: /mount2
        volumes:
          - name: empty-dir
            emptyDir: {}
    ```
  - ### **(2) hostPath**

    hostPath는 `Pod의 데이터를 저장하기 위한 용도가 아닌, Node에 있는 데이터를 Pod에서 쓰기 위한 용도`이다.
    hostPath에서 host란 Pad가 올라가있는 Node를 의미한다.

    전체 구조는 Node내부에 Pod가 있고, Pod가 Node에 존재하는 Volume에 연결되어있는 구조이다. 그리고 Node 내의 Volume은 Pod의 유/무와 관계없이 사라지지 않는다.

    - #### **문제상황** 

      Node1에 Pod1, Pod2가 있고, Volume에 두 Pod가 연결되어있는 상황에서 Pod2에 문제가 생겨서 재생성되었을때, 스케줄러에 이해서 Node2에 재생성이 되거나 Node1에 장애가 발생을 해서 통으로 다른 Node로 이전을 해야되는 상황이 생겼을때, 기존 Node1에 있는 Volume에 연결되지 못한다.
      이러한 상황에서 해결방법은 Node가 추가될때마다 항상 동일한 경로에 마운트 시켜주는 것이다. (Node1의 Volume(`/node-v1`), Node2의 Volume(`/node-v1`))

    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod-volume-2
    spec:
      containers:
        - name: container
          image: tmkube/init
          volumeMounts:
            - name: host-path
              mountPath: /mount1
      volumes:
        - name: host-path
          hostPath:
            # 사전에 해당 경로에 생성을 해둬야 한다.
            path: /node-v
            type: Directory
    ```

    - #### **사용사례**

      각각의 노드는 자기자신을 위해서 사용되는 파일이 있다. (시스템 파일, 기타 설정 파일들)
      Pod는 자신이 속해있는 host(Node)의 데이터를 읽거나 써야될때 hostPath 유형의 Volume을 사용한다.  

  - ### **(3) PVC(Persistence Volume Claim) / PV(Persistence Volume)**

    PVC/PV의 목적은 Pod에 영속성있는 Volume을 제공하기 위함이다.

    우선 PVC/PV의 흐름을 살펴보면, 우선 `(1)admin이 PV 정의에 대해서 생성`을 하면, `(2)사용자가 PVC를 생성`한다. 그런 다음에 `(3)K8S가 PVC의 내용에 맞는 적절한 Volume에 연결`을 시켜준다. 
    Pod를 생성할때 사용자가 생성한 PVC를 사용하면 된다.

    흐름도는 `Pod -> PVC -> PV (복수) -> Volume`인데, Pod가 PV에 직접 연결이 되지 않고, PVC를 통해서 연결되는 이유는 k8s는 volume영역에 대해 사용자 영역과 관리자 영역으로 나뉘기 때문이다.
    여기서 사용자란 Pod에 서비스를 만들고 관리/배포를 담당하는 업무를 하며, 관리자는 PV를 작성한다.
    `Volume`에는 Local과 Remote 원격 Volume이 있는데, 원격 Volume에는 AWS, Git, NFS(Network File System), Volume을 직접 만들고 서비스도 가능한 Sorage OS가 있다.

     `(Pod ~ PVC 영역이 사용자 영역이며, PV ~ Volume이 관리자 영역이다)`

    `pod.yml`
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod-volume-3
    spec:
      containers:
        - name: container
          image: tmkube/init
          volumeMounts:
            - name: pvc-pv
              mountPath: /volume
      volumes:
        - name: pvc-pv
          persistentVolumeClaim:
            # 이 claimName이 PVC 정의 파일의 metadata name과 연결된다.
            claimName: pvc-01
    ```

    `pvc.yml`
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: pvc-01
    spec:
      accessModes:
        - ReadWriteOnce # 읽기/쓰기 모드가 되고,
      resources:
        requests:
          storage: 1G # 용량이 1G인 Volume이 필요(용량 할당)
      # 현재 생성되어 있는 PV들 중 선택이 된다. ""(필수)    
      storageClassName: ""
    ```

    `pv.yml`
    ```yml
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-01
        # 앞서 k8s가 PVC 내용에 맞는 적절한 Volume에 연결을 해준다고 했는데,
        # 바로 아래 spec의 capacity와 accessModes의 내용이 근거가 된다!!
        # 설정이 되면, PVC에서 작성한 요청에 맞는 Volume에 연결이 된다.  
    spec:
      capacity:
        storage: 1G
      accessModes:
        - ReadWriteOnce # 읽기/쓰기 모드가 되고,
      # 실제로는 많이 사용되지 않는다. 
      local:
        path: /node-v
        # 아래의 내용은 PV에 연결되는 Pod들은 node들은 node1(label)
        # 노드 위에서만 무조건적으로 생성된다는 의미이다.
      nodeAffinity:
        required:
          nodeSelectorTerms:
            - matchExpressions:
              - {key: node, operator: In, values: [node1]}
      resources:
        requests:
          storage: 1G # 용량이 1G인 Volume이 필요(용량 할당)
      # 현재 생성되어 있는 PV들 중 선택이 된다. ""(필수)    
      storageClassName: ""
    ```

    PV를 전문적으로 관리하는 관리자가 필요한 이유는 아래와같이 세부적으로 PV에 대한 작성을 해야되며, PV를 만들어두면, User는 사용을 위해서 PVC를 생성하는 패턴으로 작업을 하기 때문이다.

    아래 내용은 remote volume 지정시, spec의 하위에 정의한다.
    ```yml
    nfs:
      service:
      path:
    iscsi:
      targetPortal:
      iqn:
      lun:
      fsType:
      readOnly:
      chapAuthSession:
    gitRepo:
      repository:
      revision:
      directory:
    ```
    



