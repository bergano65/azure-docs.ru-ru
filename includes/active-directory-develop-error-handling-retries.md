---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760639"
---
## <a name="retrying-after-errors-and-exceptions"></a>Повторные попытки после ошибок и исключений

При вызове MSAL требуется реализовать собственные политики повтора. MSAL выполняет HTTP-вызовы к службе Azure AD, и иногда могут возникать сбои. Например, сеть может быть остановлена или сервер перегружен.  

### <a name="http-429"></a>HTTP 429

Когда сервер токенов службы (STS) перегружен из-за слишком большого количества запросов, он возвращает ошибку HTTP 429 с указанием времени ожидания до следующей попытки в поле ответа `Retry-After`.