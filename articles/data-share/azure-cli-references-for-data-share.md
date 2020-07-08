---
title: Ссылки Azure CLI для общей папки данных Azure
description: Целевая страница ссылки на Azure CLI для общей папки данных Azure
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84302646"
---
# <a name="azure-cli-for-azure-data-share"></a>Azure CLI для общей папки данных Azure

Интерфейс командной строки Azure ([Azure CLI](/cli/azure/what-is-azure-cli)) — это набор команд для создания ресурсов Azure и управления ими.  Она доступна для многих служб Azure, включая общую папку данных Azure.  Общая папка данных содержит более 65 различных команд!  Эти команды дают возможность эффективно работать со службой из командной строки.

## <a name="references-for-data-share"></a>Ссылки на общую папку данных

Все команды Azure CLI для общего ресурса данных Azure в настоящее время являются расширениями для Azure CLI.  Расширение предоставляет доступ к экспериментальным и предварительным командам.  Узнайте больше о расширениях из статьи [Использование расширений с Azure CLI](/cli/azure/azure-cli-extensions-overview).

|Справочник по Azure CLI |Описание:
|-|-|-|
| [AZ Share](/cli/azure/ext/datashare/datashare) | Все команды для управления общей папкой данных Azure.
| [AZ Общая учетная запись](/cli/azure/ext/datashare/datashare/account) | Команды для управления учетными записями общих ресурсов Azure.
| [AZ файл-потребитель](/cli/azure/ext/datashare/datashare/consumer) | Команды для потребителей для управления общей папкой данных Azure.
| [AZ файловый набор данных](/cli/azure/ext/datashare/datashare/dataset) | Команды для поставщиков для управления наборами данных общего ресурса Azure.
| [AZ поделиться приглашением](/cli/azure/ext/datashare/datashare/invitation) | Команды для потребителей, управляющих приглашениями на обмен данными Azure.
| [AZ файл поставщик-поделиться — подписка](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Команды для поставщиков, управляющих общими подписками на данные Azure.
| [AZ файловая синхронизация](/cli/azure/ext/datashare/datashare/synchronization)  | Команды для управления синхронизацией общего ресурса данных Azure.
| [AZ файл Синхронизация — Настройка](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Команды для поставщиков, позволяющие управлять параметрами синхронизации общего ресурса данных Azure.

## <a name="reference-examples"></a>Примеры ссылок

Примеры приведены для каждого справочника по Azure CLI. Хотя эти задачи также можно выполнить с помощью портала Azure, при использовании Azure CLI требуется одна командная строка.  Ниже представлено несколько блоков кода, которые помогут вам понять, насколько просто использовать Azure CLI.

Для работы с общей папкой данных Azure вам сначала потребуется группа ресурсов.  Группы ресурсов Azure можно без усилий создавать и администрировать с помощью Azure CLI.  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

Создавать учетную запись общей папки данных очень просто.

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>См. также раздел

* [Начните работу с Azure CLI](/cli/azure/get-started-with-azure-cli), чтобы узнать об установке и входе.

* Дополнительные ссылки на [ядро](/cli/azure/reference-index) и [расширение](/cli/azure/azure-cli-extensions-list) см. в документации по Azure CLI.
