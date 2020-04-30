---
title: Настройка локальных метрик и журналов для самостоятельно размещенного шлюза управления API Azure | Документация Майкрософт
description: Узнайте, как настроить локальные метрики и журналы для самостоятельно размещенного шлюза службы управления API Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: dd49680da6f52e32ddb52dbdb23ad5e8f627a91e
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205070"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Настройка локальных метрик и журналов для самостоятельно размещенного шлюза службы управления API Azure

В этой статье содержатся сведения о настройке локальных метрик и журналов для [самостоятельно размещенного шлюза](./self-hosted-gateway-overview.md). Сведения о настройке метрик и журналов облака см. в [этой статье](how-to-configure-cloud-metrics-logs.md). 

## <a name="metrics"></a>Метрики
Самостоятельно размещенный шлюз поддерживает [статистику](https://github.com/statsd/statsd), которая стала согласованным протоколом для сбора и агрегирования метрик. В этом разделе описано, как развернуть статистику по Kubernetes, настроить шлюз на выдачу метрик через статистику и использовать [Prometheus](https://prometheus.io/) для мониторинга метрик. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>Развертывание статистики и Prometheus в кластере

Ниже приведен пример конфигурации YAML для развертывания статистики и Prometheus в кластере Kubernetes, в котором развернута локальная шлюз. Он также создает [службу](https://kubernetes.io/docs/concepts/services-networking/service/) для каждого из них. На собственном шлюзе будут опубликованы метрики для службы с статистикой. Мы будем обращаться к панели мониторинга Prometheus через свою службу.   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: prom/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

Сохраните конфигурации в файл с именем `metrics.yaml` и используйте приведенную ниже команду, чтобы развернуть все данные в кластере.

```console
kubectl apply -f metrics.yaml
```

После завершения развертывания выполните приведенную ниже команду, чтобы проверить работу модулей Pod. Обратите внимание, что имя Pod будет отличаться. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Выполните приведенную ниже команду, чтобы проверить, работают ли службы. Запишите и `CLUSTER-IP` `PORT` в статистике службы, которая будет потребоваться позже. Вы можете посетить панель мониторинга Prometheus, используя `EXTERNAL-IP` и `PORT`.

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>Настройка самостоятельно размещенного шлюза для выдачи метрик

Теперь, когда были развернуты и Prometheus, можно обновить конфигурации самостоятельно размещенного шлюза, чтобы начать выдачу метрик через статистику. Эту функцию можно включить или отключить с помощью `telemetry.metrics.local` ключа в ConfigMap развертывания самостоятельного шлюза с дополнительными параметрами. Ниже приведена разбивка доступных вариантов.

| Поле  | По умолчанию | Описание |
| ------------- | ------------- | ------------- |
| данные телеметрии. метрики. локальные  | `none` | Включает ведение журнала с использованием статистики. Значение может быть `none`равно `statsd`,. |
| телеметрии. метрики. Локальные. statsd. Конечная точка  | н/д | Указывает статистику конечной точки. |
| данные телеметрии. метрики. Локальные. statsd. выборка  | н/д | Задает частоту выборки метрик. Значение может находиться в диапазоне от 0 до 1. Например,`0.5`|
| данные телеметрии. метрики. local. statsed. Tag-Format  | н/д | [Формат расстановки тегов](https://github.com/prometheus/statsd_exporter#tagging-extensions)в статистике. Значение может быть `none`, `librato`, `dogStatsD`, `influxDB`. |

Ниже приведен пример конфигурации.

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

Обновите файл YAML в развертывании саморазмещенного шлюза с указанными выше конфигурациями и примените изменения с помощью следующей команды: 

```console
kubectl apply -f <file-name>.yaml
 ```

Чтобы получить последние изменения конфигурации, перезапустите развертывание шлюза с помощью следующей команды:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>Просмотр метрик

Теперь, когда у нас есть все, что было развернуто и настроено, шлюз с автономным размещением должен сообщать метрики через статистику. Prometheus выберет метрики из статистики. Перейдите на панель мониторинга Prometheus, используя `EXTERNAL-IP` и `PORT` службу Prometheus. 

Выполните некоторые вызовы API через шлюз, размещенный в собственном расположении. Если все настроено правильно, вы сможете просматривать метрики ниже:

| Метрика  | Описание |
| ------------- | ------------- |
| Requests  | Количество запросов API за период |
| DurationInMS | Количество миллисекунд с момента поступления запроса в шлюз до момента полной отправки ответа |
| баккенддуратионинмс | Время в миллисекундах, затраченное на все операции ввода-вывода серверной части (подключение, отправка и получение байтов)  |
| клиентдуратионинмс | Время в миллисекундах, затраченное на все операции ввода-вывода клиента (подключение, отправка и получение байтов)  |

## <a name="logs"></a>Журналы

Саморазмещенный шлюз выводит журналы в `stdout` и `stderr` по умолчанию. Журналы можно легко просмотреть с помощью следующей команды:

```console
kubectl logs <pod-name>
```

Если собственный шлюз развернут в службе Azure Kubernetes, можно включить [Azure Monitor для контейнеров](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) , собирающих `stdout` и `stderr` исключающих рабочие нагрузки, а также просматривать журналы в log Analytics. 

Шлюз, размещенный на собственном сервере, также поддерживает ряд протоколов `rfc5424`, включая `journal` `localsyslog`, и. В таблице ниже приведены все поддерживаемые параметры. 

| Поле  | По умолчанию | Описание |
| ------------- | ------------- | ------------- |
| данные телеметрии. Logs. STD  | `text` | Включает ведение журнала для стандартных потоков. Значение может быть `none`равно `text`,,`json` |
| данные телеметрии. Logs. local  | `none` | Включает локальное ведение журнала. Значение может быть `none`, `auto`, `localsyslog`, `rfc5424`,`journal`  |
| данные телеметрии. Logs. local. локалсислог. Endpoint  | н/д | Указывает конечную точку локалсислог.  |
| данные телеметрии. Logs. local. локалсислог. помещений  | н/д | Указывает [код устройства](https://en.wikipedia.org/wiki/Syslog#Facility)локалсислог. Например,`7` 
| данные телеметрии. Logs. local. rfc5424. Endpoint  | н/д | Указывает конечную точку rfc5424.  |
| данные телеметрии. Logs. local. rfc5424. помещений  | н/д | Указывает код устройства для каждого [rfc5424](https://tools.ietf.org/html/rfc5424). Например,`7`  |
| данные телеметрии. Logs. local. журнал. Endpoint  | н/д | Указывает конечную точку журнала.  |

Ниже приведен пример конфигурации локального ведения журнала.

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные сведения о самостоятельно размещенном шлюзе см. в статье [Обзор самостоятельного размещения шлюза в службе управления API Azure](self-hosted-gateway-overview.md) .
* Сведения о [настройке и сохранении журналов в облаке](how-to-configure-local-metrics-logs.md)

