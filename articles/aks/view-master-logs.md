---
title: Просмотр журналов контроллера Службы Azure Kubernetes (AKS)
description: Узнайте, как включить и просмотреть журналы для главного узла Kubernetes в Службе Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/03/2019
ms.author: mlearned
ms.openlocfilehash: dc72a8d448a189918def35da0250d83c81da7fa0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812813"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Включение и просмотр журналов главного узла Kubernetes в Службе Azure Kubernetes (AKS)

При использовании Службы Azure Kubernetes (AKS) основные компоненты, такие как *kube-apiserver* и *kube-controller-manager*, предоставляются как управляемая служба. Вы создадите узлы, где будут запущены *kubelet* и среда выполнения контейнера, и сможете управлять ими, а также развернете свои приложения через управляемый сервер API Kubernetes. Чтобы устранить неполадки в приложении и службах, вам может потребоваться просмотреть журналы, созданные этими основными компонентами. В этой статье описано, как использовать журналы Azure Monitor для включения и запроса журналов из основных компонентов Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

Для работы с этой статьей у вас должен быть кластер AKS, выполняющийся в учетной записи Azure. Если у вас еще нет кластера AKS, создайте его с помощью [Azure CLI][cli-quickstart] или [портал Azure][portal-quickstart]. Журналы Azure Monitor работают в кластерах AKS с поддержкой и без поддержки RBAC.

## <a name="enable-diagnostics-logs"></a>Включение журналов диагностики

Чтобы собрать и просмотреть данные из нескольких источников, журналы Azure Monitor предоставляют язык запросов и механизм аналитики, обеспечивающий когнитивные аналитические сведения среде. Рабочая область используется для сопоставления и анализа данных и может интегрироваться с другими службами Azure, такими как Application Insights и центр безопасности Azure. Чтобы использовать другую платформу для анализа журналов, вы можете отправить журналы диагностики в учетную запись хранения Azure или концентратор событий. Дополнительные сведения см. в статье [что такое Azure Monitor журналов?][log-analytics-overview].

Журналы Azure Monitor включены и управляются в портал Azure. Чтобы включить сбор журналов для основных компонентов Kubernetes в своем кластере AKS, откройте портал Azure в веб-браузере и выполните шаги ниже.

1. Выберите группу ресурсов для кластера AKS, например *myResourceGroup*. Не выбирайте группу ресурсов, которая содержит отдельные ресурсы кластера AKS, например *MC_myResourceGroup_myAKSCluster_eastus*.
1. В области слева выберите **Параметры диагностики**.
1. Выберите кластер AKS, например *myAKSCluster*, а затем выберите **Добавить параметр диагностики**.
1. Введите имя, например *myAKSClusterLogs*, а затем выберите **Отправить в Log Analytics**.
1. Выберите существующую рабочую область или создайте новую. При создании рабочей области Укажите имя рабочей области, группу ресурсов и расположение.
1. В списке доступных журналов выберите журналы, которые вы хотите включить. К общим журналам относятся *KUBE-аписервер*, *KUBE-Controller-Manager*и *KUBE-Scheduler*. Вы можете включить дополнительные журналы, такие как *kube-audit* и *cluster-autoscaler*. Когда рабочая область Log Analytics будет включена, вы сможете вернуть или изменить собранные журналы.
1. Когда настройка будет завершена, нажмите кнопку **Сохранить**, чтобы включить сбор выбранных журналов.

На следующем примере снимок экрана портала отображается окно " *параметры диагностики* ":

![Включение рабочей области Log Analytics для журналов Azure Monitor кластера AKS](media/view-master-logs/enable-oms-log-analytics.png)

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
    image: nginx:1.15.5
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

Создайте модуль Pod с помощью команды [kubectl Create][kubectl-create] и укажите файл YAML, как показано в следующем примере:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Просмотр собранных журналов

Включение журналов диагностики и их отображение в рабочей области Log Analytics может занять несколько минут. В портал Azure выберите группу ресурсов для рабочей области Log Analytics, например *myResourceGroup*, а затем выберите ресурс log Analytics, например *мякслогс*.

![Выбор рабочей области Log Analytics для кластера AKS](media/view-master-logs/select-log-analytics-workspace.png)

В области слева выберите **Журналы**. Чтобы просмотреть *kube-apiserver*, введите следующий запрос в текстовом поле:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Многие журналы возвращаются для сервера API. Чтобы сузить запрос до просмотра журналов о модуле pod NGINX, созданном на предыдущем шаге, добавьте дополнительную инструкцию *where* для поиска *pods/nginx*, как показано в следующем примере запроса:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Появятся конкретные журналы для модуля pod NGINX, как показано в следующем примере снимка экрана:

![Результаты запроса Log Analytics для примера модуля pod NGINX](media/view-master-logs/log-analytics-query-results.png)

Чтобы просмотреть дополнительные журналы, вы можете обновить запрос, указав для имени *категории* *kube-controller-manager* или *kube-scheduler*, в зависимости от того, какие дополнительные журналы вы активируете. Затем вы можете использовать дополнительные инструкции *where* для уточнения событий, которые вы ищете.

Дополнительные сведения о том, как запрашивать и фильтровать данные журнала, см. в разделе [Просмотр или анализ данных, собранных с помощью поиска по журналам log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Схема событий журналов

Чтобы проанализировать данные журнала, в следующей таблице представлена схема, используемая для каждого события:

| Имя поля               | Описание |
|--------------------------|-------------|
| *resourceId*             | Ресурс Azure, с помощью которого был создан журнал |
| *time*                   | Метка времени — время передачи журнала |
| *category*               | Имя контейнера или компонента, генерирующего журнал |
| *operationName*          | Всегда *Служба контейнеров Майкрософт, управляемые кластеры, журналы диагностики, чтение* |
| *properties.log*         | Полный текст журнала из компонента |
| *properties.stream*      | *stderr* или *stdout* |
| *properties.pod*         | Имя модуля pod, из которого получен журнал |
| *properties.containerID* | Идентификатор контейнера DOCKER, из которого получен этот журнал |

## <a name="log-roles"></a>Роли журнала

| Role                     | Описание |
|--------------------------|-------------|
| *акссервице*             | Отображаемое имя в журнале аудита для операции плоскости управления (из Хкпсервице) |
| *мастерклиент*           | Отображаемое имя в журнале аудита для Мастерклиентцертификате — сертификат, полученный с помощью команды AZ AKS Get-credentials. |
| *нодеклиент*             | Отображаемое имя для ClientCertificate, которое используется узлами агента |

## <a name="next-steps"></a>Следующие шаги

В этой статье вы узнали, как включить и просмотреть журналы для основных компонентов Kubernetes в своем кластере AKS. Чтобы отслеживать и устранять неполадки, можно также [Просмотреть журналы Kubelet][kubelet-logs] и [включить доступ к узлам SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
