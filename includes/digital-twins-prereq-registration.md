---
author: baanders
description: Включение файла для работы с Azure Digital Twins — предварительные требования для настройки регистрации приложения
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 2ce534972cf6509cdc1ca026f4b29efd3df91afd
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96303589"
---
Чтобы проверить подлинность всех ресурсов, используемых в этой статье, необходимо **настроить регистрацию приложения [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md)**. Следуйте инструкциям в [*Практическое руководство. Создайте регистрацию приложения*](../articles/digital-twins/how-to-create-app-registration.md), чтобы настроить ее. 

После регистрации приложения вам потребуются **_идентификатор приложения (клиента)_** и **_идентификатор каталога (клиента)_** ([можно найти на портале Azure](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)). Запишите эти значения, чтобы использовать их позже для предоставления доступа к API Azure Digital Twins.