---
title: Аутентификация на основе управляемых удостоверений
description: Доступ к ресурсам в других клиентах Active Directory Azure без входинысты с учетными данными или секретами с помощью управляемой идентификации
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117371"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Authenticate доступ к ресурсам Azure с помощью управляемых идентификаторов в приложениях логики Azure

Для доступа к ресурсам в других клиентах Azure Active Directory (Azure AD) и аутентификации вашей личности без входини, приложение логики может использовать [управляемую идентификацию](../active-directory/managed-identities-azure-resources/overview.md) (ранее управляемая идентификация службы или MSI), а не учетные данные или секреты. Azure управляет этим удостоверением за вас и помогает защитить учетные данные, потому что вам не нужно предоставлять или сменять секреты.

Приложения Azure Logic Apps поддерживают как [*назначенные системой,*](../active-directory/managed-identities-azure-resources/overview.md) так и [*управляемые*](../active-directory/managed-identities-azure-resources/overview.md) идентификаторами. Приложение логики может использовать либо установленную системой идентификацию, либо *один* значок, назначенный пользователем, которым можно поделиться в группе логических приложений, но не обоими. В настоящее время только [конкретные встроенные триггеры и действия](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) поддерживают управляемые идентификаторы, не управляемые разъемы или соединения, например:

* HTTP
* Проверка
* Cлужба управления Azure API 
* Службы приложений Azure

В этой статье показано, как настроить оба типа управляемых идентификационных данных для приложения логики. Дополнительные сведения см. в следующих статьях:

* [Триггеры и действия, поддерживающие управляемые идентификаторы](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Поддерживаемые типы аутентификации на исходящих вызовах](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Управляемые ограничения идентификации для логических приложений](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Службы Azure, поддерживающие аутентификацию Azure AD с управляемыми идентификаторами](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет ее, вы можете [зарегистрироваться для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/). Как управляемый идентификатор, так и целевой ресурс Azure, где вам нужен доступ, должны использовать одну и ту же подписку Azure.

* Чтобы предоставить управляемый доступ к ресурсу Azure, необходимо добавить роль в целевой ресурс для этой идентификации. Для добавления ролей требуется [разрешение администратора Azure AD,](../active-directory/users-groups-roles/directory-assign-admin-roles.md) которое может присвоить значениям для идентификационных данных в соответствующем наемном администраторе Azure AD.

* Целевой ресурс Azure, к который требуется получить доступ. На этом ресурсе вы добавите роль для управляемого итев, которая помогает логике приложения аутентифицировать доступ к целевому ресурсу.

* Приложение логики, где требуется использовать [триггер или действия, поддерживающие управляемые идентификаторы](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Включение управляемого удостоверения

Чтобы настроить управляемую идентификацию, которую вы хотите использовать, перейдите по ссылке для этой идентификации:

* [Назначаемое системой удостоверение](#system-assigned)
* [Назначаемое пользователем удостоверение](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Включить установленное удостоверение, назначенное системой

В отличие от удостоверений, назначенных пользователем, вам не нужно вручную создавать удостоверение, назначенное системой. Для настройки системного удостоверения для приложения логики приведены в изыскание:

* [Портал Azure](#azure-portal-system-logic-app)
* [Шаблоны диспетчера ресурсов Azure](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Включить систему назначенных удостоверений личности на портале Azure

1. На [портале Azure](https://portal.azure.com) откройте приложение логики в конструкторе приложений логики.

1. В меню приложения логики в разделе **Параметры** выберите **Идентификатор**. Выберите **систему, назначенную** > **на** > **Сохранение**. Когда Azure подскажет вам подтвердить, выберите **Yes**.

   ![Включить установленное системой удостоверение](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Если у вас есть ошибка, в которой может быть только одна управляемая идентификация, приложение логики уже связано с удостоверением, назначенным пользователем. Прежде чем добавить установленное системой удостоверение личности, необходимо сначала *удалить* удостоверение, назначенное пользователем, из приложения логики.

   Приложение логики теперь может использовать установленное системой удостоверение, которое зарегистрировано в Active Directory Azure и представлено идентификатором объекта.

   ![Идентификатор объекта для установленного системой удостоверения](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Свойство | Значение | Описание |
   |----------|-------|-------------|
   | **Идентификатор объекта** | <*идентификационный ресурс-ID*> | Глобальный уникальный идентификатор (GUID), представляющий системно-назначенное удостоверение для приложения логики в аниматоре Azure AD |
   ||||

1. Теперь выполните [шаги, которые дают этому identity доступ к ресурсу](#access-other-resources) позже в этой теме.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Включить системное удостоверение в шаблоне менеджера ресурсов Azure

Для автоматизации создания и развертывания ресурсов Azure, таких как логические приложения, можно использовать [шаблоны Azure Resource Manager.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) Для включения управляемого идентификатора системы для `identity` приложения логики в шаблоне добавьте объект и свойство `type` ребенка в определение ресурсов приложения логики в шаблоне, например:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Когда Azure создает определение ресурсов `identity` приложения логики, объект получает следующие свойства:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property (JSON) | Значение | Описание |
|-----------------|-------|-------------|
| `principalId` | <*основной id*> | Глобальный уникальный идентификатор (GUID) основного объекта службы для управляемого идентификатора, представляющего ваше логическое приложение в арендаторе Azure AD. Этот GUID иногда отображается как `objectID`"идентификатор объекта" или . |
| `tenantId` | <*Azure-AD-арендатор-ID*> | Глобальный уникальный идентификатор (GUID), представляющий арендаторAz AD, в котором приложение логики теперь является участником. В клиенте Azure AD субъект-служба имеет то же имя, что и экземпляр приложения логики. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Включить удостоверение, назначенное пользователем

Чтобы настроить управляемое удостоверение, назначенное пользователем, для приложения логики необходимо сначала создать эту идентификацию в качестве отдельного отдельного ресурса Azure. Вот варианты, которые вы можете использовать:

* [Портал Azure](#azure-portal-user-identity)
* [Шаблоны диспетчера ресурсов Azure](#template-user-identity)
* Azure PowerShell
  * [Создание удостоверений, назначенного пользователем](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Добавление назначения роли](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Создание удостоверений, назначенного пользователем](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Добавление назначения роли](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* REST API Azure
  * [Создание удостоверений, назначенного пользователем](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Добавление назначения роли](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Создание удостоверений личности, назначенного пользователем на портале Azure

1. На [портале Azure](https://portal.azure.com)в поле поиска на `managed identities`любой странице введите и выберите **управляемые идентификаторы.**

   ![Найти и выбрать "Управляемые удостоверения"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Под **управляемыми удостоверениями,** выберите **Добавить**.

   ![Добавление нового управляемого удостоверения](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Предоставьте информацию о управляемой идентификации, а затем выберите **Создать,** например:

   ![Создание управляемого удостоверения, назначенного пользователем](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Свойство | Обязательно | Значение | Описание |
   |----------|----------|-------|-------------|
   | **Имя ресурса** | Да | <*Имя пользователя, назначенное личность*> | Имя, удостоверяющего личность пользователя. В этом примере используется "Fabrikam-пользователь-назначенный-идентификация". |
   | **Подписка** | Да | <*Имя подписки Azure*> | Имя подписки Azure, которую нужно использовать. |
   | **Группа ресурсов** | Да | <*Azure-ресурс-группа-имя*> | Название группы ресурсов для использования. Создайте новую группу или выберите существующую группу. Этот пример создает новую группу под названием "fabrikam-управляемых идентичностей-RG". |
   | **Расположение** | Да | <*Лазурный регион*> | Область Azure, где можно хранить информацию о ресурсе. В этом примере используется регион "Западная часть США". |
   |||||

   Теперь вы можете добавить установленную пользователем идентификацию в приложение логики. Вы не можете добавить в приложение логики более одного удостоверения, назначенного пользователем.

1. На портале Azure найдите приложение логики и откройте его в конструкторе приложений логики.

1. В меню приложения логики, в **настройках,** выберите **Identity,** а затем выберите **Назначенный** > пользователь**Добавить.**

   ![Добавление назначенного пользователем управляемого удостоверения](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. В **добавлении пользователь учлевому развищу управляемого** панели идентификации из списка **подписки** выберите подписку Azure, если она еще не выбрана. Из списка, отображающего *все* управляемые идентификаторы в этой подписке, найдите и выберите нуждаемый идентификатор, назначенный пользователем. Чтобы отфильтровать список, в поле поиска **управляемых идентификаторов пользователя** введите имя для группы идентификации или ресурса. Когда вы закончите, **выберите Добавить.**

   ![Выберите присвоиваемый пользователем ими иственный](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Если у вас есть ошибка, в которой может быть только одно управляемое удостоверение, приложение логики уже связано с установленной системой идентификацией. Прежде чем вы сможете добавить удостоверение, назначенное пользователем, необходимо сначала отключить установленную системой идентификацию в приложении логики.

   Приложение логики теперь связано с назначенным пользователем управляемым удостоверением личности.

   ![Ассоциация с удостоверением, удостоверяющим личность пользователя](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Теперь выполните [шаги, которые дают этому identity доступ к ресурсу](#access-other-resources) позже в этой теме.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Создание удостоверения, назначенного пользователем в шаблоне управления ресурсами Azure

Для автоматизации создания и развертывания ресурсов Azure, таких как логические приложения, можно использовать [шаблоны Azure Resource Manager,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)которые поддерживают [имена пользователей, назначенные пользователями, для проверки подлинности.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) В `resources` разделе шаблона определение ресурсов приложения логики требует следующих элементов:

* Объект `identity` с `type` свойством, установленным для`UserAssigned`

* Объект `userAssignedIdentities` ребенка, опознавающий идентификатор ресурса, `principalId` `clientId` который является еще одним объектом ребенка, который имеет свойства и свойства

Этот пример показывает определение ресурсов логического приложения для запроса `identity` HTTP PUT и включает непараметризированный объект. Ответ на запрос PUT и последующая `identity` операция GET также имеют этот объект:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

| Property (JSON) | Значение | Описание |
|-----------------|-------|-------------|
| `principalId` | <*основной id*> | Глобальный уникальный идентификатор (GUID) для управляемого удостоверения пользователя в аниматоре Azure AD |
| `clientId` | <*клиент-ID*> | Глобальный уникальный идентификатор (GUID) для новой идентификации приложения логики, используемой для вызовов во время выполнения |
||||

Если шаблон также включает определение ресурсов управляемого итока, можно параметризировать `identity` объект. В этом примере `userAssignedIdentities` показано, `userAssignedIdentity` как объект «ребенок» ссылается на переменную, которую вы определяете в разделе шаблона. `variables` Эта переменная ссылается на идентификатор ресурса для идентификатора, назначенного пользователем.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Property (JSON) | Значение | Описание |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-арендатор-ID*> | Глобальный уникальный идентификатор (GUID), представляющий арендатор Azure AD, в котором теперь является пользовательская идентификация. В арендаторе Azure AD директор службы имеет то же имя, что и имя, назначенное пользователем. |
| `principalId` | <*основной id*> | Глобальный уникальный идентификатор (GUID) для управляемого удостоверения пользователя в аниматоре Azure AD |
| `clientId` | <*клиент-ID*> | Глобальный уникальный идентификатор (GUID) для новой идентификации приложения логики, используемой для вызовов во время выполнения |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Предоставить identity-доступ к ресурсам

Прежде чем использовать управляемое удостоверение приложения логики для проверки подлинности, навяжьте доступ к этому ипотечности на ресурсе Azure, где планируется использовать идентификацию. Чтобы выполнить эту задачу, назначаем соответствующую роль этой идентификации в целевом ресурсе Azure. Вот варианты, которые вы можете использовать:

* [Портал Azure](#azure-portal-assign-access)
* [Шаблон менеджера ресурсов Azure](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell[(New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)) - Для получения дополнительной информации [см.](../role-based-access-control/role-assignments-powershell.md)
* Azure CLI[(создание назначения роли аз)](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)- Для получения дополнительной информации см. [Add role assignment by using Azure RBAC and Azure CLI](../role-based-access-control/role-assignments-cli.md)
* [REST API Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Назначить доступ на портале Azure

1. На [портале Azure](https://portal.azure.com)перейдите на ресурс Azure, где необходимо получить доступ к управляемому итогу.

1. Из меню ресурса выберите**назначения ролей** **управления доступом (IAM),** > где можно просмотреть текущие назначения ролей для этого ресурса. На панели инструментов выберите назначение роли **Добавить.** > **Add role assignment**

   ![Выберите "Добавить" > "Добавить назначение ролей"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Если параметр **назначения роли Add** отключен, у вас, скорее всего, нет разрешений. Для получения дополнительной информации о разрешениях, которые позволяют управлять ролями для ресурсов, смотрите [разрешения ролей администратора в Active Directory Azure.](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

1. При **назначении роли Добавить**выберите **роль,** которая предоставляет вашей личности необходимый доступ к целевому ресурсу.

   Для примера этой темы для вашей идентификации требуется [роль, которая может получить доступ к каплей в контейнере хранения Azure.](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)

   ![Выберите роль "Вкладчик данных хранилища Blob"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Следуйте этим шагам для вашей управляемой идентификации:

   * **Назначаемое системой удостоверение**

     1. В **присваивать доступ к** коробке выберите **Logic App**. При пояснении свойства **подписки** выберите подписку Azure, связанную с вашей личностью.

        ![Выберите доступ для установленного системой удостоверения](./media/create-managed-service-identity/assign-access-system.png)

     1. Под полем **Select** выберите логическое приложение из списка. Если список слишком длинный, используйте поле **Select** для фильтрации списка.

        ![Выберите приложение логики для установленного системой удостоверения](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Назначаемое пользователем удостоверение**

     1. В **присваивайте доступ к** коробке, выберите **присвоил управляемому ипониму пользователю.** При пояснении свойства **подписки** выберите подписку Azure, связанную с вашей личностью.

        ![Выберите доступ для удостоверения, установленного пользователем](./media/create-managed-service-identity/assign-access-user.png)

     1. Под полем **Select** выберите идентификацию из списка. Если список слишком длинный, используйте поле **Select** для фильтрации списка.

        ![Выберите удостоверение, назначенное пользователем](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Когда все будет готово, нажмите кнопку **Сохранить**.

   Список назначений ролей целевого ресурса теперь показывает выбранные управляемые удостоверения и роль. В этом примере показано, как можно использовать установленную системой идентификацию для одного приложения логики и удостоверение, назначенное пользователем для группы других логических приложений.

   ![Добавлены управляемые идентификаторы и роли в целевой ресурс](./media/create-managed-service-identity/added-roles-for-identities.png)

   Для получения дополнительной [информации назначаем управляемый доступ к ресурсу с помощью портала Azure.](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)

1. Теперь следуйте [за мерами по аутентификации доступа с помощью триггера](#authenticate-access-with-identity) или действия, поддерживающего управляемые идентификаторы.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Аутентифицировать доступ с управляемой идентификацией

После [включения управляемой идентификации для приложения логики](#azure-portal-system-logic-app) и [предоставления этой идентификации целевому ресурсу или объекту](#access-other-resources)вы можете использовать эту идентификацию в [триггерах и действиях, поддерживающих управляемые идентификаторы.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

> [!IMPORTANT]
> Если у вас есть функция Azure, в которой требуется использовать установленную системой идентификацию, сначала [включите аутентификацию для функций Azure.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)

Эти шаги показывают, как использовать управляемый интальный отлик с триггером или действием через портал Azure. Чтобы указать управляемую идентификацию в определении JSON или действия, [см.](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)

1. На [портале Azure](https://portal.azure.com)откройте логическое приложение в Logic App Designer.

1. Если вы еще не сделали этого, добавьте [триггер или действие, поддерживающее управляемые идентификаторы.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

   Например, триггер или действие HTTP может использовать установленную системой идентификацию, включенную для приложения логики. В целом триггер или действие HTTP использует эти свойства для указания ресурса или объекта, к которым вы хотите получить доступ:

   | Свойство | Обязательно | Описание |
   |----------|----------|-------------|
   | **Метод** | Да | Метод HTTP, используемый операцией, которую вы хотите запустить |
   | **URI** | Да | URL-адрес конечных точек для доступа к целевому ресурсу или объекту Azure. Синтаксис URI обычно включает [идентификатор ресурсов](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) для ресурса или службы Azure. |
   | **Заголовки** | нет | Любые значения заголовка, необходимые или необходимые для включения в исходящий запрос, например тип содержимого |
   | **Запросы** | нет | Любые параметры запроса, которые необходимо включить или которые необходимо включить в запрос, например параметр для конкретной операции или версию API для операции, которую вы хотите выполнить |
   | **Проверка подлинности** | Да | Тип проверки подлинности для проверки подлинности доступа к целевому ресурсу или объекту |
   ||||

   В качестве конкретного примера предположим, что требуется запустить [операцию Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) на капле в учетной записи хранилища Azure, где вы предварительно настроили доступ для вашей идентификации. Однако [разъем хранилища Azure Blob](https://docs.microsoft.com/connectors/azureblob/) в настоящее время не предлагает эту операцию. Вместо этого вы можете запустить эту операцию с помощью [действия HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) или другой [операции Blob Service REST API.](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)

   > [!IMPORTANT]
   > Чтобы получить доступ к учетным записям хранения данных Azure за брандмауэрами с помощью запросов HTTP и управляемых идентификаторов, убедитесь, что вы также настроили учетную запись хранения, [за исключением, позволяющей получить доступ к доверенным службам Майкрософт.](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)

   Для выполнения [операции Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)действие HTTP определяет следующие свойства:

   | Свойство | Обязательно | Пример значения | Описание |
   |----------|----------|---------------|-------------|
   | **Метод** | Да | `PUT`| Метод HTTP, используемый в операции Snapshot Blob |
   | **URI** | Да | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Идентификатор ресурса для файла хранения Azure Blob в глобальной (публичной) среде Azure, использующем этот синтаксис |
   | **Заголовки** | Да, для хранения Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Значения `x-ms-blob-type` `x-ms-version` заголовка, необходимые для операций хранения Azure. <p><p>**Важно:** При исходящих запросах на триггеры И действия `x-ms-version` HTTP для хранения Azure для заголовка требуется свойство и версия API для операции, которую вы хотите запустить. <p>Дополнительные сведения см. в следующих статьях: <p><p>- [Запрос заголовки - Снимок Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Версия для служб хранения Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Запросы** | Да, для этой операции | `comp` = `snapshot` | Имя параметра запроса и значение для операции Snapshot Blob. |
   |||||

   Вот пример действия HTTP, которое показывает все эти значения свойств:

   ![Добавление действия HTTP для доступа к ресурсу Azure](./media/create-managed-service-identity/http-action-example.png)

1. Теперь добавьте свойство **аутентификации** в действие HTTP. Из **списка параметров Добавить новый,** выберите **аутентификацию**.

   ![Добавление свойства "Аутентификация" в действие HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Не все триггеры и действия поддерживают возможность добавления типа проверки подлинности. Для получения дополнительной информации [см. Добавить аутентификацию к исходящим вызовам.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Из списка **типов аутентификации** выберите **Управляемую идентификацию.**

   ![Для "Аутентификации" выберите "Управляемая идентичность"](./media/create-managed-service-identity/select-managed-identity.png)

1. Из управляемого списка идентификационных данных выберите из доступных вариантов на основе вашего сценария.

   * Если вы настроили установленную системой идентификацию, выберите **Систему, назначенную управляемой идентификацией,** если она еще не выбрана.

     ![Выберите "Система назначенная управляемая идентификация"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Если вы настроили удостоверение, назначенное пользователем, выберите эту идентификацию, если она еще не выбрана.

     ![Выберите удостоверение, назначенное пользователем](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Этот пример продолжается с **системой Назначенные Управляемые удостоверения .**

1. На некоторых триггерах и действиях также отображается свойство **аудитории** для установки идентификатора целевого ресурса. Установите свойство **аудитории** в [идентификатор ресурса для целевого ресурса или службы.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) В противном случае по умолчанию свойство **аудитории** использует идентификатор `https://management.azure.com/` ресурсов, который является идентификатором ресурсов для менеджера ресурсов Azure.

   > [!IMPORTANT]
   > Убедитесь, что идентификатор целевого ресурса *точно соответствует* значению, которое ожидает Azure Active Directory (AD), включая любые необходимые слэши. Например, идентификатор ресурсов для всех учетных записей Хранилища Azure Blob памяти требует слэйд. Однако идентификатор ресурса для определенной учетной записи хранилища не требует слэйд. Проверьте [идоверки ресурсов для служб Azure, поддерживающих AD Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

   Этот пример устанавливает свойство **аудитории** таким образом, чтобы `https://storage.azure.com/` маркеры доступа, используемые для проверки подлинности, были действительны для всех учетных записей хранения. Тем не менее, вы также `https://fabrikamstorageaccount.blob.core.windows.net`можете указать URL корневой службы, для конкретной учетной записи хранения.

   ![Установите свойство "Аудитория" для целевого идентификатора ресурса](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Для получения дополнительной информации об авторизации доступа с Azure AD для хранения Azure смотрите следующие темы:

   * [Разрешить доступ к каплям и очередям Azure с помощью active-каталога Azure](../storage/common/storage-auth-aad.md)
   * [Разрешить доступ к хранилищам Azure с помощью активного каталога Azure](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Продолжайте строить логическое приложение так, как вы хотите.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Отключить управляемую идентичность

Чтобы отказаться от использования управляемой идентификации для приложения логики, у вас есть следующие параметры:

* [Портал Azure](#azure-portal-disable)
* [Шаблоны диспетчера ресурсов Azure](#template-disable)
* Azure PowerShell
  * [Удаление назначения ролей](../role-based-access-control/role-assignments-powershell.md)
  * [Удаление удостоверений, назначенного пользователем](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Удаление назначения ролей](../role-based-access-control/role-assignments-cli.md)
  * [Удаление удостоверений, назначенного пользователем](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* REST API Azure
  * [Удаление назначения ролей](../role-based-access-control/role-assignments-rest.md)
  * [Удаление удостоверений, назначенного пользователем](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

При удалении приложения логики Azure автоматически удаляет управляемый итог из Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Отключить управляемую идентификацию на портале Azure

На портале Azure сначала удалите доступ к [целевому ресурсу.](#disable-identity-target-resource) Затем выключите установленное в системе удостоверение личности или удалите удостоверение, назначенное пользователем, из [приложения логики.](#disable-identity-logic-app)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Удаление доступа к идентификационной личности из ресурсов

1. На [портале Azure](https://portal.azure.com)перейдите на целевой ресурс Azure, где требуется удалить доступ к управляемому итогу.

1. Из меню целевого ресурса выберите **элемент управления доступом (IAM).** Под панелью инструментов выберите **назначения ролей.**

1. В списке ролей выберите управляемые идентификаторы, которые необходимо удалить. На панели инструментов выберите **Удалить**.

   > [!TIP]
   > Если **опция «Удалить»** отключена, у вас, скорее всего, нет разрешений. Для получения дополнительной информации о разрешениях, которые позволяют управлять ролями для ресурсов, смотрите [разрешения ролей администратора в Active Directory Azure.](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

Управляемое удостоверение теперь удаляется и больше не имеет доступа к целевому ресурсу.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Отключить управляемое удостоверение в приложении логики

1. На [портале Azure](https://portal.azure.com) откройте приложение логики в конструкторе приложений логики.

1. В меню приложения логики, в **настройках,** выберите **Identity,** а затем выполните последующие шаги для вашей личности:

   * Выберите **систему, назначенную** > **на** > **Сохранение**. Когда Azure подскажет вам подтвердить, выберите **Yes**.

     ![Отогнаните итог, назначенный системой](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Выберите **назначенный пользователь** и управляемая личность, а затем выберите **Удалить**. Когда Azure подскажет вам подтвердить, выберите **Yes**.

     ![Удаление установленного пользователем удостоверения](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Управляемая идентификация теперь отключена в приложении логики.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Отключить управляемое удостоверение в шаблоне менеджера ресурсов Azure

Если вы создали управляемое итог приложения логики с помощью шаблона менеджера ресурсов Azure, установите `identity` свойство ребенка `type` объекта для `None` Для системного идентификатора это действие также удаляет основной идентификатор из Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Безопасный доступ и данные в приложениях логики Azure](../logic-apps/logic-apps-securing-a-logic-app.md)