
## PrometheusRule：监控与报警的利器

我们知道之前我们使用自定义的方式可以在 Prometheus 的配置文件之中指定 AlertManager 实例和 报警的 rules 文件，现在我们通过 Operator 部署的呢？我们可以在 Prometheus Dashboard 的 Config 页面下面查看关于 AlertManager 的配置：

```
alerting:
  alert_relabel_configs:
  - separator: ;
    regex: prometheus_replica
    replacement: $1
    action: labeldrop
  alertmanagers:
  - follow_redirects: true
    enable_http2: true
    scheme: http
    path_prefix: /
    timeout: 10s
    api_version: v2
    relabel_configs:
    - source_labels: [__meta_kubernetes_service_name]
      separator: ;
      regex: alertmanager-main
      replacement: $1
      action: keep
    - source_labels: [__meta_kubernetes_endpoint_port_name]
      separator: ;
      regex: web
      replacement: $1
      action: keep
    kubernetes_sd_configs:
    - role: endpoints
      kubeconfig_file: ""
      follow_redirects: true
      enable_http2: true
      namespaces:
        own_namespace: false
        names:
        - monitoring
rule_files:
- /etc/prometheus/rules/prometheus-k8s-rulefiles-0/*.yaml
```

上面 alertmanagers 的配置我们可以看到是通过 role 为 `endpoints` 的 kubernetes 的自动发现机制获取的，匹配的是服务名为 `alertmanager-main`，端口名为 web 的 Service 服务，我们可以查看下 alertmanager-main 这个 Service：

```
$ kubectl describe svc alertmanager-main -n monitoring
Name:              alertmanager-main
Namespace:         monitoring
Labels:            app.kubernetes.io/component=alert-router
                   app.kubernetes.io/instance=main
                   app.kubernetes.io/name=alertmanager
                   app.kubernetes.io/part-of=kube-prometheus
                   app.kubernetes.io/version=0.27.0
Annotations:       <none>
Selector:          app.kubernetes.io/component=alert-router,app.kubernetes.io/instance=main,app.kubernetes.io/name=alertmanager,app.kubernetes.io/part-of=kube-prometheus
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                172.16.29.21
IPs:               172.16.29.21
Port:              web  9093/TCP
TargetPort:        web/TCP
Endpoints:         192.168.0.173:9093,192.168.0.175:9093,192.168.0.176:9093
Port:              reloader-web  8080/TCP
TargetPort:        reloader-web/TCP
Endpoints:         192.168.0.173:8080,192.168.0.175:8080,192.168.0.176:8080
Session Affinity:  ClientIP
Events:            <none>
```

可以看到服务名正是 `alertmanager-main`，Port 定义的名称也是 web，符合上面的规则，所以 `Prometheus` 和 `AlertManager` 组件就正确关联上了。

而对应的报警规则文件位于：`/etc/prometheus/rules/prometheus-k8s-rulefiles-0/`目录下面所有的 YAML 文件。

我们可以进入 Prometheus 的 Pod 中验证下该目录下面是否有 YAML 文件：

```
kex prometheus-k8s-0 -nmonitoring -- sh

/prometheus $ cd /etc/prometheus/rules/prometheus-k8s-rulefiles-0/
/etc/prometheus/rules/prometheus-k8s-rulefiles-0 $ ls
monitoring-alertmanager-main-rules-b6e33381-7319-4dd3-8b96-6b99ec46bcf3.yaml
monitoring-grafana-rules-eedbd431-e04a-4a08-83a0-17cbd96fe942.yaml
monitoring-kube-prometheus-rules-0da776fa-e5f4-498e-bcc6-5e310b2995ba.yaml
monitoring-kube-state-metrics-rules-2242037f-5eaf-41a7-b122-036d05526c6b.yaml
monitoring-kubernetes-monitoring-rules-e31ffca7-ac96-4f9e-bb82-6a37e1ce6500.yaml
monitoring-prometheus-k8s-prometheus-rules-55f68dd4-e3f1-45ca-9c5b-bb6b952d2ae4.yaml
monitoring-prometheus-operator-rules-6e51dbe0-410b-468e-b4f2-84b32cd32665.yaml
```

**这个 YAML 文件实际上就是我们之前创建的一个 PrometheusRule 文件包含的内容：**

```
$ cat grafana-prometheusrule.yaml

apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  labels:
    app.kubernetes.io/component: grafana
    app.kubernetes.io/name: grafana
    app.kubernetes.io/part-of: kube-prometheus
    app.kubernetes.io/version: 11.4.0
    prometheus: k8s
    role: alert-rules
  name: grafana-rules
  namespace: monitoring
spec:
  groups:
  - name: GrafanaAlerts
    rules:
    - alert: GrafanaRequestsFailing
      annotations:
        message: '{{ $labels.namespace }}/{{ $labels.job }}/{{ $labels.handler }} is experiencing {{ $value | humanize }}% errors'
        runbook_url: https://runbooks.prometheus-operator.dev/runbooks/grafana/grafanarequestsfailing
      expr: |
        100 * namespace_job_handler_statuscode:grafana_http_request_duration_seconds_count:rate5m{handler!~"/api/datasources/proxy/:id.*|/api/ds/query|/api/tsdb/query", status_code=~"5.."}
        / ignoring (status_code)
        sum without (status_code) (namespace_job_handler_statuscode:grafana_http_request_duration_seconds_count:rate5m{handler!~"/api/datasources/proxy/:id.*|/api/ds/query|/api/tsdb/query"})
        > 50
      for: 5m
      labels:
        severity: warning
  - name: grafana_rules
    rules:
    - expr: |
        sum by (namespace, job, handler, status_code) (rate(grafana_http_request_duration_seconds_count[5m]))
      record: namespace_job_handler_statuscode:grafana_http_request_duration_seconds_count:rate5m
```

我们这里的 `PrometheusRule` 的 name 为 `prometheus-k8s-rules`，namespace 为 monitoring，我们可以猜想到我们创建一个 PrometheusRule 资源对象后，会自动在上面的 `prometheus-k8s-rulefiles-0` 目录下面生成一个对应的 `{namespace}-{name}.yaml` 文件，所以如果以后我们需要自定义一个报警选项的话，只需要定义一个 PrometheusRule 资源对象即可。

**至于为什么 Prometheus 能够识别这个 PrometheusRule 资源对象**呢？

这就需要查看我们创建的 prometheus 这个资源对象了，**<mark>里面有非常重要的一个属性 `ruleSelector`，用来匹配 `rule` 规则的过滤器，要求匹配具有 `prometheus=k8s` 和 `role=alert-rules `标签的 `PrometheusRule `资源对象</mark>**

```
ruleSelector:
  matchLabels:
    prometheus: k8s
    role: alert-rules
```

> 添加和不添加 ruleSelector 的主要区别在于 Prometheus 实例如何选择并加载告警规则 (Alerting Rules)。

### 什么是 ruleSelector

ruleSelector 是 Prometheus-Operator 中的一个配置选项，它用于指定 Prometheus 实例加载哪些告警规则 (PrometheusRule) 资源。

**通过标签匹配的方式，ruleSelector 允许用户精确控制 Prometheus 实例加载的规则来源。**

### 配置区别

#### **不添加 ruleSelector**

 * **行为**：  Prometheus 实例将会尝试加载同一命名空间中的所有 **PrometheusRule 资源（或者根据 ruleNamespaceSelector 的配置，从其他命名空间中加载**
 * **优点**：
 	* 简单易用，不需要为每个 PrometheusRule 资源添加标签
 	* 适合小规模部署或开发环境
 *  缺点
 	* 在生产环境中，可能会导致 Prometheus 实例加载不相关的告警规则，增加负担。
 	*  如果有多个 Prometheus 实例运行，容易出现冲突或重复加载。

#### **添加 ruleSelector**

```
ruleSelector:
  matchLabels:
    prometheus: k8s
    role: alert-rules
```

**行为**:

**Prometheus 实例只会加载符合 ruleSelector 指定标签的 PrometheusRule 资源**。例如，只有同时拥有以下标签的 PrometheusRule 会被加载

* `prometheus: k8s`
* `role: alert-rules`

 
**优点**:

* 精确控制 Prometheus 实例加载的告警规则，避免加载无关规则。
* 在多 Prometheus 实例场景中，可以通过标签隔离规则来源。
* 方便管理大规模部署中的规则集

 
**缺点**

* 需要在每个 PrometheusRule 资源上添加相应的标签。
* 配置稍显复杂，可能导致规则遗漏（忘记添加标签）。

### 实际场景中的应用

**场景 1：单实例 Prometheus**

<mark>如果您的集群中只有一个 Prometheus 实例，可以不使用 ruleSelector，让其自动加载所有规则。这种方式适合简单部署和测试环境。</mark>

**场景 2：多实例 Prometheus**

在复杂环境中（例如多团队共享集群或跨环境监控），建议使用 ruleSelector，为每个 Prometheus 实例指定特定的告警规则：

* 不同团队维护不同的规则，通过标签隔离。
* 在生产和测试环境中分别部署 Prometheus，加载不同的规则。

* **不添加 ruleSelector:**
	* 加载所有 PrometheusRule 资源
	*  资源简单易用，自动加载所有规则
	*  不适合大规模环境，规则管理混乱

* **添加 ruleSelector**
	* 按标签筛选加载特定的 PrometheusRule
	* 精确控制规则加载，避免冲突，适合生产环境
	* 配置复杂，需要正确设置标签

## 深入理解 PrometheusRule：创建高效的告警规则

PrometheusRule 是 Prometheus Operator 提供的一种自定义资源，用于定义和管理 Prometheus 的告警规则和记录规则（Recording Rules）。这些规则是 Prometheus 在监控环境中发挥告警和数据处理能力的关键工具。

本篇文章将详细讲解 PrometheusRule 的结构、配置方法、应用场景，以及如何根据实际需求优化告警策略。

### PrometheusRule 的作用

PrometheusRule 的主要作用包括：

**告警规则（Alerting Rules**）

*  定义监控数据的条件，当这些条件满足时触发告警。
* 例如，当某个应用的请求延迟超过阈值时发出警告。

**记录规则（Recording Rules）**

* 定期将复杂的查询结果存储为新的时间序列，以优化查询性能。
* 例如，将一段时间内的平均 CPU 使用率存储为一个新指标。

通过 PrometheusRule，可以将规则配置与 Prometheus 的管理逻辑分离，实现规则的灵活分组和统一管理。

### PrometheusRule 的基础结构

一个 PrometheusRule 资源由以下几个关键部分组成：

**元数据部分**

定义 PrometheusRule 的名称、命名空间和标签信息。

```
metadata:
  name: example-rules
  namespace: monitoring
  labels:
    app.kubernetes.io/name: prometheus
```

**spec 部分**

spec 定义了**规则组（groups**），其中每个组包含一个或多个规则。

**groups:**

* name：规则组的名称，用于逻辑分组。
* rules：规则列表，定义具体的告警或记录规则

以下是一个完整的 PrometheusRule 示例：

```
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: example-rules
  namespace: monitoring
spec:
  groups:
name: example-group
rules:
```

**详解规则的字段**

**Alerting Rules**

告警规则的核心字段包括：

* **alert**
	* 告警名称，简明描述告警内容
	* HighRequestLatency.
* **expr**
	* PromoL 表达式，用于定义触发告警的条件
	* `rate(http_request_duration_seconds_bucket{le="0.5"} < 0.1` 表示过去 5 分钟内请求延迟大于 0.5 秒的比例小于 10%。
* **for**
	* 持续时间。告警条件需要满足多长时间后才会触发
	* 例如：2m 表示条件需持续2分钟
* **labels**
	* 自定义标签，用于对告警进行分类。
	*  `severity: warning`
* **annotations**
	* 注释信息，通常用于告警的详细描述，
	* summary:
	* description：详细描述，可包含变量占位符（如比 **{{$labels.instance }}**）

### 应用场景

**系统资源监控告警**

监控节点 CPU 和内存的使用情况，设置告警规则：

```
lert: HighCpuUsage
expr: node_cpu_seconds_total{mode="idle"} / node_cpu_seconds_total < 0.2
for: 1m
labels:
  severity: critical
annotations:
  summary: "High CPU usage detected"
  description: "Instance {{ $labels.instance }} has high CPU usage."
```

**应用性能监控告警**

例如，监控 HTTP 请求错误率

```
alert: HighHttpErrorRate
expr: rate(http_requests_total{status=~"5.*"}[5m]) / rate(http_requests_total[5m]) > 0.05
for: 5m
labels:
  severity: warning
annotations:
  summary: "High HTTP error rate detected"
  description: "Instance {{ $labels.instance }} has an HTTP error rate above 5%."
```

**数据聚合与优化**

通过记录规则优化查询性能，例如记录每个服务的总请求数：

```
record: job:http_requests:total
expr: sum(rate(http_requests_total[5m])) by (job)
```

### 扩展

我们要想自定义一个报警规则，只需要创建一个具有 `prometheus=k8s` 和 `role=alert-rules ` 标签的 PrometheusRule 对象就行了，我这里准备声明一个 ArgoCD 的 Rule：

```
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: argocd-rules
  namespace: monitoring
  labels:
    prometheus: k8s
    role: alert-rules
    app.kubernetes.io/name: argocd
    app.kubernetes.io/part-of: argocd
spec:
  groups:
  - name: argocd.rules
    rules:
    # ArgoCD Application Controller 高延迟告警
    - alert: ArgoCDApplicationControllerHighLatency
      expr: histogram_quantile(0.95, rate(argocd_app_controller_reconciliation_duration_seconds_bucket[5m])) > 1
      for: 2m
      labels:
        severity: warning
      annotations:
        summary: "ArgoCD Application Controller Reconciliation Latency High ({{ $labels.namespace }})"
        description: "The reconciliation latency for ArgoCD application controller in namespace {{ $labels.namespace }} is over 1 second for more than 2 minutes."
        
    # ArgoCD Server 高请求错误率告警
    - alert: ArgoCDServerHighRequestErrorRate
      expr: rate(argocd_server_http_requests_total{status=~"5.*"}[5m]) > 0.05
      for: 2m
      labels:
        severity: critical
      annotations:
        summary: "ArgoCD Server High Error Rate ({{ $labels.namespace }})"
        description: "The ArgoCD server in namespace {{ $labels.namespace }} has an HTTP error rate over 5% for the past 5 minutes."

    # ArgoCD Repo Server 同步失败告警
    - alert: ArgoCDRepoServerSyncFailed
      expr: increase(argocd_repo_server_git_request_failures_total[5m]) > 5
      for: 2m
      labels:
        severity: critical
      annotations:
        summary: "ArgoCD Repo Server Sync Failures ({{ $labels.namespace }})"
        description: "ArgoCD Repo Server in namespace {{ $labels.namespace }} has more than 5 sync failures in the last 5 minutes."

    # ArgoCD Dex Server 不可用告警
    - alert: ArgoCDDexServerDown
      expr: up{job="argocd-dex-server"} == 0
      for: 1m
      labels:
        severity: critical
      annotations:
        summary: "ArgoCD Dex Server Down ({{ $labels.namespace }})"
        description: "The ArgoCD Dex Server in namespace {{ $labels.namespace }} is not running for more than 1 minute."

    # ArgoCD Application 运行失败告警
    - alert: ArgoCDApplicationOutOfSync
      expr: argocd_app_info{health_status!="Healthy"} > 0
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "ArgoCD Applications Out of Sync ({{ $labels.namespace }})"
        description: "There are applications in ArgoCD that are not in a healthy state for more than 5 minutes."
```

### 优化告警策略

* **避免告警风暴**
	* 使用 for 字段，避免短时间内的波动触发大量告警。
* **分级告警**
	* 设置不同严重级别（如 critical 和 warning）的告警规则。
* **基于历史数据优化阈值**
	* 通过查询历史数据，确定合理的告警触发阈值。
* **定期回顾和优化规则**
	*  定期检查规则的触发频率和准确性，调整表达式或阈值