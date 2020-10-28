---
title: Включить файл
description: Включить файл
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 817c41a969f03ad04d372c516a16ef6b770f3e18
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755778"
---
## <a name="access-the-virtual-machine"></a>Доступ к виртуальной машине

В следующих шагах используется Azure CLI в Azure Cloud Shell. При желании можно [установить Azure CLI](/cli/azure/install-azure-cli) на компьютере разработки и выполнить команды локально.

Ниже показано, как настроить виртуальную машину Azure, чтобы разрешить доступ **SSH** . Представленные шаги предполагают, что имя, которое вы выбрали для ускорителя решения, — **contoso-simulation** . Замените это значение на имя вашего развертывания.

1. Список содержимого группы ресурсов, которая содержит ресурсы ускорителя решения, приведен ниже.

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Запишите имя виртуальной машины, общедоступный IP-адрес и группу безопасности сети — эти значения понадобятся вам позже.

1. Обновите группу безопасности сети, чтобы разрешить доступ SSH. Следующая команда предполагает, что имя группы сетевой безопасности, — **contoso-simulation-nsg** . Замените это значение на имя вашей группы безопасности сети.

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Включать доступ по протоколу SSH нужно только во время тестирования и разработки. Если вы включили протокол SSH, [то следует отключить его снова как можно скорее](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Обновите пароль для учетной записи **azureuser** на виртуальной машине на известный вам пароль. Выберите свой пароль, если вы запускаете следующую команду.

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Найдите общедоступный IP-адрес вашей виртуальной машины. Следующая команда предполагает, что имя виртуальной машины — **vm-vikxv** . Замените это значение именем виртуальной машины, которое вы записали ранее.

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Запишите общедоступный IP-адрес вашей виртуальной машины.
