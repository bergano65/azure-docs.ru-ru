---
title: Шаблон с зависимыми ресурсами
description: Узнайте, как создавать шаблон Azure Resource Manager с несколькими ресурсами, а также, как развертывать его, используя портал Azure
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3ed653c511dbd775d124e1abd6f4bb02923edb25
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102078"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Руководство по созданию шаблонов Resource Manager с зависимыми ресурсами

Узнайте, как создать шаблон Azure Resource Manager (ARM) для развертывания нескольких ресурсов и настроить порядок развертывания. После создания шаблон можно развернуть с помощью Cloud Shell, используя портал Azure.

В рамках этого руководства вы создадите учетную запись хранения, виртуальную машину, виртуальную сеть и другие зависимые ресурсы. Некоторые ресурсы невозможно развернуть до тех пор, пока не будет существовать другой ресурс. Например, невозможно создать виртуальную машину, пока не существуют ее учетная запись хранения и сетевой интерфейс. Эта связь определяется путем пометки зависимости одного ресурса от других. Диспетчер ресурсов оценивает зависимости между ресурсами и развертывает эти ресурсы согласно установленным зависимостям. Если ресурсы не зависят друг от друга, диспетчер ресурсов развертывает их параллельно. Дополнительные сведения см. в статье [Определение порядка развертывания ресурсов в шаблонах ARM](./define-resource-dependency.md).

![Схема порядка развертывания зависимых ресурсов шаблона Resource Manager](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Открытие шаблона быстрого запуска
> * Обзор шаблона
> * Развертывание шаблона

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* Visual Studio Code с расширением средств Resource Manager. См. [Краткое руководство. Создание шаблонов Azure Resource Manager c помощью Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. Ниже приведен пример создания пароля.

    ```console
    openssl rand -base64 32
    ```

    Для защиты криптографических ключей и других секретов используйте Azure Key Vault. Дополнительные сведения см. в статье [Учебник. Интеграция с Azure Key Vault при развертывании шаблона ARM](./template-tutorial-use-key-vault.md). Мы также рекомендуем обновлять пароль каждые три месяца.

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблоны быстрого запуска Azure — это репозиторий для шаблонов ARM. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом руководстве, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows).

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Чтобы открыть файл, выберите **Открыть**.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл на локальный компьютер с именем **azuredeploy.json**.

## <a name="explore-the-template"></a>Обзор шаблона

Рассматривая шаблон в этом разделе, постарайтесь ответить на следующие вопросы:

* Сколько ресурсов Azure определено в этом шаблоне?
* Одним из ресурсов является учетная запись хранения Azure.  Похоже ли это определение на использованное в предыдущем руководстве?
* Можете ли вы найти справочники по шаблонам для ресурсов, определенных в этом шаблоне?
* Можете ли вы найти зависимости ресурсов?

1. В Visual Studio Code сверните элементы, пока не появятся элементы только первого уровня и элементы второго уровня внутри **ресурсов**.

    ![Шаблоны Azure Resource Manager в Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Шаблоном определено шесть ресурсов:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Перед настройкой шаблона рекомендуется ознакомиться с его справочником.

1. Разверните первый ресурс. Это учетная запись хранения. Сравните определение ресурса с определением в [справочнике по шаблону](/azure/templates/Microsoft.Storage/storageAccounts).

    ![Определение учетной записи хранения для шаблонов Azure Resource Manager в Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Разверните второй ресурс. В качестве типа ресурса используйте `Microsoft.Network/publicIPAddresses`. Сравните определение ресурса с определением в [справочнике по шаблону](/azure/templates/microsoft.network/publicipaddresses).

    ![Определение общедоступного IP-адреса для шаблонов Azure Resource Manager в Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. Разверните третий ресурс. В качестве типа ресурса используйте `Microsoft.Network/networkSecurityGroups`. Сравните определение ресурса с определением в [справочнике по шаблону](/azure/templates/microsoft.network/networksecuritygroups).

    ![Определение группы безопасности сети для шаблонов Azure Resource Manager в Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Разверните четвертый ресурс: В качестве типа ресурса используйте `Microsoft.Network/virtualNetworks`.

    ![Элемент dependsOn для виртуальной сети для шаблонов Azure Resource Manager в Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    Элемент dependsOn позволяет определить один ресурс как зависимый от одного или нескольких ресурсов. Этот ресурс зависит от одного из этих ресурсов:

    * `Microsoft.Network/networkSecurityGroups`

1. Разверните пятый ресурс. В качестве типа ресурса используйте `Microsoft.Network/networkInterfaces`. Этот ресурс зависит от двух других ресурсов:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Разверните шестой ресурс. Этот ресурс — это виртуальная машина. Он зависит от двух других ресурсов:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

На следующей схеме приведены ресурсы и информация о зависимости для этого шаблона.

![Схема зависимости шаблонов Azure Resource Manager в Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Путем указания зависимостей диспетчер ресурсов позволяет эффективно развертывать решение. Развертывание учетной записи хранения, общедоступного IP-адреса и виртуальной сети происходит в параллельном режиме, так как они не имеют зависимостей. После развертывания общедоступного IP-адреса и виртуальной сети создается сетевой интерфейс. Когда все ресурсы развернуты, диспетчер ресурсов развертывает виртуальную машину.

## <a name="deploy-the-template"></a>Развертывание шаблона

1. Войдите в [Azure Cloud Shell](https://shell.azure.com).

1. В левом верхнем углу выберите используемую среду — **PowerShell** или **Bash** (для CLI).  После переключения желательно перезагрузить оболочку.

    ![Файл отправки Cloud Shell на портале Azure](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Выберите **Отправка и скачивание файлов**, а затем **Отправить**. См. предыдущий снимок экрана. Выберите файл, сохраненный ранее. После отправки вы можете использовать команды **ls** и **cat**, чтобы проверить отправку файла.

1. а затем выполните следующий сценарий PowerShell для его развертывания.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    echo "Enter the virtual machine admin username:" &&
    read adminUsername &&
    echo "Enter the DNS label prefix:" &&
    read dnsLabelPrefix &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters adminUsername=$adminUsername dnsLabelPrefix=$dnsLabelPrefix
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

1. RDP-подключение к виртуальной машине для ее проверки успешно установлено.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Фильтровать по имени** введите имя группы ресурсов.
3. Выберите имя группы ресурсов. В группе ресурсов должно появиться шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве описано, как разработать и развернуть шаблон для создания виртуальной машины, виртуальной сети и зависимых ресурсов. Сведения об использовании скриптов развертывания для выполнения операций до и после развертывания см. в следующих статьях.

> [!div class="nextstepaction"]
> [Использование скрипта развертывания](./template-tutorial-deployment-script.md)
