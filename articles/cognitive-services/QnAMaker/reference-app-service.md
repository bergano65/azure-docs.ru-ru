---
title: Конфигурация службы — QnA Maker
description: Узнайте, как и где следует настраивать ресурсы.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: eac930971cab041fbf398da1ac5f8a055412832d
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376865"
---
# <a name="service-configuration"></a>Конфигурация службы

Каждая версия QnA Maker использует другой набор ресурсов Azure (служб). В этой статье описаны поддерживаемые настройки для этих служб. 

## <a name="app-service"></a>Служба приложений

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker (стабильный выпуск)](#tab/v1)

QnA Maker использует службу приложений для предоставления среды выполнения запросов, используемой [API женератеансвер](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).

Эти параметры доступны в портал Azure для службы приложений. Параметры доступны, выбрав **Параметры** , а затем — **Конфигурация**.

Можно задать отдельный параметр в списке Параметры приложения или изменить несколько параметров, выбрав пункт **дополнительно изменить**.

|Ресурс|Параметр|
|--|--|
|азуресеарчадминкэй|Когнитивный поиск — используется для хранения пар QnA и ранжирования #1|
|азуресеарчнаме|Когнитивный поиск — используется для хранения пар QnA и ранжирования #1|
|дефаултансвер|Текст ответа, если совпадений не найдено|
|усераппинсигхтсаппид|Журнал разговора и данные телеметрии|
|усераппинсигхтскэй|Журнал разговора и данные телеметрии|
|усераппинсигхтснаме|Журнал разговора и данные телеметрии|

После завершения внесения изменений необходимо **перезапустить** службу на странице **обзора** портал Azure.

# <a name="qna-maker-managed-preview-release"></a>[Управляемый QnA Maker (предварительный выпуск)](#tab/v2)

Настройки службы приложений не применяются к QnA Maker Managed (Предварительная версия).

---

## <a name="qna-maker-service"></a>Служба QnA Maker

Служба QnA Maker предоставляет конфигурацию для следующих пользователей для совместной работы с одной QnA Makerной службой и всеми ее базами знаний.

Узнайте [, как добавить в службу участников совместной работы](./how-to/collaborate-knowledge-base.md) .

## <a name="change-azure-cognitive-search"></a>Изменение Когнитивный поиск Azure

Узнайте [, как изменить службу когнитивный Поиск,](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) связанную со службой QnA Maker.

## <a name="change-default-answer"></a>Изменение ответа по умолчанию

Узнайте [, как изменить текст ответов по умолчанию](How-To/change-default-answer.md). 

## <a name="telemetry"></a>Телеметрия

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker (стабильный выпуск)](#tab/v1)

Application Insights используется для мониторинга телеметрии с QnA Maker. Нет параметров конфигурации, относящихся к QnA Maker.

# <a name="qna-maker-managed-preview-release"></a>[Управляемый QnA Maker (предварительный выпуск)](#tab/v2)

Узнайте [, как добавить телеметрию в службу QnA Maker Managed (Предварительная версия)](How-To/get-analytics-knowledge-base.md). 

---

## <a name="app-service-plan"></a>План службы приложений

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker (стабильный выпуск)](#tab/v1)

План службы приложений не имеет параметров конфигурации, относящихся к QnA Maker.

# <a name="qnamaker-managed-preview-release"></a>[Управляемый QnAMaker (предварительный выпуск)](#tab/v2)

План службы приложений не используется с управляемым QnA Maker (Предварительная версия).

---

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [форматах](reference-document-format-guidelines.md) документов и URL-адресов, которые необходимо импортировать в базу знаний.