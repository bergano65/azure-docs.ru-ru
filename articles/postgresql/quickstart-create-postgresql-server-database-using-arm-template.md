---
title: Создание Базы данных Azure для PostgreSQL с помощью шаблона ARM
description: В этой статье показано, как создать сервер Базы данных Azure для PostgreSQL с помощью шаблона Azure Resource Manager.
author: mgblythe
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 05/14/2020
ms.openlocfilehash: 6273043fc50938961caaf9f5a84273ec93c7521c
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83801722"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---single-server"></a>Краткое руководство. Создание Базы данных Azure для PostgreSQL с помощью шаблона ARM (отдельный сервер)

База данных Azure для PostgreSQL — это управляемая служба, с помощью которой можно запускать и масштабировать базы данных PostgreSQL высокой доступности, а также управлять ими в облаке. В этом кратком руководстве показано, как с помощью шаблона ARM создать Базу данных Azure для PostgreSQL (отдельный сервер), используя портал Azure, PowerShell или Azure CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Предварительные требования

# <a name="portal"></a>[Портал](#tab/azure-portal)

Учетная запись Azure с активной подпиской. [Создайте бесплатно](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Учетная запись Azure с активной подпиской. [Создайте бесплатно](https://azure.microsoft.com/free/).
* Если вы хотите выполнить код локально, используйте [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[CLI](#tab/CLI)

* Учетная запись Azure с активной подпиской. [Создайте бесплатно](https://azure.microsoft.com/free/).
* Если вы хотите выполнить код локально, используйте [Azure CLI](/cli/azure/).

---

## <a name="create-an-azure-database-for-postgresql-server"></a>Создание сервера Базы данных Azure для PostgreSQL

Сервер Базы данных Azure для PostgreSQL создается с настроенным набором вычислительных ресурсов и ресурсов хранения. См. сведения в статье [Ценовые категории Базы данных Azure для PostgreSQL (отдельный сервер)](concepts-pricing-tiers.md) Он создается в [группе ресурсов Azure](../azure-resource-manager/management/overview.md).

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-postgresql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-postgresql-with-vnet/azuredeploy.json" range="001-233" highlight="151,164,178,201,215":::

Этот шаблон определяет пять ресурсов:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks);
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets);
* [**Microsoft.DBforPostgreSQL/servers**](/azure/templates/microsoft.dbforpostgresql/servers)
* [**Microsoft.DBforPostgreSQL/servers/virtualNetworkRules**](/azure/templates/microsoft.dbforpostgresql/servers/virtualnetworkrules)
* [**Microsoft.DBforPostgreSQL/servers/firewallRules**](/azure/templates/microsoft.dbforpostgresql/servers/firewallrules)

Дополнительные примеры шаблонов Базы данных Azure PostgreSQL см. в [списке шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbforpostgresql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Развертывание шаблона

# <a name="portal"></a>[Портал](#tab/azure-portal)

Щелкните следующую ссылку, чтобы развернуть шаблон сервера Базы данных Azure для PostgreSQL с помощью портала Azure.

[![Развертывание в Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

На странице **Развертывание Базы данных Azure для PostgreSQL с виртуальной сетью** сделайте следующее.

1. В разделе **Группа ресурсов** щелкните **Создать новую**, введите имя новой группы ресурсов и нажмите **ОК**.

2. Если вы создаете новую группу ресурсов, выберите **расположение** для группы ресурсов и новый сервер.

3. Введите значения в полях **Имя сервера**, **Имя входа администратора** и **Пароль администратора для входа**.

    ![Окно развертывания Базы данных Azure для PostgreSQL с виртуальной сетью, шаблон быстрого запуска Azure, портал Azure](./media/quickstart-create-postgresql-server-database-using-arm-template/deploy-azure-database-for-postgresql-with-vnet.png)

4. Измените остальные стандартные параметры по своему усмотрению.

    * **Подписка** — выберите подписку Azure, которую вы примените для этого сервера.
    * **Емкость SKU** — количество виртуальных ядер, где допускаются значения *2* (по умолчанию), *4*, *8*, *16*, *32* или *64*.
    * **Имя SKU** — префикс уровня SKU, семейство SKU и емкость SKU, соединенные символами подчеркивания, например так: *B_Gen5_1*, *GP_Gen5_2* (по умолчанию) или *MO_Gen5_32*.
    * **Размер SKU в МБ** — объем хранилища сервера Базы данных Azure для PostgreSQL в мегабайтах (по умолчанию это *51200*).
    * **Уровень SKU** — уровень развертывания, например *Basic*, *GeneralPurpose* (по умолчанию) или *MemoryOptimized*.
    * **Семейство SKU** — значение *Gen4* или *Gen5*, которое обозначает поколение оборудования для развертывания сервера.
    * **Версия PostgreSQL** — версия сервера PostgreSQL для развертывания, например *9.5*, *9.6*, *10* или *11* (по умолчанию).
    * **Backup Retention Days** (Срок хранения резервных копий) — требуемый период для хранения геоизбыточных резервных копий в днях (по умолчанию *7*).
    * **Геоизбыточное резервное копирование** — *Включено* или *Выключено* (по умолчанию), в зависимости от потребности в геоизбыточном аварийном восстановлении (GEO-DR).
    * **Имя виртуальной сети** — имя используемой виртуальной сети (по умолчанию *azure_postgresql_vnet*).
    * **Имя подсети** — имя используемой подсети (по умолчанию *azure_postgresql_subnet*).
    * **Имя правила виртуальной сети** — имя правила виртуальной сети, которое разрешает эту подсеть (по умолчанию *AllowSubnet*).
    * **Префикс адресов виртуальной сети** — префикс адресов для виртуальной сети (по умолчанию *10.0.0.0/16*).
    * **Префикс подсети** — префикс адресов для подсети (по умолчанию *10.0.0.0/16*).

5. Прочтите условия использования и установите флажок **Я принимаю указанные выше условия**.

6. Щелкните **Приобрести**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

С помощью следующего интерактивного кода создайте сервер Базы данных Azure для PostgreSQL на основе шаблона. В коде вам предложено ввести имя нового сервера, имя и расположение новой группы ресурсов, а также имя и пароль учетной записи администратора.

Чтобы выполнить этот код в Azure Cloud Shell, щелкните **Попробовать** в правом верхнем углу блока кода.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

С помощью следующего интерактивного кода создайте сервер Базы данных Azure для PostgreSQL на основе шаблона. В коде вам предложено ввести имя нового сервера, имя и расположение новой группы ресурсов, а также имя и пароль учетной записи администратора.

Чтобы выполнить этот код в Azure Cloud Shell, щелкните **Попробовать** в правом верхнем углу блока кода.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for PostgreSQL server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for PostgreSQL server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

# <a name="portal"></a>[Портал](#tab/azure-portal)

Чтобы просмотреть сводные сведения о новом сервере Базы данных Azure для PostgreSQL, сделайте следующее.

1. На [портале Azure](https://portal.azure.com) выполните поиск по фразе **Серверы Базы данных Azure для PostgreSQL** и выберите этот вариант.

2. В списке баз данных выберите новый сервер. Откроется страница **Обзор** для нового сервера Базы данных Azure для PostgreSQL.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Выполните следующий интерактивный код, чтобы просмотреть сведения о сервере Базы данных Azure для PostgreSQL. Вам будет предложено ввести имя нового сервера.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DbForPostgreSQL/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Выполните следующий интерактивный код, чтобы просмотреть сведения о сервере Базы данных Azure для PostgreSQL. Вам нужно ввести имя группы ресурсов для нового сервера.

```azurecli-interactive
read -p "Enter your Azure Database for PostgreSQL server name: " serverName &&
read -p "Enter the resource group where the Azure Database for PostgreSQL server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForPostgreSQL/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ненужную группу ресурсов, когда надобность в ней отпадет. Ресурсы в ней также будут удалены.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. На [портале Azure](https://portal.azure.com) найдите и выберите элемент **Группы ресурсов**.

2. В списке групп ресурсов выберите имя нужной группы ресурсов.

3. На странице **Обзор** для этой группы ресурсов выберите **Удалить группу ресурсов**.

4. В диалоговом окне подтверждения введите имя группы ресурсов и щелкните **Удалить**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the Resource Group name: " resourceGroupName &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Дальнейшие действия

Пошаговые инструкции по созданию шаблона см. в следующей статье:

> [!div class="nextstepaction"]
> [Руководство. Создание и развертывание первого шаблона Azure Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
