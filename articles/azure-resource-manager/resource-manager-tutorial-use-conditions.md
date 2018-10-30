---
title: Использование условия в шаблонах Azure Resource Manager | Документация Майкрософт
description: Узнайте, как развернуть ресурсы Azure на основе условий.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/18/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 552b39c520396942fa81f963c0cfa1c8c7b47db4
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456972"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Руководство. Использование условия в шаблонах Azure Resource Manager

Узнайте, как развернуть ресурсы Azure на основе условий. 

В этом руководстве используется сценарий из статьи [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md). В этом руководстве вы создадите виртуальную машину, виртуальную сеть и другие зависимые ресурсы, включая учетную запись хранения. Чтобы не создавать учетную запись хранения каждый раз, вы можете позволить пользователям выбрать, создавать ли новую учетную запись хранения или использовать существующую. Для достижения этой цели нужно определить дополнительный параметр. Если значение параметра равно "new", будет создана новая учетная запись хранения.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Открытие шаблона быстрого запуска
> * Изменение шаблона
> * Развертывание шаблона
> * Очистка ресурсов

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/) с [расширением средств Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблоны быстрого запуска Azure являются репозиторием для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом руководстве, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows).

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл на локальный компьютер с именем **azuredeploy.json**.

## <a name="modify-the-template"></a>Изменение шаблона

Внесите два изменения в имеющийся шаблон:

* Добавьте параметр имени для учетной записи хранения. Пользователи могут указать имя новой или существующей учетной записи хранения.
* Добавьте новый параметр с именем **newOrExisting**. Развертывание использует этот параметр, чтобы определить нужное действие: создавать новую учетную запись хранения или использовать имеющуюся.

1. Откройте файл **azuredeploy.json** в Visual Studio Code.
2. Замените во всем шаблоне **variables('storageAccountName')** на **parameters('storageAccountName')**.  **variables('storageAccountName')** указан три раза.
3. Удалите следующее определение переменной:

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    ```
4. Добавьте следующие два параметра в шаблон:

    ```json
    "storageAccountName": {
      "type": "string"
    },    
    "newOrExisting": {
      "type": "string", 
      "allowedValues": [
        "new", 
        "existing"
      ]
    },
    ```
    Определение обновленных параметров выглядит так:

    ![Условие использования Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Добавьте следующую строку в начало определения учетной записи хранения.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Условие проверяет значение параметра с именем **newOrExisting**. Если значение параметра равно **new**, то в результате развертывания создается учетная запись хранения.

    Обновленное определение учетной записи хранения выглядит так:

    ![Условие использования Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Присвойте параметру **storageUri** следующее значение:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Это изменение необходимо при использовании существующей учетной записи хранения с другой группой ресурсов.

7. Сохраните изменения.

## <a name="deploy-the-template"></a>Развертывание шаблона

За инструкциями по развертыванию шаблона обратитесь к [этому разделу](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template).

При развертывании шаблона с помощью Azure PowerShell необходимо указать один дополнительный параметр:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password"
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
$vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
New-AzureRmResourceGroupDeployment -Name mydeployment1018 -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin -adminPassword $vmPW `
    -dnsLabelPrefix $dnsLabelPrefix -storageAccountName $storageAccountName -newOrExisting $newOrExisting `
    -TemplateFile azuredeploy.json
```

> [!NOTE]
> Развертывание завершается сбоем, если **newOrExisting** — **new**, а учетная запись хранения с указанным именем уже существует.

Попробуйте сделать другое развертывания, задав для **newOrExisting** значение "existing" и указав имеющуюся учетную запись хранения. Чтобы создать учетную запись хранения, обратитесь к [этой статье](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве был разработан шаблон, позволяющий пользователю выбирать между созданием учетной записи хранения и использованием имеющейся. Для виртуальной машины, созданной в рамках этого руководства, требуется имя пользователя и пароль администратора. Вместо того чтобы передавать пароль во время развертывания, можно предварительно сохранить пароль с помощью Azure Key Vault и получить его во время развертывания. Чтобы узнать, как получать секреты из Azure Key Vault и использовать их в шаблоне развертывания, обратитесь к статье:

> [!div class="nextstepaction"]
> [Руководство. Интеграция Azure Key Vault в развертывание шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md)
