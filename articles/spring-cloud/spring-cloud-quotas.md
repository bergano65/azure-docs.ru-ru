---
title: Планы обслуживания и квоты для Azure Веснного облака
description: Сведения о квотах служб и планах обслуживания для Azure Веснного облака
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7c4c832819f61d208d0722823d0a74354960f182
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904262"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Квоты и планы обслуживания для Azure Веснного облака

**Эта статья относится к:** ✔️ Java ✔️ C #

Все службы Azure устанавливают ограничения по умолчанию и квоты для ресурсов и компонентов.   Azure Веснного облака предлагает две ценовые категории: "базовый" и "Стандартный". Ограничения для обоих уровней будут подробно описаны в этой статье.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Уровни облачных служб Azure весны и ограничения

| Ресурс | Basic | Standard
------- | ------- | -------
vCPU | 1 на экземпляр службы | 4 на экземпляр службы
Память | 2 ГБ на экземпляр службы | 8 ГБ на экземпляр службы
Экземпляры службы Azure Spring Cloud по регионам на одну подписку | 10 | 10
Всего экземпляров приложений на экземпляр службы Azure Spring Cloud | 25 | 500
Постоянные тома | 1 ГБ/приложение x 10 приложений | 50 ГБ/приложение x 10 приложений

## <a name="next-steps"></a>Дальнейшие действия

Некоторые ограничения по умолчанию можно увеличить. Если для установки требуется увеличение, [Создайте запрос в службу поддержки](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
