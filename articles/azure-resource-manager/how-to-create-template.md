---
title: Создание шаблона Azure Resource Manager
description: Описание процесса разработки шаблона Azure Resource Manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: bbe891aa584423d64531ae4b23bb8a6ead38c3da
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205558"
---
# <a name="create-azure-resource-manager-template"></a>Создание шаблона Azure Resource Manager

В этой статье описывается процесс и решения разработки, которые вы принимаете при создании шаблона Azure Resource Manager Она содержит обзор примеров и функций, которые могут помочь вам при разработке шаблона. В этой статье предполагается, что вы развертываете ресурсы в группе ресурсов. Если вам нужно развернуть ресурсы в своей подписке Azure, например, создать политики Azure или назначения управления доступом на основе ролей, см. статью [Создание групп ресурсов и ресурсов для подписки Azure](deploy-to-subscription.md).

## <a name="select-json-editor"></a>Выбор редактора JSON

Шаблон Resource Manager — это JSON-файл. Вам необходимо средство разработки для работы с JSON-файлом. Существует много вариантов, но если у вас еще нет редактора, который вы предпочитаете, установите [Visual Studio Code (VS Code)](https://code.visualstudio.com/). 

После установки VS Code добавьте [расширение средств Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Это расширение добавляет множество функций, которые упрощают создание шаблона.

![Шаблон Visual Studio Code](./media/how-to-create-template/template-visual-studio-code.png)

На снимке экрана показан шаблон Resource Manager, открытый в Visual Studio Code. 

Руководство по установке расширения средств Resource Manager и использованию VS Code см. в статье [Краткое руководство. Создание шаблона Azure Resource Manager c помощью Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md).

## <a name="understand-the-template-structure"></a>Общие сведения о структуре шаблона

Давайте рассмотрим части шаблона, чтобы понять, как работает шаблон. Ваш шаблон может иметь не все разделы. Ниже приведены разделы, на которых мы сосредоточимся.

* Раздел [параметры](resource-group-authoring-templates.md#parameters), в котором показаны значения, которые можно указать во время развертывания для настройки развертываемой инфраструктуры. 

* Раздел [переменные](resource-group-authoring-templates.md#variables), в котором показаны значения, используемые в шаблоне.

* Раздел [функции](resource-group-authoring-templates.md#functions), который показывает настроенные выражения шаблона, используемые в вашем шаблоне.

* Раздел [ресурсы](resource-group-authoring-templates.md#resources), в котором показаны ресурсы Azure, развернутые в вашей подписке.

* Раздел [выходные данные](resource-group-authoring-templates.md#outputs), в котором отображаются значения, возвращаемые после завершения развертывания.

## <a name="look-for-similar-templates"></a>Поиск похожих шаблонов

Часто вы можете найти существующий шаблон, который развертывает решение, похожее на то, что вам нужно. [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/) содержат сотни шаблонов от участников сообщества.

![Репозиторий шаблонов быстрого запуска](./media/how-to-create-template/template-quickstart-repo.png)

Найдите в этом репозитории шаблон, похожий на тот, который вам нужен. Ничего страшного, если шаблон не делает то, что вам нужно, его можно настроить.

Найдя шаблон, выберите **Browse on Github** (Найти на Github), а затем скопируйте файл **azuredeploy.json** из репозитория. В VS Code создайте новый файл с именем **azuredeploy.json** и добавьте содержимое файла шаблона, который вы скопировали из репозитория быстрого запуска.

## <a name="add-resources"></a>Добавление ресурсов

Вероятно, вы хотите настроить шаблон, чтобы убедиться, что он делает именно то, что вам нужно. Сначала просмотрите развернутые ресурсы. Вам может понадобиться добавить, удалить или изменить ресурсы в шаблоне. Описание и синтаксис ресурсов см. в статье [Define resources in Azure Resource Manager templates](/azure/templates/) (Определение ресурсов в шаблонах Azure Resource Manager).

![Справочник по шаблонам](./media/how-to-create-template/template-reference.png)

Изучив эти свойства, внесите необходимые изменения. Рекомендации о том, как определить ресурсы, см. в разделе [Resources](template-best-practices.md#resources) (Ресурсы).

## <a name="add-or-remove-parameters"></a>Добавление или удаление параметров

Вам также может понадобиться настроить параметры для вашего шаблона. Вы можете добавлять или удалять параметры в зависимости от того, сколько настроек вы хотите включить во время развертывания. Рекомендации о том, как использовать параметры, см. в разделе [Parameters](template-best-practices.md#parameters) (Параметры).

## <a name="add-tags"></a>Добавление тегов

Вы можете добавить теги к своим ресурсам, чтобы логически упорядочить их по категориям и разделить расходы на выставление счетов. Добавить теги легко, вы применяете их в JSON для ресурса. Например, следующая учетная запись хранения имеет два тега.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

Вы также можете динамически применять теги из параметров. Дополнительные сведения см. в разделе [Теги](resource-group-using-tags.md#templates).

## <a name="review-template-functions"></a>Просмотр функций шаблона

Вы можете заметить выражения в шаблоне, которые заключены в квадратные скобки, например `"[some-expression]"`. Эти выражения используют шаблонные функции для динамического создания значений во время развертывания.

Например, вы часто видите следующее выражение.

```json
"name": "[parameters('siteName')]"
```

Это выражение возвращает значение параметра. Значение присваивается имени свойства.

Или вы можете увидеть более сложное выражение, которое использует несколько других функций.

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Это выражение получает объект со свойствами учетной записи хранения.

Чтобы понять, что делают эти функции, просмотрите документацию [Функции шаблонов диспетчера ресурсов Azure](resource-group-template-functions.md).

## <a name="create-more-than-one-instance"></a>Создание нескольких экземпляров

Иногда требуется создать более одного экземпляра ресурса. Например, может потребоваться несколько учетных записей хранения. Вместо повторения ресурса с помощью вашего шаблона вы можете использовать синтаксис `copy`, чтобы указать более одного экземпляра.

В следующем примере создаются три учетные записи хранения.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Вы также можете указать количество экземпляров из параметра динамически. Дополнительные сведения см. в статье [Развертывание нескольких экземпляров ресурса или свойства в шаблонах Azure Resource Manager](resource-group-create-multiple.md).

## <a name="conditionally-deploy-a-resource"></a>Условное развертывание ресурса

Иногда во время развертывания необходимо указать, развернут ли ресурс в шаблоне. Например, вам может потребоваться гибкость для развертывания нового ресурса или использования существующего ресурса. Элемент `condition` дает возможность включать или отключать развертывание для ресурса. Если выражение в элементе условия имеет значение true, ресурс развертывается. Если значение равно false, ресурс пропускается во время развертывания.

В следующем примере условно развертывается учетная запись хранения.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Дополнительные сведения см. в разделе [Условие](resource-group-authoring-templates.md#condition).

## <a name="review-dependencies"></a>Просмотр зависимостей

Некоторые ресурсы в вашем шаблоне необходимо развернуть раньше других ресурсов. Например, SQL Server должен существовать до создания базы данных SQL. Resource Manager неявно определяет порядок развертывания ресурсов при использовании [reference](resource-group-template-functions-resource.md#reference). Тем не менее в некоторых случаях необходимо явно определить зависимости с помощью элемента `dependsOn`. Просмотрите свой шаблон, чтобы увидеть, нужно ли добавлять какие-либо зависимости. Будьте внимательны и не добавляйте ненужные зависимости, поскольку они могут замедлить развертывание или создать циклические ссылки.

На следующем изображении показан порядок зависимостей для различных ресурсов службы приложений:

![Зависимости службы приложений](./media/how-to-create-template/web-dependencies.png)

В приведенном ниже примере представлена часть шаблона, которая определяет зависимости.

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Дополнительные сведения см. в статье [Define the order for deploying resources in Azure Resource Manager Templates](resource-group-define-dependencies.md) (Определение порядка развертывания ресурсов в шаблонах Azure Resource Manager).

## <a name="review-recommended-practices"></a>Просмотр рекомендаций

Прежде чем развертывать шаблон, просмотрите [Azure Resource Manager template best practices](template-best-practices.md) (Рекомендации по работе с шаблоном Azure Resource Manager), чтобы узнать, есть ли какие-либо рекомендуемые подходы, которые вы хотите реализовать в своем шаблоне.

Если вам нужно использовать шаблон в разных облачных средах Azure, см. статью [Разработка шаблонов Azure Resource Manager для обеспечения согласованности с облаком](templates-cloud-consistency.md).

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы развернуть шаблон, см. статью [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](resource-group-template-deploy-cli.md) или [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md).
* Пошаговое руководство по созданию шаблона см. в статье [Краткое руководство. Создание шаблона Azure Resource Manager c помощью Visual Studio Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md).
* Список доступных в шаблоне функций см. в статье о [функциях шаблонов](resource-group-template-functions.md).
