---
title: 'Руководство: использование файла параметров для развертывания шаблона Azure Resource Manager'
description: Используйте файлы параметров, которые содержат значения, используемые для развертывания шаблона Azure Resource Manager.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fa29ea3d2f6edbbb016ce5c0c74415a5e765e85a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177550"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>Руководство по использованию файлов параметров для развертывания шаблона Resource Manager

В этом учебнике вы узнаете, как использовать [файлы параметров](resource-manager-parameter-files.md) для хранения значений, передаваемых во время развертывания. В предыдущих учебниках вы использовали встроенные параметры с помощью команды развертывания. Этот подход сработал для тестирования шаблона, но при автоматизации развертываний может быть проще передать набор значений для вашей среды. Файлы параметров упрощают упаковку значений параметров для конкретной среды. В этом учебнике вы создадите файлы параметров для среды разработки и рабочей среды. Это занимает около **12 минут**.

## <a name="prerequisites"></a>Предварительные требования

Советуем выполнить инструкции из [руководства о тегах](template-tutorial-add-tags.md), но это необязательно.

Вам понадобится Visual Studio Code с расширением инструментов Resource Manager и либо Azure PowerShell, либо Azure CLI. Дополнительные сведения см. в разделе об [инструментах шаблона](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Проверка шаблона

Шаблон содержит множество параметров, которые можно указать во время развертывания. В конце предыдущего учебника шаблон выглядел следующим образом:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json)]

Этот шаблон хорошо работает, но теперь вам нужно упростить управление параметрами, передаваемыми для шаблона.

## <a name="add-parameter-files"></a>Добавление файлов параметров

Файлы параметров — это JSON-файлы со структурой, аналогичной шаблону. В файле укажите значения параметров, которые необходимо передать во время развертывания.

В VS Code создайте файл со следующим содержимым. Сохраните файл с именем **azuredeploy.parameters.dev.json**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json)]

Это файл параметров для среды разработки. Обратите внимание, что он использует Standard_LRS для учетной записи хранения, именует ресурсы с использованием префикса **dev** и задает для тега **Environment** значение **Dev**.

Опять же, создайте файл со следующим содержимым. Сохраните файл с именем **azuredeploy.parameters.prod.json**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json)]

Это файл параметров для рабочей среды. Обратите внимание, что он использует Standard_GRS для учетной записи хранения, именует ресурсы с применением префикса **contoso** и задает для тега **Environment** значение **Production**. В реальной рабочей среде также необходимо использовать службу приложений с номером SKU, отличным от "Бесплатный", но мы продолжим использовать его в рамках этого учебника.

## <a name="deploy-the-template"></a>Развертывание шаблона

Для развертывания шаблона используйте либо Azure CLI, либо Azure PowerShell.

Для окончательной проверки шаблона создадим две группы ресурсов: для среды разработки и для рабочей среды.

Сначала мы выполним развертывание в среде разработки.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az group deployment create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

Теперь мы выполним развертывание в рабочей среде.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az group deployment create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

## <a name="verify-the-deployment"></a>Проверка развертывания

Чтобы проверить развертывание, просмотрите группы ресурсов на портале Azure.

1. Войдите на [портале Azure](https://portal.azure.com).
1. В меню слева выберите **Группы ресурсов**.
1. Вы увидите две новые группы ресурсов, развернутые при работе с этим учебником.
1. Выберите любую группу ресурсов и просмотрите развернутые ресурсы. Обратите внимание, что они соответствуют значениям, указанным в файле параметров для этой среды.

## <a name="clean-up-resources"></a>Очистка ресурсов

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Фильтровать по имени** введите имя группы ресурсов. Если вы поработали со всеми учебниками в этой серии, у вас есть три группы ресурсов для удаления — myResourceGroup, myResourceGroupDev и myResourceGroupProd.
3. Выберите имя группы ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

Поздравляем, вы завершили ознакомление с развертыванием шаблонов в Azure. Оставьте комментарии и предложения для нас в разделе отзывов. Спасибо!

Вы можете перейти к более сложным понятиям шаблонов. В следующем учебнике подробно рассматривается использование справочной документации по шаблонам для определения развертываемых ресурсов.

> [!div class="nextstepaction"]
> [Руководство. Создание шаблона Azure Resource Manager для развертывания зашифрованной учетной записи хранения](resource-manager-tutorial-create-encrypted-storage-accounts.md)
