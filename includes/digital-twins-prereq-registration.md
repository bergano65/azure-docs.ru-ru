---
author: baanders
description: Включение файла для работы с Azure Digital Twins — предварительные требования для настройки регистрации приложения
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 0dd97e109ee907e8eec2d31c7d469e8fb1e70e7b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020315"
---
Чтобы проверить подлинность всех ресурсов, используемых в этой статье, необходимо настроить **регистрацию приложений** [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md). Следуйте инструкциям в [*Практическое руководство. Создайте регистрацию приложения*](../articles/digital-twins/how-to-create-app-registration.md), чтобы настроить ее. 

После регистрации приложения вам потребуются **_идентификатор приложения (клиента)_** и **_идентификатор каталога (клиента)_** ([можно найти на портале Azure](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)). Запишите эти значения, чтобы использовать их позже для предоставления доступа к API Azure Digital Twins.