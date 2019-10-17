---
title: Ошибки в Azure, связанные с родительским ресурсом | Документация Майкрософт
description: Описание способов устранения ошибок при работе с родительским ресурсом в шаблоне Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 197554e16e28b4928cab351838f00e1631c269fd
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390235"
---
# <a name="resolve-errors-for-parent-resources"></a>Устранение ошибок, связанных с родительскими ресурсами

В этой статье приведены ошибки, которые могут возникнуть при развертывании ресурсов, зависящих от родительского ресурса.

## <a name="symptom"></a>Симптом

При развертывании ресурса, являющегося дочерним для другого ресурса, может появиться следующая ошибка:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Причина:

Если один ресурс является дочерним для другого ресурса, перед созданием дочернего ресурса должен существовать его родительский ресурс. Имя дочернего ресурса определяет соединение с родительским ресурсом. Формат имени дочернего ресурса: `<parent-resource-name>/<child-resource-name>` Например, база данных SQL может быть определена следующим образом.

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Если вы развертываете сервер и базу данных в одном шаблоне, но не определяете зависимость от сервера, база данных может быть развернута раньше сервера. 

Если родительский ресурс уже существует и не развертывается в том же шаблоне, вы получаете эту ошибку, если Resource Manager не может связать дочерний ресурс с родительским. Эта ошибка может возникнуть, если дочерний ресурс имеет неправильный формат или дочерний ресурс развернут в группе ресурсов, которая отличается от группы родительского ресурса.

## <a name="solution"></a>Решение

Чтобы устранить эту ошибку при развертывании родительских и дочерних ресурсов в одном шаблоне, укажите зависимость.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Чтобы устранить эту ошибку в случае, когда родительский ресурс был ранее развернут в другом шаблоне, не устанавливайте зависимость. Вместо этого разверните дочерний ресурс в ту же группу ресурсов и укажите имя родительского ресурса.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlServerName": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2014-04-01",
            "type": "Microsoft.Sql/servers/databases",
            "location": "[resourceGroup().location]",
            "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
            "properties": {
                "collation": "SQL_Latin1_General_CP1_CI_AS",
                "edition": "Basic"
            }
        }
    ],
    "outputs": {}
}
```

Дополнительные сведения см. в статье [Определение порядка развертывания ресурсов в шаблонах Azure Resource Manager](resource-group-define-dependencies.md).