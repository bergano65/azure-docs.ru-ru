---
title: Создание нескольких экземпляров ресурса с помощью Azure Resource Manager | Документация Майкрософт
description: Узнайте, как создать шаблон Azure Resource Manager для создания нескольких экземпляров ресурса Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/10/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 63a18a6ae0ee4c6e0a01bd7ac4a26a4fb89746c2
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419494"
---
# <a name="tutorial-create-multiple-resource-instances-using-resource-manager-templates"></a>Руководство. Создание нескольких экземпляров ресурса с помощью шаблонов Resource Manager

Узнайте, как выполнить итерацию в шаблоне Azure Resource Manager для создания нескольких экземпляров ресурса Azure. В предыдущем руководстве вы изменили существующий шаблон, чтобы создать зашифрованную учетную запись хранилища Azure. В этом руководстве вы измените тот же шаблон для создания трех экземпляров учетной записи хранения.

> [!div class="checklist"]
> * Открытие шаблона быстрого запуска
> * Изменение шаблона
> * Развертывание шаблона

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/).
* Расширение средств Resource Manager. Для установки см. раздел [Создание шаблона Azure Resource Manager c помощью Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблон, используемый в этом кратком руководстве, называется [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Создание стандартной учетной записи хранения). Шаблон определяет ресурс учетной записи службы хранилища Azure.

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл в качестве **azuredeploy.json** на локальном компьютере.

## <a name="edit-the-template"></a>Изменение шаблона

Цель данного руководства — использовать итерацию ресурсов для создания трех учетных записей хранения.  С помощью примера шаблона можно создать только одну учетную запись хранения. 

Внесите следующие четыре изменения в Visual Studio Code.

![Создание нескольких экземпляров с помощью Azure Resource Manager](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Добавьте элемент `copy` к определению ресурса учетной записи хранения. В элементе копирования укажите число итераций и имя для этого цикла. Значение count должно быть положительным целым числом не больше 800.
2. Функция `copyIndex()` возвращает текущую итерацию в цикле. `copyIndex()` отсчитывается, начиная с нуля. Чтобы сместить значение индекса, можно передать нужное значение в функцию copyIndex(). Например, *copyIndex(1)*.
3. Удалите элемент **Переменные**, поскольку он больше не используется.
4. Удалите элемент **Выходные данные**.

Завершенный шаблон выглядит следующим образом.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

Дополнительные сведения о создании нескольких экземпляров см. в разделе [Развертывание нескольких экземпляров ресурса или свойства в шаблонах Azure Resource Manager](./resource-group-create-multiple.md).

## <a name="deploy-the-template"></a>Развертывание шаблона

Дополнительные сведения о [развертывании шаблона](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) см. в кратком руководстве по Visual Studio Code.

Чтобы получить список всех трех учетных записей хранения, опустите параметр --name.

# <a name="clitabcli"></a>[ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](#tab/CLI)
```cli
az storage account list --resource-group <ResourceGroupName>
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzureRmStorageAccount -ResourceGroupName <ResourceGroupName>
```

---

Сравните имена учетных записей хранения с определением имени в шаблоне.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать несколько экземпляров учетной записи хранения. На данный момент вы создали одну учетную запись хранения или несколько экземпляров учетной записи хранения. С помощью следующего руководства вы разработаете шаблон с несколькими ресурсами и несколькими типами ресурсов. Некоторые ресурсы обладают зависимыми ресурсами.

> [!div class="nextstepaction"]
> [Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
