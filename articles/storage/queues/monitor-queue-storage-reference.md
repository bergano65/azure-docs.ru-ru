---
title: Справочник по данным мониторинга хранилища очередей Azure | Документация Майкрософт
description: Справочник по журналам и метрикам для мониторинга данных из хранилища очередей Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 6164617c66401811715007548de59ebbf5ff253b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787488"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Справочник по данным мониторинга хранилища очередей Azure

Сведения о сборе и анализе данных мониторинга для службы хранилища Azure см. на странице [Мониторинг службы хранилища Azure](monitor-queue-storage.md).

## <a name="metrics"></a>Метрики

В следующих таблицах перечислены метрики платформы, собранные для службы хранилища Azure: 

### <a name="capacity-metrics"></a>Метрики емкости

Значения метрик емкости отправляются в Azure Monitor каждый час. Значения обновляются каждый день. Интервал времени определяет интервал, за который представлены значения метрик. Поддерживаемый интервал времени для всех метрик емкости — один час (PT1H).

Служба хранилища Azure предоставляет следующие метрики емкости в Azure Monitor.

#### <a name="account-level"></a>На уровне учетной записи

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage"></a>Хранилище очередей

В этой таблице показаны [метрики хранилища очередей](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices).

| Метрика | Описание |
| ------------------- | ----------------- |
| QueueCapacity | Объем хранилища очередей, используемый учетной записью хранения. <br/><br/> Единица измерения: Байты <br/> Тип агрегирования: Среднее <br/> Пример значения. 1024 |
| QueueCount   | Количество очередей в учетной записи хранения. <br/><br/> Единица измерения: Count <br/> Тип агрегирования: Среднее <br/> Пример значения. 1024 |
| QueueMessageCount | Приблизительное количество сообщений очередей в службе очередей учетной записи хранения. <br/><br/>Единица измерения: Count <br/> Тип агрегирования: Среднее <br/> Пример значения. 1024 |

### <a name="transaction-metrics"></a>Метрики транзакций

Метрики транзакций создаются при каждом запросе к учетной записи хранения, отправляемом из службы хранилища Azure в Azure Monitor. Если учетная запись хранения неактивна, данные на основе метрик транзакций за этот период будут отсутствовать. Все метрики транзакций доступны на уровне службы хранилища учетной записи и очереди. Интервал времени определяет интервал, за который представлены значения метрик. Поддерживаемые интервалы времени для всех метрик транзакций — PT1H и PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Измерения метрик

Служба хранилища Azure поддерживает следующие измерения метрик в Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Журналы ресурсов (предварительная версия)

> [!NOTE]
> Журналы службы хранилища Azure в Azure Monitor предоставляются в общедоступной предварительной версии. Они также доступны для предварительного тестирования во всех регионах общедоступного облака. Чтобы зарегистрироваться для использования предварительной версии, см. [эту страницу](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Эта предварительная версия включает журналы для больших двоичных объектов (в том числе Azure Data Lake Storage 2-го поколения), файлов, очередей, таблиц, учетных записей хранения категории "Премиум" общего назначения версии 1 и учетных записей хранения общего назначения версии 2. Классические учетные записи хранения не поддерживаются.

В следующей таблице перечислены свойства журналов ресурсов службы хранилища Azure при их сборе в журналах Azure Monitor или службе хранилища Azure. Свойства содержат сведения об операции, службе и типе авторизации, которые использовались для выполнения операции.

### <a name="fields-that-describe-the-operation"></a>Поля со сведениями об операции

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Поля, описывающие аутентификацию операции

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Поля со сведениями о службе

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>См. также статью

- Описание мониторинга службы хранилища Azure см. в статье [мониторинг хранилища очередей Azure](monitor-queue-storage.md) .
- Подробные сведения о мониторинге ресурсов Azure см. в статье [Мониторинг ресурсов Azure с помощью Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).