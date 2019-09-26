---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: 3b6f8ef5f2ec9f77dcba9c006fdd28b5ec668033
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315027"
---
Связь с Azure AD B2C происходит через приложение, регистрируемое в клиенте B2C. В этом разделе перечислены необязательные действия, которые можно выполнить, чтобы создать тестовое приложение, если вы его еще не создали.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выберите фильтр **каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Или выберите **все службы** , найдите и выберите **Azure AD B2C**.
1. Щелкните **Приложения**, а затем выберите **Добавить**.
1. Введите имя приложения. Например, *testapp1*.
1. Для **веб-приложения или веб-API**выберите **Да**.
1. В качестве **URL-адреса ответа**введите`https://jwt.ms`
1. Нажмите кнопку **Создать**.
