---
title: 'Azure CLI: Развертывание сервера FHIR для Azure с открытым кодом — Azure API для Azure'
description: В этом кратком руководстве объясняется, как развернуть сервер FHIR Майкрософт для Azure с открытым кодом.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f8047ebeb8e47f609db79e3ac1235b5cd65a4fd4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2020
ms.locfileid: "84819979"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Краткое руководство. Развертывание сервера FHIR с открытым кодом с помощью Azure CLI

В этом кратком руководстве показано, как развернуть сервер FHIR&reg; с открытым кодом в Azure с помощью Azure CLI.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Создать группу ресурсов

Выберите имя группы ресурсов, которая будет содержать подготовленные ресурсы, и создайте ее:

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Развертывание шаблона

[Репозиторий GitHub](https://github.com/Microsoft/fhir-server) с сервером FHIR Майкрософт для Azure содержит шаблон, на основе которого можно развернуть все необходимые ресурсы. Разверните сервер с помощью следующего кода:

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>Проверка работы сервера FHIR

Получите инструкцию возможностей от сервера FHIR:

```console
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

Первый ответ сервера может поступить через минуту или около того.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите группу ресурсов, выполнив следующие действия:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве показано, как развернуть сервер FHIR Майкрософт для Azure с открытым кодом в своей подписке. Чтобы узнать, как получить доступ к API FHIR с помощью Postman, перейдите к руководству по Postman.
 
>[!div class="nextstepaction"]
>[Получение доступа к API FHIR с помощью Postman](access-fhir-postman-tutorial.md)