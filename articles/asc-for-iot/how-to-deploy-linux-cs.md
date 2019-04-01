---
title: Руководство по установке и развертывании Linux C# агента центра безопасности Azure для Интернета вещей Preview | Документация Майкрософт
description: Сведения об установке агента IoT в центре безопасности Azure в 32-разрядных и 64-разрядной Linux.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: be4c663d3a1e99ef67cbbbc2f39b315f1080125c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758333"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Развертывание центра безопасности Azure для IoT C#-основе security agent для Linux

> [!IMPORTANT]
> Центр безопасности Azure для Интернета вещей находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этом руководстве объясняется, как установить и развернуть центр безопасности Azure (ASC) для Интернета вещей C#-агент безопасности на базе Linux.

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * Install
> * Проверка развертывания
> * Удаление агента
> * Устранение неполадок 

## <a name="prerequisites"></a>Технические условия

Другие платформы и версии агента, см. в разделе [выберите агент защиты](how-to-deploy-agent.md).

1. Чтобы развернуть агент безопасности, на компьютере, которым вы хотите установить на необходимы права локального администратора. 

1. [Создайте модуль безопасности](quickstart-create-security-twin.md) для устройства.

## <a name="installation"></a>Установка 

Чтобы развернуть агент безопасности, сделайте следующее:

1. Скачать последнюю версию на локальный компьютер из [Github](https://aka.ms/iot-security-github-cs).

1. Извлеките содержимое пакета и перейдите к _/Install_ папки.

1. Добавьте разрешения, запущенного для **InstallSecurityAgent сценарий** , выполнив `chmod +x InstallSecurityAgent.sh` 

1. Далее выполните: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   См. в разделе [Настройка проверки подлинности](concept-security-agent-authentication-methods.md) Дополнительные сведения о параметрах проверки подлинности.

Скрипт выполняет следующее:

- Установка необходимых компонентов.

- Добавляет пользователя службы (с помощью интерактивного входа в систему отключены).

- Устанавливает агент в качестве **управляющей программы** -это предполагает, что устройство использует **systemd** для управления службами.

- Настраивает **sudoers** разрешить агенту для выполнения определенных задач в качестве привилегированного пользователя.

- Настраивает агент с параметрами предоставленный проверки подлинности.


Для получения дополнительной поддержки, запустите скрипт с параметром – help: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Удаление агента

Чтобы удалить агент, запустите сценарий с параметром – u: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Удаление не приводит к удалению все отсутствующие необходимые компоненты, которые были установлены во время установки.

## <a name="troubleshooting"></a>Устранение неполадок  

1. Проверьте состояние развертывания, выполнив:

    `systemctl status ASCIoTAgent.service`

2. Включение ведения журнала.  
   Если агент не запускается, включите ведение журнала для получения дополнительных сведений.

   Включите ведение журнала по:

   1. Откройте файл конфигурации для редактирования в любом редакторе Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Измените следующие значения: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       **LogFilePath** значение настраивается. 

       > [!NOTE]
       > Мы рекомендуем включить ведение журнала **off** после завершения устранения неполадок. Оставив ведения журнала **на** увеличения журнала файла размера и использования данных.

   1. Перезапустите агент, выполнив:

       `systemctl restart ASCIoTAgent.service`

   1. Просмотрите файл журнала, Дополнительные сведения о сбое.  

       — Расположение файла журнала: `/var/ASCIoTAgent/IotAgentLog.log`

       Измените путь к расположению файла в соответствии с именем, выбранным для **logFilePath** на шаге 2. 

## <a name="next-steps"></a>Дальнейшие действия

- Чтение ASC для службы Интернета вещей [Обзор](overview.md)
- Дополнительные сведения о ASC для Интернета вещей [архитектуры](architecture.md)
- Включить [службы](quickstart-onboard-iot-hub.md)
- Чтение [часто задаваемые вопросы](resources-frequently-asked-questions.md)
- Понять [оповещения](concept-security-alerts.md)