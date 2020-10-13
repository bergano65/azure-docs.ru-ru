---
title: включить файл
description: включить файл
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009803"
---
> [!NOTE]
> Время ожидания веб-приложения может истечь после 20 минут бездействия. Сбросить таймер можно только с помощью запросов к фактическому веб-приложению. Таймер не будет сброшен при просмотре конфигурации приложения на портале Azure или выполнении запросов к сайту расширенных средств (`https://<app_name>.scm.azurewebsites.net`). Если веб-приложение настроено для выполнения непрерывных или запланированных заданий (триггер таймера), установите параметр **Всегда включено** в Azure на странице **Конфигурация** веб-приложения, чтобы обеспечить надежное выполнение веб-заданий. Эта функция доступна только в [ценовых категориях](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) "Базовый", "Стандартный" и "Премиум".
