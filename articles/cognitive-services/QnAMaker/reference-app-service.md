---
title: Конфигурация сервиса - NA Maker
description: Понять, как и где настроить ресурсы.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 8ef6fecbfeb119d0c68ec5bc3bbc90ec449dbb7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651864"
---
# <a name="service-configuration"></a>Конфигурация сервиса

Создатель NA использует несколько ресурсов (услугИ Azure), включая Cognitive Search, App Service, App Service Plan и Application Insight.

Все настройки этих настроек, поддерживаемых qnA Maker, перечислены ниже.

## <a name="app-service"></a>Служба приложений

NA Maker использует службу app Service для предоставления времени выполнения запроса, используемого [API generateAnswer.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)


Эти настройки доступны на портале Azure для службы приложений. Настройки доступны при выборе **настроек,** а затем **конфигурации.**

Вы можете настроить индивидуальный параметр либо через список настроек приложений, либо изменить несколько настроек, выбрав **Расширенное изменение.**

|Ресурс|Параметр|
|--|--|
|AzureSearchAdminKey|Когнитивный поиск - используется для хранения наборов и ranker #1|
|AzureSearchName|Когнитивный поиск - используется для хранения наборов и ranker #1|
|По умолчаниюОтветнайд|Текст ответа, когда не найдено совпадений|
|UserAppInsightsAppId|Чат-журнал и телеметрия|
|UserAppInsightsKey|Чат-журнал и телеметрия|
|UserAppInsightsName|Чат-журнал и телеметрия|

Узнайте, как добавить в службу [услуги «Когнитивный поиск» сервис Cognitive Search.](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)

После внесения изменений необходимо **перезапустить** службу со страницы **«Обзор»** портала Azure.

## <a name="qna-maker-service"></a>Служба QnA Maker

Сервис «NA Maker» предоставляет конфигурацию для следующих пользователей для совместной работы над одним сервисом nA Maker и всеми его базами знаний.

[Узнайте, как добавить сотрудников](./how-to/collaborate-knowledge-base.md) в свой сервис.

## <a name="application-insights"></a>Application Insights

Приложение Insights не имеет настройки конфигурации, характерные для создателя NA.

## <a name="app-service-plan"></a>План обслуживания приложения

План службы приложений не имеет параметров конфигурации, характерных для создателя NA.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [форматах](reference-document-format-guidelines.md) документов и URL-адресов, которые необходимо импортировать в базу знаний.