---
title: Управляемое приложение с управляемой идентичностью
description: Настройте управляемое приложение с управляемой идентификацией для связи с существующими ресурсами, управления ресурсами Azure и предоставления оперативной идентификации для журнала activity Log.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651661"
---
# <a name="azure-managed-application-with-managed-identity"></a>Управляемое приложение Azure с управляемой идентификацией

> [!NOTE]
> Управляемая поддержка идентификации для управляемых приложений в настоящее время находится в предварительном просмотре. Пожалуйста, используйте версию api 2018-09-01-preview для использования управляемой идентичности.

Узнайте, как настроить управляемое приложение для содержать управляемую идентификацию. Управляемая идентификация может использоваться для предоставления клиенту управляемого приложения доступа к дополнительным существующим ресурсам. Удостоверения управляются платформой Azure, и для них не нужно подготавливать или изменять секреты. Подробнее об управляемых идентификаторах в active-каталоге Azure (AAD) можно узнать об [управляемых идентификаторах для ресурсов Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Приложению можно предоставить два типа удостоверений:

- **Назначаемое системой удостоверение** привязывается к приложению и удаляется при удалении приложения. Приложение может иметь только одно назначаемое системой удостоверение.
- **Идентификационная личность, назначенная пользователем,** является автономным ресурсом Azure, который может быть назначен вашему приложению. Приложение может иметь несколько назначаемых пользователем удостоверений.

## <a name="how-to-use-managed-identity"></a>Как использовать управляемую идентификацию

Управляемая идентификация позволяет использовать множество сценариев для управляемых приложений. Некоторые общие сценарии, которые могут быть решены:

- Развертывание управляемого приложения, связанного с существующими ресурсами Azure. Примером может быть развертывание виртуальной машины Azure (VM) в управляемом приложении, которое присоединено к [существующему сетевому интерфейсу.](../../virtual-network/virtual-network-network-interface-vm.md)
- Предоставление управляемому приложению и доступу издателя к ресурсам Azure за пределами **управляемой группы ресурсов.**
- Предоставление оперативной идентификации управляемых приложений для журнала активности и других служб в Azure.

## <a name="adding-managed-identity"></a>Добавление управляемой идентичности

Создание управляемого приложения с управляемой идентификацией требует дополнительного свойства, которое должно быть установлено на ресурсе Azure. В следующем примере показано свойство **идентификации** образца:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Существует два распространенных способа создания управляемого приложения с **помощью шаблонов identity:** [CreateUIDefinition.json](./create-uidefinition-overview.md) и [шаблонов управления ресурсами Azure.](../templates/template-syntax.md) Для простых сценариев создания одного создается, CreateUIDefinition следует использовать для включения управляемой идентификации, поскольку она обеспечивает более богатый опыт. Однако при работе с передовыми или сложными системами, требующими автоматического или нескольких развертываний управляемых приложений, можно использовать шаблоны.

### <a name="using-createuidefinition"></a>Использование CreateUIDefinition

Управляемое приложение можно настроить с управляемой идентичностью через [CreateUIDefinition.json](./create-uidefinition-overview.md). В [разделе выводов](./create-uidefinition-overview.md#outputs)ключ `managedIdentity` может быть использован для переопределения свойства идентификации шаблона управляемого приложения. Образец ниже позволит **системы назначенных** удостоверения личности на управляемое приложение. Более сложные объекты идентификации могут быть сформированы с помощью элементов CreateUIDefinition для ввода. Эти входные данные можно использовать для построения управляемых приложений с **помощью удостоверения, установленного пользователем.**

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Когда использовать CreateUIDefinition для управляемой идентичности

Ниже приведены некоторые рекомендации о том, когда использовать CreateUIDefinition для включения управляемой идентификации в управляемых приложениях.

- Создание управляемого приложения проходит через портал или рынок Azure.
- Управляемая идентичность требует сложного ввода потребителей.
- Управляемая идентификация необходима при создании управляемого приложения.

#### <a name="systemassigned-createuidefinition"></a>СистемаНазначено СозданиеUIDefinition

Базовое CreateUIDefinition, позволяющее systemAssigned identity для управляемого приложения.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Базовое CreateUIDefinition, которое принимает **назначенный пользователем** ресурс идентификации в качестве ввода и позволяет UserAssigned identity для управляемого приложения.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

Приведенный выше CreateUIDefinition.json создает пользовательский интерфейс, который имеет текстовый ящик для потребителя для ввода идентификатора ресурса Azure, **назначенного пользователем.** Сгенерированный опыт будет выглядеть следующим:

![Пример назначенных пользователем идентификационных данных CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Использование шаблонов диспетчера ресурсов Azure

> [!NOTE]
> Шаблоны управляемых приложений Marketplace автоматически генерируются для клиентов, проходящих через портал Azure, создавая опыт работы.
> Для этих сценариев ключ `managedIdentity` вывода на CreateUIDefinition должен использоваться для включения идентификации.

Управляемая идентификация также может быть включена через шаблоны менеджера ресурсов Azure. Образец ниже позволит **системы назначенных** удостоверения личности на управляемое приложение. Более сложные объекты идентификации могут быть сформированы с помощью параметров шаблона Azure Resource Manager для предоставления входных данных. Эти входные данные можно использовать для построения управляемых приложений с **помощью удостоверения, установленного пользователем.**

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>При использовании шаблонов управления ресурсами Azure для управляемой identity

Ниже приведены некоторые рекомендации относительно того, когда использовать шаблоны Менеджера ресурсов Azure для включения управляемой идентификации в управляемых приложениях.

- Управляемые приложения могут быть программно развернуты на основе шаблона.
- Пользовательские назначения ролей для Управляемой идентификации необходимы для предоставления Управляемого приложения.
- Управляемое приложение не нуждается в портале Azure и потоке создания рынка.

#### <a name="systemassigned-template"></a>СистемаНазначенный шаблон

Базовый шаблон Azure Resource Manager, развертывающий управляемое приложение с **системным удостоверением** личности.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>ПользовательскийШаблон

Базовый шаблон azure Resource Manager, развертывающий управляемое приложение с **удостоверением личности пользователя.**

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Предоставление доступа к ресурсам Azure

После получения управляемого приложения удостоверение личности может получить доступ к существующим ресурсам azure. Этот процесс можно сделать с помощью интерфейса управления доступом (IAM) на портале Azure. Имя управляемого приложения или **удостоверение, назначенное пользователем,** можно искать для добавления назначения ролей.

![Добавление ролевого задания для управляемого приложения](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Связывание существующих ресурсов Azure

> [!NOTE]
> Перед развертыванием управляемого приложения необходимо [настроить](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) **удостоверение, назначенное пользователем.** Кроме того, связанное развертывание ресурсов управляемых приложений поддерживается только для **рынка.**

Управляемый identity также может использоваться для развертывания управляемого приложения, требующего доступа к существующим ресурсам во время его развертывания. Когда управляемое приложение подготовлено клиентом, **имена, назначенные пользователем,** могут быть добавлены для предоставления дополнительных разрешений для развертывания **mainTemplate.**

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Авторcreate CreateUIDefinition с помощью связанного ресурса

При увязке развертывания управляемого приложения с существующими ресурсами необходимо предоставить как существующий ресурс Azure, так и **удостоверение, назначенное пользователем,** с применимым назначением ролей на этом ресурсе.

 Образец CreateUIDefinition, который требует двух входов: идентификатор ресурса сетевого интерфейса и идентификатор ресурса идентификации пользователя.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Это CreateUIDefinition.json создает пользовательский интерфейс, который имеет два поля. Первое поле позволяет пользователю ввести идентификатор ресурса Azure для ресурса, связанного с развертыванием управляемых приложений. Второй вариант заключается в вводе идентификатором ресурсов Azure, **назначенным пользователем,** который имеет доступ к связанному ресурсу Azure. Сгенерированный опыт будет выглядеть следующим:

![Образец CreateUIDefinition с двумя входными данными: идентификатор ресурса сетевого интерфейса и идентификатор ресурса, назначенный пользователем](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Авторство mainTemplate с помощью связанного ресурса

В дополнение к обновлению CreateUIDefinition, основной шаблон также должен быть обновлен, чтобы принять пройденое в подключенном идентификаторе ресурса. Основной шаблон может быть обновлен, чтобы принять новый выход путем добавления нового параметра. Поскольку `managedIdentity` выход переопределяет значение в шаблоне управляемого приложения, он не передается в основной шаблон и не должен быть включен в раздел параметров.

Основной шаблон образца, который устанавливает сетевой профиль к существующему сетевому интерфейсу, предоставленному CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Потребление управляемого приложения с помощью связанного ресурса

После создания пакета управляемых приложений управляемое приложение можно использовать через портал Azure. Прежде чем его можно употреблять, есть несколько предварительных шагов.

- Необходимо создать экземпляр требуемого связанного ресурса Azure.
- Необходимо создать **и** дать [назначения ролей](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) связанному ресурсу.
- Существующий идентификатор связанного ресурса и **идентификатор идентификации пользователя** предоставляются CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Доступ к маркеру управляемой идентичности

Теперь к маркеру управляемого приложения можно `listTokens` получить доступ через api от арендатора издателя. Пример запроса может выглядеть следующим:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Запрос Параметры тела:

Параметр | Обязательно | Описание
---|---|---
авторизацияАудитория | *Нет* | APP ID URI целевого ресурса. Это также `aud` (аудитория) претензии выданного токена. Значение по умолчанию "https://management.azure.com/
userAssignedIdentities | *Нет* | Список управляемых пользовательских идентификаторов для получения маркера. Если не `listTokens` указано, вернет токен для назначенного системой управляемого удостоверения.


Образец ответа может выглядеть следующим:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

Ответ будет содержать массив токенов `value` под свойством:

Параметр | Описание
---|---
access_token | Запрашиваемый маркер доступа.
expires_in | Количество секунд, в течение чем контонек доступа будет действительным.
expires_on | Период времени после истечения срока действия маркера доступа. Это представлено как количество секунд от эпохи.
not_before | Временной промежуток, когда токен доступа вступает в силу. Это представлено как количество секунд от эпохи.
авторизацияАудитория | Для `aud` этого был запрошен токен доступа. Это то же самое, что `listTokens` было предусмотрено в запросе.
resourceId | Идентификатор ресурса Azure для выданного токена. Это либо управляемый идентификатор приложения, либо идентификатор идентификации пользователя.
token_type | Тип маркера.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Как настроить управляемое приложение с помощью пользовательского поставщика](../custom-providers/overview.md)
