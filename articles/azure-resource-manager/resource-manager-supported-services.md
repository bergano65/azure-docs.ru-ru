---
title: Поставщики ресурсов и типы ресурсов
description: Описывает поставщиков ресурсов, которые поддерживают диспетчер ресурсов, их схемы и доступные версии API, а также регионы, допускающие размещение ресурсов.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 73cc053ab2ca19f42e3c45b8350d1e2baedfcc7a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422198"
---
# <a name="azure-resource-providers-and-types"></a>Поставщики и типы ресурсов Azure

При развертывании ресурсов часто бывает необходимо получить сведения о типах и поставщиках ресурсов. Например, если вы хотите сохранить ключи и секреты, вы работаете с поставщиком ресурсов Microsoft. KeyVault. Этот поставщик ресурсов предоставляет тип ресурсов, называемый хранилищами для создания хранилища ключей.

Формат имени типа ресурса: **{поставщик_ресурса}/{тип_ресурса}** . Тип ресурса для хранилища ключей — **Microsoft.KeyVault/vaults**.

В этой статье раскрываются следующие темы:

* просмотр всех поставщиков ресурсов в Azure;
* проверка состояния регистрации поставщика ресурсов;
* регистрация поставщика ресурсов;
* просмотр типов ресурсов для поставщика ресурсов;
* просмотр допустимых расположений для типа ресурса;
* просмотр допустимых версий API для типа ресурса.

Эти действия можно выполнить с помощью портал Azure, Azure PowerShell или Azure CLI.

Список поставщиков ресурсов для служб Azure см. в статье [поставщики ресурсов для служб Azure](azure-services-resource-providers.md).

## <a name="azure-portal"></a>портале Azure

Чтобы просмотреть всех поставщиков ресурсов, а также состояние регистрации для подписки, сделайте следующее:

1. Войдите на [портале Azure](https://portal.azure.com).
2. В меню портал Azure выберите **все службы**.

    ![выбор подписок](./media/resource-manager-supported-services/select-all-services.png)

3. В поле **Все службы** введите **подписка**, а затем выберите **Подписки**.
4. Выберите подписку из списка подписок для просмотра.
5. Выберите **Поставщики ресурсов**, а затем просмотрите список доступных поставщиков ресурсов.

    ![отображение поставщиков ресурсов](./media/resource-manager-supported-services/show-resource-providers.png)

6. Регистрация поставщика ресурсов настраивает подписку для работы с поставщиком ресурсов. Областью регистрации всегда является подписка. По умолчанию многие поставщики ресурсов регистрируются автоматически. Тем не менее, некоторые поставщики ресурсов может потребоваться зарегистрировать вручную. Чтобы зарегистрировать поставщик ресурсов, необходимо иметь разрешение на выполнение `/register/action` операции для поставщика ресурсов. Эта операция включается в роли участника и владельца. Чтобы зарегистрировать поставщика ресурсов, выберите **Регистрация**. На предыдущем снимке экрана ссылка **Регистрация** выделена в поле **Microsoft.Blueprint**.

    Вы не можете отменить регистрацию поставщика ресурсов, если у вас по-прежнему есть типы ресурсов от этого поставщика ресурсов в вашей подписке.

Чтобы просмотреть сведения для конкретного поставщика ресурсов, сделайте следующее:

1. Войдите на [портале Azure](https://portal.azure.com).
2. В меню портал Azure выберите **все службы**.
3. В поле **Все службы** введите **обозреватель ресурсов** и выберите **Обозреватель ресурсов**.

    ![Пункт "Все службы"](./media/resource-manager-supported-services/select-resource-explorer.png)

4. Разверните список **Поставщики**, щелкнув стрелку вправо.

    ![выбор поставщиков](./media/resource-manager-supported-services/select-providers.png)

5. Разверните поставщик ресурсов и тип ресурса, который вы хотите просмотреть.

    ![Выбор типа ресурсов](./media/resource-manager-supported-services/select-resource-type.png)

6. Диспетчер ресурсов поддерживается во всех регионах, однако развертываемые вами ресурсы могут поддерживаться не во всех регионах. Кроме того, на вашу подписку могут налагаться ограничения, которые препятствуют использованию определенных регионов, поддерживающих ресурс. Обозреватель ресурсов отображает допустимые расположения для типа ресурса.

    ![Отображение расположений](./media/resource-manager-supported-services/show-locations.png)

7. Версия API соответствует версии операций API REST, выполняемых поставщиком ресурсов. При появлении в поставщике ресурсов новых возможностей выпускается новая версия REST API. Обозреватель ресурсов отображает допустимые версии API для типа ресурса.

    ![Отображение версий API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы просмотреть всех поставщиков ресурсов в Azure, а также состояние регистрации для подписки, используйте команду ниже:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Она возвращает результаты, подобные следующим:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Регистрация поставщика ресурсов настраивает подписку для работы с поставщиком ресурсов. Областью регистрации всегда является подписка. По умолчанию многие поставщики ресурсов регистрируются автоматически. Тем не менее, некоторые поставщики ресурсов может потребоваться зарегистрировать вручную. Чтобы зарегистрировать поставщик ресурсов, необходимо иметь разрешение на выполнение `/register/action` операции для поставщика ресурсов. Эта операция включается в роли участника и владельца.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Она возвращает результаты, подобные следующим:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Вы не можете отменить регистрацию поставщика ресурсов, если у вас по-прежнему есть типы ресурсов от этого поставщика ресурсов в вашей подписке.

Чтобы просмотреть сведения для конкретного поставщика ресурсов, используйте команду ниже:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Она возвращает результаты, подобные следующим:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Чтобы просмотреть типы ресурсов для поставщика ресурсов, используйте команду ниже:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Возвращаемые данные:

```powershell
batchAccounts
operations
locations
locations/quotas
```

Версия API соответствует версии операций API REST, выполняемых поставщиком ресурсов. При появлении в поставщике ресурсов новых возможностей выпускается новая версия REST API.

Чтобы получить список доступных версий API для типа ресурса, используйте следующую команду:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Возвращаемые данные:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Диспетчер ресурсов поддерживается во всех регионах, однако развертываемые вами ресурсы могут поддерживаться не во всех регионах. Кроме того, на вашу подписку могут налагаться ограничения, которые препятствуют использованию определенных регионов, поддерживающих ресурс.

Чтобы получить список поддерживаемых расположений для типа ресурса, используйте следующую команду:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Возвращаемые данные:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Интерфейс командной строки Azure

Чтобы просмотреть всех поставщиков ресурсов в Azure, а также состояние регистрации для подписки, используйте команду ниже:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Она возвращает результаты, подобные следующим:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Регистрация поставщика ресурсов настраивает подписку для работы с поставщиком ресурсов. Областью регистрации всегда является подписка. По умолчанию многие поставщики ресурсов регистрируются автоматически. Тем не менее, некоторые поставщики ресурсов может потребоваться зарегистрировать вручную. Чтобы зарегистрировать поставщик ресурсов, необходимо иметь разрешение на выполнение `/register/action` операции для поставщика ресурсов. Эта операция включается в роли участника и владельца.

```azurecli
az provider register --namespace Microsoft.Batch
```

Оно возвращает сообщение о выполнении регистрации.

Вы не можете отменить регистрацию поставщика ресурсов, если у вас по-прежнему есть типы ресурсов от этого поставщика ресурсов в вашей подписке.

Чтобы просмотреть сведения для конкретного поставщика ресурсов, используйте команду ниже:

```azurecli
az provider show --namespace Microsoft.Batch
```

Она возвращает результаты, подобные следующим:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Чтобы просмотреть типы ресурсов для поставщика ресурсов, используйте команду ниже:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Возвращаемые данные:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Версия API соответствует версии операций API REST, выполняемых поставщиком ресурсов. При появлении в поставщике ресурсов новых возможностей выпускается новая версия REST API.

Чтобы получить список доступных версий API для типа ресурса, используйте следующую команду:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Возвращаемые данные:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Диспетчер ресурсов поддерживается во всех регионах, однако развертываемые вами ресурсы могут поддерживаться не во всех регионах. Кроме того, на вашу подписку могут налагаться ограничения, которые препятствуют использованию определенных регионов, поддерживающих ресурс.

Чтобы получить список поддерживаемых расположений для типа ресурса, используйте следующую команду:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Возвращаемые данные:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Дополнительная информация

* Сведения о создании шаблонов Resource Manager см. в статье [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md). 
* Схемы шаблонов поставщиков ресурсов см. в статье [Define resources in Azure Resource Manager templates](/azure/templates/) (Определение ресурсов в шаблонах Azure Resource Manager).
* Список поставщиков ресурсов для служб Azure см. в статье [поставщики ресурсов для служб Azure](azure-services-resource-providers.md).
* Чтобы просмотреть операции для поставщика ресурсов, ознакомьтесь с [Azure REST API](/rest/api/).
