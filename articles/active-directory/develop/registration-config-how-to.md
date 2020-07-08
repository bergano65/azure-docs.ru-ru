---
title: Получение конечных точек для регистрации приложения Azure AD
titleSuffix: Microsoft identity platform
description: Узнайте, как найти конечные точки проверки подлинности для настраиваемого приложения, разрабатываемого или регистрируемого в Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ms.openlocfilehash: dc37ce474ad3226f5dbf7e6efd687ddf55f7da03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82926686"
---
# <a name="how-to-discover-endpoints"></a>Обнаружение конечных точек

Конечные точки проверки подлинности для приложения можно найти на [портале Azure](https://portal.azure.com).

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите **Azure Active Directory**.
1. В разделе **Управление**выберите **Регистрация приложений**, а затем в верхнем меню выберите **конечные точки** .

    Отобразится страница **конечные точки** , в которой отображаются конечные точки проверки подлинности для вашего клиента.
    
    Используйте конечную точку, совпадающую с используемым протоколом проверки подлинности, в сочетании с **идентификатором приложения (клиента)** для создания запроса проверки подлинности, относящегося к приложению.

В **национальных облаках** (например, в Azure AD для Китая, Германии и США) есть собственный портал регистрации приложений и конечные точки проверки подлинности Azure AD. Дополнительные сведения см. в [обзоре национальных облаков](authentication-national-cloud.md).

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о конечных точках в различных средах Azure см. в [обзоре национальных облаков](authentication-national-cloud.md).
