---
title: Защита артефактов в развертываниях шаблонов Azure Resource Manager | Документация Майкрософт
description: Узнайте, как защитить артефакты, которые используются в шаблонах Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/25/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bf004f07558ae1f252a6bd26b4fd59ea9e4eea6e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069268"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Руководство. Защита артефактов в развертываниях шаблонов Azure Resource Manager

Узнайте, как защитить артефакты, которые используются в шаблонах Azure Resource Manager, с помощью подписанных URL-адресов (SAS) учетной записи хранения Azure. Артефакты развертывания — это все файлы, помимо файла основного шаблона, которые необходимы для выполнения развертывания. Например, в [руководстве по импорту BACPAC-файлов SQL с помощью шаблонов Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) основной шаблон создает базу данных SQL Azure, а также вызывает BACPAC-файл для создания таблиц и добавления данных. BACPAC-файл является артефактом. Артефакт хранится в учетной записи хранения Azure с публичным доступом. В этом руководстве используется SAS для предоставления ограниченного доступа к BACPAC-файлу в учетной записи хранения Azure. Дополнительные сведения о подписанных URL-адресах см. в статье [Использование подписанных URL-адресов (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Чтобы узнать, как защитить связанные шаблоны, ознакомьтесь со статьей [Руководство. Создание связанных шаблонов Azure Resource Manager](./resource-manager-tutorial-create-linked-templates.md).

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Подготовка BACPAC-файла
> * Открытие имеющегося шаблона
> * Изменение шаблона
> * Развертывание шаблона
> * Проверка развертывания

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/) с расширением средств Resource Manager. См. в разделе [Краткое руководство. Создание шаблона Azure Resource Manager c помощью Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Просмотрите статью [Руководство. Импорт BACPAC-файлов SQL с помощью шаблонов Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). В этом руководстве используется шаблон, созданный здесь ранее. Ссылка для скачивания завершенного шаблона предоставляется в этой статье.
* Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора SQL Server. Ниже приведен пример создания пароля.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Для защиты криптографических ключей и других секретов используйте Azure Key Vault. Дополнительные сведения см. в статье [Руководство. Интеграция с Azure Key Vault при развертывании шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md). Мы также рекомендуем обновлять пароль каждые три месяца.

## <a name="prepare-a-bacpac-file"></a>Подготовка BACPAC-файла

В этом разделе описано, как подготовить BACPAC-файл, чтобы он стал безопасным и доступным при развертывании шаблона Resource Manager. В этом разделе описаны пять процедур.

* Скачивание BACPAC-файла.
* Создание учетной записи хранения Azure.
* Создание контейнера больших двоичных объектов в учетной записи хранения.
* Передача BACPAC-файла в этот контейнер.
* Получение маркера SAS из BACPAC-файла.

Чтобы автоматизировать эти шаги с помощью скрипта PowerShell, ознакомьтесь со скриптом из раздела [Передача связанного шаблона](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>Скачивание BACPAC-файла

Скачайте [BACPAC-файл](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac) и сохраните его на локальный компьютер с таким именем — **SQLDatabaseExtension.bacpac**.

### <a name="create-a-storage-account"></a>Создание учетной записи хранения

1. Выберите следующее изображение, чтобы открыть шаблон Resource Manager на портале Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Введите следующие свойства.

    * **Подписка**: Выберите подписку Azure.
    * **Группа ресурсов**. Выберите **Создать** и назначьте ей имя. Группа ресурсов служит контейнером для ресурсов Azure для управления. В этом руководстве можно использовать ту же группу ресурсов для учетной записи хранения и базы данных SQL Azure. Запишите имя этой группы ресурсов. Оно понадобится при создании базы данных SQL Azure далее в руководстве.
    * **Расположение**. Выберите регион. Например, **центральная часть США**.
    * **Тип учетной записи хранения**. Используйте значение по умолчанию **Standard_LRS**.
    * **Расположение**. Используйте значение по умолчанию **[resourceGroup().location]** . Это означает, что для учетной записи хранения используется расположение группы ресурсов.
    * Выберите **I agree to the terms and conditions started above** (Я принимаю указанные выше условия).
3. Щелкните **Приобрести**.
4. Выберите значок уведомления (колокольчик) в правом верхнем углу портала, чтобы узнать состояние развертывания.

    ![Руководство: панель уведомлений Resource Manager на портале](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. После успешного развертывания учетной записи хранения выберите **Перейти к группе ресурсов** в области уведомлений, чтобы открыть группу ресурсов.

### <a name="create-a-blob-container"></a>Создание контейнера больших двоичных объектов

Чтобы файлы можно было передать, необходим контейнер больших двоичных объектов.

1. Выберите учетную запись хранения, чтобы открыть ее. В группе ресурсов должна быть только одна учетная запись хранения. Имя учетной записи хранения отличается от того, которое отображено на следующем снимке экрана.

    ![Учетная запись хранения учебника Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Выберите элемент **Большие двоичные объекты**.

    ![Большие двоичные объекты учебника Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Выберите **+ Container** (+ Контейнер) в верхней части экрана, чтобы создать контейнер.
4. Введите следующие значения.

    * **Имя**. Введите **sqlbacpac**.
    * **Уровень общего доступа**. Используйте значение по умолчанию **Закрытый (отсутствие анонимного доступа)** .
5. Нажмите кнопку **ОК**.
6. Выберите **sqlbacpac**, чтобы открыть созданный контейнер.

### <a name="upload-the-bacpac-file-to-the-container"></a>Передача BACPAC-файла в контейнер

1. Щелкните **Отправить**.
2. Введите следующие значения.

    * **Файлы**. Следуйте инструкциям для выбора скачанного ранее BACPAC-файла. Имя по умолчанию — **SQLDatabaseExtension.bacpac**.
    * **Тип проверки подлинности**. Выберите **SAS**.  *SAS* — это значение по умолчанию.
3. Щелкните **Отправить**.  После успешной отправки файла его имя должно быть указано в контейнере.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />Создание маркера SAS

1. Щелкните правой кнопкой мыши **SQLDatabaseExtension.bacpac** в контейнере, а затем выберите **Создать SAS**.
2. Введите следующие значения.

    * **Разрешение**. Конфигурация по умолчанию — **Чтение**.
    * **Дата и время начала и завершения**. Значение по умолчанию позволяет использовать маркер SAS в течение восьми часов. Если для работы с этим руководством необходимо больше времени, обновите **срок действия**.
    * **Разрешенные IP-адреса**. Оставьте это поле пустым.
    * **Разрешенные протоколы**. Используйте значение по умолчанию — **HTTPS**.
    * **Ключ подписывания**. Используйте значение по умолчанию — **Ключ 1**.
3. Щелкните **Создать токен SAS BLOB-объекта и URL-адрес**.
4. Скопируйте **URL-адрес SAS BLOB-объекта**. Имя файла **SQLDatabaseExtension.bacpac** находится в середине URL-адреса.  Имя файла разделяет URL-адрес на три части:

   - **Расположение артефакта**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Убедитесь, что расположение заканчивается на "/".
   - **Имя BACPAC-файла**. SQLDatabaseExtension.bacpac.
   - **Маркер SAS расположения артефактов**. Убедитесь, что перед маркером стоит знак "?".

     Эти три значения понадобятся в разделе [Развертывание шаблона](#deploy-the-template).

## <a name="open-an-existing-template"></a>Открытие имеющегося шаблона

В этом сеансе будет изменен шаблон, созданный в [руководстве по импорту BACPAC-файлов SQL с помощью шаблонов Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), чтобы вызвать BACPAC-файл с помощью маркера SAS.  Шаблон, разработанный в руководстве по расширению SQL, опубликован по адресу [https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json).

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.

    В шаблоне определено пять ресурсов:

   * `Microsoft.Sql/servers`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Прежде чем настраивать шаблон, рекомендуется получить основные сведения о нем.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл на локальный компьютер с именем **azuredeploy.json**.

## <a name="edit-the-template"></a>Изменение шаблона

Добавьте следующие дополнительные параметры:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Руководство: параметры защиты артефактов Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Обновите значение двух следующих элементов:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>Развертывание шаблона

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Дополнительные сведения о процедуре развертывания шаблона см. в [этом разделе](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template). Вместо этого используйте следующий сценарий развертывания PowerShell:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Используйте сгенерированный пароль. См. раздел [Предварительные требования](#prerequisites).
Значения _artifactsLocation, _artifactsLocationSasToken и bacpacFileName см. в разделе [Generate a SAS token](#generate-a-sas-token) (Создание маркера SAS).

## <a name="verify-the-deployment"></a>Проверка развертывания

На портале выберите Базу данных SQL из только что развернутой группы ресурсов. Выберите **Редактор запросов (предварительная версия)** , а затем введите учетные данные администратора. Должны отобразиться две таблицы, импортированные в базу данных:

![Развертывание BACPAC-файла в расширении SQL с помощью Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы развернули SQL Server, Базу данных SQL и импортированный BACPAC-файл с помощью маркера SAS. Чтобы узнать, как создать конвейер Azure для непрерывной разработки и развертывания шаблонов Resource Manager, см. следующую статью.

> [!div class="nextstepaction"]
> [Tutorial: Continuous integration of Azure Resource Manager templates with Azure Pipelines](./resource-manager-tutorial-use-azure-pipelines.md) (Учебник: непрерывная интеграция шаблонов Azure Resource Manager с Azure Pipelines)
