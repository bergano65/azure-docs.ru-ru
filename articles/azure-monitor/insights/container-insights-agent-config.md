---
title: Настройка Azure Monitor для сбора данных агента контейнеров | Документация Майкрософт
description: В этой статье описывается настройка Azure Monitor для агента контейнеров для управления набором журналов stdout/stderr и переменных среды.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: magoedte
ms.openlocfilehash: 7cd915c47fa0661a9da66d7ca3315480ce7d6b98
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709428"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Настройка сбора данных агента для Azure Monitor для контейнеров

Azure Monitor для контейнеров собирает данные stdout, stderr и переменные среды из рабочих нагрузок контейнера, развернутых в управляемых кластерах Kubernetes, размещенных в службе Kubernetes Azure (AKS), из контейнерного агента. Этот агент также может получать данные временных рядов (также называемые метриками) из Prometheus с помощью контейнерного агента без необходимости настраивать сервер и базу данных Prometheus и управлять ими. Параметры сбора данных агента можно настроить, создав настраиваемый Конфигмапс Kubernetes для управления этим интерфейсом. 

В этой статье показано, как создать ConfigMap и настроить сбор данных в соответствии с вашими требованиями.

>[!NOTE]
>В настоящее время поддержка Prometheus является функцией в общедоступной предварительной версии.
>

## <a name="configmap-file-settings-overview"></a>Общие сведения о параметрах файлов ConfigMap

Предоставляется файл шаблона ConfigMap, который позволяет легко редактировать его с помощью настроек без необходимости создавать его с нуля. Прежде чем начать, ознакомьтесь с документацией по Kubernetes о [конфигмапс](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) и ознакомьтесь с созданием, настройкой и развертыванием конфигмапс. Это позволит вам фильтровать stderr и stdout на пространство имен или во всем кластере, а также переменные среды для любого контейнера, работающего во всех модулях Pod и узлах в кластере.

>[!IMPORTANT]
>Минимальная версия агента, поддерживаемая для получения переменных среды stdout, stderr и Variable из рабочих нагрузок контейнера, — ciprod06142019 или более поздней версии. Минимальная версия агента, поддерживаемая для брака Prometheus метрик, — ciprod07092019 или более поздней. Чтобы проверить версию агента, на вкладке **узел** выберите узел и в области свойства запишите значение свойства **тег образа агента** .  

### <a name="data-collection-settings"></a>Параметры сбора данных

Ниже приведены параметры, которые можно настроить для управления сбором данных.

|Ключ |Тип данных |Значение |Описание |
|----|----------|------|------------|
|`schema-version` |Строка (с учетом регистра) |Версия 1 |Это версия схемы, используемая агентом при анализе этого ConfigMap. Текущая поддерживаемая версия схемы — v1. Изменение этого значения не поддерживается и будет отклонено при вычислении ConfigMap.|
|`config-version` |Строковое | | Поддерживает возможность отслеживания версии этого файла конфигурации в системе управления версиями или в репозитории. Максимально допустимое количество символов равно 10, а все остальные символы усекаются. |
|`[log_collection_settings.stdout] enabled =` |логический | Значение true или false | Определяет, включен ли сбор журналов контейнеров stdout. Если задано `true` значение и ни одно пространство имен не исключено для сбора`log_collection_settings.stdout.exclude_namespaces` журналов stdout (значение ниже), журналы stdout будут собираться из всех контейнеров во всех модулях или узлах в кластере. Если параметр не указан в Конфигмапс, по умолчанию `enabled = true`используется значение. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Строковое | Массив с разделителями-запятыми |Массив пространств имен Kubernetes, для которых не будут собираться журналы stdout. Этот параметр эффективен, только если `log_collection_settings.stdout.enabled` имеет `true`значение. Если параметр не указан в ConfigMap, по умолчанию `exclude_namespaces = ["kube-system"]`используется значение.|
|`[log_collection_settings.stderr] enabled =` |логический | Значение true или false |Определяет, включен ли сбор журналов контейнеров stderr. Если задано `true` значение и ни одно пространство имен не исключается для`log_collection_settings.stderr.exclude_namespaces` сбора журналов stdout (параметр), то журналы stderr будут собираться из всех контейнеров во всех модулях или узлах в кластере. Если параметр не указан в Конфигмапс, по умолчанию `enabled = true`используется значение. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Строковое |Массив с разделителями-запятыми |Массив пространств имен Kubernetes, для которых не будут собираться журналы stderr. Этот параметр эффективен, только если `log_collection_settings.stdout.enabled` имеет `true`значение. Если параметр не указан в ConfigMap, по умолчанию `exclude_namespaces = ["kube-system"]`используется значение. |
| `[log_collection_settings.env_var] enabled =` |логический | Значение true или false | Определяет, включена ли коллекция переменных среды. Если задано `false`значение, переменные среды не собираются ни для одного контейнера, работающего во всех модулях Pod/Nodes в кластере. Если параметр не указан в ConfigMap, по умолчанию `enabled = true`используется значение. |

### <a name="prometheus-scraping-settings"></a>Параметры отхода Prometheus

![Архитектура мониторинга контейнеров для Prometheus](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

Azure Monitor для контейнеров — это простой интерфейс для включения сбора метрик Prometheus с помощью нескольких побракованных с помощью следующих механизмов, как показано в следующей таблице. Метрики собираются с помощью набора параметров, заданного в одном файле ConfigMap, который является тем же файлом, который используется для настройки коллекции stdout, stderr и переменных среды из рабочих нагрузок контейнера. 

Активные отходы метрик из Prometheus выполняются с одной из двух перспектив:

* URL-адрес в Интернете на основе кластера и обнаружение целей из указанных конечных точек службы, K8S таких служб, как KUBE-DNS и KUBE-State-метрики, а также аннотации Pod, характерные для приложения. Метрики, собранные в этом контексте, будут определены в разделе ConfigMap *[Prometheus data_collection_settings. Cluster]* .
* URL-адрес на уровне узла и обнаружение целей из указанных конечных точек службы. Метрики, собранные в этом контексте, будут определены в разделе ConfigMap *[Prometheus_data_collection_settings. Node]* .

| Конечная точка | `Scope` | Пример |
|----------|-------|---------|
| Аннотация к Pod | На уровне кластера | Примечания <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000" <br>prometheus.io/scheme: "http"` |
| Служба Kubernetes | На уровне кластера | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL-адрес или конечная точка | На узел или на уровне кластера | `http://myurl:9101/metrics` |

Если указан URL-адрес, Azure Monitor для контейнеров только перемещается в конечную точку. Если указана служба Kubernetes, имя службы разрешается с помощью DNS-сервера кластера для получения IP-адреса, а затем разрешенная служба переводится в отходы.

|`Scope` | Ключ | Тип данных | Значение | Описание |
|------|-----|-----------|-------|-------------|
| На уровне кластера | | | | Укажите один из следующих трех методов, чтобы отбракировать конечные точки для метрик. |
| | `urls` | Строковое | Массив с разделителями-запятыми | Конечная точка HTTP (указан IP-адрес или допустимый путь URL-адреса). Например, `urls=[$NODE_IP/metrics]`. ($NODE _IP — это конкретная Azure Monitor для параметра Containers, которую можно использовать вместо IP-адреса узла. Необходимо использовать все прописные буквы.) |
| | `kubernetes_services` | Строковое | Массив с разделителями-запятыми | Массив Kubernetes служб для сбора метрик из KUBE-State-метрик. Например,`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | логический | Значение true или false | Если задано `true` значение в параметрах на уровне кластера, Azure Monitor для агента контейнеров будет наделять модули Pod Kubernetes по всему кластеру на наличие следующих заметок Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | логический | Значение true или false | Включает брак/отход модуля Pod. Для параметра `monitor_kubernetes_pods` нужно задать значение `true`. |
| | `prometheus.io/scheme` | Строковое | HTTP или HTTPS | По умолчанию используется отработка отказа по протоколу HTTP. При необходимости задайте значение `https`. | 
| | `prometheus.io/path` | Строковое | Массив с разделителями-запятыми | Путь HTTP-ресурса, из которого извлекать метрики. Если путь метрик не `/metrics`равен, определите его с помощью этой заметки. |
| | `prometheus.io/port` | Строковое | 9102 | Укажите порт для прослушивания. Если параметр port не установлен, по умолчанию будет 9102. |
| На уровне узла | `urls` | Строковое | Массив с разделителями-запятыми | Конечная точка HTTP (указан IP-адрес или допустимый путь URL-адреса). Например, `urls=[$NODE_IP/metrics]`. ($NODE _IP — это конкретная Azure Monitor для параметра Containers, которую можно использовать вместо IP-адреса узла. Необходимо использовать все прописные буквы.) |
| На уровне узла или на уровне кластера | `interval` | Строковое | 60 | Интервал сбора по умолчанию равен одной минуте (60 секунд). Коллекцию можно изменить для *[prometheus_data_collection_settings. Node]* и/или *[prometheus_data_collection_settings. Cluster]* на единицы времени, такие как NS, US (или âμс), MS, s, m, h. |
| На уровне узла или на уровне кластера | `fieldpass`<br> `fielddrop`| Строковое | Массив с разделителями-запятыми | Можно указать метрики, которые должны быть собраны или не включены в конечную точку, задав`fieldpass`список разрешений ()`fielddrop`и запретить (). Сначала необходимо задать список разрешений. |

ConfigMap является глобальным списком, и к нему может быть применен только один ConfigMap. У вас не может быть другой ConfigMap.

## <a name="configure-and-deploy-configmaps"></a>Настройка и развертывание Конфигмапс

Выполните следующие действия, чтобы настроить и развернуть файл конфигурации ConfigMap в кластере.

1. [Скачайте](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) файл шаблона ConfigMap YAML и сохраните его как Container-АЗМ-MS-ажентконфиг. YAML.  
1. Измените файл ConfigMap YAML с вашими настройками.

    - Чтобы исключить определенные пространства имен для сбора журналов stdout, необходимо настроить ключ или значение, используя следующий пример: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Чтобы отключить коллекцию переменных среды для определенного контейнера, установите ключ/значение `[log_collection_settings.env_var] enabled = true` , чтобы включить коллекцию переменных глобально, а затем выполните действия, описанные [здесь](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) , чтобы завершить настройку для конкретного контейнера.
    
    - Чтобы отключить кластер для сбора журналов stderr, настройте ключ/значение, используя следующий пример: `[log_collection_settings.stderr] enabled = false`.
    
    - В следующих примерах показано, как настроить метрики файлов ConfigMap из всего кластера URL-адресов, из Дамеонсет на уровне узла агента и указав аннотацию Pod.

        - Prometheus метрики из определенного URL-адреса в кластере.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h.
         fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
         fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
         urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

        - Prometheus метрики из управляющей программы агента, выполняемой на каждом узле в кластере.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings 
         [prometheus_data_collection_settings.node] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h. 
         # Node level scrape endpoint(s). These metrics will be scraped from agent's DaemonSet running in every node in the cluster 
         urls = ["http://$NODE_IP:9103/metrics"] 
         fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
         fielddrop = ["metric_to_drop"] 
        ```

        - Prometheus метрики, указав аннотацию Pod.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h
         monitor_kubernetes_pods = true #replicaset will scrape Kubernetes pods for the following prometheus annotations: 
          - prometheus.io/scrape:"true" #Enable scraping for this pod 
          - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
          - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
          - prometheus.io/port:"8000" #If port is not 9102 use this annotation
        ```

1. Создайте ConfigMap, выполнив следующую команду kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Пример: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    До вступления в силу изменение конфигурации может занять несколько минут, и все omsagent Pod в кластере будут перезапущены. Перезагрузка является пошаговым перезапуском для всех модулей omsagent Pod, а не всех перезапусков одновременно. После завершения перезагрузки отображается сообщение, похожее на следующее и содержащее результат: `configmap "container-azm-ms-agentconfig" created`.

Чтобы убедиться, что конфигурация успешно применена, выполните следующую команду, чтобы проверить журналы из модуля Pod агента: `kubectl logs omsagent-fdf58 -n=kube-system`. При наличии ошибок конфигурации из модулей Pod omsagent в выходных данных отобразятся ошибки, аналогичные приведенным ниже.

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Ошибки, связанные с применением изменений конфигурации для Prometheus, также доступны для проверки.  Либо из журналов модуля Pod агента, используя одну и ту `kubectl logs` же команду, либо из активных журналов. В журналах в реальном времени отображаются ошибки, аналогичные приведенным ниже.

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

Ошибки не позволяют omsagent анализировать файл, что приводит к перезапуску и использованию конфигурации по умолчанию. После исправления ошибок в ConfigMap сохраните файл YAML и примените обновленный Конфигмапс, выполнив команду: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Применение обновленных ConfigMap

Если вы уже развернули ConfigMap в кластере и хотите обновить ее с помощью более новой конфигурации, вы можете изменить ранее использовавшийся файл ConfigMap, а затем применить его с помощью той же команды, что и ранее, `kubectl apply -f <configmap_yaml_file.yaml`.

До вступления в силу изменение конфигурации может занять несколько минут, и все omsagent Pod в кластере будут перезапущены. Перезагрузка является пошаговым перезапуском для всех модулей omsagent Pod, а не всех перезапусков одновременно. После завершения перезагрузки отображается сообщение, похожее на следующее и содержащее результат: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Проверка версии схемы

Поддерживаемые версии схемы конфигурации доступны в виде заметки к Pod (версии схемы) в модуле omsagent. Их можно увидеть с помощью следующей команды kubectl:`kubectl describe pod omsagent-fdf58 -n=kube-system`

Выходные данные будут выглядеть так, как показано ниже, с помощью схемы аннотации — версии:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="review-prometheus-data-usage"></a>Проверка использования данных Prometheus

Чтобы просмотреть метрики Prometheus, которые забракованы Azure Monitor, укажите "Prometheus" в качестве пространства имен. Ниже приведен пример запроса для просмотра метрик Prometheus из пространства имен `default` kubernetes.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| extend tags=parse_json(Tags)
| where tostring(tags.namespace) == "default" 
```

Данные Prometheus также можно запрашивать напрямую по имени.

```
InsightsMetrics 
| where Name contains "some_prometheus_metric"
```

Чтобы определить, является ли объем приема для каждого метрики размером в ГБ в день, чтобы понять, насколько он высокий, предоставляется следующий запрос.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
В выходных данных отобразятся результаты, аналогичные приведенным ниже.

![Регистрация результатов запроса для тома приема данных](./media/container-insights-agent-config/log-query-example-usage-03.png)

Чтобы оценить, какой размер метрик в Гб соответствует месяцу, чтобы понять, является ли объем данных, полученных в рабочей области, большим, предоставляется следующий запрос.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

В выходных данных отобразятся результаты, аналогичные приведенным ниже.

![Регистрация результатов запроса для тома приема данных](./media/container-insights-agent-config/log-query-example-usage-02.png)

Дополнительные сведения о мониторинге использования данных и анализе затрат см. в руководствах по [управлению использованием и затратами Azure Monitor журналов](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Следующие шаги

Azure Monitor для контейнеров не включает предопределенный набор предупреждений. Ознакомьтесь с разработкой оповещений о [производительности с помощью Azure Monitor для контейнеров](container-insights-alerts.md) , чтобы узнать, как создавать Рекомендуемые оповещения для высокой загрузки ЦП и памяти для поддержки DevOps или рабочих процессов и процедур.

- Чтобы продолжить изучение способов использования Azure Monitor и отслеживать другие аспекты кластера AKS, ознакомьтесь со статьей [Общие сведения о мониторинге производительности кластера AKS с помощью Azure Monitor для контейнеров (предварительная версия)](container-insights-analyze.md).

- Просмотрите [примеры запросов журналов](container-insights-log-search.md#search-logs-to-analyze-data) , чтобы просмотреть предварительно определенные запросы и примеры для проверки или настройки предупреждений, визуализации или анализа кластеров.
