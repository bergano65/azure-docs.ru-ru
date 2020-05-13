---
title: Создание рабочей области с помощью шаблона Azure Resource Manager
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать шаблон Azure Resource Manager для создания новой Машинное обучение Azure рабочей области.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: 568bcdcfd8ae50fff58964ecc74176b151db22a4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121326"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Создание рабочей области для Машинное обучение Azure с помощью шаблона Azure Resource Manager

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

Из этой статьи вы узнаете несколько способов создания Машинное обучение Azure рабочей области с помощью шаблонов Azure Resource Manager. Шаблон Resource Manager позволяет легко создать все ресурсы в отдельной скоординированной операции. Шаблон представляет собой документ JSON, в котором определены необходимые для развертывания ресурсы. Также здесь можно указать параметры развертывания. Эти параметры позволят предоставить входные значения при использовании шаблона.

Узнайте подробнее [о развертывании приложения с помощью шаблона диспетчера ресурсов Azure](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Если у вас ее нет, попробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree).

* Чтобы использовать шаблон из командной строки, вам потребуется [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) или [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Шаблон Resource Manager

Следующий шаблон диспетчер ресурсов можно использовать для создания Машинное обучение Azure рабочей области и связанных ресурсов Azure:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Этот шаблон создает следующие ресурсы Azure:

* группа ресурсов Azure.
* Учетная запись хранения Azure
* Azure Key Vault
* Azure Application Insights
* Реестр контейнеров Azure
* Рабочая область службы "Машинное обучение Azure"

Группа ресурсов выполняет функции контейнера, который содержит службы. Для рабочего пространства Службы машинного обучения Azure нужно несколько служб.

Наш пример шаблона принимает два параметра.

* **Расположение** указывает, где будут созданы группа ресурсов и службы.

    Этот шаблон будет использовать выбранное расположение для большинства ресурсов. Исключением является служба Application Insights, которая доступна не во всех тех расположениях, в которых доступны другие службы. Если вы выберете расположение, где эта служба не доступна, ее экземпляр будет создан в регионе "центрально-южная часть США".

* **Имя рабочего пространства** Службы машинного обучения Azure в удобном для пользователя формате.

    > [!NOTE]
    > Имя рабочей области не учитывает регистр.

    Имена для всех остальных служб создаются случайным образом.

> [!TIP]
> Хотя шаблон, связанный с этим документом, создает новый реестр контейнеров Azure, вы также можете создать новую рабочую область, не создавая реестр контейнеров. Он будет создан при выполнении операции, требующей наличия реестра контейнеров. Например, обучение или развертывание модели.
>
> Вы также можете сослаться на существующий реестр контейнеров или учетную запись хранения в шаблоне Azure Resource Manager, а не создать новый.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Дополнительные сведения о шаблонах вы найдете в следующих статьях.

* [Шаблоны диспетчера ресурсов Azure](../azure-resource-manager/templates/template-syntax.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/templates/deploy-powershell.md)
* [Типы ресурсов Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Расширенный шаблон

В следующем примере шаблона показано, как создать рабочую область с тремя параметрами:

* Включить параметры высокой конфиденциальности для рабочей области
* Включение шифрования для рабочей области
* Использует существующий Azure Key Vault для получения ключей, управляемых клиентом

Дополнительные сведения см. [в разделе Шифрование неактивных](concept-enterprise-security.md#encryption-at-rest)данных.

> [!IMPORTANT]
> Перед использованием этого шаблона необходимо выполнить некоторые особые требования к подписке:
> * Приложение __машинное обучение Azure__ должно быть __участником__ подписки Azure.
> * Необходимо иметь существующий Azure Key Vault, содержащий ключ шифрования.
> * Необходимо иметь политику доступа в Azure Key Vault, которая предоставляет __доступ к приложению__ __Azure Cosmos DB__ для __получения__, __переноса и распаковки__.
> * Azure Key Vault должны находиться в том же регионе, где вы планируете создать рабочую область Машинное обучение Azure.
> * Ваша подписка должна поддерживать __ключи, управляемые клиентом__ , для Azure Cosmos DB.

__Чтобы добавить машинное обучение Azure приложение в качестве участника__, используйте следующие команды:

1. Чтобы выполнить аутентификацию в Azure с помощью интерфейса командной строки, используйте следующую команду:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. Чтобы получить идентификатор объекта Машинное обучение Azure приложения, используйте следующую команду. Это значение может отличаться для каждой подписки Azure.

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    Эта команда возвращает идентификатор объекта, который является идентификатором GUID.

1. Чтобы добавить идентификатор объекта в качестве участника в подписку, используйте следующую команду. Замените `<object-ID>` на идентификатор GUID из предыдущего шага. Замените `<subscription-ID>` именем или идентификатором подписки Azure:

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

__Чтобы добавить ключ в Azure Key Vault__, используйте сведения в разделе [Добавление ключа, секрета или сертификата в раздел хранилище ключей](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) статьи __Управление Key Vault помощью Azure CLI__ .

__Чтобы добавить политику доступа в хранилище ключей, используйте следующие команды__:

1. Чтобы получить идентификатор объекта Azure Cosmos DB приложения, используйте следующую команду. Это значение может отличаться для каждой подписки Azure.

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    Эта команда возвращает идентификатор объекта, который является идентификатором GUID.

1. Чтобы задать политику, используйте следующую команду. Замените на `<keyvault-name>` имя существующего Azure Key Vault. Замените `<object-ID>` на идентификатор GUID из предыдущего шага:

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

__Чтобы включить управляемые клиентом ключи для Azure Cosmos DB__, отправьте почту по azurecosmosdbcmk@service.microsoft.com идентификатору подписки Azure. Дополнительные сведения см. в статье [Настройка ключей, управляемых клиентом, для учетной записи Azure Cosmos](..//cosmos-db/how-to-setup-cmk.md).

__Чтобы получить значения__ для параметра `cmk_keyvault` (идентификатор Key Vault) и `resource_cmk_uri` параметров (URI ключа), необходимых для этого шаблона, выполните следующие действия.

1. Чтобы получить идентификатор Key Vault, используйте следующую команду:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    Эта команда возвращает значение следующего вида: `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault`.

1. Чтобы получить значение универсального кода ресурса (URI) для управляемого клиентом ключа, используйте следующую команду:

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    Эта команда возвращает значение следующего вида: `https://mykeyvault.vault.azure.net/keys/mykey/{guid}`.

__Пример шаблона__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> После создания рабочей области нельзя изменить параметры конфиденциальных данных, шифрования, идентификатора хранилища ключей или идентификаторов ключей. Чтобы изменить эти значения, необходимо создать новую рабочую область, используя новые значения.

## <a name="use-the-azure-portal"></a>Использование портала Azure

1. Выполните инструкции из раздела [Развертывание ресурсов с помощью настраиваемого шаблона](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Дойдя до экрана __Изменить шаблон__, вставьте шаблон, настроенный при изучении этого раздела.
1. Щелкните __Сохранить__, чтобы применить этот шаблон. Укажите описанные ниже сведения и подтвердите согласие с предложенными условиями.

   * Подписка. Выберите подписку Azure, которая будет использоваться для этих ресурсов.
   * Группа ресурсов. Выберите или создайте группу ресурсов для хранения служб.
   * Имя рабочей области. имя, которое будет использоваться для создаваемой рабочей области Машинное обучение Azure. Для имени рабочего пространства допускается длина от 3 до 33 символов. Имя может содержать только буквы, цифры и дефис ("-").
   * Расположение. Выберите расположение, в котором будут создаваться ресурсы.

Дополнительные сведения см. в разделе [Развертывание ресурсов с помощью настраиваемого шаблона](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Использование Azure PowerShell

В этом примере предполагается, что вы сохранили шаблон в файл с именем `azuredeploy.json` в текущем каталоге.

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Дополнительные сведения см. в статьях [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) и [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Использование Azure CLI

В этом примере предполагается, что вы сохранили шаблон в файл с именем `azuredeploy.json` в текущем каталоге.

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Дополнительные сведения см. в статьях [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/templates/deploy-cli.md) и [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="resource-provider-errors"></a>Ошибки поставщика ресурсов

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Политики доступа Azure Key Vault и шаблоны Azure Resource Manager

При использовании шаблона Azure Resource Manager для создания рабочей области и связанных с ней ресурсов (включая Azure Key Vault) несколько раз. Например, использование шаблона несколько раз с теми же параметрами, что и часть конвейера непрерывной интеграции и развертывания.

Большинство операций создания ресурсов через шаблоны идемпотентными, но Key Vault удаляет политики доступа каждый раз при использовании шаблона. Очистка политик доступа нарушает доступ к Key Vault для любой существующей рабочей области, которая ее использует. Например, может произойти сбой функции "отключить/создать" для виртуальных машин Azure Notebook.  

Чтобы избежать этой проблемы, рекомендуется использовать один из следующих подходов.

* Не развертывайте шаблон более одного раза для одних и тех же параметров. Или удалите существующие ресурсы перед использованием шаблона для их повторного создания.

* Изучите политики доступа Key Vault, а затем используйте эти политики, чтобы задать `accessPolicies` свойство шаблона. Чтобы просмотреть политики доступа, используйте следующую команду Azure CLI.

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Дополнительные сведения об использовании `accessPolicies` раздела шаблона см. в [справочнике по объектам акцессполициентри](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Убедитесь, что Key Vault ресурс уже существует. Если это так, не создавайте его повторно с помощью шаблона. Например, чтобы использовать существующий Key Vault вместо создания нового, внесите следующие изменения в шаблон:

    * **Добавьте** параметр, который принимает идентификатор существующего Key Vault ресурса:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Удалите** раздел, который создает ресурс KEY Vault:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Удалите** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` строку из `dependsOn` раздела рабочей области. Также **измените** `keyVault` запись в `properties` разделе рабочей области, чтобы она ссылалась на `keyVaultId` параметр:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    После внесения этих изменений можно указать идентификатор существующего Key Vault ресурса при запуске шаблона. Затем шаблон повторно использует Key Vault, присвоив `keyVault` свойству рабочей области значение идентификатора.

    Чтобы получить идентификатор Key Vault, можно сослаться на выходные данные исходного шаблона Run или использовать Azure CLI. Следующая команда является примером использования Azure CLI для получения Key Vaultного идентификатора ресурса:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Эта команда возвращает значение следующего вида:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывайте ресурсы с помощью шаблонов диспетчер ресурсов и диспетчер ресурсов REST API](../azure-resource-manager/templates/deploy-rest.md).
* [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
