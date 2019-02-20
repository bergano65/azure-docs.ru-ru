---
title: Запуск Azure IoT Edge на виртуальных машинах Ubuntu | Документация Майкрософт
description: Инструкции по настройке Azure IoT Edge на виртуальных машинах Ubuntu 16.04 из Azure Marketplace.
author: gregman
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: gregman
ms.openlocfilehash: 7ff7671425e2a2a5dbebe2d09cadb8ef71bc7c97
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55896632"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Запуск Azure IoT Edge на виртуальных машинах Ubuntu

Среда выполнения Azure IoT Edge превращает устройство в устройство IoT Edge. Среду выполнения можно развернуть на всех устройствах — от небольшого Raspberry Pi до технического сервера. Как только на устройстве будет настроена среда выполнения IoT Edge, вы можете начать развертывать в нее бизнес-логику из облака.

Дополнительные сведения о работе среды выполнения IoT Edge и ее компонентах см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре](iot-edge-runtime.md).

В этой статье приведены действия по запуску среды выполнения Azure IoT Edge на виртуальной машине Ubuntu 16.04 с помощью предварительно настроенного предложения Azure Marketplace [Azure IoT Edge on Ubuntu](http://aka.ms/azure-iot-edge-ubuntuvm). 

При первой загрузке Azure IoT Edge на виртуальной машине Ubuntu выполняется предварительная установка последней версии этой среды выполнения. Кроме того, выполняется сценарий настройки строки подключения, а затем среда выполнения перезапускается. Этот перезапуск можно инициировать удаленно на портале виртуальной машины Azure или с помощью командной строки Azure, что позволяет легко настроить и подключить устройство IoT Edge без запуска сеанса удаленного рабочего стола или SSH. Этот сценарий не начинает настройку строки подключения до полной установки клиента IoT Edge, так что автоматизировать этот процесс не нужно.

## <a name="deploy-from-the-azure-marketplace"></a>Развертывание из Azure Marketplace
1.  Перейдите к предложению [Azure IoT Edge on Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) или выполните поиск по запросу Azure IoT Edge on Ubuntu в [Azure Marketplace](https://azuremarketplace.microsoft.com/).
2.  Нажмите кнопку **Получить**, а затем в следующем диалоговом окне выберите **Продолжить**.
3.  Войдите на портал Azure, выберите **Создать** и следуйте указаниям мастера, чтобы развернуть виртуальную машину. 
    *   Если вы впервые тестируете виртуальную машину, проще использовать пароль и включить SSH в меню настройки общедоступного входящего порта. 
    *   При наличии ресурсоемкой рабочей нагрузки следует обновить размер виртуальной машины, добавив дополнительные ЦП и (или) память.
4.  После развертывания виртуальной машины настройте ее подключение к Центру Интернета вещей:
    1.  Скопируйте строку подключения с устройства IoT Edge, созданного в Центре Интернета вещей (выполните инструкции из руководства [Регистрация нового устройства Azure IoT Edge на портале Azure](how-to-register-device-portal.md), если вы не знаете, как это делать).
    1.  Выберите недавно созданный ресурс виртуальной машины на портале Azure и щелкните **Выполнить команду**.
    1.  Выберите параметр **RunShellScript**.
    1.  Выполните следующий сценарий в окне командной строки, указав свою строку подключения к устройству: `/etc/iotedge/configedge.sh “{device_connection_string}”`.
    1.  Выберите **Выполнить**.
    1.  Подождите несколько секунд и на экране отобразится сообщение об успешном выполнении, указывающее об успешной настройке строки подключения.


## <a name="deploy-from-the-azure-portal"></a>Развертывание на портале Azure
На портале Azure выполните поиск по запросу Azure IoT Edge и выберите **Ubuntu Server 16.04 LTS + Azure IoT Edge runtime**, чтобы начать создание виртуальной машины. После этого выполните шаги 3 и 4 из приведенного выше раздела "Развертывание из Azure Marketplace".

## <a name="deploy-from-azure-cli"></a>Развертывание с помощью Azure CLI
1.  Если вы впервые развертываете виртуальную машину с помощью CLI, необходимо включить программное развертывание в своей подписке Azure:
    1. Откройте предложение Marketplace [Azure IoT Edge on Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm).
    1. Нажмите кнопку **Получить** и в следующем диалоговом окне выберите **Продолжить**.
    1. Выберите **Хотите выполнить программное развертывание? Начните сейчас** в нижней части диалогового окна на портале.
    1. На странице **Настройка программного развертывания** нажмите кнопку **Включить**, а затем — **Сохранить**.
1.  Если вы используете Azure CLI на настольном компьютере, сначала выполните вход:

    ```azurecli-interactive
    az login
    ```
    
1.  Если у вас несколько подписок, выберите ту, которую необходимо использовать:
    1.  Отобразите список подписок:
    
       ```azurecli-interactive
       azure account list --output table
       ```
    
    1.  Скопируйте поле SubscriptionID подписки, которую необходимо использовать.
    1.  Выполните следующую команду, используя скопированный идентификатор:
    
       ```azurecli-interactive 
       az account set -s {SubscriptionId}
       ```
    
1.  Создайте группу ресурсов (или выберите уже имеющуюся на следующих этапах):

    ```azurecli-interactive
    az group create --name IoTEdgeResources --location westus2
    ```
    
1.  Создайте виртуальную машину:

    ```azurecli-interactive
    az vm create --resource-group IoTEdgeResources --name EdgeVM –image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
    ```

1.  Задайте строку подключения к устройству (выполните инструкции из руководства [Регистрация нового устройства Azure IoT Edge с помощью Azure CLI](how-to-register-device-cli.md), если вы не знаете, как это делать):

    ```azurecli-interactive
    az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
    ```

Если после завершения установки вы хотите подключиться к этой виртуальной машине по протоколу SSH, укажите общедоступный IP-адрес в следующей команде: `ssh azureuser@{publicIpAddress}`.


## <a name="next-steps"></a>Дополнительная информация

Теперь, когда подготовлено устройство IoT Edge и установлена среда выполнения, вы можете [развернуть модули IoT Edge](how-to-deploy-modules-portal.md).

Если вам не удается установить среду выполнения Edge, перейдите на страницу со сведениями об [устранении неполадок](troubleshoot.md).

Чтобы обновить существующую установку до последней версии IoT Edge, см. раздел [Обновление управляющей программы безопасности и среды выполнения IoT Edge](how-to-update-iot-edge.md).
