---
title: Plan your Preview environment - Azure Time Series Insights | Microsoft Docs
description: Learn how to plan your Azure Time Series Insights Preview environment.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: 9fb2dcf2c05b709340f8e9ae549bab5756e6abf2
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420307"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Запланируйте среду службы"Аналитика временных рядов Azure (предварительная версия)"

This article describes best practices to plan and get started quickly by using Azure Time Series Insights Preview.

> [!NOTE]
> For best practices to plan a general availability Time Series Insights instance, see [Plan your Azure Time Series Insights general availability environment](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Рекомендации для планирования и подготовки

Best practices surrounding planning for and preparing your environment are described further in the following articles:

* What you get when you [provision a Time Series Insights Preview environment](#the-preview-environment).
* What your [Time Series IDs and Timestamp properties are](#configure-time-series-ids-and-timestamp-properties).
* What the new [Time Series Model is](#understand-the-time-series-model), and how to build your own.
* How to [send events efficiently in JSON](#shape-your-events).
* Time Series Insights [business disaster recovery options](#business-disaster-recovery).

Azure Time Series Insights employs a pay-as-you-go business model. Дополнительные сведения о расходах и производительности см. в статье [Цены на Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>The preview environment

При подготовке среды "Аналитика временных рядов (предварительная версия)" создаются два ресурса Azure:

* Среда "Аналитика временных рядов Azure (предварительная версия)"
* Учетная запись службы хранилища Azure общего назначения версии 1

As part of the provisioning process, you specify whether you want to enable a warm store. Warm store provides you with a tiered query experience. When enabled, you must specify a retention period between 7 and 30 days. Queries executed within the warm store retention period generally provide faster response times. When a query spans over the warm store retention period, it's served from cold store.

Queries on warm store are free, while queries on cold store incur costs. It's important to understand your query patterns and plan your warm store configuration accordingly. We recommend that interactive analytics on the most recent data reside in your warm store and pattern analysis and long-term trends reside in cold.

> [!NOTE]
> We currently support a maximum of 1,000 properties with warm store.

Для начала работы вам понадобятся три дополнительных элемента:

* [Модель временных рядов](./time-series-insights-update-tsm.md)
* [Источник событий, подключенный к службе "Аналитика временных рядов Azure"](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [События, поступающие в источник событий ](./time-series-insights-send-events.md), которые сопоставлены с моделью и имеют допустимый формат JSON

## <a name="review-preview-limits"></a>Review preview limits

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configure Time Series IDs and Timestamp properties

Чтобы создать новую среду службы "Аналитика временных рядов", выберите идентификатор временных рядов. Это действует как логический раздел для ваших данных. Как уже говорилось, убедитесь в том, что у вас есть готовые идентификаторы временных рядов.

> [!IMPORTANT]
> Time Series IDs *can't be changed later*. Перед окончательным выбором и первым использованием проверьте каждый их них.

You can select up to three keys to uniquely differentiate your resources. Дополнительные сведения см. в статьях [Best practices for choosing a Time Series ID](./time-series-insights-update-how-to-id.md) (Рекомендации при выборе идентификатора временного ряда) и [Storage and ingress](./time-series-insights-update-storage-ingress.md) (Хранение и доступ к данным в службе "Аналитика временных рядов Azure (предварительная версия)").

The **Timestamp** property is also important. Вы можете назначить это свойство при добавлении источников событий. Каждый источник событий имеет дополнительное свойство метки времени, которое используется для отслеживания источников событий с течением времени. Значения метки времени учитывают регистр и должны быть отформатированы в соответствии с индивидуальной спецификацией каждого источника события.

> [!TIP]
> Проверьте требования к форматированию и анализу для ваших источников событий.

Если оставить значение пустым, в качестве метки времени события будет использоваться время постановки события в очередь в пределах источника событий. Если вы отправляете данные истории или пакетные события, настройка свойства метки времени более полезна, чем время постановки события в очередь по умолчанию. For more information, read about how to [add event sources in Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Понимание модели временного ряда

Теперь вы можете настроить модель временных рядов среды службы "Аналитика временных рядов Azure". Новая модель позволяет легко находить и анализировать данные Центра Интернета вещей. Это позволяет осуществлять обработку, обслуживание и обогащение данных временных рядов и помогает подготовить наборы данных готовые для потребителей. The model uses Time Series IDs, which map to an instance that associates the unique resource with variables, known as types, and hierarchies. Прочтите о новой [модели временных рядов](./time-series-insights-update-tsm.md).

Модель является динамичной, поэтому ее можно построить в любое время. Чтобы быстро приступить к работе, создайте и загрузите ее до загрузки данных в службу "Аналитика временных рядов Azure". Чтобы построить модель, см. статью [Use the Time Series Model](./time-series-insights-update-how-to-tsm.md) (Использование модели временных рядов).

Для многих клиентов модель временных рядов сопоставляется с уже существующей моделью активов или системой ERP. Если у вас нет существующей модели, [предоставляется](https://github.com/Microsoft/tsiclient) предварительно созданный пользовательский интерфейс для быстрого запуска и выполнения. Чтобы представить, как модель может вам помочь, посмотрите [демонстрационный пример среды](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Формирование ваших событий

Вы можете проверить способ отправки событий в службу "Аналитика временных рядов Azure". В идеале, ваши мероприятия денормализуются хорошо и эффективно.

Хорошее правило:

* Store metadata in your Time Series Model.
* Ensure that Time Series Mode, instance fields, and events include only necessary information, such as a Time Series ID or Timestamp property.

Дополнительные сведения см. в статье [Отправка событий в среду Time Series Insights через концентратор событий](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Дальнейшие действия

- Review [Azure Advisor](../advisor/advisor-overview.md) to plan out your business recovery configuration options.
- Read more about [storage and ingress](./time-series-insights-update-storage-ingress.md) in the Time Series Insights Preview.
- Learn about [data modeling](./time-series-insights-update-tsm.md) in the Time Series Insights Preview.
