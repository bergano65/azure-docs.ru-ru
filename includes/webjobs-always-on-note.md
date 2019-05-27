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
ms.openlocfilehash: 14f2e663e3db81684a73c4ea093ed0403cbb09ed
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66136204"
---
> [!NOTE]
> Время ожидания веб-приложения может истечь после 20 минут бездействия. Запросы к фактическому сайту сбрасывают таймер. Просмотр конфигурации приложения на портале Azure или выполнения запросов к сайту расширенные средства (`https://<app_name>.scm.azurewebsites.net`) не сбрасывают таймер. Если приложение выполняет непрерывные веб-задания или веб-задания по расписанию, включите **Всегда включено** для обеспечения надежного выполнения веб-заданий. Эта функция доступна только в [ценовых категориях](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) "Базовый", "Стандартный" и "Премиум".
