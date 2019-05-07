---
title: Запустите Azure IoT Edge на виртуальных машинах Windows Server | Документация Майкрософт
description: Azure IoT Edge инструкции по установке на Windows Server Marketplace виртуальные машины
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: gregman
ms.openlocfilehash: be7479d3f042d6e64428a07e0509907b78595200
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159785"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Запустите Azure IoT Edge на виртуальных машинах Windows Server
Среда выполнения Azure IoT Edge превращает устройство в устройство IoT Edge. Среду выполнения можно развернуть на всех устройствах — от небольшого Raspberry Pi до технического сервера. Как только на устройстве будет настроена среда выполнения IoT Edge, вы можете начать развертывать в нее бизнес-логику из облака.

Дополнительные сведения о работе среды выполнения IoT Edge и ее компонентах см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре](iot-edge-runtime.md).

В этой статье перечислены действия, чтобы запустить среду выполнения Azure IoT Edge на Windows Server 2019 виртуальную машину, используя [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) предложение Azure Marketplace. Следуйте инструкциям в [установить среду выполнения Azure IoT Edge](how-to-install-iot-edge-windows.md) на Windows для использования с другими версиями.

> [!NOTE]
> Среда выполнения IoT Edge в Windows Server находится в [общедоступной предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-from-the-azure-marketplace"></a>Развертывание из Azure Marketplace
1.  Перейдите к [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) предложение Azure Marketplace или поиска «Windows Server» на [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Выберите **получить СЕЙЧАС** 
3.  В **план программного обеспечения**найдите «Контейнеры Windows Server 2019 Datacenter Server Core с» и затем выберите **Продолжить** в следующем окне.
    * Эти инструкции можно также использовать для других версий Windows Server с контейнерами
4.  Войдите на портал Azure, выберите **Создать** и следуйте указаниям мастера, чтобы развернуть виртуальную машину. 
    *   Если осуществляется в первый раз, поработав с виртуальной Машины, проще использовать пароль и включить в меню открытый входящий порт RDP и SSH. 
    *   При наличии ресурсоемкой рабочей нагрузки следует обновить размер виртуальной машины, добавив дополнительные ЦП и (или) память.
5.  После развертывания виртуальной машины настройте ее подключение к Центру Интернета вещей:
    1.  Скопируйте строку подключения с устройства IoT Edge, созданного в Центре Интернета вещей (выполните инструкции из руководства [Регистрация нового устройства Azure IoT Edge на портале Azure](how-to-register-device-portal.md), если вы не знаете, как это делать).
    1.  Выберите недавно созданный ресурс виртуальной машины на портале Azure и щелкните **Выполнить команду**.
    1.  Выберите **RunPowerShellScript** параметр
    1.  Скопируйте этот сценарий в командном окне строку подключения устройства: 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Выполните скрипт, чтобы установить среду выполнения Edge и задать строку подключения, выбрав **запуска**
    1.  После одну-две минуты вы увидите сообщение, что в среду выполнения Edge была установлена и успешно подготовлен.


## <a name="deploy-from-the-azure-portal"></a>Развертывание с помощью портала Azure
1. На портале Azure найдите «Windows Server» и выберите **Windows Server 2019 Datacenter** чтобы начать рабочий процесс создания виртуальной Машины. 
2. Из **выберите план программного обеспечения** выберите «Контейнеры Windows Server 2019 Datacenter Server Core с», а затем выберите **создать**
3. Полные инструкции шагу 5 раздела «Развертывание из Azure Marketplace» выше.

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
   1. Выполните следующую команду с Идентификатором, который вы скопировали.
    
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
   * Эта команда предложит ввести пароль, но вы можете добавить параметр `--admin-password` для задания более просто в сценарий
   * Образ Windows Server Core есть команда строка поддержка только с помощью удаленного рабочего стола, поэтому если вы хотите все возможности рабочего стола, не следует выбирать `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` как изображение

1. Задайте строку подключения к устройству (выполните инструкции из руководства [Регистрация нового устройства Azure IoT Edge с помощью Azure CLI](how-to-register-device-cli.md), если вы не знаете, как это делать):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда подготовлено устройство IoT Edge и установлена среда выполнения, вы можете [развернуть модули IoT Edge](how-to-deploy-modules-portal.md).

Если возникли проблемы с в среду выполнения Edge, установка должным образом, см. статью [Устранение неполадок](troubleshoot.md) страницы.

Чтобы обновить существующую установку до последней версии IoT Edge, см. раздел [Обновление управляющей программы безопасности и среды выполнения IoT Edge](how-to-update-iot-edge.md).

Дополнительные сведения об использовании виртуальных машин Windows в [документация по виртуальным машинам Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Если требуется подключиться по протоколу SSH в эту виртуальную Машину после завершения установки следуйте [установке OpenSSH для Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) руководство по с помощью удаленного рабочего стола или удаленный powershell.
