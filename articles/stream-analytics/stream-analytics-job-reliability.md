---
title: Устранение прерывания работы служб в заданиях Azure Stream Analytics
description: В этой статье приводятся инструкции по обеспечению надежности заданий Stream Analytics при установке новых версий служб.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425990"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Обеспечение надежности заданий Stream Analytics во время обновления служб

Неотъемлемой частью управляемой службы является возможность быстро добавлять в нее новые функциональные возможности и улучшения. Это позволяет Stream Analytics осуществлять обновление служб еженедельно (или даже чаще). Независимо от того, сколько проведено тестов, всегда существует риск, что работа выполняющегося задания может нарушиться из-за добавления ошибки. Если вы работаете миссии критических заданий, эти риски необходимо избегать. Уменьшить этот риск можно, следуя модели **[парных регионов](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Как сопряженные регионы Azure помогают устранить эту проблему?

Stream Analytics гарантирует, что задания в сопряженных регионах обновляются в разных пакетах. В результате между обновлениями существует достаточный временной разрыв для выявления потенциальных проблем и их устранения.

_За исключением региона "Центральная Индия"_ (в сопряженном регионе которого ("Южная Индия") нет Stream Analytics), развертывание обновления в Stream Analytics не произойдет одновременно в наборе сопряженных регионов. Операции развертывания в нескольких регионах, находящихся **в одной группе**, могут происходить **одновременно**.

Самые актуальные сведения о парах регионов можно найти в статье о **[доступности и парах регионов](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)**.

Рекомендуется развертывать одинаковые задания в обоих парных регионах. Затем вы должны [контролировать эти рабочие места,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) чтобы получить уведомление, когда что-то неожиданное происходит. Если одно из этих заданий попадает в [неработание](https://docs.microsoft.com/azure/stream-analytics/job-states) после обновления службы Stream Analytics, вы можете связаться со службой поддержки клиентов, чтобы определить причину. Вы также должны потерпеть неудачу над любыми потребителями вниз по течению к здоровому выходу работы.

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое Stream Analytics?](stream-analytics-introduction.md)
* [Начало работы со Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий Stream Analytics](stream-analytics-scale-jobs.md)
* [Ссылка на язык запроса Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics management REST API reference](https://msdn.microsoft.com/library/azure/dn835031.aspx) (Справочник по API-интерфейсу REST для управления Stream Analytics)
