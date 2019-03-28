---
title: Выберите и разверните ASC для Интернета вещей агент предварительной версии | Документация Майкрософт
description: Узнайте о том, как выбрать и развернуть ASC для агентов безопасности Интернета вещей на устройствах Интернета вещей.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541967"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Выберите и разверните агент безопасности на устройстве Интернета вещей

> [!IMPORTANT]
> ASC для Интернета вещей находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC для Интернета вещей предоставляет эталонные образцы архитектуры для агентов безопасности, которые отслеживают и собирают данные с устройств Интернета вещей.

Агенты безопасности разрабатываются в виде проектов с открытым кодом и доступны в двух вариантах: <br> [C](https://aka.ms/iot-security-github-c), и [ C# ](https://aka.ms/iot-security-github-cs).

## <a name="supported-platforms-for-asc-for-iot-agents"></a>Поддерживаемые платформы для ASC для Интернета вещей агентов

В следующем списке приведены все платформы, поддерживаемые в настоящее время.  

|ASC для Интернета вещей агента |Операционная система |Архитектура |
|--------------|------------|--------------|
|C|Ubuntu 16.04. |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04.    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Базовая, сборка 17763 |x64|


## <a name="which-flavor-should-i-use"></a>Какие flavor следует использовать?

Ответьте на следующие вопросы относительно устройств Интернета вещей:

- Используете ли вы _Windows Server_ или _Windows IoT Core_? 

    Используйте [ASC для Интернета вещей установки для Windows с помощью C#-агент безопасности](tutorial-deploy-windows-cs.md).

- Вы используете дистрибутив Linux с помощью x86 архитектуры? 

    Используйте [ASC для Интернета вещей установки для Linux с агентом безопасности на основе C](tutorial-deploy-linux-c.md).
- Вы используете дистрибутив Linux с помощью x64 архитектуры?

    Можно использовать обоих видов. <br>
    [ASC для Интернета вещей установки для Linux с агентом безопасности на основе C](tutorial-deploy-linux-c.md) и/или [ASC для Интернета вещей установки для Linux с помощью C#-агент безопасности](tutorial-deploy-linux-cs.md).

Оба имеют одинаковый набор функций и поддерживают аналогичные параметры конфигурации. Агент безопасности на основе C имеет больший объем используемой памяти.


## <a name="next-steps"></a>Дальнейшие действия
- [Обзор](overview.md)
- [Агенты безопасности](security-agent-architecture.md)
- [Методы проверки подлинности агента безопасности](concept-security-agent-authentication-methods.md)
- [ASC для Интернета вещей часто задаваемые вопросы](resources-frequently-asked-questions.md)