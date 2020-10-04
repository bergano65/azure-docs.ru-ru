---
title: Справочник по данным мониторинга хранилища таблиц Azure | Документация Майкрософт
description: Справочник по журналам и метрикам для мониторинга данных из хранилища таблиц Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: e86be87faf6bb0c58674a15882e52dcef43fe42e
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711524"
---
# <a name="azure-table-storage-monitoring-data-reference"></a>Справочник по данным мониторинга хранилища таблиц Azure

Сведения о сборе и анализе данных мониторинга для службы хранилища Azure см. на странице [Мониторинг службы хранилища Azure](monitor-table-storage.md).

## <a name="metrics"></a>Метрики

В следующих таблицах перечислены метрики платформы, собранные для службы хранилища Azure: 

### <a name="capacity-metrics"></a>Метрики емкости

Значения метрик емкости отправляются в Azure Monitor каждый час. Значения обновляются каждый день. Интервал времени определяет интервал, за который представлены значения метрик. Поддерживаемый интервал времени для всех метрик емкости — один час (PT1H).

Служба хранилища Azure предоставляет следующие метрики емкости в Azure Monitor.

#### <a name="account-level"></a>На уровне учетной записи

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="table-storage"></a>Хранилище таблиц

В этой таблице показаны [метрики хранилища таблиц](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountstableservices).

| Метрика | Описание |
| ------------------- | ----------------- |
| TableCapacity | Объем хранилища таблиц, используемый учетной записью хранения. <br/><br/> Единица измерения: Байты <br/> Тип агрегирования: Среднее <br/> Пример значения. 1024 |
| TableCount   | Количество таблиц в учетной записи хранения. <br/><br/> Единица измерения: Count <br/> Тип агрегирования: Среднее <br/> Пример значения. 1024 |
| TableEntityCount | Количество сущностей таблицы в учетной записи хранения. <br/><br/> Единица измерения: Count <br/> Тип агрегирования: Среднее <br/> Пример значения. 1024 |

### <a name="transaction-metrics"></a>Метрики транзакций

Метрики транзакций создаются при каждом запросе к учетной записи хранения, отправляемом из службы хранилища Azure в Azure Monitor. Если учетная запись хранения неактивна, данные на основе метрик транзакций за этот период будут отсутствовать. Все метрики транзакций доступны на уровне службы хранилища учетных записей и таблиц. Интервал времени определяет интервал, за который представлены значения метрик. Поддерживаемые интервалы времени для всех метрик транзакций — PT1H и PT1M.

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

## <a name="see-also"></a>См. также раздел

- Описание мониторинга службы хранилища Azure см. в статье [мониторинг табличного хранилища Azure](monitor-table-storage.md) .
- Подробные сведения о мониторинге ресурсов Azure см. в статье [Мониторинг ресурсов Azure с помощью Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).