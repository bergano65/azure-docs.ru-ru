---
title: Учебник. добавление тегов в ресурсы в шаблоне Azure Resource Manager
description: Добавьте теги к ресурсам, развертываемым в шаблоне Azure Resource Manager. Теги позволяют логически упорядочивать ресурсы.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8b6ff50f7254a51bcdf37ecb0afd8f0041a2c5da
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177569"
---
# <a name="tutorial-add-tags-in-your-resource-manager-template"></a>Руководство по Добавление тегов в шаблон Resource Manager

В этом учебнике вы узнаете, как добавлять теги к ресурсам в шаблоне. [Теги](resource-group-using-tags.md) помогают логически упорядочивать ресурсы. Значения тегов отображаются в отчетах о затратах. Для выполнения инструкций из этого учебника требуется **8 минут**.

## <a name="prerequisites"></a>Предварительные требования

Советуем выполнить инструкции из [учебника по шаблонам быстрого запуска](template-tutorial-quickstart-template.md), но это необязательно.

Вам понадобится Visual Studio Code с расширением инструментов Resource Manager и либо Azure PowerShell, либо Azure CLI. Дополнительные сведения см. в разделе об [инструментах шаблона](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Проверка шаблона

Предыдущий шаблон развернул учетную запись хранения, план службы приложений и веб-приложение.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json)]

После развертывания этих ресурсов может потребоваться мониторинг затрат и поиск ресурсов, принадлежащих категории. Для решения этих задач можно добавить теги.

## <a name="add-tags"></a>Добавление тегов

Вы добавляете теги в ресурсы, чтобы использовать значения, помогающие определить их использование. Например, можно добавить теги, в которых перечислены среда и проект. Можно добавить теги, которые определяют центр затрат или группу, которой принадлежит ресурс. Добавьте любые значения, имеющие смысл для вашей организации.

В следующем примере показаны изменения шаблона. Скопируйте весь файл и замените шаблон на его содержимое.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json?range=1-118&highlight=46-52,64,78,100)]

## <a name="deploy-the-template"></a>Развертывание шаблона

Пора развернуть шаблон и просмотреть результаты.

Если вы еще не создали группу ресурсов, см. [этот раздел](template-tutorial-create-first-template.md#create-resource-group). В этом примере предполагается, что для переменной **templateFile** указан путь к файлу шаблона, как показано в [первом учебнике](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-the-deployment"></a>Проверка развертывания

Чтобы проверить развертывание, просмотрите группу ресурсов на портале Azure.

1. Войдите на [портале Azure](https://portal.azure.com).
1. В меню слева выберите **Группы ресурсов**.
1. Выберите группу ресурсов, в которую выполнено развертывание.
1. Выберите один из ресурсов, например ресурс учетной записи хранения. Вы увидите, что теперь он содержит теги.

   ![Отображение тегов](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы переходите к следующему учебнику, вам не нужно удалять группу ресурсов.

Если вы останавливаете работу сейчас, вам может потребоваться очистить развернутые ресурсы, удалив группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом учебнике вы добавили теги в ресурсы. В следующем учебнике вы узнаете, как использовать файлы параметров для упрощения передачи значений в шаблон.

> [!div class="nextstepaction"]
> [Использование файла параметров](template-tutorial-use-parameter-file.md)
