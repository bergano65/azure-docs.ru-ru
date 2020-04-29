---
title: План обеспечения непрерывности бизнеса — QnA Maker
description: Основная цель плана обеспечения непрерывности бизнес-процессов — создание отказоустойчивой конечной точки базы знаний, которая обеспечит отсутствие простоев бота или приложения, которые его используют.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80887080"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Создание плана по обеспечению непрерывности бизнес-процессов для службы QnA Maker

Основная цель плана обеспечения непрерывности бизнес-процессов — создание отказоустойчивой конечной точки базы знаний, которая обеспечит отсутствие простоев бота или приложения, которые его используют.

## <a name="business-continuity-with-traffic-manager"></a>Непрерывность бизнес-процессов с помощью диспетчера трафика

> [!div class="mx-imgBorder"]
> ![План по обеспечению непрерывности бизнес-процессов для QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

В общих чертах для воплощения представленной выше концепции нужно сделать следующее:

1. Настройте две параллельные [службы QnA Maker](set-up-qnamaker-service-azure.md) в [сопряженных регионах Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Создайте резервную копию](../../../app-service/manage-backup.md) основной службы приложений QnA Maker и [восстановите](../../../app-service/web-sites-restore.md) ее во вторичной установке. Это обеспечит работу обеих настроек с одним и тем же именем узла и ключами.

3. Обеспечьте синхронизацию основного и дополнительного индексов поиска Azure. Используйте образец GitHub [здесь](https://github.com/pchoudhari/QnAMakerBackupRestore) , чтобы узнать, как выполнить резервное копирование для восстановления индексов Azure.

4. Создайте резервную копию Application Insights с помощью [непрерывного экспорта](../../../application-insights/app-insights-export-telemetry.md).

5. После настройки первичного и вторичного стеков воспользуйтесь [диспетчером трафика](../../../traffic-manager/traffic-manager-overview.md) для настройки двух конечных точек и метода маршрутизации.

6. Вам потребуется создать протокол TLS, ранее известный как SSL (SSL), сертификат для конечной точки диспетчера трафика. [Привяжите сертификат TLS/SSL](../../../app-service/configure-ssl-bindings.md) в службах приложений.

7. Наконец, используйте конечную точку диспетчера трафика в боте или приложении.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Выбор емкости](./improve-knowledge-base.md)