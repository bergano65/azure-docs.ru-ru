---
title: План обеспечения непрерывности бизнеса — QnA Maker
description: Основная цель плана обеспечения непрерывности бизнес-процессов — создание отказоустойчивой конечной точки базы знаний, которая обеспечит отсутствие простоев бота или приложения, которые его используют.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887080"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Создание плана по обеспечению непрерывности бизнес-процессов для службы QnA Maker

Основная цель плана обеспечения непрерывности бизнес-процессов — создание отказоустойчивой конечной точки базы знаний, которая обеспечит отсутствие простоев бота или приложения, которые его используют.

## <a name="business-continuity-with-traffic-manager"></a>Преемственность бизнеса с менеджером по трафику

> [!div class="mx-imgBorder"]
> ![План по обеспечению непрерывности бизнес-процессов для QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

В общих чертах для воплощения представленной выше концепции нужно сделать следующее:

1. Настройте две параллельные [службы QnA Maker](set-up-qnamaker-service-azure.md) в [сопряженных регионах Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Резервное копирование](../../../app-service/manage-backup.md) основного сервиса app SnA Maker и [восстановление](../../../app-service/web-sites-restore.md) его во вторичной настройке. Это гарантирует, что обе установки работают с одинаковым именем и ключами.

3. Синхронизируйте основные и вторичные поисковые индексы Azure. Используйте образец GitHub [здесь,](https://github.com/pchoudhari/QnAMakerBackupRestore) чтобы узнать, как восстановить индексы Azure.

4. Создайте резервную копию Application Insights с помощью [непрерывного экспорта](../../../application-insights/app-insights-export-telemetry.md).

5. После настройки первичного и вторичного стеков воспользуйтесь [диспетчером трафика](../../../traffic-manager/traffic-manager-overview.md) для настройки двух конечных точек и метода маршрутизации.

6. Необходимо создать сертификат Transport Layer Security (TLS), ранее известный как Безопасный слой розеток (SSL), сертификат для конечных точек диспетчера трафика. [Свяжите сертификат TLS/SSL](../../../app-service/configure-ssl-bindings.md) в службах приложения.

7. Наконец, используйте конечную точку диспетчера трафика в боте или приложении.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Выбор емкости](./improve-knowledge-base.md)