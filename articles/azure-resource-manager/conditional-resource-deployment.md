---
title: Условное развертывание с помощью шаблонов Azure Resource Manager
description: Описание условного развертывания ресурса в шаблоне Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 88f8b6a8dcce0e498a7b81b8741072bcf4cfcad8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259513"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Условное развертывание в шаблонах диспетчер ресурсов

Иногда при необходимости можно развернуть ресурс в шаблоне. Используйте элемент `condition` , чтобы указать, развернут ли ресурс. Этот элемент возвращает значение True или False. Если значение true, ресурс создан. Если значение false, ресурс не создан. Значение может применяться только ко всему ресурсу.

## <a name="new-or-existing-resource"></a>Новый или существующий ресурс

Условное развертывание можно использовать для создания нового ресурса или использования существующего. В следующем примере показано, как использовать условие для развертывания новой учетной записи хранения или использования существующей учетной записи хранения.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Если параметр **неворексистинг** имеет значение **New**, условие принимает значение true. Учетная запись хранения будет развернута. Однако если **неворексистинг** имеет значение **existing**, условие принимает значение false, а учетная запись хранения не развернута.

Полный пример шаблона с использованием элемента `condition` см. по адресу [VM with a new or existing Virtual Network, Storage, and Public IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) (Виртуальная машина с новой или существующей виртуальной сетью, хранилищем и общедоступным IP-адресом).

## <a name="allow-condition"></a>Разрешить условие

Можно передать значение параметра, указывающее, разрешено ли условие. В следующем примере выполняется развертывание SQL Server и при необходимости разрешены IP-адреса Azure.

```json
{
    "type": "Microsoft.Sql/servers",
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
    },
    "resources": [
        {
            "condition": "[parameters('allowAzureIPs')]",
            "type": "firewallRules",
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
            ],
            "properties": {
                "endIpAddress": "0.0.0.0",
                "startIpAddress": "0.0.0.0"
            }
        }
    ]
}
```

Полный шаблон см. на странице [логический сервер SQL Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Функции среды выполнения

Если вы используете функцию [ссылки](resource-group-template-functions-resource.md#reference) или [списка](resource-group-template-functions-resource.md#list) с ресурсом, который условно развернут, функция вычисляется, даже если ресурс не развернут. Если функция ссылается на несуществующий ресурс, возникает ошибка.

Используйте функцию [If](resource-group-template-functions-logical.md#if) , чтобы убедиться, что функция вычисляется только для условий при развертывании ресурса. См. [функцию if](resource-group-template-functions-logical.md#if) для примера шаблона, который использует оператор If и ссылку с условно развернутым ресурсом.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные рекомендации по созданию шаблонов см. в статье [Рекомендации по работе с шаблонами Azure Resource Manager](template-best-practices.md).
* Сведения о создании нескольких экземпляров ресурса см. [в разделе ресурс, свойство или итерация переменной в шаблонах Azure Resource Manager](resource-group-create-multiple.md).