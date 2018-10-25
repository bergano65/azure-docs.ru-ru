---
title: Коды ответов HTTP для API — QnA Maker
titleSuffix: Azure Cognitive Services
description: Сведения о том, какие коды ответов HTTP возвращаются из API служб QnA Maker
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 96782d32817e4989c02e0ed098d7772c80aa26c8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079670"
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