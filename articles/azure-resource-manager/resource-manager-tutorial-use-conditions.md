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
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 90431ea7649b38da6cbbd242b00c21278d8e8967
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268895"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Руководство по Использование условия в шаблонах Azure Resource Manager

Узнайте, как развернуть ресурсы Azure на основе условий.

В руководстве по [настройке порядка развертывания ресурсов](./resource-manager-tutorial-create-templates-with-dependent-resources.md) описано, как создать виртуальную машину, виртуальную сеть и некоторые другие зависимые ресурсы, включая учетную запись хранения. Чтобы не создавать учетную запись хранения каждый раз, вы можете позволить пользователям выбрать, создавать ли новую учетную запись хранения или использовать существующую. Для достижения этой цели нужно определить дополнительный параметр. Если значение параметра равно "new", будет создана новая учетная запись хранения.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * открытие шаблона быстрого запуска;
> * Изменение шаблона
> * Развертывание шаблона
> * Очистка ресурсов

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

Шаблоны быстрого запуска Azure являются репозиторием для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом руководстве, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows).

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

## <a name="modify-the-template"></a>Изменение шаблона

Внесите два изменения в имеющийся шаблон:

* Добавьте параметр имени для учетной записи хранения. Пользователи могут указать имя новой или существующей учетной записи хранения.
* Добавьте новый параметр с именем **newOrExisting**. Развертывание использует этот параметр, чтобы определить нужное действие: создавать новую учетную запись хранения или использовать имеющуюся.

Вот что нужно сделать, чтобы внести изменения:

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

За инструкциями по развертыванию шаблона обратитесь к [этому разделу](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template).

При развертывании шаблона с помощью Azure PowerShell необходимо указать один дополнительный параметр: Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. См. раздел [Предварительные требования](#prerequisites).

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile azuredeploy.json
```

> [!NOTE]
> Существует проблема с вводом-выводом файла при использовании Azure PowerShell в Cloud Shell.  Отображается сообщение об ошибке: *Cannot retrieve the dynamic parameters for the cmdlet. Cannot find path 'Azure:/azuredeploy.json' because it does not exist* (Не удалось получить динамические параметры командлета. Не удалось найти путь Azure:/azuredeploy.json, так как он не существует).  Временное решение — не добавлять параметр **-TemplateFile** в команду `New-AzResourceGroupDeploy`. Команда предложит ввести имя файла.

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

В этом руководстве описано, как разработать шаблон, позволяющий пользователю выбирать между созданием учетной записи хранения и использованием существующей. Чтобы узнать, как получать секреты из Azure Key Vault и использовать их в шаблоне развертывания в качестве паролей, обратитесь к статье:

> [!div class="nextstepaction"]
> [Руководство. Интеграция Azure Key Vault в развертывание шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md)
