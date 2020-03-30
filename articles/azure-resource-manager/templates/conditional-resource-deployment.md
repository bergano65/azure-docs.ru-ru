---
title: Условное развертывание с шаблонами
description: Описывает, как условно развернуть ресурс в шаблоне управления ресурсами Azure.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f170710118c0e3de6f3643b6216ed55b83b5c7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153426"
---
# <a name="conditional-deployment-in-arm-templates"></a>Условное развертывание в шаблонах ARM

Иногда требуется дополнительное развертывание ресурса в шаблоне Azure Resource Manager (ARM). Используйте `condition` элемент, чтобы указать, развертывается ли ресурс. Этот элемент возвращает значение True или False. Если значение true, ресурс создан. Если значение false, ресурс не создан. Значение может применяться только ко всему ресурсу.

## <a name="new-or-existing-resource"></a>Новый или существующий ресурс

Условное развертывание можно использовать для создания нового ресурса или использовать существующий ресурс. В следующем примере показано, как использовать условие для развертывания новой учетной записи хранилища или существующей учетной записи хранилища.

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

Когда параметр **newOrExisting** устанавливается на **новый,** условие оценивается как истинное. Учетная запись хранилища развернута. Однако, когда **newOrExisting** устанавливается к **существующему,** условие оценивается как ложное, а учетная запись хранилища не развернута.

Полный пример шаблона с использованием элемента `condition` см. по адресу [VM with a new or existing Virtual Network, Storage, and Public IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) (Виртуальная машина с новой или существующей виртуальной сетью, хранилищем и общедоступным IP-адресом).

## <a name="allow-condition"></a>Разрешить условие

Вы можете пройти в значении параметра, которое указывает, разрешено ли условие. В следующем примере развертывается сервер S'L и опционально позволяет использовать i-провайдеры Azure.

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

Для полного шаблона [см.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)

## <a name="runtime-functions"></a>Функции выполнения

Если вы используете функцию [ссылки](template-functions-resource.md#reference) или [списка](template-functions-resource.md#list) с условно развернутым ресурсом, функция оценивается, даже если ресурс не развернут. Вы получаете ошибку, если функция относится к ресурсу, который не существует.

Используйте функцию [if,](template-functions-logical.md#if) чтобы убедиться, что функция оценивается только для условий, когда ресурс развернут. Просифункцию [if](template-functions-logical.md#if) для шаблона образца, который использует if и ссылку с условно развернутым ресурсом.

Вы устанавливаете [ресурс, зависящий от](define-resource-dependency.md) условного ресурса точно так же, как и любой другой ресурс. Когда условный ресурс не развернут, менеджер ресурсов Azure автоматически удаляет его из требуемых зависимостей.

## <a name="condition-with-complete-mode"></a>Состояние с полным режимом

Если развертывать шаблон с [полным режимом](deployment-modes.md) и ресурс не развертывается, поскольку состояние оценивается как ложное, результат зависит от того, какую версию REST API использует для развертывания шаблона. Если вы используете версию раньше 2019-05-10, ресурс **не удаляется.** С 2019-05-10 или позже, ресурс **удаляется**. Последние версии Azure PowerShell и Azure CLI удаляют ресурс, когда состояние является ложным.

## <a name="next-steps"></a>Дальнейшие действия

* Рекомендации по созданию шаблонов можно узнать над [ими.](template-best-practices.md)
* Чтобы создать несколько экземпляров ресурса, см. [итерацию ресурсов в шаблонах ARM.](copy-resources.md)