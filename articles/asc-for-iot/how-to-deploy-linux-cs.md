---
title: Инструкции по установке и развертыванию C# агента Linux центра безопасности Azure для IOT | Документация Майкрософт
description: Узнайте, как установить центр безопасности Azure для агента Интернета вещей на 32-и 64-разрядной платформе Linux.
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
ms.openlocfilehash: 0d77a1be2a3469282dabb646b02c43e350313ce5
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596299"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Развертывание центра безопасности Azure для агента C# безопасности на основе IOT для Linux


В этом руководство объясняется, как установить и развернуть центр безопасности Azure для C#агента безопасности на основе Интернета вещей на платформе Linux.

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * Установка
> * Проверить развертывание
> * Удаление агента.
> * Устранение неполадок 

## <a name="prerequisites"></a>предварительные требования

Сведения о других платформах и разновидностях агентов см. в разделе Выбор подходящего [агента безопасности](how-to-deploy-agent.md).

1. Для развертывания агента безопасности требуются права локального администратора на компьютере, используемом для установки. 

1. [Создайте модуль безопасности](quickstart-create-security-twin.md) для устройства.

## <a name="installation"></a>Установка 

Чтобы развернуть агент безопасности, выполните следующие действия.

1. Скачайте последнюю версию на компьютер с сайта [GitHub](https://aka.ms/iot-security-github-cs).

1. Извлеките содержимое пакета и перейдите в папку _/Install_.

1. Добавьте права на выполнение к **скрипту InstallSecurityAgent**, выполнив следующую команду `chmod +x InstallSecurityAgent.sh`. 

1. Затем выполните приведенную ниже команду: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Дополнительные сведения о параметрах проверки подлинности см. [в разделе Настройка проверки](concept-security-agent-authentication-methods.md)подлинности.

Сценарий выполняет следующие действия:

- Установка необходимых компонентов.

- Добавляет пользователя службы (с отключенным интерактивным входом).

- Устанавливает агент в качестве **управляющей** программы. предполагает, что устройство использует **систему** для классической модели развертывания.

- Настраивает параметры **sudo** , чтобы разрешить агенту выполнять определенные задачи в качестве привилегированных.

- Настройка агента с предоставленными параметрами проверки подлинности.


Для получения дополнительной справки запустите скрипт с параметром –help: `./InstallSecurityAgent.sh --help`.

### <a name="uninstall-the-agent"></a>Удаление агента

Чтобы удалить агент, запустите скрипт с параметром –u: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Удаление не приводит к удалению всех ранее отсутствующих необходимых компонентов, которые были установлены во время установки.

## <a name="troubleshooting"></a>Устранение неполадок  

1. Проверьте состояние развертывания, выполнив следующую команду:

    `systemctl status ASCIoTAgent.service`

2. Включите ведение журналов.  
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

- Ознакомьтесь с [обзором](overview.md) центра безопасности Azure для службы IOT
- Узнайте больше о центре безопасности Azure для [архитектуры](architecture.md) IOT
- Включите [службу](quickstart-onboard-iot-hub.md).
- Просмотрите [часто задаваемые вопросы](resources-frequently-asked-questions.md).
- Ознакомьтесь со сведениями об [оповещениях](concept-security-alerts.md).