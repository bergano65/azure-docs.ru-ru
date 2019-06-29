---
title: План обеспечения непрерывности бизнеса — QnA Maker
titleSuffix: Azure Cognitive Services
description: Основная цель плана обеспечения непрерывности бизнес-процессов — создание отказоустойчивой конечной точки базы знаний, которая обеспечит отсутствие простоев бота или приложения, которые его используют.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: f9892acb387a655e173ee5d2bde28e7346a6c535
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447539"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Создание плана по обеспечению непрерывности бизнес-процессов для службы QnA Maker

Основная цель плана обеспечения непрерывности бизнес-процессов — создание отказоустойчивой конечной точки базы знаний, которая обеспечит отсутствие простоев бота или приложения, которые его используют.

![План по обеспечению непрерывности бизнес-процессов для QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

В общих чертах для воплощения представленной выше концепции нужно сделать следующее:

1. Настройте две параллельные [службы QnA Maker](../How-To/set-up-qnamaker-service-azure.md) в [сопряженных регионах Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Поддерживайте синхронизацию основного и дополнительного индексов поиска Azure. Просмотрите приведенный [здесь](https://github.com/pchoudhari/QnAMakerBackupRestore) пример на GitHub, чтобы узнать, как создавать резервные копии индексов Azure и восстанавливать их.

3. Создайте резервную копию Application Insights с помощью [непрерывного экспорта](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. После настройки первичного и вторичного стеков воспользуйтесь [диспетчером трафика](https://docs.microsoft.com/azure/traffic-manager/) для настройки двух конечных точек и метода маршрутизации.

5. Для конечной точки диспетчера трафика потребуется создать SSL-сертификат. [Привяжите SSL-сертификат](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) к службам приложений.

6. Наконец, используйте конечную точку диспетчера трафика в боте или приложении.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Выбор ресурсов для развертывания QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
