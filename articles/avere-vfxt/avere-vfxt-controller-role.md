---
title: Настраиваемая роль доступа контроллера в Avere vFXT для Azure
description: Создание настраиваемой роли доступа для контроллера кластера Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 2a0f4a628764aaa561a5567d3435a42da804a994
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417846"
---
# <a name="customized-controller-access-role"></a>Настраиваемая роль доступа для контроллера

Контроллер кластера Avere vFXT для Azure использует управляемый идентификатор и управление доступом на основе ролей (RBAC), чтобы позволить ему создавать кластер и управлять им. 

По умолчанию контроллеру кластера назначена [встроенная роль владельца](../role-based-access-control/built-in-roles.md#owner). Кроме того, доступ к контроллеру ограничен его группой ресурсов, он не может изменять элементы вне группы ресурсов кластера.

В этой статье объясняется, как создать собственную роль доступа для контроллера кластера вместо использования настроек по умолчанию. 

## <a name="edit-the-role-prototype"></a>Изменение роли прототипа

Начните с прототипа роли, которая доступна в <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt>.

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/read",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

Добавьте идентификатор подписки для развертывания Avere vFXT для Azure в операторе AssignableScopes. Настройте имя и добавьте или измените определения по мере необходимости. 

Будьте внимательны при ограничении разрешений. Создание кластера может завершиться сбоем, если у контроллера нет соответствующего доступа. 

Для получения справки о том, какие разрешения необходимы контроллеру для создания кластера, см. раздел [Открытие запроса в службу поддержки для Avere vFXT](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt). 

Сохраните свое определение роли в виде JSON-файла. 

## <a name="define-the-role"></a>Определение роли 

Чтобы добавить пользовательское определение роли в подписку, выполните следующие действия. 

1. Откройте Azure Cloud Shell на портале Azure или перейдите по адресу [https://shell.azure.com](https://shell.azure.com).

1. Переключитесь на свою подписку vFXT с помощью команды Azure CLI:

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. Создайте роль:

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   Используйте имя файла и путь вместо ```/avere-contributor-custom.json``` в этом примере. 

Сохранение выходных данных команды определения ролей ​​содержит идентификатор роли, который необходимо предоставить в шаблон создания кластера. 

## <a name="find-the-role-id"></a>Поиск идентификатора роли

Для шаблона развертывания Avere vFXT необходим глобальный уникальный идентификатор роли (GUID), чтобы назначить контроллеру настраиваемую роль. 

Роль GUID являет собою 32-символьную строку в этой форме. 8e3af657-a8ff-443c-a75c-2fe8c4bcb635

Чтобы найти свою роль GUID, используйте следующую команду с именем роли в параметре ```--name```.

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
При развертывании Avere vFXT для Azure введите эту строку в поле **Avere cluster create role ID** (Идентификатор роли создания кластера Avere).

## <a name="next-steps"></a>Дальнейшие действия

Прочтите инструкции по развертыванию Avere vFXT для Azure [в этой статье](avere-vfxt-deploy.md)
