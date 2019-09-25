---
title: Журналы и метрики в Azure | Документация Майкрософт
description: Общие сведения о журналах диагностики в Azure, которые предоставляют обширные, часто встречающиеся данные о работе ресурсов Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262366"
---
# <a name="logs-and-metrics-in-azure"></a>Журналы и метрики в Azure
Существуют различные [журналы](data-platform-logs.md) и [метрики](data-platform-metrics.md)

Мониторинг приложений и служб в Azure можно разделить на данные, которые хранятся на [платформе данных Azure](data-platform.md). 

Журналы и метрики можно разделить на две категории.

- — Журналы и метрики платформы, которые автоматически создаются без какой-либо необходимой конфигурации, кроме простого 



| Слой | Журналы платформы | Метрики платформы | Особые журналы | Пользовательские метрики |
|:---|:---|:---|:---|:---|
| Приложение  | | | | |
| Гостевая ОС     | Пульс |  | Расширение системы диагностики<br>Агент Log Analytics | Расширение системы диагностики |
| Resource     | [Журналы ресурсов](resource-logs-overview.md)<br>(конкретно для каждой службы) | [Метрики ресурсов](metrics-supported.md)<br>(конкретно для каждой службы) | | [Пользовательские метрики](metrics-custom-overview.md) |
| Подписка | [Журнал действий](activity-logs-overview.md) | | | |
| Клиент       | 

## <a name="next-steps"></a>Следующие шаги

* [Потоковая передача журналов диагностики Azure в **Центры событий**](resource-logs-stream-event-hubs.md)
* [Создание или обновление диагностического параметра](https://docs.microsoft.com/rest/api/monitor/)
* [Анализ журналов из службы хранилища Azure с помощью Azure Monitor](collect-azure-metrics-logs.md)
