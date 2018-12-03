---
title: Интеграция Azure Key Vault в развертывание шаблона Azure Resource Manager | Документация Майкрософт
description: Узнайте, как использовать Azure Key Vault для передачи значений безопасного параметра во время развертывания шаблона Resource Manager
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
ms.openlocfilehash: 0bcff6e0cec234f17b0aaab9828602eb4a194d85
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334216"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Руководство. Интеграция Azure Key Vault в развертывание шаблона Resource Manager

Узнайте, как получить значения секрета из Azure Key Vault и передавать эти значения в качестве параметров во время развертывания Resource Manager. Это значение никогда не будет раскрыто, так как указывается только его идентификатор в Key Vault. Дополнительные сведения см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](./resource-manager-keyvault-parameter.md).

В руководстве по [настройке порядка развертывания ресурсов](./resource-manager-tutorial-create-templates-with-dependent-resources.md) описано, как создать виртуальную машину, виртуальную сеть и некоторые другие зависимые ресурсы. Вы также настроите шаблон, чтобы получить пароль администратора виртуальной машины из Azure Key Vault.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Создание Key Vault
> * открытие шаблона быстрого запуска;
> * Изменение файла параметров
> * Развертывание шаблона
> * Проверка развертывания
> * Очистка ресурсов

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/) с [расширением Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. Ниже приведен пример создания пароля.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Для защиты криптографических ключей и других секретов используйте Azure Key Vault. Дополнительные сведения см. в статье [Руководство. Интеграция Azure Key Vault в развертывание шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md). Мы также рекомендуем обновлять пароль каждые три месяца.

## <a name="prepare-the-key-vault"></a>Создание Key Vault

В этом разделе описано, как использовать шаблон Resource Manager, чтобы создать Key Vault и секрет. Этот шаблон делает следующее.

* Создание Key Vault с помощью включенного свойства `enabledForTemplateDeployment`. Это свойство должно получить значение true до того, как процесс развертывания шаблона сможет получить доступ к секретам, определенным в этом Key Vault.
* Добавление секрета в Key Vault.  Секрет хранит пароль администратора виртуальной машины.

Если вы (в качестве пользователя для развертывания шаблона виртуальной машины) не являетесь владельцем или участником Key Vault, владелец или участник Key Vault должен предоставить вам доступ к разрешению Microsoft.KeyVault/vaults/deploy/action для Key Vault. Дополнительные сведения см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](./resource-manager-keyvault-parameter.md).

Шаблону для настройки разрешений требуется идентификатор объекта пользователя Azure AD. Следующее действие возвращает идентификатор объекта (GUID), а также создает пароль администратора. Для предотвращения атак с подбором пароля рекомендуется использовать созданные пароли.

1. Выполните следующую команду в интерфейсе командной строки Azure или Azure PowerShell.  

    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    openssl rand -base64 32
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzureADUser -ObjectId $upn).ObjectId
    openssl rand -base64 32
    ```
2. Запишите идентификатор объекта и созданный пароль. Они потребуются вам позднее.
3. Убедитесь, что созданный пароль соответствует требованиям к паролю виртуальной машины. У каждой службы Azure есть определенные требования к паролю. Чтобы увидеть требования к паролю виртуальной машины, обратитесь к разделу [Какие требования к паролю при создании виртуальной машины?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

Чтобы создать Key Vault, сделайте следующее:

1. Выберите следующее изображение, чтобы войти на портал Azure и открыть шаблон. Шаблон создает Key Vault и секрет Key Vault.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" /></a>

2. Введите или выберите следующие значения.  Не выбирайте **Купить** после ввода значений.

    ![Шаблон Resource Manager на портале интеграции развертывания Key Vault](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Подписка**. Выберите нужную подписку Azure.
    * **Группа ресурсов**. Укажите уникальное имя. Запишите это имя, так как вы будете использовать ту же группу ресурсов для развертывания виртуальной машины в следующем сеансе. Размещение Key Vault и виртуальной машиной в той же группе ресурсов облегчает очистку ресурса в конце этого руководства.
    * **Расположение**. Выберите расположение.  Расположение по умолчанию: **Центральная часть США**.
    * **Имя Key Vault** . Укажите уникальное имя. 
    * **Идентификатор клиента**. Функция шаблона автоматически получает идентификатор клиента.  Не изменяйте значение по умолчанию.
    * **Ad User Id** (Идентификатор пользователя AD). Введите идентификатор объекта пользователя Azure AD, который вы получили на последнем этапе.
    * **Имя секрета.** Имя по умолчанию — **vmAdminPassword**. Если изменить имя секрета здесь, то нужно обновить имя секрета при развертывании виртуальной машины.
    * **Secret Value** (Значение секрета). Ведите ваш секрет.  Секрет — это пароль, используемый для входа в виртуальную машину. Рекомендуется использовать созданный на последнем шаге пароль.
    * **I agree to the terms and conditions state above** (Я принимаю указанные выше условия). Установите этот флажок.
3. Выберите **Изменить параметры** в верхней части экрана, чтобы взглянуть на шаблон.
4. Перейдите к строке 28 JSON-файла шаблона. Это определение ресурса Key Vault.
5. Перейдите к строке 35:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment` — это свойство Key Vault. Это свойство должно получить значение true, чтобы вы могли получить секреты из этого Key Vault во время развертывания.
6. Перейдите к строке 89. Это определение секрета Key Vault.
7. В нижней части страницы щелкните **Отклонить**. Не изменяйте ничего.
8. Проверьте, указали ли вы все значения, как показано на предыдущем снимке экрана, а затем в нижней части страницы щелкните **Купить**.
9. Выберите значок колокольчика (уведомление) в верхней части страницы, чтобы открыть область **Уведомления**. Подождите, пока ресурс не будет успешно развернут.
10. Выберите **Перейти к группе ресурсов** в области **Уведомления**. 
11. Выберите имя Key Vault, чтобы открыть его.
12. В левой области выберите **Политики доступа**. Имя (Active Directory) должно быть указано, в противном случае у вас нет разрешения на доступ к хранилищу ключей.
13. Выберите **Щелкните, чтобы показать политики расширенного доступа**. Обратите внимание, что выбрано значение **Включить доступ к Azure Resource Manager для развертывания шаблонов**. Это еще одно условие, чтобы интеграция Key Vault работала.

    ![Шаблон Resource Manager: политики доступа Key Vault для интеграции](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
14. Выберите **Свойства** в левой области.
15. Создайте копию **идентификатора ресурса**. Вам понадобиться этот идентификатор при развертывании виртуальной машины.  Формат идентификатора ресурса выглядит следующим образом:

    ```json
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблоны быстрого запуска Azure являются репозиторием для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом руководстве, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows).

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**. Это тот же сценарий, который использовался в статье [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
4. Шаблоном определено пять ресурсов:

    * `Microsoft.Storage/storageAccounts`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Прежде чем настраивать шаблон, рекомендуется получить основные сведения о нем.
5. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл на локальный компьютер с именем **azuredeploy.json**.
6. Повторите шаги 1–4, чтобы открыть следующий URL-адрес, а затем сохраните файл как **azuredeploy.parameters.json**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Изменение файла параметров

В файл шаблона не нужно вносить изменения.

1. Откройте файл **azuredeploy.parameters.json** в Visual Studio Code, если он еще не открыт.
2. Обновите параметр **adminPassword** до:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```
    Замените **идентификатор** идентификатором ресурса вашего Key Vault, созданным на предыдущем этапе.  

    ![Интеграция Key Vault и файла параметров развертывания виртуальной машины шаблона Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Укажите значения для таких свойств:

    * **adminUsername**. Имя учетной записи администратора виртуальной машины.
    * **dnsLabelPrefix**: назовите dnsLabelPrefix.
4. Сохраните изменения.

## <a name="deploy-the-template"></a>Развертывание шаблона

За инструкциями по развертыванию шаблона обратитесь к [этому разделу](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Вам нужно отправить оба файла — **azuredeploy.json** и **azuredeploy.parameters.json** в Cloud Shell. После этого используйте следующий сценарий PowerShell для развертывания шаблона:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name of the Key Vault"
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
```

При развертывании шаблона используйте ту же группу ресурсов, что и для Key Vault. Это упрощает очистку ресурсов. Вам нужно удалить одну группу ресурсов вместо двух.

## <a name="valid-the-deployment"></a>Проверка развертывания

После успешного развертывания виртуальной машины, протестируйте вход в систему с помощью пароля, сохраненного в Key Vault.

1. Откройте [портал Azure](https://portal.azure.com).
2. Выберите **Группы ресурсов**/**Имя_вашей_группы_ресурсов >**/**simpleWinVM**
3. В верхней части страницы выберите **Подключение**.
4. Щелкните **Скачать RDP-файл** и следуйте инструкциям, чтобы войти в виртуальную машину с помощью пароля, сохраненного в Key Vault.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

С помощью этого руководства вы получили секрет из Azure Key Vault и применили его в развертывании шаблона.  Чтобы узнать, как создавать связанные шаблоны, обратитесь к статье:

> [!div class="nextstepaction"]
> [Tutorial: Create linked Azure Resource Manager templates](./resource-manager-tutorial-create-linked-templates.md) (Руководство по созданию связанных шаблонов Azure Resource Manager)
