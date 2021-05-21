# K8S常用命令

## pod
```bash
kubectl get pod app-base-5db6b765cb-bfvr9 -n developer-center    # 查看pod运行状态
kubectl describe pod app-base-5db6b765cb-bfvr9 -n developer-center    #

kubectl delete pod dev-hr-core-586696fbdc-c9cls --force --grace-period=0 -n c87e2267-1001-4c70-bb2a-ab41f3b81aa3    # 立即删除pod
```

## kubectl cp
```bash
# 从pod里copy文件到本地
## 默认从pod里第一个容器里拷贝, -c参数指定容器
## 目标容器里必须有tar命令，否则会报错
kubectl cp -c cloud-loan-gate uat/cloud-786d84c554-p7jz7:app/logs/app/cloud.log cloud.log
```

## node
```bash
kubectl label node 10.10.18.1 name=node1
```
