---
title: Запуск Azure IoT Edge на виртуальных машинах Windows Server | Документация Майкрософт
description: Инструкции по установке Azure IoT Edge на виртуальных машинах Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: gregman
ms.openlocfilehash: b32bbfa5e849c1a0490bba5d09d1838268033b26
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964659"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Запуск Azure IoT Edge на виртуальных машинах Windows Server

Среда выполнения Azure IoT Edge превращает устройство в устройство IoT Edge. Среду выполнения можно развернуть на всех устройствах — от небольшого Raspberry Pi до технического сервера. Как только на устройстве будет настроена среда выполнения IoT Edge, вы можете начать развертывать в нее бизнес-логику из облака.

Дополнительные сведения о работе среды выполнения IoT Edge и ее компонентах см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре](iot-edge-runtime.md).

В этой статье перечислены действия по запуску среды выполнения Azure IoT Edge на виртуальной машине Windows Server 2019 с помощью предложения [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace. Следуйте инструкциям в статье [Установка среды выполнения Azure IOT Edge](how-to-install-iot-edge-windows.md) в Windows для использования с другими версиями.

## <a name="deploy-from-the-azure-marketplace"></a>Развертывание из Azure Marketplace

1.  Перейдите к предложению [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace или найдите "Windows Server" в [Azure Marketplace](https://azuremarketplace.microsoft.com/) .
2.  Выберите **получить сейчас** 
3.  В **плане программного обеспечения**найдите пункт "Windows Server 2019 Datacenter Server Core с контейнерами" и нажмите кнопку **продолжить** в следующем диалоговом окне.
    * Эти инструкции также можно использовать для других версий Windows Server с контейнерами
4.  Войдите на портал Azure, выберите **Создать** и следуйте указаниям мастера, чтобы развернуть виртуальную машину. 
    *   Если вы впервые используете виртуальную машину, проще всего использовать пароль и включить RDP и SSH в меню общедоступный входящий порт. 
    *   При наличии ресурсоемкой рабочей нагрузки следует обновить размер виртуальной машины, добавив дополнительные ЦП и (или) память.
5.  После развертывания виртуальной машины настройте ее подключение к Центру Интернета вещей:
    1.  Скопируйте строку подключения устройства с устройства IoT Edge, созданного в центре Интернета вещей. См. процедуру [получения строки подключения в портал Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1.  Выберите недавно созданный ресурс виртуальной машины на портале Azure и щелкните **Выполнить команду**.
    1.  Выберите параметр **рунповершеллскрипт**
    1.  Скопируйте этот скрипт в командное окно со строкой подключения устройства: 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Выполните скрипт, чтобы установить среду выполнения IoT Edge и задать строку подключения, выбрав команду **выполнить** .
    1.  Через одну или две минуты вы увидите сообщение о том, что среда выполнения пограничной установки и успешно подготовлена.

## <a name="deploy-from-the-azure-portal"></a>Развертывание из портал Azure

1. В портал Azure выполните поиск по запросу "Windows Server" и выберите **Windows server 2019 Datacenter** , чтобы начать рабочий процесс создания виртуальной машины. 
2. На **панели Выбор плана программного обеспечения** выберите пункт "Windows Server 2019 Datacenter Server Core с контейнерами", а затем щелкните **создать** .
3. Выполните шаг 5 из приведенных выше инструкций по развертыванию из Azure Marketplace.

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
   1. Выполните эту команду с скопированным ИДЕНТИФИКАТОРом:

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

   * Эта команда предложит ввести пароль, но можно добавить параметр `--admin-password`, чтобы сделать его более простым в сценарии.
   * Образ Windows Server Core поддерживает командную строку только с помощью удаленного рабочего стола, поэтому, если вы хотите использовать все возможности рабочего стола, укажите `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` в качестве образа.

1. Задайте строку подключения устройства (если вы не знакомы с этим процессом, можно следовать процедуре [получения строки подключения с Azure CLIной](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) процедурой):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда подготовлено устройство IoT Edge и установлена среда выполнения, вы можете [развернуть модули IoT Edge](how-to-deploy-modules-portal.md).

Если у вас возникают проблемы с правильной установкой среды выполнения пограничных сред, ознакомьтесь со страницей [устранения неполадок](troubleshoot.md) .

Чтобы обновить существующую установку до последней версии IoT Edge, см. раздел [Обновление управляющей программы безопасности и среды выполнения IoT Edge](how-to-update-iot-edge.md).

Дополнительные сведения об использовании виртуальных машин Windows см. в [виртуальные машины Windows документации](https://docs.microsoft.com/azure/virtual-machines/windows/).

Если вы хотите установить подключение по протоколу SSH к этой виртуальной машине после установки, следуйте указаниям в статье [Установка OpenSSH для Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) с помощью удаленного рабочего стола или удаленной PowerShell.
