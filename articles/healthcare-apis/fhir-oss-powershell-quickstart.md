---
title: PowerShell. Развертывание сервера FHIR для Azure — Azure API для FHIR
description: В этом кратком руководстве объясняется, как развернуть сервер FHIR Майкрософт с открытым кодом с помощью PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: c133a309cc85ffcfb69be2ae7bbb614cbb540f2e
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847148"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Краткое руководство. Развертывание сервера FHIR с открытым кодом с помощью PowerShell

Из этого краткого руководства вы узнаете, как развернуть сервер FHIR Майкрософт для Azure с открытым кодом с помощью PowerShell.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Выберите имя группы ресурсов, которая будет содержать подготовленные ресурсы, и создайте ее:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>Развертывание шаблона сервера FHIR

[Репозиторий GitHub](https://github.com/Microsoft/fhir-server) с сервером FHIR Майкрософт для Azure содержит шаблон, на основе которого можно развернуть все необходимые ресурсы. Разверните его с помощью следующего кода:

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>Проверка работы сервера FHIR

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

Первый ответ сервера может поступить через минуту или около того.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите группу ресурсов, выполнив следующие действия:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого руководства вы развернули сервер FHIR Майкрософт для Azure с открытым кодом в своей подписке. Чтобы узнать, как получить доступ к API FHIR с помощью Postman, перейдите к руководству по Postman.
 
>[!div class="nextstepaction"]
>[Получение доступа к API FHIR с помощью Postman](access-fhir-postman-tutorial.md)
