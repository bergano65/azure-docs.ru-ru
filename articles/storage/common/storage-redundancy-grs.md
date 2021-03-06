---
title: Геоизбыточное хранилище (GRS) для обеспечения надежности в разных регионах
titleSuffix: Azure Storage
description: Геоизбыточное хранилище (GRS) производит репликацию данных между двумя регионами, которые разделены сотнями километров. GRS обеспечивает защиту от сбоев оборудования в центре обработки данных, а также защиту от региональных сбоев.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 6bb93c3fb6599a05978e11ef5fbc179ccfaa9ec2
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614890"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Геоизбыточное хранилище (GRS). Межрегиональная репликация для службы хранилища Azure

[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Геоизбыточное хранилище с доступом для чтения

Геоизбыточное хранилище с доступом для чтения (RA-GRS) максимально увеличивает доступность учетной записи хранения. Оно предоставляет доступ только для чтения к данным во вторичном местоположении в дополнение к георепликации между двумя регионами.

При включении доступа только для чтения для данных во вторичном регионе ваши данные будут доступны как в первичной конечной точке, так и во вторичной конечной точке учетной записи хранения. Вторичная конечная точка аналогична первичной конечной точке, но к имени учетной записи добавляется суффикс `–secondary` . Например, если первичная конечная точка для службы BLOB-объектов — `myaccount.blob.core.windows.net`, то вторичная конечная точка — `myaccount-secondary.blob.core.windows.net`. Ключи доступа для учетной записи хранения одинаковые как для первичной, так и для вторичной конечных точек.

Рекомендации при работе с RA-GRS:

- При использовании RA-GRS для приложения необходимо указать, с какой конечной точкой взаимодействовать.
- Так как асинхронная репликация подразумевает задержку, изменения, еще не реплицированные в дополнительный регион, могут быть потеряны, если данные будет невозможно восстановить из основного региона.
- Вы можете проверить свойство **время последней синхронизации** для вашей учетной записи хранения. **Время последней синхронизации** — это значение даты и времени по Гринвичу. Все основные операции записи, выполненные до **последней синхронизации** , были успешно записаны в дополнительное расположение. Это означает, что они доступны для чтения из дополнительного расположения. Первичная запись после **последней синхронизации** может быть недоступна для чтения. Это значение можно запросить с помощью PowerShell, Azure CLI или одной из клиентских библиотек службы хранилища Azure. Дополнительные сведения см. в статье **Получение времени последней синхронизации** при [разработке высокодоступных приложений с помощью геоизбыточного хранилища с доступом для чтения](storage-designing-ha-apps-with-ragrs.md#getting-the-last-sync-time).
- Если запустить отработку отказа учетной записи (предварительная версия) GRS или RA-GRS в дополнительный регион, доступ на запись к этой учетной записи восстанавливается после завершения отработки отказа. Дополнительные сведения см. в статье [Что делать в случае простоя службы хранилища Azure](storage-disaster-recovery-guidance.md).
- RA-GRS предназначено для обеспечения высокой доступности. Дополнительные сведения по масштабируемости см. в статье [Производительность хранилища Microsoft Azure и контрольный список масштабируемости](storage-performance-checklist.md).
- Рекомендации по обеспечению высокого уровня доступности с RA-GRS см. в статье [Проектирование высокодоступных приложений с использованием RA-GRS](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Что такое RPO и RTO для GRS?

**Целевая точка восстановления (RPO).** В хранилищах GRS и RA-GRS служба хранилища асинхронно геореплицирует данные из основного расположения в дополнительное. В случае, если основной регион становится недоступным, вы можете выполнить отработку отказа учетной записи (предварительная версия) в дополнительный регион. При запуске отработки отказа последние изменения, которые не были геореплицированы, могут быть утеряны. Число минут утерянных потенциальных данных называется RPO. RPO указывает момент времени, до которого данные могут быть восстановлены. Обычно RPO в службе хранилища Azure не превышает 15 минут, хотя на данный момент нет какого-либо соглашения об уровне обслуживания, регулирующего длительность георепликации.

**Цель времени восстановления (RTO).** RTO — это время, необходимое для выполнения отработки отказа и возобновления работы учетной записи хранения. Время, затрачиваемое на отработку отказа, обусловлено следующими действиями:

- Время, пока клиент не инициирует отработку отказа учетной записи хранения из основного в дополнительный регион.
- Время, необходимое Azure на выполнение отработки отказа путем изменения основных DNS-записей для указания на дополнительное расположение.

## <a name="paired-regions"></a>Пары регионов

При создании учетной записи хранения вы выбираете первичный регион для учетной записи. Парный дополнительный регион определяется на основе основного региона, его невозможно изменить. Обновленную информацию о регионах, поддерживаемых Azure, см. в статье [Непрерывность бизнес-процессов и аварийное восстановление в службах BizTalk: пары регионов Azure](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>См. также

- [Репликация службы хранилища Azure](storage-redundancy.md)
- [Локально избыточное хранилище (LRS). Недорогая избыточность данных для службы хранилища Azure](storage-redundancy-lrs.md)
- [Хранилище, избыточное между зонами (ZRS). Высокодоступные приложения для службы хранилища Azure](storage-redundancy-zrs.md)
