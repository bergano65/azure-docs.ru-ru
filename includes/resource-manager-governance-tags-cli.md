---
title: включение файла
description: включение файла
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c1259584e91461865b0c7e7bbbd6aced1781827b
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246244"
---
Чтобы добавить два тега в группу ресурсов, используйте команду [az group create](/cli/azure/group):

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Предположим, вы хотите добавить третий тег. Выполните команду еще раз с новым тегом. Он будет добавлен к уже существующим.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Ресурсы не наследуют теги от группы ресурсов. Сейчас в группе ресурсов три тега, но у ресурсов нет ни одного. Чтобы добавить все теги из группы ресурсов к ресурсам из этой группы и сохранить существующие теги ресурсов, используйте следующий скрипт:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

Также можно применить теги группы ресурсов к ресурсам без сохранения существующих тегов:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

Для объединения нескольких значений в один тег используйте строку JSON.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Чтобы удалить все теги в группе ресурсов, используйте следующую команду:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
