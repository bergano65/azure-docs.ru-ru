---
title: Использование высокой доступности Service Fabric Reliable тома в приложении Azure Service Fabric Mesh | Документация Майкрософт
description: Узнайте, как сохранить состояние в приложении "Сетка Azure Service Fabric" путем подключения тома надежного диска Service Fabric в контейнере с помощью Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: ashishnegi
manager: raunakpandya
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: b5e4ad30a65b25140cfb2c80dd15d8cd28fb827b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850859"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Подключение тома надежного диска высокой доступности Service Fabric в приложении "Сетка Azure Service Fabric" 
Распространенный способ сохранения состояния в приложениях-контейнерах — использовать удаленное хранилище, такое как хранилище файлов Azure, или базу данных, такую как Azure Cosmos DB. Это влечет за собой значительную сетевую задержку при доступе к удаленному хранилищу для чтения и записи.

В этой статье показано, как сохранить состояние на надежном диске высокой доступности Service Fabric, подключив том в контейнере приложения "Сетка Azure Service Fabric".
Надежный диск Service Fabric предоставляет тома для локальных операций чтения с репликацией записей в кластере Service Fabric для обеспечения высокой доступности. Это устраняет необходимость в сетевых вызовах для чтения и уменьшает задержку сети для операций записи. Если контейнер перезапустится или переместится на другой узел, новый экземпляр контейнера будет рассматривать прежний том как устарелый. Это одновременно обеспечивает и эффективность, и высокую доступность.

В этом примере приложение Counter содержит службу ASP.NET Core с веб-страницей, на которой в браузере отображается значение счетчика.

`counterService` периодически считывает значение счетчика из файла, увеличивает его и записывает обратно в файл. Этот файл хранится в папке, подключенной к тому на надежном диске Service Fabric.

## <a name="prerequisites"></a>Технические условия

Для выполнения этой задачи можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Чтобы использовать Azure CLI в рамках работы с этой статьей, убедитесь, что `az --version` возвращает по крайней мере версию `azure-cli (2.0.43)`.  Установите (или обновите) модуль расширения интерфейса командной строки службы "Сетка Azure Service Fabric", выполнив эти [инструкции](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в Azure и настройте подписку.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов, в которую будет развертываться приложение. Следующая команда создает группу ресурсов с именем `myResourceGroup` в расположении на востоке Соединенных Штатов Америки. Если вы измените имя группы ресурсов в приведенной ниже команде, не забудьте изменить его во всех последующих командах.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Развертывание шаблона

Следующая команда развертывает приложение Linux с помощью [шаблона counter.sfreliablevolume.linux.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Чтобы развернуть приложение Windows, используйте [шаблон counter.sfreliablevolume.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Имейте в виду, что развертывание больших образов контейнеров может занять больше времени.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Можно также просмотреть состояние развертывания с помощью команды:

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Обратите внимание на имя ресурса шлюза, который имеет тип ресурса `Microsoft.ServiceFabricMesh/gateways`. Он будет использоваться при получении общедоступного IP-адреса приложения.

## <a name="open-the-application"></a>Запуск приложения

Когда приложение будет успешно развернуто, получите IP-адрес ресурса шлюза для него. Используйте имя шлюза, которое вы записали в разделе, приведенном выше.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --gateway-name counterGateway
```

В выходных данных должно содержаться свойство `ipAddress`, которое является общедоступным IP-адресом для конечной точки службы. Откройте его в веб-браузере. Отобразится веб-страница со значением счетчика, которое обновляется каждую секунду.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Проверка доступности тома для приложения

Приложение создает файл `counter.txt` в томе, расположенном в папке `counter/counterService`. Этот файл содержит значение счетчика, отображаемое на веб-странице.

## <a name="delete-the-resources"></a>Удаление ресурсов

Регулярно удаляйте ресурсы, которые больше не используются в Azure. Чтобы удалить ресурсы, относящиеся к этому примеру, удалите группу ресурсов, в которой они были развернуты (при этом будут удалены все элементы, связанные с этой группой ресурсов), с помощью следующей команды:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите пример приложения с надежным производительным диском Service Fabric на [сайте GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Узнайте больше о модели ресурсов Service Fabric из раздела [Introduction to Service Fabric Resource Model](service-fabric-mesh-service-fabric-resources.md) (Общие сведения о модели ресурсов Service Fabric).
- Узнайте больше о службе "Сетка Service Fabric" из раздела [Что такое Сетка Service Fabric?](service-fabric-mesh-overview.md)
