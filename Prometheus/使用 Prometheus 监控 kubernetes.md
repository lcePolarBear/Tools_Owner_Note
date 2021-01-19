## 使用 Prometheus 监控 kubernetes

__监控的内容__
- kubernetes 自身监控的内容
    - Node 资源利用率
    - Node 数量
    - Node 下 Pods 数量
    - 资源对象状态

- Pod 监控的内容
    - Pods 数量
    - 容器资源利用率
    - 应用程序

监控 kubernetes 下 Pod 的资源利用率
- Prometheus 必须拥有授权才能从 kube-apiserver 获取数据，所以在 master 结点部署 [rbac.yaml](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/%E6%89%80%E9%9C%80%E7%9A%84%E6%96%87%E4%BB%B6/rbac.yaml)
    ```
    kubectl apply -f rbac.yaml
    ```
- 查看是否已部署
    ```
    kubectl get sa prometheus -n kube-system
    ```
- 获取 rbac 的 Token 名称
    ```
    kubectl get sa prometheus -n kube-system -o yaml
    ```
    - `- name: prometheus-token-gv66c`
- 使用 Token 名称获取 Token 内容
    ```
    kubectl describe secret prometheus-token-gv66c -n kube-system
    ```
- 复制 Token 内容到 `token.k8s` 文件下，将文件放入 Prometheus 所在的路径下，配置 `prometheus.yml`
    ```
    - job_name: kubernetes-nodes-cadvisor
        metrics_path: /metrics
        scheme: https
        kubernetes_sd_configs:
        - role: node
          api_server: https://192.168.31.61:6443
          bearer_token_file: /opt/monitor/prometheus/token.k8s 
          tls_config:
            insecure_skip_verify: true
        bearer_token_file: /opt/monitor/prometheus/token.k8s 
        tls_config:
          insecure_skip_verify: true
        relabel_configs:
        # 将标签(.*)作为新标签名，原有值不变
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.*)
        # 修改NodeIP:10250为APIServerIP:6443
        - action: replace
          regex: (.*)
          source_labels: ["__address__"]
          target_label: __address__
          replacement: 192.168.1.202:6443
        # 实际访问指标接口 https://NodeIP:10250/metrics/cadvisor 这个接口只能APISERVER访问，故此重新标记标签使用APISERVER代理访问
        - action: replace
          source_labels: [__meta_kubernetes_node_name]
          target_label: __metrics_path__
          regex: (.*)
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
    ```

__监控对象资源装填__
- 在 master 结点部署 [kube-state-metrics.yaml](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/%E6%89%80%E9%9C%80%E7%9A%84%E6%96%87%E4%BB%B6/kube-state-metrics.yaml)
    ```
    kubectl apply -f kube-state-metrics.yaml
    ```
- 在 `prometheus.yml` 配置文件中配置 job
    ```
    - job_name: kubernetes-service-endpoints
      kubernetes_sd_configs:
      - role: endpoints
        api_server: https://192.168.1.202:6443
        bearer_token_file: /opt/monitor/prometheus/token.k8s
        tls_config:
          insecure_skip_verify: true
      bearer_token_file: /opt/monitor/prometheus/token.k8s
      tls_config:
        insecure_skip_verify: true
      # Service没配置注解prometheus.io/scrape的不采集
      relabel_configs:
      - action: keep
        regex: true
        source_labels:
        - __meta_kubernetes_service_annotation_prometheus_io_scrape
      # 重命名采集目标协议
      - action: replace
        regex: (https?)
        source_labels:
        - __meta_kubernetes_service_annotation_prometheus_io_scheme
        target_label: __scheme__
      # 重命名采集目标指标URL路径
      - action: replace
        regex: (.+)
        source_labels:
        - __meta_kubernetes_service_annotation_prometheus_io_path
        target_label: __metrics_path__
      # 重命名采集目标地址
      - action: replace
        regex: ([^:]+)(?::\d+)?;(\d+)
        replacement: $1:$2
        source_labels:
        - __address__
        - __meta_kubernetes_service_annotation_prometheus_io_port
        target_label: __address__
      # 将K8s标签(.*)作为新标签名，原有值不变
      - action: labelmap
        regex: __meta_kubernetes_service_label_(.+)
      # 生成命名空间标签
      - action: replace
        source_labels:
        - __meta_kubernetes_namespace
        target_label: kubernetes_namespace
      # 生成Service名称标签
      - action: replace
        source_labels:
        - __meta_kubernetes_service_name
        target_label: kubernetes_service_name

    - job_name: kubernetes-pods
      kubernetes_sd_configs:
      - role: pod
        api_server: https://192.168.1.202:6443
        bearer_token_file: /opt/monitor/prometheus/token.k8s
        tls_config:
          insecure_skip_verify: true
      bearer_token_file: /opt/monitor/prometheus/token.k8s
      tls_config:
        insecure_skip_verify: true
      # 重命名采集目标协议
      relabel_configs:
      - action: keep
        regex: true
        source_labels:
        - __meta_kubernetes_pod_annotation_prometheus_io_scrape
      # 重命名采集目标指标URL路径
      - action: replace
        regex: (.+)
        source_labels:
        - __meta_kubernetes_pod_annotation_prometheus_io_path
        target_label: __metrics_path__
      # 重命名采集目标地址
      - action: replace
        regex: ([^:]+)(?::\d+)?;(\d+)
        replacement: $1:$2
        source_labels:
        - __address__
        - __meta_kubernetes_pod_annotation_prometheus_io_port
        target_label: __address__
      # 将K8s标签(.*)作为新标签名，原有值不变
      - action: labelmap
        regex: __meta_kubernetes_pod_label_(.+)
      # 生成命名空间标签
      - action: replace
        source_labels:
        - __meta_kubernetes_namespace
        target_label: kubernetes_namespace
      # 生成Service名称标签
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_name
        target_label: kubernetes_pod_name
    ```
- 因为 Dokcer 容器所在的网段和 Prometheus 所在的网段并不相同所以 Prometheus 是获取不到 docker 的容器信息的，如果需要获取则需要对网络进行配置
- 当然如果直接在 kubernetes 中部署 Prometheus 则不会出现这个问题