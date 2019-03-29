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
ms.openlocfilehash: 208f7cdbc9d610713d73c5af1e02de5faf11474c
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580798"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Выберите и разверните агент безопасности на устройстве Интернета вещей

> [!IMPORTANT]
> ASC для Интернета вещей находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC для Интернета вещей предоставляет эталонные образцы архитектуры для агентов безопасности, которые отслеживают и собирают данные с устройств Интернета вещей.
См. в разделе [эталонной архитектуры безопасности агента](security-agent-architecture.md) для получения дополнительных сведений.

Агенты разрабатываются в виде проектов с открытым кодом и доступны в двух вариантах: <br> [C](https://aka.ms/iot-security-github-c), и [ C# ](https://aka.ms/iot-security-github-cs).

В этой статье раскрываются следующие темы: 
> [!div class="checklist"]
> * Сравнить версии агента безопасности
> * Обнаружение агента поддерживаемых платформ
> * Выберите версию правой агента для вашего решения

## <a name="understand-security-agent-options"></a>Понять параметры безопасности агента

Каждый ASC для flavor агент безопасности Интернета вещей предлагает тот же набор функций, а также поддерживает аналогичные параметры конфигурации. 

Агент безопасности на основе C имеет объем используемой памяти и является идеальным выбором для устройств с меньшим объемом доступных ресурсов. 

|     | Агент безопасности на основе C | C#-агент безопасности |
| --- | ----------- | --------- |
| Открытый исходный код | Доступны в разделе [лицензии MIT](https://en.wikipedia.org/wiki/MIT_License) в [Github](https://aka.ms/iot-security-github-cs) | Доступны в разделе [лицензии MIT](https://en.wikipedia.org/wiki/MIT_License) в [Github](https://aka.ms/iot-security-github-c) |
| Язык разработки    | C | C# |
| Поддерживаемые платформы Windows? | Нет  | Yes |
| Предварительные требования для Windows | --- | [WMI](https://docs.microsoft.com/en-us/windows/desktop/wmisdk/) |
| Поддерживаемые платформы Linux? | Да, x64 и x86 | Да, только x64 |
| Предварительные требования для Linux | libunwind8, libcurl3, среда выполнения uuid, auditd, подключаемые модули audispd | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| Дискового пространства | 10.5 МБ | 90 МБ |
| Объем памяти (в среднем) | 5.5 МБ | 33 МБ |
| [Проверка подлинности](concept-security-agent-authentication-methods.md) к центру Интернета вещей | Yes | Yes |
| Данные безопасности [коллекции](how-to-agent-configuration.md#supported-security-events) | Yes | Yes |
| Агрегация событий | Yes | Yes |
| Конфигурация удаленного через [двойник модуля безопасности](concept-security-module.md) | Yes | Yes |


## <a name="choose-an-agent-flavor"></a>Выберите конфигурация агента 

Ответьте на следующие вопросы, касающиеся устройства Интернета вещей, чтобы выбрать правильный агент.

- Используете ли вы _Windows Server_ или _Windows IoT Core_? 

    [Развертывание C#-основе security agent для Windows](how-to-deploy-windows-cs.md).

- Вы используете дистрибутив Linux с помощью x86 архитектуры? 

    [Развернуть агент безопасности на основе C для Linux](how-to-deploy-linux-c.md).

- Вы используете дистрибутив Linux с помощью x64 архитектуры?

    Можно использовать либо версия агента. <br>
    [Развернуть агент безопасности на основе C для Linux](how-to-deploy-linux-c.md) и/или [развернуть C#-основе security agent для Linux](how-to-deploy-linux-cs.md).

Обоих видов агента обеспечивают тот же набор функций и поддерживают аналогичные параметры конфигурации.
См. в разделе [сравнения агент безопасности](how-to-deploy-agent.md#understand-security-agent-options) для получения дополнительных сведений.

## <a name="supported-platforms"></a>Поддерживаемые платформы

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о параметрах конфигурации, продолжайте практическим руководством по конфигурации агента. 
> [!div class="nextstepaction"]
> [Конфигурация агента практического руководства](./how-to-agent-configuration.md)