---
title: Руководство по установке и развертывании C ОС Linux агент центра безопасности Azure для Интернета вещей предварительной версии | Документация Майкрософт
description: Сведения об установке агента IoT в центре безопасности Azure в 32-разрядных и 64-разрядной Linux.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 147813ae096114b4dfc1a20d2e0a70639aa82445
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754436"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Развертывание центра безопасности для безопасности на основе Интернета вещей C агента для Linux

> [!IMPORTANT]
> Центр безопасности Azure для Интернета вещей находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этом руководстве объясняется, как установить и развернуть центр безопасности Azure (ASC) для агента безопасности на основе C Интернета вещей в Linux.

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * Install
> * Проверка развертывания
> * Удаление агента
> * Устранение неполадок 

## <a name="prerequisites"></a>Технические условия

Другие платформы и версии агента, см. в разделе [выберите агент защиты](how-to-deploy-agent.md).

1. Чтобы развернуть агент безопасности, необходимые права локального администратора на компьютере, которым вы хотите установить на (sudo).

1. [Создайте модуль безопасности](quickstart-create-security-twin.md) для устройства.

## <a name="installation"></a>Установка 

Чтобы установить и развернуть агент безопасности, сделайте следующее:


1. Скачать последнюю версию на локальный компьютер из [Github](https://aka.ms/iot-security-github-c).

1. Извлеките содержимое пакета и перейдите к _/Install_ папки.

1. Добавьте работающей разрешения для **InstallSecurityAgent скрипт** путем выполнения следующего:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Далее выполните: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   См. в разделе [Настройка проверки подлинности](concept-security-agent-authentication-methods.md) Дополнительные сведения о параметрах проверки подлинности.

Скрипт выполняет следующее:

1. Установка необходимых компонентов.

2. Добавляет пользователя службы (с помощью интерактивного входа в систему отключены).

3. Устанавливает агент в качестве **управляющей программы** -предполагается, что устройство использует **systemd** для управления службами.

4. Настраивает агент с предоставленными параметрами проверки подлинности. 

Для получения дополнительной поддержки, запустите скрипт с параметром – help: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Удаление агента

Чтобы удалить агент, запустите скрипт с-удалить параметр:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Устранение неполадок
Проверьте состояние развертывания, выполнив:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Дальнейшие действия
- Чтение ASC для службы Интернета вещей [Обзор](overview.md)
- Дополнительные сведения о ASC для Интернета вещей [архитектуры](architecture.md)
- Включить [службы](quickstart-onboard-iot-hub.md)
- Чтение [часто задаваемые вопросы](resources-frequently-asked-questions.md)
- Понять [оповещения системы безопасности](concept-security-alerts.md)