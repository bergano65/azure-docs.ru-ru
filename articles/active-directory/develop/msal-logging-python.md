---
title: Ведение журнала ошибок и исключений в MSAL для Python
titleSuffix: Microsoft identity platform
description: Сведения о записи ошибок и исключений в MSAL для Python
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8488325613b05d54b352a19a06860e08f1779877
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063120"
---
# <a name="logging-in-msal-for-python"></a>Ведение журналов в MSAL для Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL для ведения журнала Python

Ведение журнала в MSAL Python использует стандартный механизм ведения журнала Python, например, `logging.info("msg")` можно настроить ведение журнала MSAL следующим образом (и увидеть его в действии [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Включить ведение журнала отладки для всех модулей

По умолчанию ведение журнала в любом сценарии Python отключено. Если вы хотите включить ведение журнала отладки для всех модулей во всем сценарии Python, используйте:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Ведение журнала MSAL только бездействия

Чтобы безвозвратно вести журнал MSAL Library и включить ведение журнала отладки во всех остальных модулях скрипта Python, отключите средство ведения журнала, используемое MSAL Python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Личные и организационные данные в Python

MSAL для Python не регистрирует персональные данные или данные Организации. Отсутствует свойство для включения или отключения ведения журнала личных данных или организации.

Вы можете использовать стандартное ведение журнала Python, чтобы заносить в журнал любые нужные данные, но вы несете ответственность за безопасную обработку конфиденциальных данных и соблюдение нормативных требований.

Дополнительные сведения о ведении журнала в Python см. в статье  [ведение журнала Python: инструкции](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры кода см. в статье [примеры кода платформы идентификации Майкрософт](sample-v2-code.md).
