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
ms.openlocfilehash: a31076d17ebaf8020fe4b3f82594b98a7c3bde74
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977460"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Создание плана по обеспечению непрерывности бизнес-процессов для службы QnA Maker

Основная цель плана обеспечения непрерывности бизнес-процессов — создание отказоустойчивой конечной точки базы знаний, которая обеспечит отсутствие простоев бота или приложения, которые его используют.

![План по обеспечению непрерывности бизнес-процессов для QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

В общих чертах для воплощения представленной выше концепции нужно сделать следующее:

1. Настройте две параллельные [службы QnA Maker](../How-To/set-up-qnamaker-service-azure.md) в [сопряженных регионах Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Обеспечьте синхронизацию основного и дополнительного индексов поиска Azure. Используйте образец GitHub [здесь](https://github.com/pchoudhari/QnAMakerBackupRestore) , чтобы узнать, как выполнить резервное копирование для восстановления индексов Azure.

3. Создайте резервную копию Application Insights с помощью [непрерывного экспорта](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. После настройки первичного и вторичного стеков воспользуйтесь [диспетчером трафика](https://docs.microsoft.com/azure/traffic-manager/) для настройки двух конечных точек и метода маршрутизации.

5. Вам потребуется создать сертификат SSL (SSL) для конечной точки диспетчера трафика. [Привяжите SSL-сертификат](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) к службам приложений.

6. Наконец, используйте конечную точку диспетчера трафика в боте или приложении.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Выбор ресурсов для развертывания QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
