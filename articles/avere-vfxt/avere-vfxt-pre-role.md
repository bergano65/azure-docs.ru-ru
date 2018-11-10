---
title: Создание роли Avere без контроллера. Avere vFXT для Azure
description: Метод создания необходимой роли RBAC без виртуальной машины контроллера кластера
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670031"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>Создание роли доступа для среды выполнения кластера Avere vFXT без контроллера

В этом документе показан метод создания роли доступа к узлу кластера из командной строки перед созданием виртуальной машины контроллера кластера. 

Чтобы создать роль доступа из контроллера кластера, обратитесь к разделу [Создание роли доступа к узлу кластера](avere-vfxt-deploy.md#create-the-cluster-node-access-role). Образ контроллера содержит файл прототипа роли. Можно обновить файл, указав идентификатор подписки, и использовать его для определения роли локально на виртуальной машине контроллера.

## <a name="create-an-azure-rbac-role"></a>Создание роли RBAC Azure

Система Avere vFXT использует [управление доступом на основе ролей](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC), чтобы авторизовать узлы кластера vFXT для выполнения необходимых задач.  

В рамках обычной операции кластера vFXT отдельные узлы vFXT должны считывать свойства ресурса Azure, управлять хранилищем, а также параметрами сетевого интерфейса других узлов. 

Эта роль используется только для узлов vFXT, а не для виртуальной машины контроллера кластера.  

Если вы хотите создать эту роль до контроллера, выполните следующие действия: 

1. Откройте Azure Cloud Shell на портале Azure или перейдите по адресу [https://shell.azure.com](https://shell.azure.com).

1. Переключитесь на свою подписку vFXT с помощью команды Azure CLI:

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. Чтобы скачать определение роли из образа marketplace и изменить его, используйте следующие команды. 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. Измените файл, добавив идентификатор своей подписки и удалив строку над ним. Сохраните файл как ``avere-cluster.json``.

   ![Текстовый редактор консоли, отображающий идентификатор подписки и выбранную строку, которую нужно удалить](media/avere-vfxt-edit-role.png)

5. Создайте роль:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

При создании кластера укажите имя роли (в этом случае `avere-cluster`). Сценарий для создания кластеров назначает роль для только что созданных узлов кластера. 

## <a name="sample-cluster-node-runtime-role-definition"></a>Пример определения роли для среды выполнения узла кластера

> [!IMPORTANT] 
> Пример определения был взят из предварительной версии продукта. Если версия, которую вы получаете с текущим дистрибутивом продукта, отличается, используйте эту версию.

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
    "NotActions": [],
    "Actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
    ],
    "DataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
    ]
}

```