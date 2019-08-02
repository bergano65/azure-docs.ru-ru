---
title: Выбор и развертывание центра безопасности Azure для агента IoT | Документация Майкрософт
description: Узнайте, как выбрать и развернуть центр безопасности Azure для агентов безопасности IoT на устройствах IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: ffc6ea447ae90649be0455abbed6245c078e518d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596341"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Выбор и развертывание агента безопасности на устройстве IoT

Центр безопасности Azure для Интернета вещей предоставляет эталонные архитектуры для агентов безопасности, которые отслеживают и собираются данные с устройств IoT.
Дополнительные сведения см. в разделе [Эталонная архитектура агента безопасности](security-agent-architecture.md).

Агенты разрабатываются в виде проектов с открытым исходным кодом и доступны в двух разновидностях: <br> [C](https://aka.ms/iot-security-github-c), и [C#](https://aka.ms/iot-security-github-cs).

В этой статье раскрываются следующие темы: 
> [!div class="checklist"]
> * Сравнение разновидностей агента безопасности
> * Обнаружение поддерживаемых платформ агентов
> * Выберите подходящую версию агента для своего решения

## <a name="understand-security-agent-options"></a>Общие сведения о параметрах агента безопасности

Каждый центр безопасности Azure для версии агента безопасности IoT предлагает один и тот же набор функций и поддерживает аналогичные параметры конфигурации. 

У агента безопасности на основе C есть меньше памяти, и это идеальный вариант для устройств с меньшим количеством доступных ресурсов. 

|     | Агент безопасности на основе C | C#агент безопасности на основе |
| --- | ----------- | --------- |
| Открытый исходный код | Доступно в рамках [лицензии MIT](https://en.wikipedia.org/wiki/MIT_License) в [GitHub](https://aka.ms/iot-security-github-cs) | Доступно в рамках [лицензии MIT](https://en.wikipedia.org/wiki/MIT_License) в [GitHub](https://aka.ms/iot-security-github-c) |
| Язык разработки    | В | C# |
| Поддерживаемые платформы Windows | Нет | Да |
| Предварительные требования для Windows | --- | [ИНТЕРФЕЙСА](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Поддерживаемые платформы Linux? | Да, x64 и x86 | Да, только x64 |
| Предварительные требования для Linux | libunwind8, libcurl3, UUID — среда выполнения, аудит, аудиспд — подключаемые модули | libunwind8, libcurl3, UUID-Runtime, audited, аудиспд-plugins, sudo, netstat, iptables |
| Место на диске | 10,5 МБ | 90 МБ |
| Объем памяти (в среднем) | 5,5 МБ | 33 МБ |
| [Проверка](concept-security-agent-authentication-methods.md) подлинности в центре Интернета вещей | Да | Да |
| [Сбор](how-to-agent-configuration.md#supported-security-events) данных безопасности | Да | Да |
| Агрегация событий | Да | Да |
| Удаленная настройка через [модуль безопасности двойника](concept-security-module.md) | Да | Да |
|

## <a name="security-agent-installation-guidelines"></a>Рекомендации по установке агента безопасности

Для **Windows**: Сценарий Install Секуритяжент. ps1 должен быть выполнен из окна администратора PowerShell. 

Для **Linux**: InstallSecurityAgent.sh должен быть запущен от имени суперпользователя. Рекомендуется выполнить префикс "Sudo" для команды установки.


## <a name="choose-an-agent-flavor"></a>Выберите разновидность агента 

Чтобы выбрать правильный агент, ответьте на следующие вопросы о своих устройствах IoT:

- Используете ли вы _Windows Server_ или _Windows IOT Core_? 

    [Развертывание агента безопасности на основе Windows. C#](how-to-deploy-windows-cs.md)

- Используете ли вы дистрибутив Linux с архитектурой x86? 

    [Развертывание агента безопасности на основе C для Linux](how-to-deploy-linux-c.md).

- Используете ли вы дистрибутив Linux с архитектурой x64?

    Можно использовать любую из разновидностей агента. <br>
    [Развертывание агента безопасности на основе C для Linux](how-to-deploy-linux-c.md) и (или [ C#) развертывание агента безопасности на основе для Linux](how-to-deploy-linux-cs.md).

Обе разновидности агента предлагают один и тот же набор функций и поддерживают аналогичные параметры конфигурации.
Дополнительные сведения см. в разделе [Сравнение агента безопасности](how-to-deploy-agent.md#understand-security-agent-options) .

## <a name="supported-platforms"></a>Поддерживаемые платформы

В следующем списке перечислены все поддерживаемые в настоящее время платформы.

|Центр безопасности Azure для агента IoT |Операционная система |Архитектура |
|--------------|------------|--------------|
|В|Ubuntu 16.04 |   x64|
|В|Ubuntu 18.04 |   x64|
|В|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Базовая, сборка 17763 |x64|
|

## <a name="next-steps"></a>Следующие шаги

Чтобы узнать больше о параметрах конфигурации, перейдите к руководству по настройке агента. 
> [!div class="nextstepaction"]
> [Руководство по настройке агента](./how-to-agent-configuration.md)
