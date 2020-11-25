---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027229"
---
Для отправки SMS-сообщений на мобильный телефон в этом примере используется служба [Twilio](https://www.twilio.com/). В Функциях Azure уже реализована поддержка Twilio через [привязку Twilio](../articles/azure-functions/functions-bindings-twilio.md). В этом образце используется эта функция.

Первое, что вам нужно — это учетная запись Twilio. Вы можете создать бесплатную учетную запись здесь: https://www.twilio.com/try-twilio. Получив учетную запись, добавьте следующие три **параметра приложения** в приложение-функцию.

| Имя параметра приложения | Описание значения |
| - | - |
| **TwilioAccountSid**  | Идентификатор безопасности вашей учетной записи в Twilio. |
| **TwilioAuthToken**   | Маркер проверки подлинности вашей учетной записи в Twilio. |
| **TwilioPhoneNumber** | Номер телефона, связанный с вашей учетной записью в Twilio. Используется для отправки SMS-сообщений. |