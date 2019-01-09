---
title: Создание связанных шаблонов Azure Resource Manager | Документация Майкрософт
description: Сведения о создании связанных шаблонов Azure Resource Manager для создания виртуальной машины
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fd2c5c0aab9b9b9f2977b3a38b9e08c51e98d451
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973491"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Руководство. Создание связанных шаблонов Azure Resource Manager

Узнайте, как создать связанные шаблоны Azure Resource Manager. Используя связанные шаблоны, можно одним шаблоном вызвать другой. Это отлично подходит для создания модулей шаблонов. В этом руководстве используется тот же шаблон, что и в статье [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md), который создает виртуальную машину, виртуальную сеть и другие зависимые ресурсы, включая учетную запись хранения. Для создания ресурса учетной записи хранения можно использовать связанный шаблон.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * открытие шаблона быстрого запуска;
> * Создание связанного шаблона
> * Передача связанного шаблона
> * Ссылка на связанный шаблон
> * Настройка зависимостей
> * Развертывание шаблона
> * Дополнительные рекомендации

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/) с [расширением Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. Ниже приведен пример создания пароля.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Для защиты криптографических ключей и других секретов используйте Azure Key Vault. Дополнительные сведения см. в статье [Руководство. Интеграция с Azure Key Vault при развертывании шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md). Мы также рекомендуем обновлять пароль каждые три месяца.

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблоны быстрого запуска Azure являются репозиторием для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом руководстве, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows). Это тот же шаблон, который используется в статье [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Можно сохранить две копии одного шаблона для использования как:

* **основной шаблон** — создает все ресурсы, за исключением учетной записи хранения;
* **связанный шаблон** — создает учетную запись хранения.

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.
4. Шаблоном определено пять ресурсов:

    * `Microsoft.Storage/storageAccounts`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). 
    * `Microsoft.Network/publicIPAddresses`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses). 
    * `Microsoft.Network/virtualNetworks`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks). 
    * `Microsoft.Network/networkInterfaces`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces). 
    * `Microsoft.Compute/virtualMachines`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Прежде чем настраивать шаблон, рекомендуется получить основные сведения о нем.
5. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл на локальный компьютер с именем **azuredeploy.json**.
6. Для создания другой копии файла с именем **linkedTemplate.json** выберите **Файл**>**Сохранить как**.

## <a name="create-the-linked-template"></a>Создание связанного шаблона

Связанный шаблон создает учетную запись хранения. Этот шаблон почти идентичен изолированному шаблону, который создает учетную запись хранения. В этом руководстве связанный шаблон должен передать значение основного шаблона. Это значение определяется в элементе `outputs`.

1. В Visual Studio Code откройте файл azuredeploy.parameters.json, если он еще не открыт.
2. Выполните следующие изменения:

    * Удалите все ресурсы, за исключением учетной записи хранения. Удаляются всего четыре ресурса.
    * Измените значение элемента **name** учетной записи хранения ресурса на:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```
    * Удалите элемент **variables** и все определения переменных.
    * Удалить все параметры, кроме **location**.
    * Добавьте параметр с именем **storageAccountName**. Как параметр имя учетной записи хранения передается из основного в связанный шаблон.

        ```json
        "storageAccountName":{
        "type": "string",
        "metadata": {
            "description": "Azure Storage account name."
        }
        },
        ```
    * Обновите элемент **выходных данных**, чтобы он выглядел так, как изображено ниже.

        ```json
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
        ```
        Для определения ресурсов виртуальной машины в основном шаблоне необходим **storageUri**.  В качестве значения выходных данных передайте значения обратно в основной шаблон.

    Когда все готово, шаблон должен выглядеть, как показано ниже.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountName":{
            "type": "string",
            "metadata": {
              "description": "Azure Storage account name."
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
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
          }
        ],
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
    }
    ```
3. Сохраните изменения.

## <a name="upload-the-linked-template"></a>Передача связанного шаблона

Основной шаблон и связанный шаблон должны быть доступны из любой точки выполнения развертывания. В этом руководстве используется метод развертывания Cloud Shell, который применялся в статье [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Основной шаблон (azuredeploy.json) загружается в оболочку. Связанный шаблон (linkedTemplate.json) должен быть размещен в общем безопасном расположении. Следующий сценарий PowerShell создает учетную запись хранения Azure, передает в нее шаблон, а затем создает маркер SAS для предоставления ограниченного доступа к файлу шаблона. Для упрощения руководства этот скрипт скачивает полный связанный шаблон из общего расположения. Если вы хотите использовать созданный связанный шаблон, можно отправить его с помощью [Cloud Shell](https://shell.azure.com), а затем изменить скрипт для использования этого шаблона.

> [!NOTE]
> Сценарий ограничивает срок использования маркера SAS до восьми часов. Если для работы с этим руководством необходимо больше времени, увеличьте срок действия.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzureStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzureStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzureStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

echo "You need the following values later in the tutorial:"
echo "Resource Group Name: $resourceGroupName"
echo "Linked template URI with SAS token: $templateURI"
```

1. Нажмите зеленую кнопку **Попробовать**, чтобы открыть панель Azure Cloud Shell.
2. Нажмите кнопку **Копировать**, чтобы скопировать сценарий PowerShell.
3. Щелкните правой кнопкой мыши внутри панели оболочки (область темно-синего цвета), а затем выберите **Вставить**.
4. Запишите эти два значения (имя группы ресурсов и URI связанного шаблона), содержащиеся в конце области оболочки. Они понадобятся вам позже при работе с этим руководством.
5. Выберите **Выйти из режима фокусировки**, чтобы закрыть панель оболочки.

На практике вы создаете маркер SAS при развертывании основного шаблона и настраиваете для этого маркера небольшой срок действия, чтобы сделать его более безопасным. Дополнительные сведения см. в разделе [Предоставление маркера SAS во время развертывания](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Вызов связанного шаблона

Основной шаблон находится в файле с именем azuredeploy.json.

1. Откройте файл azuredeploy.json в Visual Studio Code, если он еще не открыт.
2. Удалите определение ресурса учетной записи хранения из шаблона:

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    ```
3. К тому месту, где необходимо определение учетной записи хранения, добавьте приведенный ниже фрагмент кода JSON.

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Обратите внимание на следующие детали.

    * В основном шаблоне используется ресурс `Microsoft.Resources/deployments` для ссылки на другой шаблон.
    * Ресурс `deployments` имеет имя `linkedTemplate`. Это имя используется для [настройки зависимостей](#configure-dependency).  
    * Для обоих связанного и вложенного шаблонов можно использовать только [добавочный](./deployment-modes.md) режим развертывания.
    * `templateLink/uri` содержит URI связанного шаблона. Обновите это значение, указав URI, полученный при отправке связанного шаблона (тот, который с маркером SAS).
    * Чтобы передать значение из основного шаблона в связанный, используйте `parameters`.
4. Укажите для элемента `uri` значение, полученное при отправке связанного шаблона (тот, который с маркером SAS). На практике вам необходимо указать параметр для универсального кода ресурса (URI).
5. Сохраните измененный шаблон.

## <a name="configure-dependency"></a>Настройка зависимостей

Как следует из статьи [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md), ресурс виртуальной машины зависит от учетной записи хранения:

![Схема зависимости шаблонов Azure Resource Manager в Visual Studio Code](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Так как учетная запись хранения теперь определяется в связанном шаблоне, необходимо обновить следующие два элемента ресурса `Microsoft.Compute/virtualMachines`.

* Перенастройте элемент `dependOn`. Определение учетной записи хранения перемещается на связанный шаблон.
* Перенастройте элемент `properties/diagnosticsProfile/bootDiagnostics/storageUri`. В разделе [Создать связанный шаблон](#create-the-linked-template) вы добавили значение выходных данных.

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Основной шаблон требует это значение.

1. Откройте файл azuredeploy.json в Visual Studio Code, если он еще не открыт.
2. Разверните определение ресурса виртуальной машины, обновив **dependsOn**, как показано на следующем снимке экрана.

    ![Связанные шаблоны Azure Resource Manager настраивают зависимости ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* — имя ресурса развертываний.  
3. Обновите **properties/diagnosticsProfile/bootDiagnostics/storageUri**, как показано на предыдущем снимке экрана.
4. Сохраните измененный шаблон.

Дополнительные сведения см. в разделе [Использование связанных шаблонов в при развертывании ресурсов Azure](./resource-group-linked-templates.md).

## <a name="deploy-the-template"></a>Развертывание шаблона

Дополнительные сведения о процедуре развертывания шаблона см. в [этом разделе](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Используйте для хранения связанного шаблона ту же группу ресурсов, к которой принадлежит учетная запись хранения. Это упростит очистку ресурсов, выполняемую при работе со следующим разделом. Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. См. раздел [Предварительные требования](#prerequisites).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="additional-practice"></a>Дополнительные рекомендации

Чтобы улучшить готовый проект, внесите в него следующие дополнительные изменения:

1. Измените основной шаблон (azuredeploy.json) таким образом, чтобы он принимал значение URI связанного шаблона с помощью параметра.
2. Вместо создания маркера SAS при передаче связанного шаблона создайте его при развертывании основного шаблона. Дополнительные сведения см. в разделе [Предоставление маркера SAS во время развертывания](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы разделили шаблон на основной и связанный. Чтобы узнать, как использовать расширения виртуальной машины для задач, выполняемых после развертывания, перейдите к следующей статье:

> [!div class="nextstepaction"]
> [Развертывание расширений виртуальной машины](./deployment-manager-tutorial.md)
