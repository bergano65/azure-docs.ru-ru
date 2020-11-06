---
author: baanders
description: Включение файла для работы с Azure Digital Twins — предварительные требования для настройки регистрации приложения
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 0dd97e109ee907e8eec2d31c7d469e8fb1e70e7b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124852"
---
Чтобы проверить подлинность всех ресурсов, используемых в этой статье, необходимо настроить **регистрацию приложений** [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md). Следуйте инструкциям в [*Практическое руководство. Создайте регистрацию приложения*](../articles/digital-twins/how-to-create-app-registration.md), чтобы настроить ее. 

После регистрации приложения вам потребуются **_идентификатор приложения (клиента)_** и **_идентификатор каталога (клиента)_** ( [можно найти на портале Azure](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)). Запишите эти значения, чтобы использовать их позже для предоставления доступа к API Azure Digital Twins.