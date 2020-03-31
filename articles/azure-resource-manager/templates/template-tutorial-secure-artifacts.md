---
title: Защита артефактов в шаблонах
description: Узнайте, как защитить артефакты, которые используются в шаблонах Azure Resource Manager.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ad6ea3c68ed6f48ac48bbbdafed7f8660df23937
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239230"
---
# <a name="tutorial-secure-artifacts-in-arm-template-deployments"></a>Руководство по защите артефактов в развертываниях шаблона ARM

Узнайте, как защитить артефакты, которые используются в шаблонах Azure Resource Manager (ARM), с помощью подписанных URL-адресов (SAS) учетной записи хранения Azure. Артефакты развертывания — это все файлы, помимо файла основного шаблона, которые необходимы для выполнения развертывания. Например, в [руководстве по импорту BACPAC-файлов SQL с помощью шаблонов ARM](./template-tutorial-deploy-sql-extensions-bacpac.md) основной шаблон создает экземпляр Базы данных SQL Azure. Он также вызывает BACPAC-файл для создания таблиц и добавления данных. BACPAC-файл является артефактом и хранится в учетной записи хранения Azure. Для доступа к артефакту использовался ключ учетной записи хранения.

В этом руководстве используется SAS для предоставления ограниченного доступа к BACPAC-файлу в учетной записи хранения Azure. Дополнительные сведения о подписанных URL-адресах см. в статье [Использование подписанных URL-адресов (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Чтобы узнать, как защитить связанные шаблоны, ознакомьтесь со статьей [Руководство. Создание связанных шаблонов ARM](./template-tutorial-create-linked-templates.md).

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * подготовка BACPAC-файла;
> * открытие имеющегося шаблона;
> * изменение шаблона;
> * Разверните шаблон.
> * проверка развертывания.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* Visual Studio Code с расширением средств Resource Manager. См. сведения об [использовании Visual Studio Code для создания шаблонов Resource Manager](./use-vs-code-to-create-template.md).
* Просмотрите статью [Руководство. Импорт BACPAC-файлов SQL с помощью шаблонов ARM](./template-tutorial-deploy-sql-extensions-bacpac.md). В этом руководстве используется шаблон, созданный здесь ранее. Ссылка для скачивания завершенного шаблона предоставляется в этой статье.
* Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора SQL Server. Ниже приведен пример создания пароля.

    ```console
    openssl rand -base64 32
    ```

    Для защиты криптографических ключей и других секретов используйте Azure Key Vault. Дополнительные сведения см. в статье [Учебник. Интеграция с Azure Key Vault при развертывании шаблона ARM](./template-tutorial-use-key-vault.md). Мы также рекомендуем обновлять пароль каждые три месяца.

## <a name="prepare-a-bacpac-file"></a>Подготовка BACPAC-файла

В этом разделе описано, как подготовить BACPAC-файл, чтобы он стал безопасным и доступным при развертывании шаблона ARM. В этом разделе описаны пять процедур.

* Скачивание BACPAC-файла.
* Создание учетной записи хранения Azure.
* Создание контейнера больших двоичных объектов в учетной записи хранения.
* Передача BACPAC-файла в этот контейнер.
* Получение маркера SAS из BACPAC-файла.

1. Выберите **Попробовать**, чтобы открыть Cloud Shell. Затем вставьте приведенный ниже скрипт PowerShell в окно оболочки.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName `
        -Location $location `
        -SkuName "Standard_LRS"

    $context = $storageAccount.Context

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Запишите URL-адрес BACPAC-файла и маркер SAS. Эти значения понадобятся при развертывании шаблона.

## <a name="open-an-existing-template"></a>Открытие имеющегося шаблона

В этом сеансе будет изменен шаблон, созданный в [руководстве по импорту BACPAC-файлов SQL с помощью шаблонов ARM](./template-tutorial-deploy-sql-extensions-bacpac.md), чтобы вызвать BACPAC-файл с помощью маркера SAS. Шаблон, разработанный при выполнении инструкций из руководства по расширению SQL, доступен на [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. В Visual Studio Code выберите **Файл** > **Открыть файл**.
1. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. Чтобы открыть файл, выберите **Открыть**.

    В шаблоне определено четыре ресурса:

   * `Microsoft.Sql/servers`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

        Прежде чем настраивать шаблон, рекомендуется получить основные сведения о нем.
1. Выберите **Файл** > **Сохранить как**, чтобы сохранить файл на локальный компьютер с именем *azuredeploy.json*.

## <a name="edit-the-template"></a>Изменение шаблона

1. Замените определение параметра storageAccountKey следующим определением параметра:

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Руководство: параметры защиты артефактов Resource Manager](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. Обновите значение следующих трех элементов ресурса расширения SQL:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

Завершенный шаблон выглядит следующим образом.

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>Развертывание шаблона

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Дополнительные сведения о процедуре развертывания шаблона см. в [этом разделе](./template-tutorial-create-multiple-instances.md#deploy-the-template). Вместо этого используйте следующий скрипт развертывания PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

Используйте сгенерированный пароль. См. раздел [Предварительные требования](#prerequisites).
Значения _artifactsLocation, _artifactsLocationSasToken и bacpacFileName см. в разделе [Подготовка BACPAC-файла](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Проверка развертывания

На портале выберите Базу данных SQL из только что развернутой группы ресурсов. Выберите **Редактор запросов (предварительная версия)** , а затем введите учетные данные администратора. Должны отобразиться две таблицы, импортированные в базу данных:

![Редактор запросов (предварительная версия)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Фильтровать по имени** введите имя группы ресурсов.
3. Выберите имя группы ресурсов. В группе ресурсов должно появиться шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дальнейшие действия

Работая с этим учебником, вы развернули SQL Server, Базу данных SQL и импортированный BACPAC-файл с помощью маркера SAS. Чтобы узнать, как развертывать ресурсы Azure в нескольких регионах и как использовать безопасное развертывание, ознакомьтесь со следующей статьей.

> [!div class="nextstepaction"]
> [Руководство. Использование диспетчера развертывания Azure с шаблонами Resource Manager (закрытая предварительная версия)](./deployment-manager-tutorial.md)
