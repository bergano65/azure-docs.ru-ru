---
title: Учебник. Экспорт шаблона Azure Resource Manager из портала Azure
description: Узнайте, как использовать экспортированный шаблон для разработки шаблона.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6e4f246cac0ecc1ab5942e522595f59c3625db8f
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243209"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Руководство по использованию экспортированного шаблона из портала Azure

В рамках этой серии учебников вы создали шаблон для развертывания учетной записи хранения Azure. В следующих двух учебниках вы добавите *план службы приложений* и *веб-сайт*. Вместо создания шаблонов с нуля вы узнаете, как экспортировать шаблоны с портала Azure и как использовать примеры шаблонов из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/). Вы настроите эти шаблоны для своего варианта использования. В этом учебнике будет рассмотрен экспорт шаблонов и настройка результатов для шаблона. Это займет около **14 минут**.

## <a name="prerequisites"></a>Предварительные требования

Советуем выполнить инструкции из [учебника по выходным данным](template-tutorial-add-outputs.md), но это необязательно.

Вам понадобится Visual Studio Code с расширением инструментов Resource Manager и либо Azure PowerShell, либо Azure CLI. Дополнительные сведения см. в разделе об [инструментах шаблона](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Проверка шаблона

В конце предыдущего учебника шаблон содержал следующий код JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json)]

Этот шаблон хорошо подходит для развертывания учетных записей хранения, но вы можете добавить к нему больше ресурсов. Вы можете экспортировать шаблон из имеющегося ресурса, чтобы быстро получить JSON для этого ресурса.

## <a name="create-app-service-plan"></a>Создание плана службы приложений

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выберите **Создать ресурс**.
1. В поле **Поиск по Marketplace** введите **План службы приложений**, а затем выберите **План службы приложений**.  Не выбирайте параметр **План службы приложений (классический)** .
1. Нажмите кнопку **Создать**.
1. Введите:

    - **Подписка**. Выберите подписку Azure.
    - **Группа ресурсов**. Выберите **Создать** и укажите имя. Укажите имя группы ресурсов, отличное от того, которое вы использовали в этой серии учебников.
    - **Имя**: введите имя для плана службы приложений.
    - **Операционная система**: выберите **Linux**.
    - **Регион**: выберите расположение Azure. Например, **центральная часть США**.
    - **Ценовая категория**: чтобы снизить затраты, измените номер SKU на **Basic B1** (Базовый B1) (в разделе "Разработка и тестирование").

    ![Портал экспортирования шаблона Resource Manager](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Выберите **Просмотреть и создать**.
1. Нажмите кнопку **Создать**. Создание ресурса занимает несколько секунд.

## <a name="export-the-template"></a>Экспорт шаблона

1. Выберите **Перейти к ресурсу**.

    ![Переход к ресурсу](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Выберите **Экспортировать шаблон**.

    ![Экспорт шаблона Resource Manager](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   Функция экспорта шаблона принимает текущее состояние ресурса и создает шаблон для его развертывания. Экспорт шаблона может быть полезным для быстрого получения JSON, необходимого для развертывания ресурса.

1. Скопируйте определение **Microsoft.Web/serverfarms** и определение параметра в свой шаблон.

    ![Экспорт шаблона и экспортированный шаблон Resource Manager](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Обычно экспортированный шаблон подробнее, чем может потребоваться при создании шаблона. Например, объект SKU в экспортированном шаблоне имеет пять свойств. Этот шаблон работает, но вы можете просто использовать свойство **name**. Вы можете начать с экспортированного шаблона, а затем изменить его в соответствии со своими требованиями.

## <a name="revise-the-existing-template"></a>Изменение имеющегося шаблона

Экспортированный шаблон предоставляет большую часть необходимого кода JSON, но его нужно настроить для шаблона. Обратите особое внимание на различия в параметрах и переменных между своим шаблоном и экспортированным. Очевидно, что в процессе экспорта неизвестны параметры и переменные, которые вы уже определили в своем шаблоне.

В следующем примере выделены дополнения к вашему шаблону. Он содержит экспортированный код вместе с некоторыми изменениями. Во-первых, он изменяет имя параметра в соответствии соглашением об именовании. Во-вторых, он использует параметр расположения для местоположения плана службы приложения. В-третьих, он удаляет **name** внутри объекта **properties**, так как это значение является избыточным для свойства **name** на уровне ресурса.

Скопируйте весь файл и замените шаблон на его содержимое.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json?range=1-77&highlight=28-31,50-69)]

## <a name="deploy-the-template"></a>Развертывание шаблона

Для развертывания шаблона используйте Azure CLI или Azure PowerShell.

Если вы еще не создали группу ресурсов, см. [этот раздел](template-tutorial-create-first-template.md#create-resource-group). В этом примере предполагается, что для переменной **templateFile** указан путь к файлу шаблона, как показано в [первом учебнике](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Проверка развертывания

Чтобы проверить развертывание, просмотрите группу ресурсов на портале Azure.

1. Войдите на [портале Azure](https://portal.azure.com).
1. В меню слева выберите **Группы ресурсов**.
1. Выберите группу ресурсов, в которую выполнено развертывание.
1. В группе ресурсов содержится учетная запись хранения и план службы приложений.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы переходите к следующему учебнику, вам не нужно удалять группу ресурсов.

Если вы останавливаете работу сейчас, вам может потребоваться очистить развернутые ресурсы, удалив группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как экспортировать шаблон с портала Azure, а также как использовать экспортированный шаблон для разработки шаблона. Вы также можете использовать шаблоны быстрого запуска Azure для упрощения разработки шаблонов.

> [!div class="nextstepaction"]
> [Tutorial: Use Azure Quickstart templates](template-tutorial-quickstart-template.md) (Учебник. Использование шаблонов быстрого запуска)
