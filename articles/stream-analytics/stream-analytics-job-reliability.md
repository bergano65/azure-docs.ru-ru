---
title: Устранение прерывания работы служб в заданиях Azure Stream Analytics
description: В этой статье приводятся инструкции по обеспечению надежности заданий Stream Analytics при установке новых версий служб.
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 672706c97a423819dd26941e0b6e22affa9c2bb8
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329834"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Обеспечение надежности заданий Stream Analytics во время обновления служб

Неотъемлемой частью управляемой службы является возможность быстро добавлять в нее новые функциональные возможности и улучшения. Это позволяет Stream Analytics осуществлять обновление служб еженедельно (или даже чаще). Независимо от того, сколько проведено тестов, всегда существует риск, что работа выполняющегося задания может нарушиться из-за добавления ошибки. При выполнении критически важных заданий, этих рисков необходимо избегать. Можно уменьшить этот риск путем следующих Azure **[парный регион](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** модели. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Как сопряженные регионы Azure помогают устранить эту проблему?

Stream Analytics гарантирует, что задания в сопряженных регионах обновляются в разных пакетах. В результате имеется достаточно временной промежуток между обновлениями, чтобы выявить потенциальные проблемы и устранить их.

_За исключением региона "Центральная Индия"_ (в сопряженном регионе которого ("Южная Индия") нет Stream Analytics), развертывание обновления в Stream Analytics не произойдет одновременно в наборе сопряженных регионов. Операции развертывания в нескольких регионах, находящихся **в одной группе**, могут происходить **одновременно**.

Самые актуальные сведения о парах регионов можно найти в статье о **[доступности и парах регионов](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** .

Рекомендуется развертывать идентичные задания на обоих сопряженных регионах. Затем следует [отслеживать эти задания](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) для получения уведомлений, когда происходит что-то непредвиденное. Если один из следующих заданий заканчивается вверх в [в состоянии сбоя](https://docs.microsoft.com/azure/stream-analytics/job-states) после обновления службы Stream Analytics, вы можете обратитесь в службу поддержки для выявления основной причины. Вы также должны отрабатывать отказ всех нисходящих потребителей выходные данные работоспособного задания.

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое Stream Analytics?](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий Azure Stream Analytics для повышения пропускной способности базы данных](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Справочник по языку запросов Stream Analytics)
* [Stream Analytics management REST API reference](https://msdn.microsoft.com/library/azure/dn835031.aspx) (Справочник по API-интерфейсу REST для управления Stream Analytics)
