---
title: 'Руководство: добавление переменной в шаблон Azure Resource Manager'
description: Добавьте переменные в шаблон Azure Resource Manager, чтобы упростить синтаксис.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fb577c0c0f00da3735da98fc2e94f3610c9bd80d
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001468"
---
# <a name="tutorial-add-variables-to-your-resource-manager-template"></a>Руководство по добавлению переменных в шаблон Azure Resource Manager

В этом учебнике вы узнаете, как добавить переменную в шаблон. Переменные упрощают работу с шаблонами, позволяя записывать выражение один раз и повторно использовать его во всем шаблоне. Для выполнения инструкций из этого учебника требуется **7 минут**.

## <a name="prerequisites"></a>Предварительные требования

Советуем выполнить инструкции из [руководства о функциях](template-tutorial-add-functions.md), но это необязательно.

Вам понадобится Visual Studio Code с расширением инструментов Resource Manager и либо Azure PowerShell, либо Azure CLI. Дополнительные сведения см. в разделе об [инструментах шаблона](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Проверка шаблона

В конце предыдущего учебника шаблон содержал следующий код JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json)]

Параметр для имени учетной записи хранения сложен в использовании, так как необходимо указать уникальное имя. Если вы работали с предыдущими учениками в этой серии, вы, вероятно, устали подбирать уникальные имена. Эту проблему можно решить, добавив переменную, создающую уникальное имя для учетной записи хранения.

## <a name="use-variable"></a>Использование переменной

В следующем примере показаны изменения для добавления переменной в шаблон, создающей уникальное имя учетной записи хранения. Скопируйте весь файл и замените шаблон на его содержимое.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json?range=1-47&highlight=5-9,29-31,36)]

Обратите внимание, что он включает переменную с именем **uniqueStorageName**. Эта переменная использует четыре функции для создания строкового значения.

Вы уже знакомы с функцией [parameters](resource-group-template-functions-deployment.md#parameters), поэтому мы не будем ее изучать.

Вы также знакомы с функцией [resourceGroup](resource-group-template-functions-resource.md#resourcegroup). В этом случае вы получаете свойство **id** вместо свойства **location**, как показано в предыдущем учебнике. Свойство **id** возвращает полный идентификатор группы ресурсов, включая идентификатор подписки и имя группы ресурсов.

Функция [uniqueString](resource-group-template-functions-string.md#uniquestring) создает значение хэша из 13 символов. Возвращаемое значение определяется передаваемыми параметрами. В этом учебнике идентификатор группы ресурсов используется в качестве входных данных для хэш-значения. Это означает, что можно развернуть этот шаблон в разных группах ресурсов и получить другое уникальное строковое значение. Однако при развертывании в той же группе ресурсов вы получаете то же значение.

Функция [concat](resource-group-template-functions-string.md#concat) принимает значения и объединяет их. Для этой переменной она принимает строку из параметра и из функции uniqueString и объединяет эти строки в одну.

Параметр **storagePrefix** позволяет передавать префикс, который помогает в определении учетных записей хранения. Вы можете создать собственное соглашение об именовании, которое упростит определение учетных записей хранения в длинном списке ресурсов после развертывания.

Наконец, обратите внимание, что в качестве имени хранилища теперь задана переменная, а не параметр.

## <a name="deploy-the-template"></a>Развертывание шаблона

Давайте развернем шаблон. Развернуть этот шаблон проще, чем предыдущие, так как вы предоставляете только префикс для имени хранилища.

Если вы еще не создали группу ресурсов, см. [этот раздел](template-tutorial-create-first-template.md#create-resource-group). В этом примере предполагается, что для переменной **templateFile** указан путь к файлу шаблона, как показано в [первом учебнике](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-the-deployment"></a>Проверка развертывания

Чтобы проверить развертывание, просмотрите группу ресурсов на портале Azure.

1. Войдите на [портале Azure](https://portal.azure.com).
1. В меню слева выберите **Группы ресурсов**.
1. Выберите группу ресурсов, в которую выполнено развертывание.
1. Вы увидите, что ресурс учетной записи хранения развернут. Имя учетной записи хранения — **store** и строка случайных символов.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы переходите к следующему учебнику, вам не нужно удалять группу ресурсов.

Если вы останавливаете работу сейчас, вам может потребоваться очистить развернутые ресурсы, удалив группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В рамках этого учебника вы добавили переменную, которая создает уникальное имя для учетной записи хранения. При работе со следующим учебником вы вернете значение из развернутой учетной записи хранения.

> [!div class="nextstepaction"]
> [Добавление выходных данных](template-tutorial-add-outputs.md)
