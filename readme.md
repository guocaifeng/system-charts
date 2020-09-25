# 1 监控部署流程：
此版本基于rancher的system-charts 0.0.6版本更改为创源PaaS内部的监控系统
重新规划使用版本为1.0.0

## 1.1 创建目录
```shell
sudo su rancher
mkdir prometheus-rules
cd prometheus-rules
```

## 1.2 上传配置文件
```shell
将文件prometheus-cluster-rules.yaml复制到上传到服务器prometheus-rules文件夹下

kubectl apply -f prometheus-cluster-rules.yaml
```

## 1.3 更新prometheus应用
 _不能有任何错误，否则会失败_
```shell
kubectl -n cattle-prometheus patch sts prometheus-cluster-monitoring -p \
'{"spec": {"template": {"spec": {"volumes": [{"configMap": {"defaultMode": 420,"name": "prometheus-cluster-cattle-prometheus-rulefiles-consutom"},"name": "prometheus-cluster-monitoring-rulefiles-0"}]}}}}'

```

## 1.4 升级webhook版本
如果要升级，则需要单独更改他的v1.0.0/charts/webhook中相关的values.yaml文件中repository的值，以及env相关内容

## 1.5 公有和私有镜像切换
更换v1.0.0中的values.yaml和v1.0.0/charts/operator-init中的values.yaml中所有的repository的名字,前缀都需要添加dockerhub.cyai.com

v1.0.0版本使用镜像清单如下：
```shell
docker tag rancher/coreos-kube-state-metrics:v1.8.0 dockerhub.cyai.com/rancher/coreos-kube-state-metrics:v1.8.0
docker tag rancher/coreos-prometheus-config-reloader:v0.32.0 dockerhub.cyai.com/rancher/coreos-prometheus-config-reloader:v0.32.0
docker tag rancher/coreos-prometheus-operator:v0.32.0 dockerhub.cyai.com/rancher/coreos-prometheus-operator:v0.32.0
docker tag rancher/coreos-configmap-reload:v0.0.1 dockerhub.cyai.com/rancher/coreos-configmap-reload:v0.0.1
docker tag rancher/grafana-grafana:6.3.6 dockerhub.cyai.com/rancher/grafana-grafana:6.3.6
docker tag rancher/prom-prometheus:v2.11.1 dockerhub.cyai.com/rancher/prom-prometheus:v2.11.1
docker tag rancher/prom-alertmanager:v0.17.0 dockerhub.cyai.com/rancher/prom-alertmanager:v0.17.0
docker tag rancher/prom-node-exporter:v0.17.0 dockerhub.cyai.com/rancher/prom-node-exporter:v0.17.0
docker tag rancher/prometheus-auth:v0.2.0 dockerhub.cyai.com/rancher/prometheus-auth:v0.2.0
docker tag rancher/istio-kubectl:1.1.5 dockerhub.cyai.com/rancher/istio-kubectl:1.1.5
docker tag rancher/nginx:1.17.4-alpine dockerhub.cyai.com/rancher/nginx:1.17.4-alpine
```

