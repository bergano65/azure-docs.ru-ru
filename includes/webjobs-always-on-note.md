---
title: включение файла
description: включение файла
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424169"
---
> [!NOTE]
> Время ожидания веб-приложения может истечь после 20 минут бездействия. Запросы к фактическому сайту сбрасывают таймер. Просмотр конфигурации приложения в портал Azure или выполнение запросов к сайту "дополнительные инструменты" (`https://<app_name>.scm.azurewebsites.net`) не приводит к сбросу таймера. Если приложение запускает непрерывное или запланированное (триггер таймера) веб-задания, включите **Always on** , чтобы убедиться в надежном запуске веб-заданий. Эта функция доступна только в [ценовых категориях](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) "Базовый", "Стандартный" и "Премиум".
