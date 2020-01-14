---
title: Создание поставщика ресурсов
description: Сведения о создании поставщика ресурсов и развертывание его настраиваемых типов ресурсов.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75648511"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Краткое руководство. созданию настраиваемого поставщика и развертыванию настраиваемых ресурсов

С помощью этого краткого руководства вы создадите собственного поставщика ресурсов и развернете для него настраиваемые типы ресурсов. Дополнительные сведения о предварительной версии настраиваемых поставщиков Azure см. в [этой статье](overview.md).

## <a name="prerequisites"></a>предварительные требования

Для работы с этим кратким руководством требуется вызвать операции REST. Существуют [различные способы отправки запросов REST](/rest/api/azure/). Если у вас нет средства для операций REST, установите [ARMClient](https://github.com/projectkudu/ARMClient). Эта программа командной строки с открытым исходным кодом, которая упрощает вызов API Azure Resource Manager.

## <a name="deploy-custom-provider"></a>Развертывание настраиваемого поставщика

Чтобы настроить настраиваемый поставщик, разверните [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) в подписке Azure.

После развертывания шаблона в вашей подписке будут следующие ресурсы:

* Приложение-функция с операциями для ресурсов и действий.
* Учетная запись хранения для хранения пользователей, которые создаются с помощью настраиваемого поставщика.
* Настраиваемый поставщик, который определяет настраиваемые типы ресурсов и действия. Он использует конечную точку приложения-функции для отправки запросов.
* Настраиваемый ресурс из настраиваемого поставщика.

Чтобы развернуть настраиваемый поставщик с помощью PowerShell, выполните следующую команду:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Вы также можете развернуть решение с помощью следующей кнопки:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Просмотр настраиваемого поставщика и ресурсов

На портале настраиваемый поставщик —это скрытый тип ресурсов. Чтобы убедиться, что поставщик ресурсов развернут, перейдите к группе ресурсов. Установите флажок **Показать скрытые типы**.

![Отображение скрытых типов ресурсов](./media/create-custom-provider/show-hidden.png)

Чтобы просмотреть развернутый тип настраиваемого ресурса, используйте операцию GET для типа ресурса.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

В ARMClient используйте следующую команду:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Вы получите такой ответ:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

## <a name="call-action"></a>Действие вызова

Настраиваемый поставщик также имеет действие с именем **ping**. Код, который обрабатывает запрос, реализуется в приложении-функции. Действие ping отправляет ответ с приветствием.

Чтобы отправить запрос проверки связи, используйте операцию POST для настраиваемого поставщика.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

В ARMClient используйте следующую команду:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Вы получите такой ответ:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Создание типа ресурсов

Чтобы создать настраиваемый тип ресурса, можно развернуть ресурс в шаблоне. Этот подход показан в шаблоне, развернутом в этом кратком руководстве. Вы также можете отправлять запрос PUT для типа ресурса.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

В ARMClient используйте следующую команду:

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Вы получите такой ответ:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

## <a name="next-steps"></a>Дальнейшие действия

Общие сведения о предварительной версии настраиваемых поставщиков Azure см. в [этой статье](overview.md).
