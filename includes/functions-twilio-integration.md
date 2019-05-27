---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132536"
---
Для отправки SMS-сообщений на мобильный телефон в этом примере используется служба [Twilio](https://www.twilio.com/). В Функциях Azure уже реализована поддержка Twilio через [привязку Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio). В этом образце используется эта функция.

Первое, что вам нужно — это учетная запись Twilio. Вы можете создать бесплатную учетную запись здесь: https://www.twilio.com/try-twilio. Получив учетную запись, добавьте следующие три **параметра приложения** в приложение-функцию.

| Имя параметра приложения | Описание значения |
| - | - |
| **TwilioAccountSid**  | Идентификатор безопасности вашей учетной записи в Twilio. |
| **TwilioAuthToken**   | Маркер проверки подлинности вашей учетной записи в Twilio. |
| **TwilioPhoneNumber** | Номер телефона, связанный с вашей учетной записью в Twilio. Используется для отправки SMS-сообщений. |