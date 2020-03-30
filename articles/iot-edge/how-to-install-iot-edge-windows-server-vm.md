---
title: Выполнить Azure IoT Edge на Windows Server Виртуальные машины (ru) Документы Майкрософт
description: Инструкции по настройке Azure IoT Edge на виртуальных машинах Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77045894"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Выполнить Azure IoT Edge на виртуальных машинах Windows Server

Среда выполнения Azure IoT Edge превращает устройство в устройство IoT Edge. Среду выполнения можно развернуть на всех устройствах — от небольшого Raspberry Pi до технического сервера. Как только на устройстве будет настроена среда выполнения IoT Edge, вы можете начать развертывать в нее бизнес-логику из облака.

Дополнительные сведения о работе среды выполнения IoT Edge и ее компонентах см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре](iot-edge-runtime.md).

В этой статье перечислены шаги для запуска времени выполнения системы Azure IoT Edge на виртуальной машине Windows Server 2019 с использованием предложения [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace. Следуйте инструкциям при [установке времени выполнения Azure IoT Edge](how-to-install-iot-edge-windows.md) на Windows для использования в других версиях.

## <a name="deploy-from-the-azure-marketplace"></a>Развертывание из Azure Marketplace

1. Перейдите на предложение [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace или путем поиска "Windows Server" на [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Выберите **GET IT сейчас**
3. В **плане программного обеспечения**найдите "Windows Server 2019 Datacenter Server Core with Containers", а затем выберите **Продолжить** следующий диалог.
    * Вы также можете использовать эти инструкции для других версий Windows Server с контейнерами
4. Войдите на портал Azure, выберите **Создать** и следуйте указаниям мастера, чтобы развернуть виртуальную машину.
    * Если вы впервые опробуете VM, проще всего использовать пароль и включить RDP и SSH в меню входящих портов.
    * При наличии ресурсоемкой рабочей нагрузки следует обновить размер виртуальной машины, добавив дополнительные ЦП и (или) память.
5. После развертывания виртуальной машины настройте ее подключение к Центру Интернета вещей:
    1. Копируйте строку подключения устройства с устройства IoT Edge, созданного в концентраторе IoT. Смотрите процедуру [Retrieve строка соединения на портале Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Выберите недавно созданный ресурс виртуальной машины на портале Azure и щелкните **Выполнить команду**.
    1. Выберите опцию **RunPowerShellScript**
    1. Скопируйте этот скрипт в окно команды строкой соединения устройства:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Выполните сценарий для установки времени выполнения IoT Edge и установите строку соединения, выбрав **Run**
    1. Через минуту или две вы должны увидеть сообщение о том, что время выполнения Edge было установлено и успешно подготовлено.

## <a name="deploy-from-the-azure-portal"></a>Развертывание с портала Azure

1. С портала Azure ищите "Windows Server" и выберите **Windows Server 2019 Datacenter,** чтобы начать работу по созданию VM.
2. Из **плана программного обеспечения** выберите "Windows Server 2019 Datacenter Server Core с контейнерами", а затем выберите **Создать**
3. Завершите шаг 5 в инструкциях "Развертывание с рынка Azure" выше.

## <a name="deploy-from-azure-cli"></a>Развертывание с помощью Azure CLI

1. Если вы используете Azure CLI на настольном компьютере, сначала выполните вход:

   ```azurecli-interactive
   az login
   ```

1. Если у вас несколько подписок, выберите ту, которую необходимо использовать:
   1. Отобразите список подписок:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Скопируйте поле SubscriptionID подписки, которую необходимо использовать.
   1. Запустите эту команду с помощью скопированных идентификаторов:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Создайте группу ресурсов (или выберите уже имеющуюся на следующих этапах):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Создайте виртуальную машину:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Эта команда подскажет вам пароль, но вы `--admin-password` можете добавить опцию, чтобы установить его более легко в сценарии
   * Изображение Windows Server Core имеет поддержку командной строки только с удаленного `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` рабочего стола, так что если вы хотите полный опыт рабочего стола, укажите, как изображение

1. Установите строку соединения устройства (Вы можете следить за строкой подключения с процедурой [Azure CLI,](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) если вы не знакомы с этим процессом):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда подготовлено устройство IoT Edge и установлена среда выполнения, вы можете [развернуть модули IoT Edge](how-to-deploy-modules-portal.md).

Если у вас возникли проблемы с установкой времени выполнения Edge должным образом, проверьте страницу [устранения неполадок.](troubleshoot.md)

Чтобы обновить существующую установку до последней версии IoT Edge, см. раздел [Обновление управляющей программы безопасности и среды выполнения IoT Edge](how-to-update-iot-edge.md).

Узнайте больше об использовании виртуальных компьютеров Windows в [документации Windows Virtual Machines.](https://docs.microsoft.com/azure/virtual-machines/windows/)

Если вы хотите, чтобы SSH в этот VM после настройки, следуйте [установка OpenSSH для Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) руководство с помощью удаленного рабочего стола или удаленной powershell.
