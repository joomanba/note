# EKS JVM 모니터링 
[MONITORING USING PROMETHEUS AND GRAFANA](https://www.eksworkshop.com/intermediate/240_monitoring/)
- [Core-dev EKS Grafana Dashboard](http://a9ea8d4f04c7e443899e930e31e41d78-796150329.ap-northeast-2.elb.amazonaws.com/)

[Grafana JVM (Micrometer) Dashboard](https://grafana.com/grafana/dashboards/4701)
- [Amazon EKS 및 Kubernetes에서 Java/JMX 샘플 워크로드 설정](https://docs.aws.amazon.com/ko_kr/AmazonCloudWatch/latest/monitoring/ContainerInsights-Prometheus-Sample-Workloads-javajmx.html)


## prometheus


### build.gradle
```xml
        //Prometheus
        implementation('io.micrometer:micrometer-core')
        implementation('io.micrometer:micrometer-registry-prometheus')
```

### application.xml

```xml
management:
  endpoint:
    metrics:
      enabled: true
    prometheus:
      enabled: true
  endpoints:
    web:
      exposure:
        include: "*"
  metrics:
    tags:
      application: ${spring.application.name}
    export:
      prometheus:
        enabled: true
```

### service.xml

```xml
...
metadata:  
  ...
  annotations:
    prometheus.io/path: /actuator/prometheus
    prometheus.io/port: "8080"
    prometheus.io/scrape: "true"
```