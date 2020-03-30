---
title: Интеграция Azure Monitor для Azure Red Hat OpenShift 4.3
description: Узнайте, как включить Azure Monitor в кластере Microsoft Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082852"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Интеграция Azure Monitor для Azure Red Hat OpenShift 4.3

> [!IMPORTANT] 
> Обратите внимание, что Azure Red Hat OpenShift 4.3 в настоящее время доступна только в частном предварительном просмотре в Восточной ЧАСТИ США. Частное предварительное принятие только по приглашению. Пожалуйста, не забудьте зарегистрировать подписку, прежде чем пытаться включить эту функцию: [Azure Red Hat OpenShift Частная регистрация предварительного просмотра](https://aka.ms/aro-preview-register)

> [!NOTE]
> Функции предварительного просмотра являются самообслуживанием и предоставляются как есть и по мере возможности и исключены из соглашения об уровне обслуживания (SLA) и ограниченной гарантии. Таким образом, функции не предназначены для использования в производстве.

В этой статье описывается, как включить приватный предварительный просмотр Azure Monitor для контейнеров для кластеров OpenShift 4.3, размещенных на прем или в любой облачной среде. Те же инструкции применяются и для обеспечения мониторинга для кластеров Azure Red Hat OpenShift (ARO) 4.3.  

## <a name="prerequisites"></a>Предварительные требования

- [Лазурный CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Шлем 3](https://helm.sh/docs/intro/install/)
- Доступ к кубеконфигу кластера кубернете
- Доступ к подписке Azure
- Доступ к кластеру OpenShift 4.3 для установки диаграммы Azure Monitor для контейнеров Helm
- Минимальное разрешение роли участника RBAC на подписке Azure  
- Агент мониторинга требует, чтобы следующие исходящие порты и домены отправляли данные мониторинга в бэкэнд Azure Monitor (если заблокирован прокси/брандмауэром):
  - No.ods.opinsights.azure.com 443
  - No.oms.opinsights.azure.com 443
  - No.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Переход

> [!TIP]
> Скрипт использует Bash 4 функции, поэтому убедитесь, что ваш Баш в актуальном состоянии. Вы можете проверить текущую версию с `bash --version`.

### <a name="download-the-onboarding-script"></a>Скачать сценарий посадки

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Выполните следующий скрипт с azureSubscriptionId, областью рабочего пространства, clusterName и контекстом кластера Kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Пример:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Сбор данных агента для настройки

По умолчанию агент мониторинга собирает журналы контейнеров «stdout; stderr» всех контейнеров, работающих во всех областях имен, кроме kube-системы.  Если вы хотите настроить коллекцию журналов контейнеров, специфичную для конкретного пространства имен или пространства имен, вы можете обратиться к [конфигурации агента Container Insights.](../azure-monitor/insights/container-insights-agent-config.md) Здесь можно настроить агент мониторинга с желаемыми настройками сбора данных с помощью карты конфигурации.

## <a name="configure-scraping-of-prometheus-metrics"></a>Настройка соскабливания метрик Прометея

Azure Monitor для контейнеров скребает метрики Prometheus и глотает в бэкэндazе. Обратитесь к [конфигурации Container Insights Prometheus](../azure-monitor/insights/container-insights-prometheus-integration.md) для инструкций по настройке соскабливания Prometheus.

После успешного посадки, перейдите к [гибридному мониторингу](https://aka.ms/azmon-containers-hybrid) и выберите окружающую среду как **"Все",** чтобы просмотреть ваш недавно на борту OpenShift v4 кластера.

## <a name="disable-monitoring"></a>Отключение мониторинга

Если вы хотите отключить мониторинг, можно удалить диаграмму Azure Monitor для контейнеров Helm, используя следующую команду, чтобы прекратить сбор и глотание данных мониторинга в Azure Monitor для бэкэнда контейнеров.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Мониторинг обновления

Повторное запуск сценария на борту, как описано в разделе [Onboarding](#onboarding) с тем же параметром для обновления до последней диаграммы Helm.

## <a name="after-successful-onboarding"></a>После успешного посадки на борт

Перейдите к [гибридному мониторингу,](https://aka.ms/azmon-containers-hybrid)и вы сможете увидеть недавно включенный кластер OpenShift/ARO v4 со состоянием здоровья во вкладке **Monitored Clusters.** Там вы можете получить более глубокие сведения, такие как метрики, запасы и журналы, нажав на столбец **кластера.**

## <a name="supported-features"></a>Поддерживаемые функции

Для получения дополнительной информации об поддерживаемых функциях и функциональности [см.](../azure-monitor/insights/container-insights-overview.md)

Свяжитесь askcoin@microsoft.com с нами для получения обратной связи и вопросов.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о мониторинге, см.:
- [Обзор контейнерных анализов](../azure-monitor/insights/container-insights-overview.md)

- [Обзор регистрационного запроса](../azure-monitor/log-query/log-query-overview.md)
