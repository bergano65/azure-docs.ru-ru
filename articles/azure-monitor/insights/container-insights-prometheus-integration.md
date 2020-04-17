---
title: Наконфигурните azure Monitor для контейнеров Prometheus Интеграция (ru) Документы Майкрософт
description: В этой статье описывается, как можно настроить Azure Monitor для агента контейнеров для скобливания метрик из Прометея с вашим кластером Kubernetes.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 7fcf52cceb69834f68f8e4ce7a2674972a6430fd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537378"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Настроить налаживание метрик Prometheus с помощью Azure Monitor для контейнеров

[Prometheus](https://prometheus.io/) является популярным решением мониторинга метрики с открытым исходным кодом и является частью [Фонда Cloud Native Compute.](https://www.cncf.io/) Azure Monitor для контейнеров обеспечивает бесшовный опыт посадки для сбора метрик Prometheus. Как правило, для использования Prometheus необходимо настроить и управлять сервером Prometheus с магазином. Интеграция с Azure Monitor не требуется серверprous. Вам просто нужно разоблачить конечную точку промедляя через ваших экспортеров или стручки (приложение), и контейнерный агент для Azure Monitor для контейнеров может очистить метрики для вас. 

![Архитектура мониторинга контейнеров для «Прометея»](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Минимальная версия агента, поддерживаемая для соскабливания метрик Prometheus, является ciprod07092019 `KubeMonAgentEvents` или более поздней версией, поддерживаемой для написания конфигурации и ошибок агента в таблице ciprod10112019. Для получения дополнительной информации о версиях агента и [agent release notes](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)о том, что включено в каждый релиз, см. To verify your agent version, from the **Node** tab select a node, and in the properties pane note value of the **Agent Image Tag** property.

Очистка метрик Prometheus поддерживается кластерами Kubernetes, размещенными на:

- Служба Azure Kubernetes (AKS)
- Лазурный стек или на территории
- Azure Red Hat OpenShift

>[!NOTE]
>Для Azure Red Hat OpenShift файл шаблона ConfigMap создается в пространстве имен *openshift-azure-logging.* Он не настроен для активного скоблить метрики или сбор данных от агента.
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Предпосылки для OpenShift Azure Red Hat

Перед тем, как начать работу, подтвердите, что вы являетесь членом роли клиента кластера Admin вашего кластера Azure Red Hat OpenShift для настройки контейнерного агента и настроек соскабливания Prometheus. Чтобы убедиться, что вы являетесь членом *группы osa-customer-adins,* запустите следующую команду:

``` bash
  oc get groups
```

Результат должен выглядеть так:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Если вы являетесь членом *группы osa-customer-adins,* `container-azm-ms-agentconfig` вы должны иметь возможность перечислить ConfigMap, используя следующую команду:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Результат должен выглядеть так:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>Настройки соскабливания Prometheus

Активное выскабливание метрик из Прометея выполняется с одной из двух точек зрения:

* Кластерный - HTTP URL и обнаружение целей из перечисленных конечных точек службы. Например, службы k8, такие как kube-dns и kube-state-metrics, и аннотации стручка, характерные для приложения. Метрики, собранные в этом контексте, будут определены в разделе ConfigMap *«Прометей data_collection_settings.кластера».*
* Узел- HTTP URL и обнаружить цели из перечисленных конечных точек службы. Метрики, собранные в этом контексте, будут определены в разделе ConfigMap *(Prometheus_data_collection_settings.node).*

| Конечная точка | Область | Пример |
|----------|-------|---------|
| Аннотация стручка | Кластерный | Аннотации: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Служба Kubernetes | Кластерный | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/конечная точка | Per-узел и/или кластерный | `http://myurl:9101/metrics` |

Когда URL указан, Azure Monitor для контейнеров только царапает конечную точку. Когда указансервис Kubernetes, имя службы решается с кластерным DNS-сервером, чтобы получить IP-адрес, а затем разрешитая служба соскребита.

|Область | Клавиши | Тип данных | Значение | Описание |
|------|-----|-----------|-------|-------------|
| Кластерный | | | | Укажите любой из следующих трех методов, чтобы очистить конечные точки для метрик. |
| | `urls` | Строка | Блок, разделенный запятой | Конечная точка HTTP (либо IP-адрес, либо указанный определенный путь URL-адреса). Например: `urls=[$NODE_IP/metrics]`. ($NODE IP является специфическим azure Monitor для параметров контейнеров и может использоваться вместо IP-адреса узлов. Должно быть все верхние части.) |
| | `kubernetes_services` | Строка | Блок, разделенный запятой | Массив kubernetes услуг для царапин метрик из kube-государство-метрики. Например, `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Логическое | true или false | При установке `true` в кластерных настройках Azure Monitor для агента контейнеров будет соскребать стручки Kubernetes по всему кластеру для следующих аннотаций Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Логическое | true или false | Позволяет выскабливание стручка. Для параметра `monitor_kubernetes_pods` нужно задать значение `true`. |
| | `prometheus.io/scheme` | Строка | HTTP или HTTPS | По умолчанию на слом более HTTP. При необходимости `https`установите. | 
| | `prometheus.io/path` | Строка | Блок, разделенный запятой | Путь ресурса HTTP, на котором можно получить метрики. Если траектория метрик не `/metrics`является, определите его с помощью этой аннотации. |
| | `prometheus.io/port` | Строка | 9102 | Укажите порт для очистки от. Если порт не установлен, он будет по умолчанию 9102. |
| | `monitor_kubernetes_pods_namespaces` | Строка | Блок, разделенный запятой | Разрешите список именных пространств для скобливания метрик из стручков Kubernetes.<br> Например `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`. |
| Узел по всей | `urls` | Строка | Блок, разделенный запятой | Конечная точка HTTP (либо IP-адрес, либо указанный определенный путь URL-адреса). Например: `urls=[$NODE_IP/metrics]`. ($NODE IP является специфическим azure Monitor для параметров контейнеров и может использоваться вместо IP-адреса узлов. Должно быть все верхние части.) |
| Узел-широкий или кластер-широкий | `interval` | Строка | 60-е годы | По умолчанию интервал сбора составляет одну минуту (60 секунд). Вы можете изменить коллекцию для *«prometheus_data_collection_settings.узла»* и/или *«prometheus_data_collection_settings.кластера»* для единиц времени, таких как s, m, h. |
| Узел-широкий или кластер-широкий | `fieldpass`<br> `fielddrop`| Строка | Блок, разделенный запятой | Вы можете указать определенные метрики, которые должны`fieldpass`быть собраны или`fielddrop`нет из конечных точек, установив допустимый () и запретить ( ) листинг. Сначала необходимо установить список разрешений. |

ConfigMaps является глобальным списком, и к агенту может быть применен только один ConfigMap. Вы не можете иметь другой ConfigMaps отменяя коллекции.

## <a name="configure-and-deploy-configmaps"></a>Настройка и развертывание ConfigMaps

Выполните следующие шаги для настройки файла конфигурации ConfigMap для кластеров Kubernetes.

1. [Загрузите](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) шаблон файла ConfigMap yaml и сохраните его в виде контейнера-azm-ms-agentconfig.yaml.

   >[!NOTE]
   >Этот шаг не требуется при работе с Azure Red Hat OpenShift, так как шаблон ConfigMap уже существует в кластере.

2. Отспособьте файл ConfigMap yaml с настройками, чтобы соскребать метрики Prometheus. Если вы редактируете файл ConfigMap yaml для Azure `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Red Hat OpenShift, сначала запустите команду, чтобы открыть файл в текстовом редакторе.

    >[!NOTE]
    >Следующая аннотация `openshift.io/reconcile-protect: "true"` должна быть добавлена под метаданные *контейнера-azm-ms-agentconfig* ConfigMap для предотвращения сверки. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Чтобы собрать кластерные службы Kubernetes, наймите файл ConfigMap на следующем примере.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Чтобы настроить соскоб метрик Prometheus из определенного URL-адреса кластера, наконзначайте файл ConfigMap на следующем примере.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Чтобы настроить выскабливание метрик Prometheus из DaemonSet агента для каждого отдельного узла в кластере, назначайте следующее в ConfigMap:
    
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
        >$NODE IP является специфическим azure Monitor для параметров контейнеров и может использоваться вместо IP-адреса узлов. Это должно быть все верхнее дело. 

    - Для настройки соскабливания метрик Prometheus путем указания аннотации стручка выполните следующие действия:

       1. В ConfigMap укажите следующее:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Укажите следующую конфигурацию для аннотаций стручка:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Если вы хотите ограничить мониторинг определенными пространствами имен для стручков, которые имеют аннотации, например, `true` включите только стручки, `monitor_kubernetes_pods_namespaces` предназначенные для производственных нагрузок, установите `monitor_kubernetes_pod` в ConfigMap и добавьте фильтр пространства имен, определяющий пространства имен, чтобы соскребать. Например `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`.

3. Для кластеров, помимо Azure Red Hat OpenShift, запустите следующую команду kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Например, `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Для Azure Red Hat OpenShift сохраните изменения в редакторе.

Изменение конфигурации может занять несколько минут, прежде чем вступит в силу, и все стручки omsagent в кластере будут перезастраны. Перезагрузка является перезапуском для всех стручков omsagent, не все перезагрузки в то же время. Когда перезагрузка закончена, отображается сообщение, аналогичное следующему и включает `configmap "container-azm-ms-agentconfig" created`результат: .

Вы можете просмотреть обновленный ConfigMap для Azure Red `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`Hat OpenShift, запустив команду, . 

## <a name="applying-updated-configmap"></a>Применение обновленного ConfigMap

Если вы уже развернули ConfigMap в кластер и хотите обновить его с помощью более новой конфигурации, можно отобразить файл ConfigMap, который вы использовали ранее, а затем применить с помощью тех же команд, что и раньше.

Для кластеров Kubernetes, помимо Azure Red `kubectl apply -f <configmap_yaml_file.yaml`Hat OpenShift, запустите команду. 

Для кластера Azure Red Hat OpenShift запустите команду, `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` чтобы открыть файл в редакторе по умолчанию, чтобы изменить, а затем сохранить его.

Изменение конфигурации может занять несколько минут, прежде чем вступит в силу, и все стручки omsagent в кластере будут перезастраны. Перезагрузка является перезапуском для всех стручков omsagent, не все перезагрузки в то же время. Когда перезагрузка закончена, отображается сообщение, аналогичное следующему и включает `configmap "container-azm-ms-agentconfig" updated`результат: .

## <a name="verify-configuration"></a>Проверка конфигурации

Чтобы проверить, что конфигурация была успешно применена к кластеру, используйте `kubectl logs omsagent-fdf58 -n=kube-system`следующую команду для просмотра журналов из стручка агента: . 

>[!NOTE]
>Эта команда не применима к кластеру Azure Red Hat OpenShift.
> 

При наличии ошибок конфигурации из стручков omsagent на выходе будут отображаться ошибки, аналогичные следующим:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Ошибки, связанные с изменением конфигурации, также доступны для просмотра. Доступны следующие варианты для выполнения дополнительных неполадок изменений конфигурации и соскабливания метрик Prometheus:

- Из журналов стручка агента `kubectl logs` с использованием той же команды 
    >[!NOTE]
    >Эта команда не применима к кластеру Azure Red Hat OpenShift.
    > 

- Из данных в реальном времени (предварительный просмотр). В журналах Live Data (preview) отображается ошибка, аналогичная следующим:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Из таблицы **KubeMonAgentEvents** в рабочем пространстве журнала Analytics. Данные отправляются каждый час с *серьезностью предупреждения* для ошибок царапин и серьезности *ошибки* для ошибок конфигурации. Если нет ошибок, запись в таблице будет иметь данные с серьезностью *Информация*, которая не сообщает ошибок. Свойство **Тегов** содержит больше информации о капсуле и идентификаторе контейнера, на котором произошла ошибка, а также о первом возникновении, последнем возникновении и подсчете в последний час.

- Для Azure Red Hat OpenShift проверьте журналы omsagent, исключив таблицу **ContainerLog,** чтобы проверить, включена ли коллекция журналов openshift-azure-logging.

Ошибки не позволяют omsagent разбирать файл, заставляя его перезапустить и использовать конфигурацию по умолчанию. После исправления ошибки (ы) в ConfigMap на кластерах, помимо Azure Red Hat OpenShift, сохраните `kubectl apply -f <configmap_yaml_file.yaml`файл yaml и примените обновленные ConfigMaps, запустив команду: . 

Для Azure Red Hat OpenShift отредконите и сохраните `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`обновленные ConfigMaps, запустив команду: .

## <a name="query-prometheus-metrics-data"></a>Данные метрик запроса Prometheus

Для просмотра метрик prometheus, соскобленных Azure Monitor, и любых ошибок конфигурации/царапин, сообщаемых агентом, просмотрите [данные метрик и](container-insights-log-search.md#query-prometheus-metrics-data) [конфигурацию запроса или ошибки соскабливания.](container-insights-log-search.md#query-config-or-scraping-errors)

## <a name="view-prometheus-metrics-in-grafana"></a>Просмотр метрик Prometheus в Графане

Azure Monitor для контейнеров поддерживает метрики просмотра, хранящиеся в рабочем пространстве log Analytics в панели мониторинга Grafana. Мы предоставили шаблон, который можно загрузить из [репозитория панели мониторинга](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) Grafana, чтобы начать работу и ссылку, чтобы помочь вам узнать, как задать запрос дополнительных данных из контролируемых кластеров, чтобы визуализировать в пользовательских панелях мониторинга Grafana. 

## <a name="review-prometheus-data-usage"></a>Просмотрите использование данных Prometheus

Чтобы определить объем приема каждого размера метрикв в ГБ в день, чтобы понять, является ли он высоким, предоставляется следующий запрос.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
На выходе будут показаны результаты, аналогичные следующим:

![Результаты запроса журнала об объеме приема данных](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Чтобы оценить, какой размер каждой метрики в ГБ в течение месяца, чтобы понять, является ли объем данных, полученных в рабочей области, высок, предоставляется следующий запрос.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

На выходе будут показаны результаты, аналогичные следующим:

![Результаты запроса журнала об объеме приема данных](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Более подробная информация о том, как контролировать использование данных и анализировать затраты, доступна в [журнале Manage и costs с помощью журналов Azure Monitor.](../platform/manage-cost-storage.md)

## <a name="next-steps"></a>Следующие шаги

Подробнее о настройке параметров сбора агентов для stdout, stderr и экологических переменных из контейнерных нагрузок [можно ознакомиться здесь.](container-insights-agent-config.md) 
