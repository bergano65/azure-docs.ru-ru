---
title: Центр безопасности Azure и служба Kubernetes Azure | Документация Майкрософт
description: Сведения об интеграции центра безопасности Azure с Azure Kubernetes Services
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
ms.openlocfilehash: 9bc01e0e703ea9f98d877be39011dcca7c7b284a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521765"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Интеграция Azure Kubernetes Services с центром безопасности (Предварительная версия)
Служба Azure Kubernetes Service (AKS) — это управляемая служба Майкрософт для разработки и развертывания контейнерных приложений и управления ими. 

Используйте AKS вместе с уровнем "Стандартный" центра безопасности Azure (см. [цены](security-center-pricing.md)), чтобы получить более глубокую видимость узлов AKS, облачного трафика и средств безопасности.

Центр безопасности предоставляет преимущества безопасности для кластеров AKS, используя данные, уже собранные главным узлом AKS. 

![Центр безопасности Azure и общий обзор службы Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Вместе эти два средства формируют лучшее облачное, собственное предложение по обеспечению безопасности Kubernetes. 

## <a name="benefits-of-integration"></a>Преимущества интеграции

Совместное использование этих двух служб обеспечивает следующие функции:

* **Рекомендации по безопасности** . Центр безопасности определяет ресурсы AKS и распределяет их по категориям: от кластеров к отдельным виртуальным машинам. Затем можно просмотреть рекомендации по безопасности для каждого ресурса. Дополнительные сведения см. [в разделе Реализация рекомендаций по безопасности](security-center-recommendations.md). 

    > [!NOTE]
    > Если имя рекомендации центра безопасности заканчивается тегом "(Предварительная версия)", оно ссылается на характер предварительной версии рекомендации. не является функцией.

* **Усиление защиты окружения** . Центр безопасности постоянно отслеживает конфигурацию кластеров Kubernetes и создает рекомендации по безопасности, отражающие отраслевые стандарты.

* **Защита во время выполнения** . Благодаря непрерывному анализу следующих источников AKS центр безопасности предупреждает об угрозах и вредоносных действиях, обнаруженных на уровне узла *и* кластера AKS (Дополнительные сведения см. в статье [Служба контейнеров Azure. ](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-container-service-)):
    * Необработанные события безопасности, такие как данные сети и создание процессов;
    * Журнал аудита Kubernetes

![Центр безопасности Azure и служба Azure Kubernetes (AKS) более подробно](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Некоторые данные, проверенные центром безопасности Azure из среды Kubernetes, могут содержать конфиденциальные сведения.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о функциях безопасности контейнеров центра безопасности см. в следующих статьях:

* [Центр безопасности Azure и безопасность контейнеров](container-security.md)

* [Интеграция с реестром контейнеров Azure](azure-container-registry-integration.md)

* [Защита виртуальных машин](security-center-virtual-machine-protection.md) . описание рекомендаций центра безопасности

* [Управление данными в корпорации Майкрософт](https://www.microsoft.com/trust-center/privacy/data-management) . описывает политики данных служб Майкрософт (включая Azure, Intune и Office 365), сведения об управлении данными Майкрософт и политики хранения, влияющие на данные.