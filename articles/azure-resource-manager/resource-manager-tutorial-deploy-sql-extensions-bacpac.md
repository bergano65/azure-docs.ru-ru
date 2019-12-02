---
title: Импорт BACPAC-файлов SQL с помощью шаблонов
description: Сведения о том, как использовать расширение Базы данных SQL для импорта BACPAC-файлов SQL с помощью шаблонов Azure Resource Manager.
author: mumian
ms.date: 11/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 741521551335712400e5f61822d7dda31199d3df
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422164"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Руководство. Импорт BACPAC-файлов SQL с помощью шаблонов Azure Resource Manager

Здесь приведены сведения о том, как использовать расширение Базы данных SQL Azure для импорта BACPAC-файлов SQL с помощью шаблонов Azure Resource Manager. Артефакты развертывания — это любые файлы, которые помимо файлов основного шаблона необходимы для выполнения развертывания. BACPAC-файл является артефактом. В этом руководстве вы создадите шаблон для развертывания Azure SQL Server, Базы данных SQL и импорта BACPAC-файла. Сведения о развертывании расширений виртуальной машины Azure с помощью шаблонов Azure Resource Manager см. в статье [Руководство. Развертывание расширений виртуальной машины с помощью шаблонов Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Подготовка BACPAC-файла
> * Открытие шаблона быстрого запуска
> * Изменение шаблона
> * Развертывание шаблона
> * Проверка развертывания

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* Visual Studio Code с расширением средств Resource Manager. Дополнительные сведения см. в статье [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md) (Создание шаблонов Azure Resource Manager с помощью Visual Studio Code).
* Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора SQL Server. Ниже приведен пример создания пароля.

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Для защиты криптографических ключей и других секретов используйте Azure Key Vault. Дополнительные сведения см. в статье [Руководство. Интеграция с Azure Key Vault при развертывании шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md). Мы также рекомендуем обновлять пароль каждые три месяца.

## <a name="prepare-a-bacpac-file"></a>Подготовка BACPAC-файла

BACPAC-файл доступен на сайте [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Сведения о создании собственного файла см. в статье [Экспорт базы данных SQL Azure в BACPAC-файл](../sql-database/sql-database-export.md). Если вы решили опубликовать файл в своем расположении, необходимо обновить шаблон позже в этом руководстве.

BACPAC-файл должен храниться в учетной записи службы хранилища Azure, прежде чем его можно будет импортировать с помощью шаблона Resource Manager.

1. Откройте [Cloud Shell](https://shell.azure.com).
1. Выберите **Отправка и скачивание файлов**, а затем **Отправить**.
1. Укажите приведенный ниже URL-адрес, а затем нажмите кнопку **ОК**.

    ```url
    https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac
    ```

1. Скопируйте приведенный ниже скрипт PowerShell и вставьте его в окно оболочки.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFile = "$HOME/SQLDatabaseExtension.bacpac"
    $blobName = "SQLDatabaseExtension.bacpac"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    Set-AzStorageBlobContent -File $bacpacFile `
                             -Container $containerName `
                             -Blob $blobName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$blobName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Запишите ключ учетной записи хранения и URL-адрес BACPAC-файла. Эти значения понадобятся при развертывании шаблона.

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Используемый в этом учебнике шаблон доступен на сайте [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

3. Чтобы открыть файл, выберите **Открыть**.

    В шаблоне определено три ресурса:

   * `Microsoft.Sql/servers`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Прежде чем настраивать шаблон, рекомендуется получить основные сведения о нем.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл на локальный компьютер с именем **azuredeploy.json**.

## <a name="edit-the-template"></a>Изменение шаблона

1. Добавьте два дополнительных параметра в конец раздела **parameters**, чтобы задать ключ учетной записи хранения и URL-адрес BACPAC-файла.

    ```json
    "storageAccountKey": {
      "type":"string",
      "metadata":{
        "description": "Specifies the key of the storage account where the BACPAC file is stored."
      }
    },
    "bacpacUrl": {
      "type":"string",
      "metadata":{
        "description": "Specifies the URL of the BACPAC file."
      }
    }
    ```

    Добавьте запятую после **adminPassword**. Чтобы отформатировать JSON-файл из Visual Studio Code, нажмите сочетание клавиш **[SHIFT]+[ALT]+F**.

    Сведения о получении этих двух значений см. в разделе [Подготовка BACPAC-файла](#prepare-a-bacpac-file).

1. Добавьте в шаблон два дополнительных ресурса.

    * Чтобы разрешить расширению Базы данных SQL импортировать BACPAC-файлы, необходимо разрешить трафик из служб Azure. Добавьте следующее определение правила брандмауэра в определение SQL Server:

        ```json
        {
          "type": "firewallrules",
          "apiVersion": "2015-05-01-preview",
          "name": "AllowAllAzureIps",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[variables('databaseServerName')]"
          ],
          "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
          }
        }
        ```

        Шаблон должен выглядеть так:

        ![Развертывание BACPAC-файла в расширении SQL с помощью Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Добавьте ресурс расширения Базы данных SQL в определение базы данных с помощью следующего кода JSON:

        ```json
        "resources": [
            {
              "type": "extensions",
              "apiVersion": "2014-04-01",
              "name": "Import",
              "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
              ],
              "properties": {
                "storageKeyType": "StorageAccessKey",
                "storageKey": "[parameters('storageAccountKey')]",
                "storageUri": "[parameters('bacpacUrl')]",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import"
              }
            }
        ]
        ```

        Шаблон должен выглядеть так:

        ![Развертывание BACPAC-файла в расширении SQL с помощью Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Сведения об определении ресурса см. в [справочнике о расширении Базы данных SQL](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Ниже приведены некоторые важные элементы.

        * **dependsOn**. Ресурс расширения должен быть создан после создания базы данных SQL.
        * **storageKeyType**. Укажите тип ключа к хранилищу данных, который необходимо использовать. Значение может быть `StorageAccessKey` или `SharedAccessKey`. Используйте `StorageAccessKey` в рамках изучения этого учебника.
        * **storageKey**. Укажите ключ для учетной записи хранения, в которой хранится BACPAC-файл. Если тип ключа к хранилищу данных — SharedAccessKey, он должен начинаться с "?".
        * **storageUri**. Укажите URL-адрес BACPAC-файла из учетной записи хранения.
        * **administratorLoginPassword**. Пароль администратора SQL. Используйте сгенерированный пароль. См. раздел [Предварительные требования](#prerequisites).

## <a name="deploy-the-template"></a>Развертывание шаблона

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Дополнительные сведения о процедуре развертывания шаблона см. в [этом разделе](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Вместо этого используйте следующий сценарий развертывания PowerShell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Рекомендуем применить имя проекта, использованное при подготовке BACPAC-файла, чтобы все ресурсы были сохранены в одной группе ресурсов.  Это упростит управление ресурсами, например очистку ресурсов. При использовании одного имени проекта вы можете либо удалить команду **New-AzResourceGroup** из скрипта, либо выбрать вариант y или n в качестве ответа на запрос обновления имеющейся группы ресурсов.

Используйте сгенерированный пароль. См. раздел [Предварительные требования](#prerequisites).

## <a name="verify-the-deployment"></a>Проверка развертывания

Чтобы получить доступ к SQL Server с клиентского компьютера, добавьте дополнительное правило брандмауэра. Дополнительные сведения см. в разделе о [создании правил брандмауэра для IP-адресов и управление ими](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

На портале выберите Базу данных SQL из только что развернутой группы ресурсов. Выберите **Редактор запросов (предварительная версия)** , а затем введите учетные данные администратора. Должны отобразиться две таблицы, импортированные в базу данных:

![Развертывание BACPAC-файла в расширении SQL с помощью Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы развернули SQL Server, Базу данных SQL и импортированный BACPAC-файл. BACPAC-файл хранится в учетной записи хранения Azure. К нему может получить доступ любой пользователь, у которого есть URL-адрес. Сведения о защите BACPAC-файла (артефакта) см. в следующем ресурсе:

> [!div class="nextstepaction"]
> [Защита артефактов](./resource-manager-tutorial-secure-artifacts.md)
