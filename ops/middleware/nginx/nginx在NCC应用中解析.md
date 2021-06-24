# nginx在NCC应用中解析
[TOC]

> edit by sswu. 2020-08-25 15:45:47 星期二

# 一、	nginx在NC产品中的安装过程
## 1. 安装器概述
安装器本质上是一个Nexus+Tomcat的web服务，后端通过nexus创建一个内部维护的软件仓库，用于组件的部署。
nexus服务维护的安装包地址为：`/data/iuap_installertools/nexus/sonatype-work/nexus/storage/`
![](http://10.10.18.229:4999/server/index.php?s=/api/attachment/visitFile/sign/64eaf635750a60f4b8782dcd06fa4de2&showdoc=.jpg)
## 2.	技术中台安装nginx
在安装技术中台时安装nginx，需要配置nginx所在主机，默认使用端口为80；另外如果配置集群模式，。。。
配置完毕后安装过程为安装器通过修改yum源后使用yum进行nginx二进制包的分发和安装。
nginx安装后状态：
-	执行文件 `/usr/sbin/nginx`
-	版本 `nginx/1.14.2`
-	配置文件地址 `/data/iuap/middleware/nginx/nginx.conf`

## 3.	NCC安装nginx
安装过程同技术中台一致。
# 二、	nginx配置文件解析
## 1.	技术中台nginx配置
技术中台的nginx主要是作为开发者中心的服务入口，主要的相关配置文件在`/data/iuap/middleware/nginx/sites-enabled/`
-	`upstream-tomcat.conf` 技术中台应用组件的服务
-	`upstream-application.conf` 用友云服务（应该是未启用）

技术中台的nginx主要定义了开发者中心的相关功能入口：包括静态页面的代理、通过ingress访问后端服务的http反向代理等,现对这两种代理类型的配置做出解释：
-	静态页面代理：
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/73c48581d7a2ece81192a2af45823aa4&showdoc=.jpg)
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/d03108cf7bda069a8a9cd3e49f05caf8&showdoc=.jpg)
技术中台的nginx主要就这一个server配置，server的main配置定义了监听和server_name, 可以通过`gpass.yyuap.com`域名和`ip:80`服务（default_server）访问,后面定义了代理服务支持的http版本为1.1，这也是目前主流的http协议版本。下面的location块定义了`/download/`作为下载地址，支持对一个本地文件系统的目录文件进行下载请求，alias参数的含义是用户访问`http://gpass.yyuap.com/download/`后会获取文件系统中`/data/nginx/download/`中的目录文件，但是不支持页面索引，因为（`autoindex off;`）
-	http反向代理：主要是对于ingress服务的代理访问。
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/cbdf5617ce9c96fd003b14d437872c71&showdoc=.jpg)
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/a2293c47883cfe4a68f6cf1a32dce65b&showdoc=.jpg)
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/1fac64b66d14908d8f06dfaa2d8b3e5c&showdoc=.jpg)
上面三块配置展示了我们点开`http://10.10.18.11 `后nginx的工作：
第一部分定义了`ingress`的后端服务配置，设置了相关的参数（权重、超时时间、试错次数等）
第二部分是一个重定向配置，将我们访问的请求返回301重定向响应，会再次向`http://10.10.18.11/fe/fe-portal/` 进行请求。
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/806dc8c37985aad62686339a1a39855c&showdoc=.jpg)
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/9dbba1065b41af8352edbcf33d80ffd6&showdoc=.jpg)
第三部分就是nginx对匹配到的`/fe/fe-portal`进行处理及响应
前三行是对后端返回的响应进行header修改处理，（主要是为了实现跨域）
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/b6ac31470ba013b7dca4b3aad1b67f24&showdoc=.jpg)
中间是对客户端发来的OPTION方法进行正确响应（这是http的机制）
下面proxy开头的都是对转发前的请求进行修改处理，主要是增加X-开头的转发header标识，将转发请求模拟成客户端请求，使后端服务可以获取到客户端的真实信息。

## 2.	NCC中nginx配置
ncc中的nginx主要是作为用户访问NCC应用的入口。其中定义了lisenceServer和ingress的转发服务。
- licneseServer 
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/2b6587e9abd81e5801d0b503ba1a2719&showdoc=.jpg)
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/b580c6a07cacaef2c03f22aeb28ef23d&showdoc=.jpg)
用户访问`http://10.10.18.14/iuap-licenseserver`的请求会被转发给后端的`http://10.10.18.13:30001/iuap-licenseserver/`进行处理，其余的参数也是在转发前进行请求的header处理，构造转发的请求。
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/7a429bcdb8260f5995e3f193e878e284&showdoc=.jpg)
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/93b1743fe293da4da6012c98d531c23e&showdoc=.jpg)
-	ingress的访问代理转发：ncc应用主要实现的代理功能。将用户访问NCC的请求根据url匹配进行对应的7层转发。
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/bfcb85397afe9d32f6a36a19f49b4b6c&showdoc=.jpg)
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/03dafae69884f4fbead4a0e9d06d7b7b&showdoc=.jpg)
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/5fe35436ab6c47bba99c82097275688d&showdoc=.jpg)
1. 第一部分是重定向；
2. 第二部分是使用lua脚本进行uri判断，根据原始url的不同进行动态的重定向判断；
3. 第三部分是真正的首页访问请求地址：前几行是修改响应头实现跨域，接下来构造代理客户端请求头，中间使用lua给请求头传入了一个新的参数callid;后面是设置x-forward请求，允许weosocket请求，并设置Host为一个域名，这相当于nginx作为客户端去访问ingress是通过域名来访问的，下面是一些基于安全策略的设置（XSS攻击保护等）。

# 三、	nginx在NC产品中的工作过程
## 1.	ncc-nginx的转发策略
- ncc的nginx提供了licenseServer和NCC产品的入口；
- licenseServer的配置跟技术中台的nginx配置相同，都是直接转给后端的licenseServer服务；
- ncc产品相关的配置主要有：
	- 入口文件的相关重写策略：通过不同的url判断，将请求地址重写至前端react对应的action文件路由，触发相应的动作进行组件的渲染。
	![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/97f5f2f8ad9fc9ba910ae77feaca7650&showdoc=.jpg)
	- 后端服务的转发策略：主要是将所有后端服务的相关请求直接转发给	ingress
	- 每个对应的后端服务都会有三个对应的location块配置
	![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/5835ba51cefa1d10fcdd1363c604c0dd&showdoc=.jpg)
	![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/9e3f42b8550d68bc8c9d204c7cc0b125&showdoc=.jpg)
	![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/ad5deeaeef451c135f20375af4fe5908&showdoc=.jpg)
	- 每个应用有多个服务，会根据规则生成对应的配置，具体内容在微服务拆分时生成的prodct.xml里可以体现

## 2.	nginx-ingress的转发策略
nginx-ingress的整体逻辑都在分为两部分：
- ingress-controller进程从APIServer监听到ingress等资源对象的变化，进行nginx配置的动态更新，这其中主要是调用nginx的lua库实现逻辑，我们产品中直接使用的openresty进行管理。
- nginx通过配置文件进行请求的转发和处理。

# 四、	Ingress工作原理

## 1.	Ingress原理
K8S中暴漏pod服务的方式：
-	通过service对象进行pod服务的统一管理，使用反向代理的方式将相同的pod服务进行暴漏，解决了pod动态漂移的问题，但是service只可用于集群内部之间的服务访问
-	暴漏service服务的方式：
	- NodePort：通过分配node主机端口接收外界客户端请求，将对应请求路由到内部service的clusterIP上，再进行相应转发。
	> user --> nodeIP:nodePort --> ClusterIP:port --> endpoints --> podIP:port `

	- LoadBalancer：通过云提供商提供的负载均衡器将服务进行暴漏，由负载均衡器将请求转发至对应的service上。(跟云提供厂商耦合较深)
	> user --> loadBalancer --> ClusterIP:Port --> endpoints --> podIP:port

	- Ingress：基于host或url把请求转发至指定的service，实现将集群外部的流量转发至集群内部完成服务发布。

### ingress工作机制
![](http://10.16.226.53:4999/server/index.php?s=/api/attachment/visitFile/sign/19bd549967414e8f749868563b09a7bd&showdoc=.jpg)
> 1. 每个应用会对应一个`service`对象`clusterIP+port`，`service`对象生成的时候会维护一个`Endpoints`对象 `[]Pod{...Pods}`
> 2. 应用创建时，技术中台的组件`app-base`会自动创建相应的`ingress`对象，`ingress`通过`service`对象维护的`endpoints`列表获取对应的后端服务信息，`ingress`本身是一系列7层转发规则的集合。
> 3. `service`、 `ingress`等都是K8S内置的资源对象，可以被`APIServer`组件监听实时获取并持久化到`etcd`中。
> 4. K8S集群的入口是`nginx-ingress`,它是一个pod，通过`hostNetwork: true`的方式暴露自己的服务，pod中包含`nginx`和`ingress-controller`服务。`ingress-controller`服务通过`APIServer`来获取`ingress`的状态，通过解析`go template`来动态生成nginx配置，并且在需要的情况下`nginx -s reload`一下，完成服务的热更新。

> ingress-controller详细工作过程解析参考：https://blog.csdn.net/shida_csdn/article/details/84032019

## 2.	NCC中的Ingress实现
基本原理上面已说明。下面以base服务对ingress机制进行说明：
### ingress的实现
在开发者中心配置流水线后进行执行，k8s会依据`pipline`的配置进行执行相应动作：
> 2020-08-26 13:48:59.982 INFO app_upload DockerImageName [reg.yyuap.io:81/c87e2267-1001-4c70-bb2a-ab41f3b81aa3/fi-fip:20200702111400]
2020-08-26 13:49:00.452 INFO app_upload AssemblyLine success

- 拉取镜像
- 启动创建service、pod、ingress等资源

应用创建完毕后技术中台的组件会生成对应的ingress对象
```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    ingress.kubernetes.io/proxy-read-timeout: "7200"
    ingress.kubernetes.io/rewrite-target: /
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/proxy-read-timeout: "7200"
  creationTimestamp: "2020-07-02T09:33:57Z"
  generation: 1
  name: dev-uap-base
  namespace: c87e2267-1001-4c70-bb2a-ab41f3b81aa3
  resourceVersion: "171880"
  selfLink: /apis/extensions/v1beta1/namespaces/c87e2267-1001-4c70-bb2a-ab41f3b81aa3/ingresses/dev-uap-base
  uid: 322a05a9-0200-4918-ab81-2d2e64179564
spec:
  rules:
  - host: dev-uap-base.prod1.yonyoucloud-k8s.com
    http:
      paths:
      - backend:
          serviceName: dev-uap-base
          servicePort: 8888
status:
  loadBalancer:
    ingress:
    - ip: 10.96.2.97
```

ingress中会指定nginx转发规则的相关信息（域名、后端、前端等）
### ingress-controller的实现
ingress-controller在k8s中是一个pod，我们以hostNetwork的方式暴漏服务作为k8s的流量入口。
```
apiVersion: v1
kind: Pod
metadata:
  annotations:
    prometheus.io/port: "10254"
    prometheus.io/scrape: "true"
  creationTimestamp: "2020-07-02T08:24:59Z"
  generateName: nginx-ingress-controller-
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
    controller-revision-hash: f56b4b7d4
    pod-template-generation: "1"
  name: nginx-ingress-controller-rrx76
  namespace: ingress-nginx
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: DaemonSet
    name: nginx-ingress-controller
    uid: 0d7bd493-57e4-4ce7-8555-e4250f6ae17d
  resourceVersion: "1114"
  selfLink: /api/v1/namespaces/ingress-nginx/pods/nginx-ingress-controller-rrx76
  uid: 4acfaf42-7d77-4705-b3d5-ff84ad9f1be0
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchFields:
          - key: metadata.name
            operator: In
            values:
            - 10.10.18.12
  containers:
  - args:
    - /nginx-ingress-controller
    - --configmap=$(POD_NAMESPACE)/nginx-configuration
    - --tcp-services-configmap=$(POD_NAMESPACE)/tcp-services
    - --udp-services-configmap=$(POD_NAMESPACE)/udp-services
    - --publish-service=$(POD_NAMESPACE)/ingress-nginx
    - --annotations-prefix=nginx.ingress.kubernetes.io
    - --http-port=80
    - --https-port=443
    env:
    - name: POD_NAME
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.name
    - name: POD_NAMESPACE
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.namespace
    image: reg.yyuap.io:81/kubernetes/nginx-ingress-controller:0.26.1
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 3
      httpGet:
        path: /healthz
        port: 10254
        scheme: HTTP
      initialDelaySeconds: 10
      periodSeconds: 10
      successThreshold: 1
      timeoutSeconds: 1
    name: nginx-ingress-controller
    ports:
    - containerPort: 80
      hostPort: 80
      name: http
      protocol: TCP
    - containerPort: 443
      hostPort: 443
      name: https
      protocol: TCP
    readinessProbe:
      failureThreshold: 3
      httpGet:
        path: /healthz
        port: 10254
        scheme: HTTP
      periodSeconds: 10
      successThreshold: 1
      timeoutSeconds: 1
    resources:
      limits:
        cpu: "1"
        memory: 512Mi
      requests:
        cpu: "1"
        memory: 512Mi
    securityContext:
      capabilities:
        add:
        - NET_BIND_SERVICE
        drop:
        - ALL
      runAsUser: 33
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: nginx-ingress-serviceaccount-token-hfjkd
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  hostNetwork: true
  nodeName: 10.10.18.12
  nodeSelector:
    node-role.kubernetes.io/ingress: ""
  priorityClassName: system-node-critical
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: nginx-ingress-serviceaccount
  serviceAccountName: nginx-ingress-serviceaccount
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
  - effect: NoSchedule
    key: node.kubernetes.io/disk-pressure
    operator: Exists
  - effect: NoSchedule
    key: node.kubernetes.io/memory-pressure
    operator: Exists
  - effect: NoSchedule
    key: node.kubernetes.io/pid-pressure
    operator: Exists
  - effect: NoSchedule
    key: node.kubernetes.io/unschedulable
    operator: Exists
  - effect: NoSchedule
    key: node.kubernetes.io/network-unavailable
    operator: Exists
  volumes:
  - name: nginx-ingress-serviceaccount-token-hfjkd
    secret:
      defaultMode: 420
      secretName: nginx-ingress-serviceaccount-token-hfjkd
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2020-07-02T08:25:00Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2020-07-02T08:25:17Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2020-07-02T08:25:17Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2020-07-02T08:24:59Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: docker://086241e4d3f01cdcdd33063626c0077e8d6d7a18f574cb7a1b9ec6360d6a684e
    image: reg.yyuap.io:81/kubernetes/nginx-ingress-controller:0.26.1
    imageID: docker-pullable://reg.yyuap.io:81/kubernetes/nginx-ingress-controller@sha256:5da1b2e84ecbdb27facbea84bc6ddc9d50145d824963230735b47828891cba7b
    lastState: {}
    name: nginx-ingress-controller
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2020-07-02T08:25:11Z"
  hostIP: 10.10.18.12
  phase: Running
  podIP: 10.10.18.12
  podIPs:
  - ip: 10.10.18.12
  qosClass: Guaranteed
  startTime: "2020-07-02T08:25:00Z"
```

# 五、nginx在NCC中的运维场景
## 1. 新增微服务的相关配置

### 从安装器安装
将新增加的微服务直接做到安装器里面。
- 保证新增微服务的代码已更到安装盘里
- 在`$NCC_HOME/splitfiles/`目录里包含了正确的拆分文件（做盘过程中要基于home和拆分文件对所有的微服务进行拆分）
- 在做盘工具或者脚本里增加新的微服务的注册记录，这样才能保证拆分及打镜像的完整
- 在`$NCC_HOME/ierp/bin/servermodulemapping.properties`文件中增加对应的记录（这个要开发确认，影响升级数据库过程，没有的话在升级数据库的时候找不到对应的模块）

### 在已有环境中新增
直接在已有的环境中新增微服务
- 保证新增微服务的代码已更到安装盘里
- 在`$NCC_HOME/splitfiles/`目录里包含了正确的拆分文件（做盘过程中要基于home和拆分文件对所有的微服务进行拆分）
- 在`$NCC_HOME/ierp/bin/servermodulemapping.properties`文件中增加对应的记录（这个要开发确认，影响升级数据库过程，没有的话在升级数据库的时候找不到对应的模块）
- 取包含微服务代码的完整盘，使用工具进行对应微服务的更新（推镜像到环境的harbor里）
- 在开发者中心中新增对应微服务的流水线，相关配置参考其他同类型的微服务，选择对应代码的镜像启动应用
- 配置管理中为对应的微服务创建`1.0.0`版本的配置文件，并启用
- 在ncc的nginx中增加对应微服务的转发规则，具体规则模板在拆分微服务过程中生成的`/data/ncc/prodcut.xml`文件中，需要手动将`{{#product.env}}-{{#package.lowCaseCode}}`变量进行转换
下面截取新增`fi-dw`微服务的nginx模板内容：

```
location ^~ /nccloud/fidata/ {
         add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';
        if ($request_method = 'OPTIONS'){
          return 204;
        }
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        set_by_lua $callId '
        if ngx.var.http_X_callId == nil then
          return string.sub(ngx.var.request_id,17,-1)
        else
          return ngx.var.http_X_callId
        end
        ';
        proxy_set_header callid $callId;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_set_header X-Forwarded-Host $http_host;
        proxy_set_header Host "{{#product.env}}-{{#package.lowCaseCode}}.prod1.yonyoucloud-k8s.com";
		add_header  X-Content-Type-Options "nosniff";
		add_header  X-XSS-Protection "1; mode=block";
		add_header  X-Frame-Options "SAMEORIGIN";
		add_header  Content-Security-Policy *;    
        proxy_redirect off;
        proxy_pass http://ingress;
    }

    location ^~ /nccloud/mob/fidata/ {
         add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';
        if ($request_method = 'OPTIONS'){
          return 204;
        }
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        set_by_lua $callId '
        if ngx.var.http_X_callId == nil then
          return string.sub(ngx.var.request_id,17,-1)
        else
          return ngx.var.http_X_callId
        end
        ';
        proxy_set_header callid $callId;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_set_header X-Forwarded-Host $http_host;
        proxy_set_header Host "{{#product.env}}-{{#package.lowCaseCode}}.prod1.yonyoucloud-k8s.com";
		add_header  X-Content-Type-Options "nosniff";
		add_header  X-XSS-Protection "1; mode=block";
		add_header  X-Frame-Options "SAMEORIGIN";
		add_header  Content-Security-Policy *;    
        proxy_redirect off;
        proxy_pass http://ingress;
    }
    location ^~ /nccloud/api/fidata/ {
         add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';
        if ($request_method = 'OPTIONS'){
          return 204;
        }
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        set_by_lua $callId '
        if ngx.var.http_X_callId == nil then
          return string.sub(ngx.var.request_id,17,-1)
        else
          return ngx.var.http_X_callId
        end
        ';
        proxy_set_header callid $callId;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_set_header X-Forwarded-Host $http_host;
        proxy_set_header Host "{{#product.env}}-{{#package.lowCaseCode}}.prod1.yonyoucloud-k8s.com";
		add_header  X-Content-Type-Options "nosniff";
		add_header  X-XSS-Protection "1; mode=block";
		add_header  X-Frame-Options "SAMEORIGIN";
		add_header  Content-Security-Policy *;    
        proxy_redirect off;
        proxy_pass http://ingress;
    }
```
