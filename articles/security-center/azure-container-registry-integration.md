---
title: Центр безопасности Azure и реестр контейнеров Azure | Документация Майкрософт
description: Сведения об интеграции центра безопасности Azure с реестром контейнеров Azure
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
ms.openlocfilehash: f0de56f968488f0e5d551ad705cc6f8ca6e7bc47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521869"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Интеграция реестра контейнеров Azure с центром безопасности (Предварительная версия)

Реестр контейнеров Azure (запись контроля доступа) — это управляемая частная служба реестра DOCKER, которая хранит образы контейнеров для развертываний Azure и управляет ими в центральном реестре. Он основан на реестре DOCKER с открытым кодом 2,0.

При использовании записи контроля доступа с уровнем "Стандартный" центра безопасности Azure (см. [цены](security-center-pricing.md)) вы получаете более глубокое представление об уязвимостях реестра и образов.

[![рекомендации по реестру контейнеров Azure (записи контроля доступа) в центре безопасности Azure](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Преимущества интеграции

Центр безопасности определяет реестры записей контроля доступа в подписке и позволяет легко предоставить следующие сведения:

* **Поиск уязвимостей** для всех отправленных образов Linux в Azure. Центр безопасности сканирует образ с помощью сканера, который является ведущим в отрасли для проверки уязвимостей поставщика, Qualys. Это собственное решение по умолчанию тесно интегрировано.

* **Рекомендации по безопасности** для образов Linux с известными уязвимостями. Центр безопасности предоставляет подробные сведения о каждой обнаруженной уязвимости и классификации серьезности. Кроме того, он предоставляет рекомендации по исправлению конкретных уязвимостей, обнаруженных в каждом образе, отправленном в реестр.

![Общий обзор центра безопасности Azure и реестра контейнеров Azure (записи контроля доступа)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о функциях безопасности контейнеров центра безопасности см. в следующих статьях:

* [Центр безопасности Azure и безопасность контейнеров](container-security.md)

* [Интеграция со службой Kubernetes Azure](azure-kubernetes-service-integration.md)

* [Защита виртуальных машин](security-center-virtual-machine-protection.md) . описание рекомендаций центра безопасности