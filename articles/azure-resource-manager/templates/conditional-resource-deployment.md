---
title: Условное развертывание с помощью шаблонов
description: Описание условного развертывания ресурса в шаблоне Azure Resource Manager (шаблон ARM).
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 5650f7fb9f1483f2dc7059607732ecc68cbb7b9d
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934787"
---
# <a name="conditional-deployment-in-arm-templates"></a>Условное развертывание в шаблонах ARM

Иногда при необходимости можно развернуть ресурс в шаблоне Azure Resource Manager (шаблон ARM). Используйте `condition` элемент, чтобы указать, развернут ли ресурс. Этот элемент возвращает значение True или False. Если значение true, ресурс создан. Если значение false, ресурс не создан. Значение может применяться только ко всему ресурсу.

> [!NOTE]
> Условное развертывание не распространяется каскадом на [дочерние ресурсы](child-resource-name-type.md). Если требуется условно развернуть ресурс и его дочерние ресурсы, необходимо применить одно и то же условие к каждому типу ресурсов.

## <a name="new-or-existing-resource"></a>Новый или существующий ресурс

Условное развертывание можно использовать для создания нового ресурса или использования существующего. В следующем примере показано, как использовать `condition` для развертывания новой учетной записи хранения или использования существующей учетной записи хранения.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

Если для параметра `newOrExisting` задано значение **New**, условие принимает значение true. Учетная запись хранения будет развернута. Однако если параметр `newOrExisting` имеет значение **existing**, условие принимает значение false, а учетная запись хранения не развернута.

Полный пример шаблона с использованием элемента `condition` см. по адресу [VM with a new or existing Virtual Network, Storage, and Public IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) (Виртуальная машина с новой или существующей виртуальной сетью, хранилищем и общедоступным IP-адресом).

## <a name="allow-condition"></a>Разрешить условие

Можно передать значение параметра, указывающее, разрешено ли условие. В следующем примере выполняется развертывание SQL Server и при необходимости разрешены IP-адреса Azure.

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
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
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
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

Если вы используете функцию [ссылки](template-functions-resource.md#reference) или [списка](template-functions-resource.md#list) с ресурсом, который условно развернут, функция вычисляется, даже если ресурс не развернут. Если функция ссылается на несуществующий ресурс, возникает ошибка.

Используйте функцию [If](template-functions-logical.md#if) , чтобы убедиться, что функция вычисляется только для условий при развертывании ресурса. См. [функцию if](template-functions-logical.md#if) для примера шаблона, который использует `if` и `reference` с условно развернутым ресурсом.

Ресурс задается [как зависимый](define-resource-dependency.md) от условного ресурса точно так же, как любой другой ресурс. Если условный ресурс не развернут, Azure Resource Manager автоматически удаляет его из необходимых зависимостей.

## <a name="complete-mode"></a>Полный режим

Если вы развертываете шаблон с [полным режимом](deployment-modes.md) , а ресурс не развернут, так как `condition` принимает значение false, результат зависит от того, какая REST API версия используется для развертывания шаблона. Если вы используете более раннюю версию, чем 2019-05-10, ресурс **не удаляется**. В 2019-05-10 или более поздней версии ресурс **удаляется**. Последние версии Azure PowerShell и Azure CLI удаляют ресурс, если условие имеет значение false.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о модуле Microsoft Learn, охватывающем условное развертывание, см. в статье [управление комплексными облачными развертываниями с помощью расширенных функций шаблонов ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Рекомендации по созданию шаблонов см. в статье рекомендации по использованию [шаблонов ARM](template-best-practices.md).
* Сведения о создании нескольких экземпляров ресурса см. [в статье итерация ресурсов в шаблонах ARM](copy-resources.md).
