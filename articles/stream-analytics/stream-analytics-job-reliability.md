---
title: Устранение прерывания работы служб в заданиях Azure Stream Analytics
description: В этой статье приводятся инструкции по обеспечению надежности заданий Stream Analytics при установке новых версий служб.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7375fb2763ad83e049b1ef30a623f164e059a792
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479462"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Обеспечение надежности заданий Stream Analytics во время обновления служб

Неотъемлемой частью управляемой службы является возможность быстро добавлять в нее новые функциональные возможности и улучшения. Это позволяет Stream Analytics осуществлять обновление служб еженедельно (или даже чаще). Независимо от того, сколько проведено тестов, всегда существует риск, что работа выполняющегося задания может нарушиться из-за добавления ошибки. Клиентам, выполняющим критически важные задания потоковой обработки, этих рисков необходимо избегать. Механизм, с помощью которого клиенты могут уменьшить этот риск, —модель **[сопряженного региона](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Как сопряженные регионы Azure помогают устранить эту проблему?

Stream Analytics гарантирует, что задания в сопряженных регионах обновляются в разных пакетах. Поэтому промежуток времени между обновлениями достаточен, чтобы выявить возможные критические ошибки и устранить их.

_За исключением региона "Центральная Индия"_ (в сопряженном регионе которого ("Южная Индия") нет Stream Analytics), развертывание обновления в Stream Analytics не произойдет одновременно в наборе сопряженных регионов. Операции развертывания в нескольких регионах, находящихся **в одной группе**, могут происходить **одновременно**.

Самые актуальные сведения о парах регионов можно найти в статье о **[доступности и парах регионов](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** .

Пользователям рекомендуется развертывать идентичные задания на обоих сопряженных регионах. Помимо внутреннего мониторинга Stream Analytics рекомендуется отслеживать **оба** эти задания как задания в рабочей среде. Если выявленное нарушение работы возникло в результате обновления службы Stream Analytics, передайте эту информацию ответственному персоналу и отработайте отказ всех нисходящих потребителей на выходные данные работоспособного задания. Передача этой информации в службу поддержки позволит предотвратить влияние нового развертывания на сопряженный регион и сохранить целостность сопряженных заданий.

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое Stream Analytics?](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий Azure Stream Analytics для повышения пропускной способности базы данных](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Справочник по языку запросов Stream Analytics)
* [Stream Analytics management REST API reference](https://msdn.microsoft.com/library/azure/dn835031.aspx) (Справочник по API-интерфейсу REST для управления Stream Analytics)
