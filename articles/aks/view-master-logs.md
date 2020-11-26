---
title: Просмотр журналов контроллера Службы Azure Kubernetes (AKS)
description: Узнайте, как включить и просмотреть журналы для главного узла Kubernetes в Службе Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 10/14/2020
ms.openlocfilehash: 59e7259ae352491bddebe054f2c34bdc810ea48a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183232"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Включение и просмотр журналов главного узла Kubernetes в Службе Azure Kubernetes (AKS)

При использовании Службы Azure Kubernetes (AKS) основные компоненты, такие как *kube-apiserver* и *kube-controller-manager*, предоставляются как управляемая служба. Вы создадите узлы, где будут запущены *kubelet* и среда выполнения контейнера, и сможете управлять ими, а также развернете свои приложения через управляемый сервер API Kubernetes. Чтобы устранить неполадки в приложении и службах, вам может потребоваться просмотреть журналы, созданные этими основными компонентами. В этой статье описано, как использовать журналы Azure Monitor для включения и запроса журналов из основных компонентов Kubernetes.

## <a name="before-you-begin"></a>Перед началом

Для работы с этой статьей у вас должен быть кластер AKS, выполняющийся в учетной записи Azure. Если у вас еще нет кластера AKS, создайте его с помощью [Azure CLI][cli-quickstart] или [портала Azure][portal-quickstart]. Журналы Azure Monitor работают с кластерами AKS Kubernetes RBAC, Azure RBAC и без RBAC.

## <a name="enable-resource-logs"></a>Включение журналов ресурсов

Чтобы собрать и просмотреть данные из нескольких источников, журналы Azure Monitor предоставляют язык запросов и механизм аналитики, обеспечивающий когнитивные аналитические сведения среде. Рабочая область используется для сопоставления и анализа данных и может интегрироваться с другими службами Azure, такими как Application Insights и центр безопасности Azure. Чтобы использовать другую платформу для анализа журналов, вместо этого можно отправить журналы ресурсов в учетную запись хранения Azure или концентратор событий. Дополнительные сведения см. в [обзоре журналов Azure Monitor][log-analytics-overview].

Журналы Azure Monitor включены и управляются в портал Azure. Чтобы включить сбор журналов для основных компонентов Kubernetes в своем кластере AKS, откройте портал Azure в веб-браузере и выполните шаги ниже.

1. Выберите группу ресурсов для кластера AKS, например *myResourceGroup*. Не выбирайте группу ресурсов, которая содержит отдельные ресурсы кластера AKS, например *MC_myResourceGroup_myAKSCluster_eastus*.
1. В области слева выберите **Параметры диагностики**.
1. Выберите кластер AKS, например *myAKSCluster*, а затем выберите **Добавить параметр диагностики**.
1. Введите имя, например *myAKSClusterLogs*, а затем выберите **Отправить в Log Analytics**.
1. Выберите существующую рабочую область или создайте новую. При создании рабочей области Укажите имя рабочей области, группу ресурсов и расположение.
1. В списке доступных журналов выберите журналы, которые вы хотите включить. В этом примере включите журналы *KUBE-Audit* и *KUBE-Audit-Admin* . К общим журналам относятся *KUBE-аписервер*, *KUBE-Controller-Manager* и *KUBE-Scheduler*. Когда рабочая область Log Analytics будет включена, вы сможете вернуть или изменить собранные журналы.
1. Когда настройка будет завершена, нажмите кнопку **Сохранить**, чтобы включить сбор выбранных журналов.

## <a name="log-categories"></a>Категории журналов

Помимо записей, записанных Kubernetes, в журналах аудита вашего проекта также есть записи из AKS.

Журналы аудита записываются в три категории: *KUBE-Audit*, *KUBE-Audit-Admin* и *Guard*.

- Категория *KUBE-Audit* содержит все данные журнала аудита для каждого события аудита, включая *Получение*, *перечисление*, *Создание*, *Обновление*, *Удаление*, *исправление* и *запись*.
- Категория *KUBE-Audit-Admin* является подмножеством категории журнала *KUBE-Audit* . *KUBE-Audit-Admin* значительно сокращает количество журналов за счет исключения событий аудита *Get* и *List* из журнала.
- Категория *Guard* — это управляемые аудиты Azure AD и Azure RBAC. Для управляемого Azure AD: токен в, сведения о пользователе. Для Azure RBAC: проверки доступа в и из.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Планирование запуска тестового модуля pod в кластере AKS

Чтобы создать несколько журналов, создайте модуль pod в своем кластере AKS. Следующий пример манифеста YAML можно использовать для создания базового экземпляра NGINX. Создайте файл с именем `nginx.yaml` в удобном редакторе и вставьте следующее содержимое:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Создайте модуль pod с помощью команды [kubectl create][kubectl-create] и укажите свой файл YAML, как показано в следующем примере:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Просмотр собранных журналов

Включение и отображение журналов диагностики может занять до 10 минут.

> [!NOTE]
> Если вам нужны все данные журнала аудита для соответствия или других целей, собирайте и храните их в недорогом хранилище, например в хранилище BLOB-объектов. Используйте категорию журнал *KUBE-Audit-Admin* , чтобы получить и сохранить осмысленный набор данных журнала аудита для целей мониторинга и оповещений.

В портал Azure перейдите к кластеру AKS и выберите **журналы** в левой части. Закройте окно *примеры запросов* , если оно отображается.

В области слева выберите **Журналы**. Чтобы просмотреть журналы *KUBE-Audit* , введите в текстовое поле следующий запрос:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Скорее всего, возвращаются многие журналы. Чтобы просмотреть журналы о модуле NGINX, созданном на предыдущем шаге, добавьте дополнительную инструкцию *WHERE* для поиска *nginx* , как показано в следующем примере запроса:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Чтобы просмотреть журналы *KUBE-Audit-Admin* , введите в текстовое поле следующий запрос:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

В этом примере запрос отображает все задания Create в *KUBE-Audit-Admin*. Вероятнее всего, возвращено много результатов. чтобы просмотреть журналы о модуле NGINX, созданном на предыдущем шаге, добавьте дополнительную инструкцию *WHERE* для поиска *nginx* , как показано в следующем примере запроса.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Дополнительные сведения о том, как запрашивать и фильтровать данные журнала, см. в разделе [Просмотр или анализ данных, собранных с помощью поиска по журналам log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Схема событий журналов

AKS регистрирует следующие события:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [контаинерлог][log-schema-containerlog]
* [контаинернодеинвентори][log-schema-containernodeinventory]
* [контаинерсервицелог][log-schema-containerservicelog]
* [Периодический сигнал][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [кубивентс][log-schema-kubeevents]
* [кубехеалс][log-schema-kubehealth]
* [кубемонажентевентс][log-schema-kubemonagentevents]
* [кубенодеинвентори][log-schema-kubenodeinventory]
* [кубеподинвентори][log-schema-kubepodinventory]
* [кубесервицес][log-schema-kubeservices]
* [Perf][log-schema-perf]

## <a name="log-roles"></a>Роли журнала

| Роль                     | Описание |
|--------------------------|-------------|
| *акссервице*             | Отображаемое имя в журнале аудита для операции плоскости управления (из Хкпсервице) |
| *мастерклиент*           | Отображаемое имя в журнале аудита для Мастерклиентцертификате — сертификат, полученный с помощью команды AZ AKS Get-credentials. |
| *нодеклиент*             | Отображаемое имя для ClientCertificate, которое используется узлами агента |

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как включить и просмотреть журналы для основных компонентов Kubernetes в своем кластере AKS. Для выполнения дальнейших действий мониторинга и устранения неполадок вы также можете [просмотреть журналы Kubelet][kubelet-logs] и [разрешить доступ к узлу SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf