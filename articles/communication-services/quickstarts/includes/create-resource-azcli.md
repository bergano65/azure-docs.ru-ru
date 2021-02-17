---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: d70d53da48f41f0a6a37da6154d29696d3824325
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820013"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/dotnet/) бесплатно.

## <a name="create-azure-communication-resource"></a>Создание ресурса Служб коммуникации Azure

Чтобы создать ресурс Служб коммуникации Azure, [войдите в Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli), а затем выполните следующую команду:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Теперь можно настроить ресурс Служб коммуникации с помощью следующих параметров:

* Группа ресурсов
* Имя ресурса Служб коммуникации
* Географический регион, с которым будет связан ресурс

На следующем шаге можно назначить ресурсу теги. Теги можно использовать для упорядочения ресурсов в Azure. Дополнительные сведения о тегах см. в [документации по маркировке ресурсов](../../../azure-resource-manager/management/tag-resources.md).

## <a name="manage-your-communication-services-resource"></a>Управление ресурсом Служб коммуникации

Чтобы добавить теги в ресурс Служб коммуникации, выполните следующие команды:

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

Подробные сведения о дополнительных командах см. в статье об [az communication](https://docs.microsoft.com/cli/azure/ext/communication/communication).