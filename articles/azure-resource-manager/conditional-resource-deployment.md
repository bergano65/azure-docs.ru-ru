---
title: Условное развертывание с помощью шаблонов
description: Описание условного развертывания ресурса в шаблоне Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: ec2337686f92bb631ae4b08ce125eb576cd8bd69
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806479"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Условное развертывание в шаблонах диспетчер ресурсов

Иногда при необходимости можно развернуть ресурс в шаблоне. Используйте элемент `condition`, чтобы указать, развернут ли ресурс. Этот элемент возвращает значение True или False. Если значение true, ресурс создан. Если значение false, ресурс не создан. Значение может применяться только ко всему ресурсу.

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

Ресурс задается [как зависимый](resource-group-define-dependencies.md) от условного ресурса точно так же, как любой другой ресурс. Если условный ресурс не развернут, Azure Resource Manager автоматически удаляет его из необходимых зависимостей.

## <a name="condition-with-complete-mode"></a>Условие с полным режимом

Если вы развертываете шаблон с [полным режимом](deployment-modes.md) , а ресурс не развернут, так как условие принимает значение false, результат зависит от того, какая REST API версия используется для развертывания шаблона. Если вы используете более раннюю версию, чем 2019-05-10, ресурс **не удаляется**. В 2019-05-10 или более поздней версии ресурс **удаляется**. Последние версии Azure PowerShell и Azure CLI удаляют ресурс, если условие имеет значение false.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные рекомендации по созданию шаблонов см. в статье [Рекомендации по работе с шаблонами Azure Resource Manager](template-best-practices.md).
* Сведения о создании нескольких экземпляров ресурса см. [в разделе ресурс, свойство или итерация переменной в шаблонах Azure Resource Manager](resource-group-create-multiple.md).