---
title: Режимы развертывания в Azure Resource Manager | Документация Майкрософт
description: В этой статье описывается, как с помощью Azure Resource Manager задать полный или пошаговый режим развертывания.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: c4347254df59c62085b2bfb195496bf479cf7b35
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344593"
---
# <a name="azure-resource-manager-deployment-modes"></a>Режимы развертывания в Azure Resource Manager

При развертывании имеющихся ресурсов можно указать, что развертывание является добавочным или полным обновлением.  Основное различие между этими двумя режимами заключается в том, как Resource Manager обрабатывает ресурсы, имеющиеся в группе ресурсов, которых нет в шаблоне. По умолчанию используется пошаговый режим.

## <a name="incremental-and-complete-deployments"></a>Добавочные и полные развертывания

При развертывании ресурсов:

* При использовании полного режима Resource Manager **удаляет** ресурсы, которые существуют в группе ресурсов, но не указаны в шаблоне.
* В пошаговом режиме Resource Manager **оставляет без изменения** ресурсы, которые существуют в группе ресурсов, но не указаны в шаблоне.

Для обоих режимов Resource Manager пытается создать все ресурсы, указанные в шаблоне. Если ресурс уже существует в группе ресурсов и его параметры не изменились, результаты операции останутся прежними. Если значения свойств ресурса были изменены, они обновятся в ресурсе. При попытке обновить расположение или тип имеющегося ресурса развертывание завершится ошибкой. Вместо этого вы можете развернуть новый ресурс нужного вам типа с необходимым расположением.

При повторном развертывании ресурса в инкрементном режиме укажите все значения свойств для ресурса, а не только те, которые вы обновляете. Если вы не укажете определенные свойства, Resource Manager интерпретирует это обновление как перезапись таких значений.

## <a name="example-result"></a>Пример результата

Чтобы понять различие между инкрементным и полным режимом, рассмотрим следующий сценарий.

**Группа ресурсов** содержит:

* ресурс А;
* ресурс Б;
* ресурс В;

**Шаблон** содержит:

* ресурс А;
* ресурс Б;
* ресурс Г.

При развертывании в **пошаговом** режиме группа ресурсов содержит:

* ресурс А;
* ресурс Б;
* ресурс В;
* ресурс Г.

При развертывании в **полном** режиме ресурс В будет удален. Группа ресурсов содержит:

* ресурс А;
* ресурс Б;
* ресурс Г.

## <a name="set-deployment-mode"></a>Установка режима развертывания

Чтобы установить режим развертывания при развертывании с помощью PowerShell, используйте параметр `Mode`.

```azurepowershell-interactive
New-AzureRmResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Чтобы установить режим развертывания при развертывании с помощью Azure CLI, используйте параметр `mode`.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

При использовании [связанного или вложенного шаблона](resource-group-linked-templates.md) необходимо присвоить свойству `mode` значение `Incremental`. Только шаблоны корневого уровня поддерживают режим полного развертывания.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

* Сведения о создании шаблонов Resource Manager см. в статье [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
* Сведения о развертывании ресурсов см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md).
* Чтобы просмотреть операции для поставщика ресурсов, ознакомьтесь с [Azure REST API](/rest/api/).
