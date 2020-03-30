---
title: включить файл
description: включить файл
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68424169"
---
> [!NOTE]
> Время ожидания веб-приложения может истечь после 20 минут бездействия. Запросы к фактическому сайту сбрасывают таймер. Просмотр конфигурации приложения на портале Azure или запросы`https://<app_name>.scm.azurewebsites.net`на сайт передовых инструментов () не сбросить таймер. Если ваше приложение работает непрерывно или запланировано (триггер Timer) WebJobs, включите **Always On,** чтобы убедиться, что WebJobs работает надежно. Эта функция доступна только в [ценовых категориях](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) "Базовый", "Стандартный" и "Премиум".
