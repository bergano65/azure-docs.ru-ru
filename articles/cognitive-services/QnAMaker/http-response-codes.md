---
title: Коды ответов HTTP для API — QnA Maker
titleSuffix: Azure Cognitive Services
description: Сведения о том, какие коды ответов HTTP возвращаются из API служб QnA Maker. Это поможет вам устранить любые ошибки.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 0416b282a869536b0cea591ba39d7a3804697bac
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446525"
---
# <a name="qna-maker-api-http-response-codes"></a>Коды ответов HTTP для API служб QnA Maker
[Управление](https://go.microsoft.com/fwlink/?linkid=2092179) и прогнозирование возврата кодов ответов HTTP API-интерфейсами. В то время как ответные сообщения содержат сведения, относящиеся к запросу, код состояния ответа HTTP имеет общий характер. 

### <a name="management"></a>Управление

|Код|Объяснение|
|:--|--|
|"2xx"|Успешно|
|400|Параметры запроса указаны неправильно. Это означает, что требуемые параметры отсутствуют, имеют неправильный формат или слишком большой размер|
|400|Текст запроса указан неправильно. Это означает, что JSON отсутствует, имеет неправильный формат или слишком большой размер|
|401|Недопустимый ключ|
|403|Доступ запрещен. У вас нет необходимых разрешений|
|404|База знаний не существует|
|410|Этот API устарел и больше недоступен|
