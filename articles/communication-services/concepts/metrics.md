---
title: Определения метрик для Служб коммуникации Azure
titleSuffix: An Azure Communication Services concept document
description: В этом документе описываются определения метрик, доступных на портале Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 05/19/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 25c7016f6639df46a9279ef9a9aab2736efd4f95
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888663"
---
# <a name="metrics-overview"></a>Общие сведения о метриках

Службы коммуникации Azure в настоящее время предоставляют метрики для чата и SMS. [Обозреватель метрик Azure](../../azure-monitor/platform/metrics-getting-started.md) можно использовать для построения собственных графиков, выявления нестандартных значений метрик и анализа трафика API с помощью данных метрик, порождаемых запросами чатов и SMS.

## <a name="where-to-find-metrics"></a>Где найти метрики

Службы чата и SMS в Службах коммуникации Azure выдают метрики для запросов API. Эти метрики можно найти в колонке "Метрики" в ресурсе Служб коммуникации. Вы также можете создавать постоянные панели мониторинга, используя колонку книги в ресурсе Служб коммуникации.

## <a name="metric-definitions"></a>Определения метрик

Существует два типа запросов в метриках Служб коммуникации: **Запросы API чата** и **запросы API SMS**.

Метрики запросов API чата и SMS содержат три измерения, которые можно использовать для фильтрации данных метрик. Эти измерения можно объединить вместе, используя тип статистической обработки `Count` и поддерживать все стандартные временные ряды статистической обработки Azure, в том числе `Sum`, `Average`, `Min` и `Max`.

Дополнительные сведения о поддерживаемых типах агрегирования и агрегированиях временных рядов можно найти в статье [Дополнительные функции обозревателя метрик Azure](../../azure-monitor/platform/metrics-charts.md#changing-aggregation)

- **Operation**. Все операции или маршруты, которые могут быть вызваны в шлюзе чата ACS.
- **Status Code**. Ответ кода состояния, отправленный после запроса.
- **StatusSubClass**. Серия кодов состояния, отправленная после ответа. 


### <a name="chat-api-request-metric-operations"></a>Операции метрик запросов API чата

В метриках запросов API чата доступны следующие операции.

| Операция или маршрут    | Описание                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | Получает сообщение по идентификатору сообщения. |
| ListChatMessages     | Получает список сообщений чата из потока. |
| SendChatMessage      | Отправляет сообщение чата в поток. |
| UpdateChatMessage    | Обновляет сообщение чата. |
| DeleteChatMessage    | Удаляет сообщение чата. |
| GetChatThread        | Получает поток чата. |
| ListChatThreads      | Получает список потоков чата пользователя. |
| UpdateChatThread     | Обновляет свойства потока чата. |
| CreateChatThread     | Создает поток чата. |
| DeleteChatThread     | Удаляет поток. |
| GetReadReceipts      | Получает уведомления о прочтении для потока. |
| SendReadReceipt      | Отправляет событие уведомления о прочтении в поток от имени пользователя. |
| SendTypingIndicator           | Отправляет событие ввода в поток от имени пользователя. |
| ListChatThreadParticipants    | Получает элементы потока. |
| AddChatThreadParticipants     | Добавляет элементы потока в поток. Если элементы уже существуют, изменения не выполняются. |
| RemoveChatThreadParticipant   | Удаляет элемент из потока. |

:::image type="content" source="./media/chat-metric.png" alt-text="Метрика запроса API чата.":::

Если запрос выполняется для операции, которая не распознана, вы получите ответ "Неправильный маршрут".

### <a name="sms-api-requests"></a>Запросы API SMS

В метриках запросов API SMS доступны следующие операции.

| Операция или маршрут    | Описание                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | Отправляет SMS-сообщение. |
| SMSDeliveryReportsReceived     | Получает отчеты о доставке SMS |
| SMSMessagesReceived      | Получает SMS сообщения. |


:::image type="content" source="./media/sms-metric.png" alt-text="Метрика запроса API SMS.":::

## <a name="next-steps"></a>Next Steps

- Дополнительные сведения о [метриках платформы данных](../../azure-monitor/platform/data-platform-metrics.md)