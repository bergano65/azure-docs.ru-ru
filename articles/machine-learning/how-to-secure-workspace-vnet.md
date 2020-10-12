---
title: Защита рабочей области Машинное обучение Azure с помощью виртуальных сетей
titleSuffix: Azure Machine Learning
description: Используйте изолированную виртуальную сеть Azure для защиты рабочей области Машинное обучение Azure и связанных с ней ресурсов.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, tracking-python, contperfq1
ms.openlocfilehash: 5d34fe403e0af4bc871ba176d0fa755650c26292
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776054"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Защита рабочей области Машинное обучение Azure с помощью виртуальных сетей

Из этой статьи вы узнаете, как защитить рабочую область Машинное обучение Azure и связанные с ней ресурсы в виртуальной сети.


Эта статья является второй частью серии из пяти частей, которая поможет вам в обеспечении безопасности Машинное обучение Azure рабочего процесса. Мы настоятельно рекомендуем прочесть первую [часть: обзор виртуальной сети](how-to-network-security-overview.md) , чтобы понять общую архитектуру. 

См. Другие статьи в этой серии:

[1. Обзор виртуальной сети](how-to-network-security-overview.md)  >  **2. Обеспечьте безопасность рабочей области**  >  [3. Обеспечьте безопасность среды обучения](how-to-secure-training-vnet.md)  >  [4. Обеспечьте безопасность окружения](how-to-secure-inferencing-vnet.md)  >  [5. Включить функциональные возможности студии](how-to-enable-studio-virtual-network.md)

В этой статье вы узнаете, как включить следующие ресурсы рабочих областей в виртуальной сети.
> [!div class="checklist"]
> - Рабочая область службы "Машинное обучение Azure"
> - Учетные записи хранения Azure
> - Машинное обучение Azure хранилища данных и наборы DataSet
> - Хранилище ключей Azure;
> - Реестр контейнеров Azure

## <a name="prerequisites"></a>Предварительные требования

+ Ознакомьтесь со статьей [Обзор сетевой безопасности](how-to-network-security-overview.md) , чтобы ознакомиться с общими сценариями виртуальной сети и общей архитектурой виртуальной сети.

+ Существующая виртуальная сеть и подсеть для использования с ресурсами вычислений.

+ Чтобы развернуть ресурсы в виртуальной сети или подсети, учетная запись пользователя должна иметь разрешения на доступ к следующим действиям в элементах управления доступом на основе ролей (RBAC) Azure.

    - "Microsoft. Network/virtualNetworks/соединение/действие" для ресурса виртуальной сети.
    - "Microsoft. Network/virtualNetworks/подсеть/соединение/действие" в ресурсе подсети.

    Дополнительные сведения о сетях RBAC с сетью см. в разделе [встроенные сетевые роли](/azure/role-based-access-control/built-in-roles#networking) .


## <a name="secure-the-workspace-with-private-endpoint"></a>Защита рабочей области с помощью частной конечной точки

Частная ссылка Azure позволяет подключаться к рабочей области с помощью частной конечной точки. Частная конечная точка — это набор частных IP-адресов в виртуальной сети. Затем можно ограничить доступ к рабочей области, чтобы она выполнялась только по частным IP-адресам. Частная ссылка помогает снизить риск утечка данных.

Дополнительные сведения о настройке рабочей области частной связи см. в разделе [Настройка частной ссылки](how-to-configure-private-link.md).

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Защита учетных записей хранения Azure с помощью конечных точек служб

Машинное обучение Azure поддерживает учетные записи хранения, настроенные для использования конечных точек службы или частных конечных точек. В этом разделе вы узнаете, как защитить учетную запись хранения Azure с помощью конечных точек службы. Сведения о частных конечных точках см. в следующем разделе.

> [!IMPORTANT]
> Вы можете разместить учетную запись хранения _по умолчанию_ для Машинного обучения Azure или _учетные записи хранения, отличные от используемых по умолчанию,_ в виртуальной сети.
>
> Учетная запись хранения по умолчанию автоматически подготавливается при создании рабочей области.
>
> Для учетных записей хранения, отличных от по умолчанию, параметр `storage_account` в функции [`Workspace.create()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-&preserve-view=true) позволяет указать настраиваемую учетную запись хранения по ИД ресурса Azure.

Чтобы использовать учетную запись хранения Azure для рабочей области в виртуальной сети, выполните следующие действия.

1. В портал Azure перейдите к службе хранилища, которую вы хотите использовать в рабочей области.

   [![Хранилище, подключенное к рабочей области Машинного обучения Azure](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. На странице Учетная запись службы хранилища выберите __брандмауэры и виртуальные сети__.

   ![Область "Брандмауэры и виртуальные сети" на странице службы хранилища Azure на портале Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. На странице __Брандмауэры и виртуальные сети__ выполните следующие действия.
    1. Выберите __Выбранные сети__.
    1. В разделе __Виртуальные сети__ щелкните ссылку __Добавить существующую виртуальную сеть__. Это действие добавляет виртуальную сеть, в которой размещается вычисление (см. шаг 1).

        > [!IMPORTANT]
        > Учетная запись хранения должна находиться в той же виртуальной сети и подсети, что и экземпляры или кластеры вычислительных ресурсов, используемые для обучения или аналитики.

    1. Установите флажок __Разрешить доверенным службам Microsoft доступ к этой учетной записи хранения__.

    > [!IMPORTANT]
    > При работе с пакетом SDK для Машинного обучения Azure среда разработки должна иметь возможность подключения к учетной записи хранения Azure. Если учетная запись хранения находится в виртуальной сети, брандмауэр должен разрешить доступ с IP-адреса среды разработки.
    >
    > Чтобы разрешить доступ к учетной записи хранения, посетите раздел __Брандмауэры и виртуальные сети__ для учетной записи хранения *из веб-браузера в клиенте разработки*. Затем используйте флажок __Добавить IP-адрес клиента__, чтобы добавить IP-адрес клиента в __ДИАПАЗОН АДРЕСОВ__. Можно также использовать поле __ДИАПАЗОН АДРЕСОВ__, чтобы вручную ввести IP-адрес среды разработки. После добавления IP-адреса клиента он может получить доступ к учетной записи хранения с помощью пакета SDK.

   [![Выберите область "Брандмауэры и виртуальные сети" на портале Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Защита учетных записей хранения Azure с помощью частных конечных точек

Машинное обучение Azure поддерживает учетные записи хранения, настроенные для использования конечных точек службы или частных конечных точек. Если учетная запись хранения использует частные конечные точки, необходимо настроить две частные конечные точки для учетной записи хранения по умолчанию.
1. Частная конечная точка с подресурсом целевого **объекта BLOB** .
1. Частная конечная точка с целевым ресурсом целевого **файла (общая** папка).

![Снимок экрана: страница настройки частной конечной точки с параметрами большого двоичного объекта и файла](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Чтобы настроить закрытую конечную точку для учетной записи хранения, которая **не** является хранилищем по умолчанию, выберите тип **целевого подресурса** , соответствующий добавляемой учетной записи хранения.

Дополнительные сведения см. в статье [использование частных конечных точек для службы хранилища Azure](../storage/common/storage-private-endpoints.md) .

## <a name="secure-datastores-and-datasets"></a>Безопасные хранилища данных и наборы

В этом разделе вы узнаете, как использовать хранилище данных и наборы DataSet в интерфейсе SDK с виртуальной сетью. Дополнительные сведения о работе с Studio см. [в статье использование машинное обучение Azure Studio в виртуальной сети](how-to-enable-studio-virtual-network.md).

Для доступа к данным с помощью пакета SDK необходимо использовать метод проверки подлинности, требуемый отдельной службой, в которой хранятся данные. Например, если зарегистрировать хранилище данных для доступа к Azure Data Lake Store Gen2, необходимо использовать субъект-службу, как описано в статье [Подключение к службам хранилища Azure](how-to-access-data.md#azure-data-lake-storage-generation-2).

### <a name="disable-data-validation"></a>Отключить проверку данных

По умолчанию Машинное обучение Azure выполняет проверку достоверности данных и учетных данных при попытке доступа к данным с помощью пакета SDK. Если данные находятся за виртуальной сетью, Машинное обучение Azure не сможет выполнить эти проверки. Чтобы избежать этого, необходимо создать хранилища данных и наборы DataSet, которые пропускают проверку.

### <a name="use-datastores"></a>Использование хранилищ данных

 Azure Data Lake Store Gen1 и Azure Data Lake Store по умолчанию пропускают проверку, поэтому дальнейшие действия не требуются. Однако для следующих служб можно использовать аналогичный синтаксис для пропуска проверки хранилища данных:

- Хранилище BLOB-объектов Azure
- Общая папка Azure
- PostgreSQL
- База данных SQL Azure

В следующем примере кода создается хранилище данных BLOB-объектов Azure и задаются наборы `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Использование наборов данных

Синтаксис для пропуска проверки набора данных аналогичен для следующих типов наборов данных:
- Файл с разделителями
- JSON 
- Parquet
- SQL
- Файл

Следующий код создает новый набор данных JSON и задает `validate=False` .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Безопасный Azure Key Vault

Машинное обучение Azure использует связанный экземпляр Key Vault для хранения следующих учетных данных:
* строка подключения связанной учетной записи хранения;
* пароли для экземпляров репозитория контейнеров Azure;
* строки подключения к хранилищам данных.

Чтобы использовать возможности экспериментирования в системе Машинного обучения Azure с хранилищем ключей Azure за виртуальной сетью, выполните следующие действия.

1. Перейдите к Key Vault, связанному с рабочей областью.

1. На странице __Key Vault__ в области слева выберите __сеть__.

1. На вкладке __брандмауэры и виртуальные сети__ выполните следующие действия.
    1. В разделе __Разрешить доступ из__выберите __Частная конечная точка и выбранные сети__.
    1. В разделе __Виртуальные сети__ выберите __Добавить существующие виртуальные сети__, чтобы добавить виртуальную сеть, в которой находится вычислительная среда для экспериментирования.
    1. В разделе __разрешить доверенным службам Майкрософт обходить этот брандмауэр?__ выберите __Да__.

   [![Раздел "Брандмауэры и виртуальные сети" в области хранилища ключей](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Включить реестр контейнеров Azure (запись контроля доступа)

Чтобы использовать реестр контейнеров Azure в виртуальной сети, необходимо соблюдать следующие требования.

* Реестр контейнеров Azure должен иметь версию Premium. Дополнительные сведения об обновлении см. в разделе [Изменение SKU](/azure/container-registry/container-registry-skus#changing-skus).

* Реестр контейнеров Azure должен находиться в той же виртуальной сети и подсети, что и учетная запись хранения и целевые объекты вычислений, используемые для обучения или аналитики.

* Рабочая область Машинного обучения Azure должна содержать [кластер вычислительных ресурсов Машинного обучения Azure](how-to-create-attach-compute-cluster.md).

    Если ACR находится за виртуальной сетью, Машинное обучение Azure не может использовать его напрямую для создания образов Docker. Вместо этого для построения образов используется кластер вычислений.

* Прежде чем использовать запись контроля доступа с Машинное обучение Azure в виртуальной сети, необходимо открыть инцидент поддержки, чтобы включить эту функцию. Дополнительные сведения см. в статье [Управление квотами и их увеличение](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

После выполнения этих требований выполните следующие действия, чтобы включить реестр контейнеров Azure.

1. Найдите имя реестра контейнеров Azure для рабочей области, используя один из следующих методов.

    __Портал Azure__

    В разделе "Обзор" вашей рабочей области значение __Реестр__ позволяет перейти в реестр контейнеров Azure.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Реестр контейнеров Azure для рабочей области" border="true":::.

    __Azure CLI__

    Если вы [установили расширение машинного обучения для интерфейса командной строки Azure](reference-azure-machine-learning-cli.md), можно использовать команду `az ml workspace show` для отображения сведений о рабочей области.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Эта команда возвращает значение следующего вида: `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. Последняя часть строки — это имя реестра контейнеров Azure для рабочей области.

1. Ограничьте доступ к виртуальной сети, выполнив действия, описанные в [разделе Настройка доступа к сети для реестра](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). При добавлении виртуальной сети выберите виртуальную сеть и подсеть для ресурсов Машинного обучения Azure.

1. Используйте пакет SDK Python для Машинного обучения Azure, чтобы настроить кластер вычислительных ресурсов для создания образов Docker. В следующем фрагменте кода показано, как это сделать.

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Учетная запись хранения, кластер вычислений и реестр контейнеров Azure должны находиться в одной подсети виртуальной сети.
    
    Дополнительную информацию см. в справочном руководстве по методу [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-&preserve-view=true).

1. Примените следующий шаблон Azure Resource Manager. Этот шаблон позволяет рабочей области взаимодействовать с ACR.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "basic",
            "name": "basic"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="next-steps"></a>Дальнейшие шаги

Эта статья является частью серии виртуальных сетей из четырех частей. Ознакомьтесь с остальными статьями, чтобы узнать, как защитить виртуальную сеть.

* [Часть 1. Обзор виртуальной сети](how-to-network-security-overview.md)
* [Часть 3. Защита среды обучения](how-to-secure-training-vnet.md)
* [Часть 4. Защита окружения](how-to-secure-inferencing-vnet.md)
* [Часть 5. Включение функций Studio](how-to-enable-studio-virtual-network.md)