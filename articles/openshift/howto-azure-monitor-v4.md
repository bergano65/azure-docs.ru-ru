---
title: Интеграция Azure Monitor для Azure Red Hat OpenShift 4,3
description: Узнайте, как включить Azure Monitor в кластере Microsoft Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79082852"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Интеграция Azure Monitor для Azure Red Hat OpenShift 4,3

> [!IMPORTANT] 
> Обратите внимание, что Azure Red Hat OpenShift 4,3 в настоящее время доступен только в закрытой предварительной версии в восточной части США. Закрытая Предварительная версия принимает только приглашение. Перед включением этой функции обязательно Зарегистрируйте подписку: [Регистрация закрытой предварительной версии Azure Red Hat OpenShift](https://aka.ms/aro-preview-register)

> [!NOTE]
> Функции предварительной версии являются самостоятельными и предоставляются как есть и доступны и исключаются из соглашения об уровне обслуживания (SLA) и ограниченной гарантии. Поэтому функции не предназначены для использования в рабочей среде.

В этой статье описывается, как включить закрытую предварительную версию Azure Monitor для контейнеров для кластеров OpenShift 4,3, размещенных локально или в любой облачной среде. Эти же инструкции также применяются для включения мониторинга кластеров Azure Red Hat OpenShift (АТО) 4,3.  

## <a name="prerequisites"></a>Предварительные условия

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- Доступ к kubeconfig кластера kubernetes
- Доступ к подписке Azure
- Доступ к кластеру OpenShift 4,3 для установки Azure Monitor for Containers Helm Chart
- Минимальное разрешение роли RBAC участника в подписке Azure  
- Агент мониторинга требует, чтобы следующие исходящие порты и домены отправляли данные мониторинга на серверную часть Azure Monitor (если она заблокирована прокси-сервером или брандмауэром):
  - *. ods.opinsights.azure.com 443
  - *. oms.opinsights.azure.com 443
  - *. blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Адаптация

> [!TIP]
> В скрипте используются функции Bash 4, поэтому убедитесь в актуальности bash. Вы можете проверить текущую версию с помощью `bash --version`.

### <a name="download-the-onboarding-script"></a>Скачивание скрипта адаптации

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Выполните следующий скрипт с azureSubscriptionId, регионом рабочей области, имя_кластера и контекстом кластера Kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Пример:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Настройка сбора данных агента

По умолчанию агент мониторинга собирает журналы контейнеров {stdout; stderr} всех контейнеров, работающих во всех пространствах имен, за исключением KUBE-System.  Если вы хотите настроить сбор журналов контейнеров для конкретного пространства имен или пространств имен, можно обратиться к [конфигурации агента аналитики контейнеров](../azure-monitor/insights/container-insights-agent-config.md). Здесь можно настроить агент мониторинга с требуемыми параметрами сбора данных с помощью схемы конфигурации.

## <a name="configure-scraping-of-prometheus-metrics"></a>Настройка брака/отхода метрик Prometheus

Azure Monitor для контейнеров — Prometheus метрики и прием Azure Monitor серверной части. Инструкции по настройке брака Prometheus см. в разделе [Prometheus Configuration for Container Insights](../azure-monitor/insights/container-insights-prometheus-integration.md) .

После успешной адаптации перейдите к [гибридному мониторингу](https://aka.ms/azmon-containers-hybrid) и выберите среда как **"все"** , чтобы просмотреть новый подключенный кластер OpenShift v4.

## <a name="disable-monitoring"></a>Отключение мониторинга

Если вы хотите отключить наблюдение, можно удалить диаграмму Azure Monitor for Containers Helm, используя следующую команду, чтобы приступать к сбору и приему данных мониторинга для Azure Monitor серверной части контейнеров.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Мониторинг обновлений

Перезапустите сценарий подключения, как описано в разделе [адаптации](#onboarding) с тем же параметром, чтобы обновить диаграмму до последней Helm.

## <a name="after-successful-onboarding"></a>После успешной адаптации

Перейдите к [гибридному мониторингу](https://aka.ms/azmon-containers-hybrid). Вы увидите, что недавно включенный кластер OPENSHIFT/АТО v4 с состоянием работоспособности находится на вкладке **наблюдаемые кластеры** . Здесь вы можете получить более подробные сведения, такие как метрики, данные инвентаризации и журналы, щелкнув столбец **кластер** .

## <a name="supported-features"></a>Поддерживаемые функции

Дополнительные сведения о поддерживаемых функциях и функциональных возможностях см. в статье [Обзор контейнера](../azure-monitor/insights/container-insights-overview.md).

Свяжитесь с нами askcoin@microsoft.com по адресу для получения отзывов и вопросов.

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о мониторинге см. в следующих статьях:
- [Обзор контейнера Insights](../azure-monitor/insights/container-insights-overview.md)

- [Общие сведения о запросе журнала](../azure-monitor/log-query/log-query-overview.md)
