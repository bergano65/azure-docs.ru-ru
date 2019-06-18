---
title: включение файла
description: включение файла
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 8d1f0f6ae3ffc123f1ae8318a6fbce4a9a3024fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814776"
---
## <a name="access-the-virtual-machine"></a>Доступ к виртуальной машине

Далее используется Azure CLI в Azure Cloud Shell. При желании вы можете [установите Azure CLI](/cli/azure/install-azure-cli) на разработки машинного и выполнять команды локально.

Ниже показано, как настроить виртуальную машину Azure, чтобы разрешить доступ **SSH**. Представленные шаги предполагают, что имя, которое вы выбрали для ускорителя решения, — **contoso-simulation**. Замените это значение на имя вашего развертывания.

1. Список содержимого группы ресурсов, которая содержит ресурсы ускорителя решения, приведен ниже.

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Запишите имя виртуальной машины, общедоступный IP-адрес и группу безопасности сети — эти значения понадобятся вам позже.

1. Обновите группу безопасности сети, чтобы разрешить доступ SSH. Следующая команда предполагает, что имя группы сетевой безопасности, — **contoso-simulation-nsg**. Замените это значение на имя вашей группы безопасности сети.

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Включать доступ по протоколу SSH нужно только во время тестирования и разработки. Если доступ по протоколу SSH включен, [его нужно отключить при первой возможности](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Обновите пароль для учетной записи **azureuser** на виртуальной машине на известный вам пароль. Выберите свой пароль, если вы запускаете следующую команду.

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Найдите общедоступный IP-адрес вашей виртуальной машины. Следующая команда предполагает, что имя виртуальной машины — **vm-vikxv**. Замените это значение именем виртуальной машины, которое вы записали ранее.

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Запишите общедоступный IP-адрес вашей виртуальной машины.
