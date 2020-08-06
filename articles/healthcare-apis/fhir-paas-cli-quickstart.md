---
title: Краткое руководство. Развертывание Azure API для FHIR с помощью Azure CLI
description: В этом кратком руководстве показано, как развернуть Azure API для FHIR в Azure с помощью Azure CLI.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: mihansen
ms.custom: devx-track-azurecli
ms.openlocfilehash: e2fdd4c4298946d943ee8a806ebc53966c8dd624
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500901"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Краткое руководство. Развертывание Azure API для FHIR с помощью Azure CLI

В этом кратком руководстве показано, как развернуть Azure API для FHIR в Azure с помощью Azure CLI.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-healthcareapis-extension"></a>Добавление расширения HealthcareAPIs

```azurecli-interactive
az extension add --name healthcareapis
```

Получите список команд для HealthcareAPIs:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Создание группы ресурсов Azure

Выберите имя группы ресурсов, которая будет содержать Azure API для FHIR, и создайте ее:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Развертывание Azure API для FHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Получение инструкции возможностей API для FHIR

Получите инструкцию возможностей из API FHIR:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите группу ресурсов, выполнив следующие действия.

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве показано, как развернуть Azure API для FHIR в своей подписке. Чтобы задать дополнительные параметры в Azure API для FHIR, см. соответствующее руководство.

>[!div class="nextstepaction"]
>[Дополнительные параметры в Azure API для FHIR](azure-api-for-fhir-additional-settings.md)
