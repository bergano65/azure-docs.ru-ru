---
title: Коды ответов HTTP для API — QnA Maker
titleSuffix: Azure Cognitive Services
description: Сведения о том, какие коды ответов HTTP возвращаются из API служб QnA Maker. Это поможет вам устранить любые ошибки.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 9eb5f1aefc8a36727e837af79f553108f624fc70
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207978"
---
# <a name="qna-maker-api-http-response-codes"></a>Коды ответов HTTP для API служб QnA Maker
[Управление](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) и прогнозирование возврата кодов ответов HTTP API-интерфейсами. В то время как ответные сообщения содержат сведения, относящиеся к запросу, код состояния ответа HTTP имеет общий характер. 

### <a name="management"></a>управления

|Код|Пояснение|
|:--|--|
|"2xx"|Успешно|
|400|Параметры запроса указаны неправильно. Это означает, что требуемые параметры отсутствуют, имеют неправильный формат или слишком большой размер|
|400|Текст запроса указан неправильно. Это означает, что JSON отсутствует, имеет неправильный формат или слишком большой размер|
|401|Недопустимый ключ|
|403|Доступ запрещен. У вас нет необходимых разрешений|
|404|База знаний не существует|
