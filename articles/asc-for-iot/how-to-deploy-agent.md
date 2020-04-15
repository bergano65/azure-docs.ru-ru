---
title: Выберите и развернете агентов безопасности
description: Узнайте о том, как выберите и развернув Центр безопасности Azure для агентов безопасности IoT на устройствах IoT.
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
ms.openlocfilehash: c3b514c79be87cd136375b4853226426965f4185
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311214"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Выберите и разместите агент безопасности на устройстве IoT

Центр безопасности Azure для IoT предоставляет эталонные архитектуры для агентов безопасности, которые отслеживают и собирают данные с устройств IoT.
Чтобы узнать больше, смотрите [архитектуру ссылки агента безопасности.](security-agent-architecture.md)

Агенты разрабатываются как проекты с открытым исходным кодом, и доступны в двух вкусах: <br> [C](https://aka.ms/iot-security-github-c), и [C .](https://aka.ms/iot-security-github-cs)

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Сравните вкусы агента безопасности
> * Откройте для себя поддерживаемые платформы агентов
> * Выберите правильный аромат агента для вашего решения

## <a name="understand-security-agent-options"></a>Понимание параметров агента безопасности

Каждый Центр безопасности Azure для аромата агента безопасности IoT предлагает один и тот же набор функций и поддерживает аналогичные параметры конфигурации.

C-агент безопасности имеет более низкий след памяти, и является идеальным выбором для устройств с меньшим количеством доступных ресурсов.

|     | C-агент безопасности | Агент по безопасности на основе C' |
| --- | ----------- | --------- |
| Открытый исходный код | Доступно под [лицензией MIT](https://en.wikipedia.org/wiki/MIT_License) в [GitHub](https://aka.ms/iot-security-github-cs) | Доступно под [лицензией MIT](https://en.wikipedia.org/wiki/MIT_License) в [GitHub](https://aka.ms/iot-security-github-c) |
| Язык разработки    | C | C# |
| Поддерживаемые платформы Windows? | нет | Да |
| Предпосылки для Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Поддерживаемые платформы Linux? | Да, x64 и x86 | Да, только x64 |
| Предварительные условия (и необходимые компоненты) для Linux | libunwind8, libcurl3, uuid-runtime, аудит, audispd-plugins | libunwind8, libcurl3, uuid-runtime, аудит, audispd-плагины, судо, netstat, iptables |
| След диска | 10,5 Мб | 90 МБ |
| След памяти (в среднем) | 5,5 Мб | 33 Мб |
| [Аутентификация](concept-security-agent-authentication-methods.md) концентратора IoT | Да | Да |
| [Сбор](how-to-agent-configuration.md#supported-security-events) данных о безопасности | Да | Да |
| Агрегация событий | Да | Да |
| Удаленная конфигурация через [модуль безопасности twin](concept-security-module.md) | Да | Да |
|

## <a name="security-agent-installation-guidelines"></a>Руководящие принципы установки агента безопасности

Для **Windows**: Скрипт Установки SecurityAgent.ps1 должен быть выполнен из окна Администратора PowerShell.

Для **Linux**: InstallSecurityAgent.sh должны быть запущены как суперпользователь. Рекомендуем предварительно закрепить команду установки "судо".

## <a name="choose-an-agent-flavor"></a>Выберите агент вкус

Ответьте на следующие вопросы о ваших устройствах IoT, чтобы выбрать правильный агент:

- Используете ли вы _Windows Server_ или _Windows IoT Core?_

    [Развертывание агента безопасности на основе C'для Windows.](how-to-deploy-windows-cs.md)

- Используете ли вы дистрибутив Linux с архитектурой x86?

    [Развертывание C-агента безопасности для Linux.](how-to-deploy-linux-c.md)

- Используете ли вы дистрибутив Linux с архитектурой x64?

    Оба агента вкусы могут быть использованы. <br>
    [Развертывание C-агента безопасности для Linux](how-to-deploy-linux-c.md) и/или [развертывание агента безопасности на основе C для Linux.](how-to-deploy-linux-cs.md)

Оба аромата агента предлагают один и тот же набор функций и поддерживают аналогичные параметры конфигурации.
[См, как сравнивайте агент по безопасности,](how-to-deploy-agent.md#understand-security-agent-options) чтобы узнать больше.

## <a name="supported-platforms"></a>Поддерживаемые платформы

Следующий список включает в себя все поддерживаемые в настоящее время платформы.

|Центр безопасности Azure для агента IoT |Операционная система |Architecture |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    X64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |X64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |X64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, сборка 17763    |X64|
|

## <a name="next-steps"></a>Следующие шаги

Чтобы узнать больше о вариантах конфигурации, перейти к руководству по конфигурации агента.
> [!div class="nextstepaction"]
> [Конфигурация агента, как руководствоваться](./how-to-agent-configuration.md)
