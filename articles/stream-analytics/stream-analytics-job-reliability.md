---
title: Устранение прерывания работы служб в заданиях Azure Stream Analytics
description: В этой статье приводятся инструкции по обеспечению надежности заданий Stream Analytics при установке новых версий служб.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 8d62f87a6bf33bbbd85b68c9d2e0c6f4a93adf16
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044436"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Обеспечение надежности заданий Stream Analytics во время обновления служб

Неотъемлемой частью управляемой службы является возможность быстро добавлять в нее новые функциональные возможности и улучшения. Это позволяет Stream Analytics осуществлять обновление служб еженедельно (или даже чаще). Независимо от того, сколько проведено тестов, всегда существует риск, что работа выполняющегося задания может нарушиться из-за добавления ошибки. Если вы используете критически важные задания, эти риски следует избегать. Этот риск можно снизить, выполнив модель **[парного региона](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Как сопряженные регионы Azure помогают устранить эту проблему?

Stream Analytics гарантирует, что задания в сопряженных регионах обновляются в разных пакетах. В результате существует достаточный промежуток времени между обновлениями для выявления потенциальных проблем и их исправления.

_За исключением региона "Центральная Индия"_ (в сопряженном регионе которого ("Южная Индия") нет Stream Analytics), развертывание обновления в Stream Analytics не произойдет одновременно в наборе сопряженных регионов. Операции развертывания в нескольких регионах, находящихся **в одной группе**, могут происходить **одновременно**.

Самые актуальные сведения о парах регионов можно найти в статье о **[доступности и парах регионов](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)**.

Рекомендуется развертывать одинаковые задания в обоих парных регионах. Затем следует [отслеживать эти задания](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) , чтобы получать уведомления о непредвиденных последствиях. Если одно из этих заданий завершается в [состоянии сбоя](https://docs.microsoft.com/azure/stream-analytics/job-states) после обновления Stream Analytics службы, можно обратиться в службу поддержки пользователей, чтобы помочь определить основную причину. Также следует выполнить отработку отказа всех нижестоящих потребителей в работоспособные выходные данные задания.

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое Stream Analytics?](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий Azure Stream Analytics для повышения пропускной способности базы данных](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Справочник по языку запросов Stream Analytics)
* [Stream Analytics management REST API reference](https://msdn.microsoft.com/library/azure/dn835031.aspx) (Справочник по API-интерфейсу REST для управления Stream Analytics)
