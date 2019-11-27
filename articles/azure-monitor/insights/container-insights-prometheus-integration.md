---
title: Настройка Azure Monitor для Prometheus интеграции контейнеров | Документация Майкрософт
description: В этой статье описывается, как настроить Azure Monitor для агента контейнеров для сбора метрик из Prometheus с помощью кластера службы Kubernetes Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 51bdf0cfedb30fbd95f9a44e8f4a0efe4e857104
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514346"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Настройка брака/отхода метрик Prometheus с Azure Monitor для контейнеров

[Prometheus](https://prometheus.io/) — это популярное решение с открытым кодом для мониторинга метрик, которое разрабатывает организация [Cloud Native Compute Foundation](https://www.cncf.io/). Azure Monitor для контейнеров обеспечивает простой процесс адаптации для сбора метрик Prometheus. Как правило, для использования Prometheus необходимо настроить сервер Prometheus и управлять им с помощью магазина. При интеграции с Azure Monitor сервер Prometheus не требуется. Вам нужно просто предоставить конечную точку метрик Prometheus с помощью средств экспорта или модулей (приложения), а контейнерный агент для Azure Monitor контейнеров может поцарапать метрики за вас. 

![Архитектура мониторинга контейнеров для Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Минимальная версия агента, поддерживаемая для брака Prometheus метрик, — ciprod07092019 или более поздней версии, а версия агента, поддерживаемая для записи ошибок конфигурации и агента в `KubeMonAgentEvents` таблице, — ciprod10112019. Дополнительные сведения о версиях агентов и о том, что входит в каждый выпуск, см. в разделе [заметки о выпуске агента](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). Чтобы проверить версию агента, на вкладке **узел** выберите узел и в области свойства запишите значение свойства **тег образа агента** .

### <a name="prometheus-scraping-settings"></a>Параметры отхода Prometheus

Активные отходы метрик из Prometheus выполняются с одной из двух перспектив:

* URL-адрес на уровне кластера и обнаружение целей из указанных конечных точек службы. Например, K8S службы, такие как KUBE-DNS и KUBE-State-метрики, и заметки Pod, относящиеся к приложению. Метрики, собранные в этом контексте, будут определены в разделе ConfigMap *[Prometheus data_collection_settings. Cluster]* .
* URL-адрес на уровне узла и обнаружение целей из указанных конечных точек службы. Метрики, собранные в этом контексте, будут определены в разделе ConfigMap *[Prometheus_data_collection_settings. Node]* .

| Конечная точка | Область | Пример |
|----------|-------|---------|
| Аннотация к Pod | На уровне кластера | Примечания <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Служба Kubernetes | На уровне кластера | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL-адрес или конечная точка | На узел или на уровне кластера | `http://myurl:9101/metrics` |

Если указан URL-адрес, Azure Monitor для контейнеров только перемещается в конечную точку. Если указана служба Kubernetes, имя службы разрешается с помощью DNS-сервера кластера для получения IP-адреса, а затем разрешенная служба переводится в отходы.

|Область | Key | Тип данных | Значение | ОПИСАНИЕ |
|------|-----|-----------|-------|-------------|
| На уровне кластера | | | | Укажите один из следующих трех методов, чтобы отбракировать конечные точки для метрик. |
| | `urls` | Строка, | Массив с разделителями-запятыми | Конечная точка HTTP (указан IP-адрес или допустимый путь URL-адреса). Например, `urls=[$NODE_IP/metrics]`. ($NODE _IP — это конкретная Azure Monitor для параметра Containers, которую можно использовать вместо IP-адреса узла. Необходимо использовать все прописные буквы.) |
| | `kubernetes_services` | Строка, | Массив с разделителями-запятыми | Массив Kubernetes служб для сбора метрик из KUBE-State-метрик. Например,`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Логическое значение. | Значение true или false | Если задано значение `true` в параметрах на уровне кластера, Azure Monitor для агента контейнеров будет Kubernetes модули Pod во всем кластере для следующих заметок Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Логическое значение. | Значение true или false | Включает брак/отход модуля Pod. Для параметра `monitor_kubernetes_pods` нужно задать значение `true`. |
| | `prometheus.io/scheme` | Строка, | HTTP или HTTPS | По умолчанию используется отработка отказа по протоколу HTTP. При необходимости задайте значение `https`. | 
| | `prometheus.io/path` | Строка, | Массив с разделителями-запятыми | Путь HTTP-ресурса, из которого извлекать метрики. Если путь метрик не `/metrics`, определите его с помощью этой заметки. |
| | `prometheus.io/port` | Строка, | 9102 | Укажите порт, из которого будет списано значение. Если параметр port не установлен, по умолчанию будет 9102. |
| | `monitor_kubernetes_pods_namespaces` | Строка, | Массив с разделителями-запятыми | Список разрешенных пространств имен для брака метрики из модулей Kubernetes.<br> Например: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| На уровне узла | `urls` | Строка, | Массив с разделителями-запятыми | Конечная точка HTTP (указан IP-адрес или допустимый путь URL-адреса). Например, `urls=[$NODE_IP/metrics]`. ($NODE _IP — это конкретная Azure Monitor для параметра Containers, которую можно использовать вместо IP-адреса узла. Необходимо использовать все прописные буквы.) |
| На уровне узла или на уровне кластера | `interval` | Строка, | 60 | Интервал сбора по умолчанию равен одной минуте (60 секунд). Коллекцию можно изменить для *[prometheus_data_collection_settings. Node]* и (или) *[prometheus_data_collection_settings. Cluster]* на единицы времени, например s, m, h. |
| На уровне узла или на уровне кластера | `fieldpass`<br> `fielddrop`| Строка, | Массив с разделителями-запятыми | Вы можете указать метрики, которые должны быть собраны или не передаваться из конечной точки, установив список разрешений (`fieldpass`) и запретить (`fielddrop`). Сначала необходимо задать список разрешений. |

Конфигмапс является глобальным списком, и к нему может быть применен только один ConfigMap. У вас не может быть другой Конфигмапс.

## <a name="configure-and-deploy-configmaps"></a>Настройка и развертывание Конфигмапс

Выполните следующие действия, чтобы настроить и развернуть файл конфигурации ConfigMap в кластере.

1. [Скачайте](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) файл шаблона ConfigMap YAML и сохраните его как Container-АЗМ-MS-ажентконфиг. YAML.

2. Измените файл YAML ConfigMap, дополнив настройки, чтобы забракировать метрики Prometheus.

    - Чтобы собираются службы Kubernetes Services на уровне кластера, настройте файл ConfigMap, используя следующий пример.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Чтобы настроить отходы метрик Prometheus по определенному URL-адресу в кластере, настройте файл ConfigMap, используя следующий пример.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Чтобы настроить отходы метрик Prometheus из набора управляющих программ агента для каждого отдельного узла в кластере, настройте следующие параметры в ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP — это конкретный Azure Monitor для параметра Containers, который можно использовать вместо IP-адреса узла. Оно должно быть в верхнем регистре. 

    - Чтобы настроить отходы метрик Prometheus, указав аннотацию Pod, выполните следующие действия.

       1. В ConfigMap укажите следующее:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Укажите следующую конфигурацию для заметок Pod:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Если вы хотите ограничить мониторинг определенными пространствами имен для модулей Pod с заметками, например включать только модули памяти, выделенные для рабочих нагрузок, установите для `monitor_kubernetes_pod` значение `true` в ConfigMap и добавьте фильтр пространства имен `monitor_kubernetes_pods_namespaces` указав пространства имен, из которых производится списание. Например: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Создайте ConfigMap, выполнив следующую команду kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Пример: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    До вступления в силу изменение конфигурации может занять несколько минут, и все omsagent Pod в кластере будут перезапущены. Перезагрузка является пошаговым перезапуском для всех модулей omsagent Pod, а не всех перезапусков одновременно. После завершения перезагрузки отображается сообщение, похожее на следующее и содержащее результат: `configmap "container-azm-ms-agentconfig" created`.

## <a name="applying-updated-configmap"></a>Применение обновленных ConfigMap

Если вы уже развернули ConfigMap в кластере и хотите обновить ее с помощью более новой конфигурации, вы можете изменить ранее использовавшийся файл ConfigMap, а затем применить его с помощью той же команды, что и ранее, `kubectl apply -f <configmap_yaml_file.yaml`.

До вступления в силу изменение конфигурации может занять несколько минут, и все omsagent Pod в кластере будут перезапущены. Перезагрузка является пошаговым перезапуском для всех модулей omsagent Pod, а не всех перезапусков одновременно. После завершения перезагрузки отображается сообщение, похожее на следующее и содержащее результат: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verify-configuration"></a>Проверка конфигурации 

Чтобы убедиться, что конфигурация была успешно применена, выполните следующую команду, чтобы проверить журналы из модуля Pod: `kubectl logs omsagent-fdf58 -n=kube-system`. При наличии ошибок конфигурации из модулей Pod omsagent в выходных данных отобразятся ошибки, аналогичные приведенным ниже.

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Ошибки, связанные с применением изменений конфигурации, также доступны для проверки. Следующие параметры позволяют выполнить дополнительные действия по устранению изменений конфигурации и браку в Prometheus метриках.

- Из журналов Pod агента с помощью одной и той же команды `kubectl logs`. 

- Из активных журналов. В журналах в реальном времени отображаются ошибки, аналогичные приведенным ниже.

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Из таблицы **кубемонажентевентс** в рабочей области log Analytics. Данные отправляются каждый час с серьезностью *предупреждения* для ошибок конфигурации и серьезности *ошибки* . Если ошибок нет, запись в таблице будет содержать данные со *сведениями об*уровне серьезности, которые не сообщают об ошибках. Свойство **Tags** содержит дополнительные сведения о Pod и идентификаторе контейнера, в которых произошла ошибка, а также первое вхождение, Последнее повторение и количество за последний час.

Ошибки не позволяют omsagent анализировать файл, что приводит к перезапуску и использованию конфигурации по умолчанию. После исправления ошибок в ConfigMap сохраните файл YAML и примените обновленный Конфигмапс, выполнив команду: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="query-prometheus-metrics-data"></a>Запрос данных метрик Prometheus

Чтобы просмотреть метрики Prometheus, которые выводятся Azure Monitor и ошибки конфигурации или брака/отхода, о которых сообщил агент, просмотрите [данные метрик запросов Prometheus](container-insights-log-search.md#query-prometheus-metrics-data) и конфигурацию [запроса или ошибки](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Просмотр метрик Prometheus в Grafana

Azure Monitor для контейнеров поддерживает просмотр метрик, хранящихся в рабочей области Log Analytics, на панелях мониторинга Grafana. Мы предоставили шаблон, который можно скачать из [репозитория панели мониторинга](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) Grafana, чтобы получить сведения о том, как запросить дополнительные данные из наблюдаемых кластеров для визуализации на пользовательских панелях мониторинга Grafana. 

## <a name="review-prometheus-data-usage"></a>Проверка использования данных Prometheus

Чтобы определить, является ли объем приема для каждого метрики размером в ГБ в день, чтобы понять, насколько он высокий, предоставляется следующий запрос.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
В выходных данных отобразятся результаты, аналогичные приведенным ниже.

![Регистрация результатов запроса для тома приема данных](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

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

![Регистрация результатов запроса для тома приема данных](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Дополнительные сведения о мониторинге использования данных и анализе затрат см. в руководствах по [управлению использованием и затратами Azure Monitor журналов](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о настройке параметров коллекции агентов для stdout, stderr и переменных среды из рабочих нагрузок контейнера см. [здесь](container-insights-agent-config.md). 