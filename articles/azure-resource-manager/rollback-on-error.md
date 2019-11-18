---
title: Откат к ошибке при успешном развертывании
description: Укажите, что неудачное развертывание должно выполнить откат к успешному развертыванию.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 0978d2547e23a9ac6f920c76be3e877ec236d9ed
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150331"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Откат к ошибке при успешном развертывании

Если развертывание завершается ошибкой, вы можете автоматически повторно развернуть ресурс, успешно развернутый ранее, из журнала развертывания. Эта функция полезна, если вы получили известное хорошее состояние развертывания инфраструктуры и хотите вернуться к этому состоянию. Существует ряд предостережений и ограничений.

- Повторное развертывание выполняется точно так же, как было запущено ранее с теми же параметрами. Изменить параметры невозможно.
- Предыдущее развертывание выполняется с использованием [полного режима](./deployment-modes.md#complete-mode). Все ресурсы, не входящие в предыдущее развертывание, удаляются, а для всех конфигураций ресурсов устанавливается предыдущее состояние. Убедитесь, что вы полностью понимаете [режимы развертывания](./deployment-modes.md).
- Повторное развертывание влияет только на ресурсы, любые изменения данных не затрагиваются.
- Эту функцию можно использовать только при развертывании группы ресурсов, а не в подписке или на уровне группы управления. Дополнительные сведения о развертывании уровня подписки см. в статье [Создание групп ресурсов и ресурсов на уровне подписки](./deploy-to-subscription.md).
- Этот параметр можно использовать только с развертываниями корневого уровня. Повторное развертывание из вложенных шаблонов не поддерживается.

Для этого развертывания должны иметь уникальные имена, чтобы их можно было идентифицировать в журнале. Если у вас нет уникальных имен, текущее неудачное развертывание может перезаписать более раннее успешное развертывание, зафиксированное в журнале.

## <a name="powershell"></a>PowerShell

Чтобы выполнить повторно последнее успешное развертывание, добавьте параметр `-RollbackToLastDeployment` в качестве флага.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Чтобы выполнить повторно конкретное развертывание, используйте параметр `-RollBackDeploymentName` и укажите имя развертывания. Указанное развертывание должно быть успешным.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Интерфейс командной строки Azure

Чтобы выполнить повторно последнее успешное развертывание, добавьте параметр `--rollback-on-error` в качестве флага.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Чтобы выполнить повторно конкретное развертывание, используйте параметр `--rollback-on-error` и укажите имя развертывания. Указанное развертывание должно быть успешным.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>Интерфейс REST API

Чтобы повторно выполнить последнее успешное развертывание, если текущее развертывание завершается сбоем, используйте следующий код:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Чтобы повторно выполнить определенное развертывание, если текущее развертывание завершается сбоем, используйте следующий код:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Указанное развертывание должно быть успешным.

## <a name="next-steps"></a>Дополнительная информация

- Для безопасного развертывания службы в нескольких регионах обратитесь к статье о [диспетчере развертывания Azure](deployment-manager-overview.md).
- Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
- Сведения об определении параметров в шаблоне см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
- Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md).
