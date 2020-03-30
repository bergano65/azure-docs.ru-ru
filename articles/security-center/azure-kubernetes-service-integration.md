---
title: Центр безопасности Azure и служба Azure Kubernetes
description: Узнайте об интеграции Центра безопасности Azure с службами Azure Kubernetes
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d1cd4691586b27282d221a19c5fb7a1af034ed6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125165"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Интеграция служб Azure Kubernetes с Центром безопасности

Служба Azure Kubernetes (AKS) — управляемая службой Майкрософт по разработке, развертыванию и управлению контейнерными приложениями. 

Используйте AKS вместе со стандартным уровнем Azure Security Center (см. [цены),](security-center-pricing.md)чтобы получить более глубокую видимость узлов AKS, облачного трафика и элементов управления безопасностью.

Центр безопасности приносит преимущества безопасности кластерам AKS, используя данные, уже собранные мастерским узлаом AKS. 

![Обзор высокого уровня Azure Security Center и сервиса Azure Kubernetes (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Вместе эти два инструмента образуют лучшее облачное предложение безопасности Kubernetes. 

## <a name="benefits-of-integration"></a>Преимущества интеграции

Совместное использование этих двух служб обеспечивает:

* **Рекомендации по безопасности** - Центр безопасности определяет ресурсы AKS и классифицирует их: от кластеров до отдельных виртуальных машин. Затем можно просмотреть рекомендации безопасности на ресурсе. Для получения дополнительной информации, см рекомендации контейнеров в [справочном списке рекомендаций](recommendations-reference.md#recs-computeapp). 

* **Затвердевание среды** - Центр безопасности постоянно отслеживает конфигурацию кластеров Kubernetes и конфигураций Docker. Затем он генерирует рекомендации по безопасности, которые отражают отраслевые стандарты.

* **Защита от времени выполнения** - Благодаря непрерывному анализу следующих источников AKS Центр безопасности предупреждает вас об угрозах и вредоносных действиях, обнаруженных на уровне кластера хоста *и* akS:
    * Необработанные события безопасности, такие как сетевые данные и создание процессов
    * Аудиторский журнал Kubernetes

    Для получения дополнительной информации [см.](threat-protection.md#azure-containers)

    Список возможных предупреждений смотрите в таблице рекомендаций по предупреждению: [оповещения уровня кластера AKS](alerts-reference.md#alerts-akscluster) и [оповещения уровня узла контейнеров.](alerts-reference.md#alerts-containerhost)  

![Центр безопасности Azure и служба Azure Kubernetes (AKS) более подробно](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Некоторые данные, отсканированные Центром безопасности Azure из среды Kubernetes, могут содержать конфиденциальную информацию.


## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о функциях безопасности контейнеров Центра безопасности, см.:

* [Центр безопасности Azure и безопасность контейнеров](container-security.md)

* [Интеграция с Реестром контейнеров Azure](azure-container-registry-integration.md)

* [Управление данными корпорации Майкрософт](https://www.microsoft.com/trust-center/privacy/data-management) - Описывает политики данных служб Майкрософт (включая Azure, Intune и Office 365), сведения об управлении данными корпорации Майкрософт и политики хранения данных, влияющие на ваши данные