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
ms.openlocfilehash: db1af4f046bd8849fddee299e949d6edbdaae86a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66143401"
---
## <a name="access-the-virtual-machine"></a>Доступ к виртуальной машине

На следующих шагах в Azure Cloud Shell используется команда `az`. При желании на компьютер можно [установить Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) и выполнять команды локально.

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
