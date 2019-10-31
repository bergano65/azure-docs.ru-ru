---
title: Запуск Azure IoT Edge на виртуальных машинах Ubuntu | Документация Майкрософт
description: Инструкции по настройке Azure IoT Edge на виртуальных машинах Ubuntu 16.04 из Azure Marketplace.
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: fec39a9e788debcd9c3ac707a0431e268d87ed35
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146194"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Запуск Azure IoT Edge на виртуальных машинах Ubuntu

Среда выполнения Azure IoT Edge превращает устройство в устройство IoT Edge. Среду выполнения можно развернуть на всех устройствах — от небольшого Raspberry Pi до технического сервера. Как только на устройстве будет настроена среда выполнения IoT Edge, вы можете начать развертывать в нее бизнес-логику из облака.

Дополнительные сведения о работе среды выполнения IoT Edge и ее компонентах см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре](iot-edge-runtime.md).

В этой статье приведены действия по запуску среды выполнения Azure IoT Edge на виртуальной машине Ubuntu 16.04 с помощью предварительно настроенного предложения Azure Marketplace [Azure IoT Edge on Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm). 

При первой загрузке Azure IoT Edge на виртуальной машине Ubuntu выполняется предварительная установка последней версии этой среды выполнения. Кроме того, выполняется сценарий настройки строки подключения, а затем среда выполнения перезапускается. Этот перезапуск можно инициировать удаленно на портале виртуальной машины Azure или с помощью командной строки Azure, что позволяет легко настроить и подключить устройство IoT Edge без запуска сеанса удаленного рабочего стола или SSH. Этот сценарий не начинает настройку строки подключения до полной установки клиента IoT Edge, так что автоматизировать этот процесс не нужно.

## <a name="deploy-from-the-azure-marketplace"></a>Развертывание из Azure Marketplace
1.  Перейдите к предложению [Azure IoT Edge on Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) или выполните поиск по запросу Azure IoT Edge on Ubuntu в [Azure Marketplace](https://azuremarketplace.microsoft.com/).
2.  Нажмите кнопку **Получить**, а затем в следующем диалоговом окне выберите **Продолжить**.
3.  Войдите на портал Azure, выберите **Создать** и следуйте указаниям мастера, чтобы развернуть виртуальную машину. 
    *   Если вы впервые тестируете виртуальную машину, проще использовать пароль и включить SSH в меню настройки общедоступного входящего порта. 
    *   При наличии ресурсоемкой рабочей нагрузки следует обновить размер виртуальной машины, добавив дополнительные ЦП и (или) память.
4.  После развертывания виртуальной машины настройте ее подключение к Центру Интернета вещей:
    1.  Скопируйте строку подключения устройства с устройства IoT Edge, созданного в центре Интернета вещей (если вы не знакомы с этим процессом, вы можете воспользоваться [строкой подключения в портал Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal) процедуре.)
    1.  Выберите недавно созданный ресурс виртуальной машины на портале Azure и щелкните **Выполнить команду**.
    1.  Выберите параметр **RunShellScript**.
    1.  Выполните следующий сценарий в окне командной строки, указав свою строку подключения к устройству: `/etc/iotedge/configedge.sh "{device_connection_string}"`.
    1.  Выберите **Выполнить**.
    1.  Подождите несколько секунд и на экране отобразится сообщение об успешном выполнении, указывающее об успешной настройке строки подключения.


## <a name="deploy-from-the-azure-portal"></a>Развертывание из портал Azure
На портале Azure выполните поиск по запросу Azure IoT Edge и выберите **Ubuntu Server 16.04 LTS + Azure IoT Edge runtime**, чтобы начать создание виртуальной машины. После этого выполните шаги 3 и 4 из приведенного выше раздела "Развертывание из Azure Marketplace".

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
    
   1. Скопируйте поле SubscriptionID для подписки, которую вы хотите использовать.

   1. Задайте рабочую подписку с только что скопированным ИДЕНТИФИКАТОРом:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Создайте группу ресурсов (или выберите уже имеющуюся на следующих этапах):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Примите условия использования виртуальной машины. Если вы хотите сначала ознакомиться с условиями, выполните действия, описанные в разделе [развертывание в Azure Marketplace](#deploy-from-the-azure-marketplace).

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Создайте виртуальную машину:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Задайте строку подключения устройства (если вы не знакомы с этим процессом, можно следовать процедуре [получения строки подключения с помощью Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) процедуры):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Если после завершения установки вы хотите подключиться к этой виртуальной машине по протоколу SSH, укажите общедоступный IP-адрес в следующей команде: `ssh azureuser@{publicIpAddress}`.


## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда подготовлено устройство IoT Edge и установлена среда выполнения, вы можете [развернуть модули IoT Edge](how-to-deploy-modules-portal.md).

Если при правильной установке среды выполнения IoT Edge возникли проблемы, ознакомьтесь со страницей [устранения неполадок](troubleshoot.md) .

Чтобы обновить существующую установку до последней версии IoT Edge, см. раздел [Обновление управляющей программы безопасности и среды выполнения IoT Edge](how-to-update-iot-edge.md).

Если вы хотите открыть порты для доступа к виртуальной машине через SSH или другие входящие подключения, см. документацию по виртуальной машине Azure, посвященную [открытию портов и конечных точек для виртуальной машины Linux](../virtual-machines/linux/nsg-quickstart.md) .
