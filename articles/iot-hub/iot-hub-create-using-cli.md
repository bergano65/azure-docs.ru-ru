---
title: Создание Центра Интернета вещей с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как использовать команды Azure CLI, чтобы создать группу ресурсов, а затем создать центр Интернета вещей в группе ресурсов. Также Узнайте, как удалить концентратор.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889518"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Создание Центра Интернета вещей с помощью Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

В этой статье показано, как создать Центр Интернета вещей с помощью Azure CLI.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Выполнение входа и установка учетной записи Azure

Если вы запускаете Azure CLI локально вместо того, чтобы использовать Cloud Shell, необходимо войти в учетную запись Azure.

В командной строке запустите [команду login](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Следуйте инструкциям, чтобы выполнить аутентификацию с использованием кода и войти в учетную запись Azure через веб-браузер.

## <a name="create-an-iot-hub"></a>Создание центра Интернета вещей

Создайте группу ресурсов и добавьте Центр Интернета вещей с помощью Azure CLI.

1. Создайте Центр Интернета вещей в группе ресурсов. Используйте имеющуюся группу ресурсов либо выполните следующую [команду для создания группы ресурсов](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > В предыдущем примере создается группа ресурсов в расположении западной части США. Список доступных расположений можно просмотреть, выполнив эту команду: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Выполните в своей группе ресурсов следующую [команду для создания Центра Интернета вещей](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create), указав глобально уникальное имя своего Центра Интернета вещей:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Предыдущая команда создает Центр Интернета вещей в оплачиваемой ценовой категории S1. Дополнительные сведения см. на странице с [ценами на Центр Интернета вещей Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Удаление Центра Интернета вещей

Вы можете использовать Azure CLI, чтобы [удалить отдельный ресурс](https://docs.microsoft.com/cli/azure/resource), например Центр Интернета вещей, или группу ресурсов со всеми ресурсами, включая любые Центры Интернета вещей.

Чтобы [удалить Центр Интернета](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete) вещей, выполните следующую команду:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Чтобы [удалить группу ресурсов](https://docs.microsoft.com/cli/azure/group#az-group-delete) со всеми ресурсами, выполните следующую команду:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об использовании Центра Интернета вещей см. в следующих статьях:

* [Руководство разработчика для Центра Интернета вещей](iot-hub-devguide.md)
* [Создание Центра Интернета вещей с помощью портала Azure](iot-hub-create-through-portal.md)
