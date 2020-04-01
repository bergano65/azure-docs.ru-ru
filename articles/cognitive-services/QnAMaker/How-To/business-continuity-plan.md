---
title: План обеспечения непрерывности бизнеса — QnA Maker
titleSuffix: Azure Cognitive Services
description: Основная цель плана обеспечения непрерывности бизнес-процессов — создание отказоустойчивой конечной точки базы знаний, которая обеспечит отсутствие простоев бота или приложения, которые его используют.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 2100426923418104c31933bf9d1406a6441d3841
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410913"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Создание плана по обеспечению непрерывности бизнес-процессов для службы QnA Maker

Основная цель плана обеспечения непрерывности бизнес-процессов — создание отказоустойчивой конечной точки базы знаний, которая обеспечит отсутствие простоев бота или приложения, которые его используют.

![План по обеспечению непрерывности бизнес-процессов для QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

В общих чертах для воплощения представленной выше концепции нужно сделать следующее:

1. Настройте две параллельные [службы QnA Maker](../How-To/set-up-qnamaker-service-azure.md) в [сопряженных регионах Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Синхронизируйте основные и вторичные поисковые индексы Azure. Используйте образец GitHub [здесь,](https://github.com/pchoudhari/QnAMakerBackupRestore) чтобы узнать, как восстановить индексы Azure.

3. Создайте резервную копию Application Insights с помощью [непрерывного экспорта](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. После настройки первичного и вторичного стеков воспользуйтесь [диспетчером трафика](https://docs.microsoft.com/azure/traffic-manager/) для настройки двух конечных точек и метода маршрутизации.

5. Необходимо создать сертификат Transport Layer Security (TLS), ранее известный как Безопасный слой розеток (SSL), сертификат для конечных точек диспетчера трафика. [Свяжите сертификат TLS/SSL](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) в службах приложения.

6. Наконец, используйте конечную точку диспетчера трафика в боте или приложении.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Выберите capactiy](./improve-knowledge-base.md)
