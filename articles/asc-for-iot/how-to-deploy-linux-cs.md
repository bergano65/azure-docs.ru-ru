---
title: Установка & развертывании агента Linux C
description: Узнайте, как установить Центр безопасности Azure для агента IoT как на 32-битном, так и на 64-битном Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: 40c6ea91fd84a0f088ed770cd7c4c3ea7b8b1c91
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311147"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Развертывание агента безопасности Центра безопасности Azure для Интернета вещей на основе C# для Linux

В этом руководстве объясняется, как установить и развернуть Центр безопасности Azure для агента безопасности на основе IoT C на Linux.

Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Установка
> * Проверка развертывания
> * Удаление агента
> * Диагностика

## <a name="prerequisites"></a>Предварительные требования

Для других платформ и ароматов агента, [см.](how-to-deploy-agent.md)

1. Для развертывания агента безопасности требуются права локального администратора на компьютере, используемом для установки.

1. [Создайте модуль безопасности](quickstart-create-security-twin.md) для устройства.

## <a name="installation"></a>Установка

Для развертывания агента безопасности используйте следующие действия:

1. Скачать самую самую версию на компьютер с [GitHub](https://aka.ms/iot-security-github-cs).

1. Извлеките содержимое пакета и перейдите к папке _/Install._

1. Добавьте права на выполнение к **скрипту InstallSecurityAgent**, выполнив следующую команду `chmod +x InstallSecurityAgent.sh`.

1. Далее запустите следующую команду с **корневыми привилегиями:**

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   для получения дополнительной информации о параметрах аутентификации [см.](concept-security-agent-authentication-methods.md)

Сценарий выполняет следующие действия:

- Установка необходимых компонентов.

- Добавляет пользователя сервиса (с интерактивным знаком в инвалидах).

- Устанавливает агент как **Daemon** - предполагает, что устройство использует **системную** для классической модели развертывания.

- Настраивает **судодомые,** чтобы агент упомята выполнять определенные задачи в качестве корня.

- Настройка агента с предоставленными параметрами проверки подлинности.

Для получения дополнительной справки запустите скрипт с параметром –help: `./InstallSecurityAgent.sh --help`.

### <a name="uninstall-the-agent"></a>Удаление агента

Чтобы удалить агент, запустите скрипт с параметром –u: `./InstallSecurityAgent.sh -u`.

> [!NOTE]
> Удаление не приводит к удалению всех ранее отсутствующих необходимых компонентов, которые были установлены во время установки.

## <a name="troubleshooting"></a>Устранение неполадок

1. Проверьте состояние развертывания, выполнив следующую команду:

    `systemctl status ASCIoTAgent.service`

1. Включите ведение журналов.
   Если агент не запускается, включите ведение журнала, чтобы просмотреть дополнительные сведения.

   Чтобы включить ведение журнала:

   1. Откройте файл конфигурации для редактирования в любом редакторе Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Измените следующие значения:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       Значение **logFilePath** настраивается.

       > [!NOTE]
       > Мы рекомендуем **отключить** ведение журнала после устранения неполадок. Если оставить ведение журнала **включенным**, размер файла журнала и использование данных увеличится.

   1. Перезапустите агент, выполнив команду:

       `systemctl restart ASCIoTAgent.service`

   1. Просмотрите дополнительные сведения о сбое в файле журнала.

       Расположение файла журнала: `/var/ASCIoTAgent/IotAgentLog.log`.

       Измените путь к расположению файла в соответствии с именем, выбранным для значения **logFilePath** на шаге 2.

## <a name="next-steps"></a>Следующие шаги

- Прочтите [обзор](overview.md) службы безопасности Azure для обслуживания IoT
- Узнайте больше о Центре безопасности Azure для [IoT-архитектуры](architecture.md)
- Включите [службу](quickstart-onboard-iot-hub.md).
- Читать [часто задаваемые вопросы](resources-frequently-asked-questions.md)
- Ознакомьтесь со сведениями об [оповещениях](concept-security-alerts.md).
