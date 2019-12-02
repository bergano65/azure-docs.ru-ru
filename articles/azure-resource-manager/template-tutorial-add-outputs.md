---
title: Учебник. Добавление выходных данных в шаблон
description: Добавьте выходные данные в шаблон Azure Resource Manager, чтобы упростить синтаксис.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 654d3f7cbf6362d982549c86e6f54fea1e890cfc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405997"
---
# <a name="tutorial-add-outputs-to-your-resource-manager-template"></a>Руководство по добавлению выходных данных в шаблон Azure Resource Manager

В этом учебнике вы узнаете, как вернуть значение из шаблона. Если требуется значение из развернутого ресурса, используются выходные данные. Для выполнения инструкций из этого учебника требуется **7 минут**.

## <a name="prerequisites"></a>Предварительные требования

Советуем выполнить инструкции из [учебника по переменным](template-tutorial-add-variables.md), но это необязательно.

Вам понадобится Visual Studio Code с расширением инструментов Resource Manager и либо Azure PowerShell, либо Azure CLI. Дополнительные сведения см. в разделе об [инструментах шаблона](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Проверка шаблона

В конце предыдущего учебника шаблон содержал следующий код JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json)]

Он развертывает учетную запись хранения, но не возвращает никаких сведений о ней. Возможно, потребуется записать свойства из нового ресурса, чтобы они были доступны позже для справки.

## <a name="add-outputs"></a>Добавление выходных данных

Выходные данные можно использовать для возврата значений из шаблона. Например, может быть полезно получить конечные точки для новой учетной записи хранения.

В следующем примере демонстрируется изменение шаблона для добавления выходного значения. Скопируйте весь файл и замените шаблон на его содержимое.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json?range=1-53&highlight=47-52)]

Обратите внимание на некоторые важные элементы добавленного выходного значения.

В качестве типа возвращаемого значения устанавливается **object**. Это означает, что возвращается объект JSON.

Для получения состояния среды выполнения учетной записи хранения используется функция [reference](resource-group-template-functions-resource.md#reference). Чтобы получить состояние среды выполнения ресурса, необходимо передать имя или идентификатор ресурса. В этом случае вы используете ту же переменную, которая использовалась для создания имени учетной записи хранения.

Наконец, значение возвращает свойство **primaryEndpoints** из учетной записи хранения.

## <a name="deploy-template"></a>Развертывание шаблона

Вы готовы к развертыванию шаблона и просмотру возвращенного значения.

Если вы еще не создали группу ресурсов, см. [этот раздел](template-tutorial-create-first-template.md#create-resource-group). В этом примере предполагается, что для переменной **templateFile** указан путь к файлу шаблона, как показано в [первом учебнике](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

В выходных данных для команды развертывания вы увидите объект примерно следующего вида:

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

## <a name="review-your-work"></a>Проверка работы

Работая с последними шестью учебниками, вы много чего сделали. Давайте взглянем на выполненную работу. Вы создали шаблон с параметрами, которые легко предоставить. Шаблон можно многократно использовать в разных средах, так как его можно настраивать и он может динамически создавать необходимые значения. Он также возвращает сведения об учетной записи хранения, которую можно использовать в скрипте.

Теперь рассмотрим группу ресурсов и журнал развертывания.

1. Войдите на [портале Azure](https://portal.azure.com).
1. В меню слева выберите **Группы ресурсов**.
1. Выберите группу ресурсов, в которую выполнено развертывание.
1. В зависимости от выполненных действий в группе ресурсов должна быть по крайней мере одна учетная запись хранения (или несколько).
1. Кроме того, в журнале должно быть зафиксировано несколько успешных развертываний. Выберите эту ссылку.

   ![Выбор развертываний](./media/template-tutorial-add-outputs/select-deployments.png)

1. Вы увидите все развертывания в журнале. Выберите развертывание с именем **addoutputs**.

   ![Отображение журнала развертывания](./media/template-tutorial-add-outputs/show-history.png)

1. Вы можете проверить входные данные.

   ![Отображение входных данных](./media/template-tutorial-add-outputs/show-inputs.png)

1. Вы можете проверить выходные данные.

   ![Отображение выходных данных](./media/template-tutorial-add-outputs/show-outputs.png)

1. Вы можете изучить шаблон.

   ![Отображение шаблона](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы переходите к следующему учебнику, вам не нужно удалять группу ресурсов.

Если вы останавливаете работу сейчас, вам может потребоваться очистить развернутые ресурсы, удалив группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В рамках этого учебника вы добавили в шаблон возвращаемое значение. В следующем учебнике вы узнаете, как экспортировать шаблон и использовать его части в шаблоне.

> [!div class="nextstepaction"]
> [Учебник по использованию экспортированного шаблона из портала Azure](template-tutorial-export-template.md)
