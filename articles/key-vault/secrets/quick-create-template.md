---
title: Краткое руководство Azure. Создание хранилища ключей Azure и секрета с помощью шаблона Azure Resource Manager | Документация Майкрософт
description: Краткое руководство, в котором показано, как создавать хранилища ключей Azure и добавлять секреты в хранилища с помощью шаблона Azure Resource Manager.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 273a467f5db2201015352aaf4a232f5a42e29673
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81618082"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью шаблона Resource Manager

[Azure Key Vault](../general/overview.md) — это облачная служба, которая обеспечивает безопасное хранение секретов, таких как ключи, пароли, сертификаты, и другой секретной информации. В этом кратком руководстве рассматривается процесс развертывания шаблона Resource Manager для создания хранилища ключей и секрета.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* Шаблону для настройки разрешений требуется идентификатор объекта пользователя Azure AD. Следующая процедура возвращает идентификатор объекта (GUID).

    1. Выполните следующую команду Azure PowerShell или Azure CLI, выбрав **Попробовать**, а затем вставьте сценарий в панель оболочки. Чтобы вставить сценарий, щелкните правой кнопкой мыши оболочку и выберите **Вставить**.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Запишите идентификатор объекта. Он понадобится в следующем разделе этого краткого руководства.

## <a name="create-a-vault-and-a-secret"></a>Создание хранилища и секрета

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json" range="1-150" highlight="107-148":::

В шаблоне определено два ресурса Azure:

* [**Microsoft.KeyVault/vaults**](/azure/templates/microsoft.keyvault/vaults) создает хранилище ключей Azure.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets) создает секрет хранилища ключей.

Другие примеры шаблонов хранилища Azure Key Vault можно найти [здесь](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

### <a name="deploy-the-template"></a>Развертывание шаблона

1. Выберите следующее изображение, чтобы войти на портал Azure и открыть шаблон. Шаблон создает хранилище ключей и секрет.

    [![Развертывание в Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Введите или выберите следующие значения.

    ![Шаблон Resource Manager, Интеграция Azure Key Vault, портал развертывания](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Если значение не указано, используйте для создания хранилища ключей и секрета значение по умолчанию.

    * **Подписка**. Выберите нужную подписку Azure.
    * **Группа ресурсов**. Щелкните **Создать**, введите уникальное имя новой группы ресурсов и нажмите кнопку **ОК**.
    * **Расположение**. Выберите расположение.  Например, **центральная часть США**.
    * **Имя хранилища ключей**: введите имя для хранилища ключей, которое должно быть уникальным в пределах всего пространства имен .vault.azure.net. В следующем разделе при проверке развертывания, необходимо имя.
    * **Идентификатор клиента**: функция шаблона автоматически получает идентификатор клиента.  Не изменяйте значение по умолчанию.
    * **Идентификатор пользователя AD**. Введите идентификатор объекта пользователя Azure AD, который вы получили на шаге [Предварительные требования](#prerequisites).
    * **Имя секрета**. Введите имя секрета, который вы храните в хранилище ключей.  Например, **adminpassword**.
    * **Значение секрета**. Ведите значение секрета.  Если вы храните пароль, рекомендуется использовать сгенерированный пароль, который вы создали на шаге "Предварительные требования".
    * **I agree to the terms and conditions state above** (Я принимаю указанные выше условия). Установите этот флажок.
3. Щелкните **Приобрести**. После успешного развертывания хранилища ключей вы получите уведомление:

    ![Уведомление Resource Manager на портале интеграции развертывания Key Vault](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Для развертывания шаблона используется портал Azure. В дополнение к порталу Azure можно также использовать Azure PowerShell, Azure CLI и REST API. Дополнительные сведения о других методах развертывания см. в статье о [развертывании с использованием шаблонов](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

Можно проверить хранилище ключей и секрет с помощью портала Azure или использовать следующий скрипт Azure CLI или Azure PowerShell, чтобы просмотреть созданный секрет.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

Результат будет выглядеть примерно так:

# <a name="cli"></a>[CLI](#tab/CLI)

![Шаблон Resource Manager, интеграция Key Vault, вывод подтверждения портала развертывания](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Шаблон Resource Manager, интеграция Key Vault, вывод подтверждения портала развертывания](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Очистка ресурсов

Другие руководства о Key Vault созданы на основе этого документа. Если вы планируете продолжить работу с последующими краткими руководствами и статьями, эти ресурсы можно не удалять.
Удалите ненужную группу ресурсов. Key Vault и связанные ресурсы будут также удалены. Чтобы удалить группу ресурсов с помощью Azure CLI или Azure PowerShell, выполните следующие действия.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Дальнейшие действия

Работая с этим кратким руководством, вы создали хранилище ключей и секрет с помощью шаблона Azure Resource Manager и проверили развертывание. Дополнительные сведения о Key Vault и Azure Resource Manager см. в следующих статьях.

- [Обзор Azure Key Vault](../general/overview.md)
- Сведения об [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Рекомендации по Azure Key Vault](../general/best-practices.md)
