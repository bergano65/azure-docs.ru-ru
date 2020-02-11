---
title: Аутентификация на основе управляемых удостоверений
description: Доступ к ресурсам в других клиентах Azure Active Directory без входа с использованием учетных данных или секретов с помощью управляемого удостоверения
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117371"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Проверка подлинности доступа к ресурсам Azure с помощью управляемых удостоверений в Azure Logic Apps

Для доступа к ресурсам в других клиентах Azure Active Directory (Azure AD) и проверки подлинности удостоверения без входа в приложение логики может использовать [управляемое удостоверение](../active-directory/managed-identities-azure-resources/overview.md) (прежнее управляемое удостоверение службы или MSI), а не учетные данные или секреты. Azure управляет этим удостоверением за вас и помогает защитить учетные данные, потому что вам не нужно предоставлять или сменять секреты.

Azure Logic Apps поддерживает управляемые удостоверения, [*назначенные системой*](../active-directory/managed-identities-azure-resources/overview.md) и [*назначенные пользователю*](../active-directory/managed-identities-azure-resources/overview.md) . Приложение логики может использовать либо назначенное системой удостоверение, либо *единое* пользовательское удостоверение, которое можно совместно использовать в группе приложений логики, но не в обоих случаях. Сейчас только [определенные встроенные триггеры и действия](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) поддерживают управляемые удостоверения, а не управляемые соединители или соединения, например:

* HTTP
* Функции Azure
* Cлужба управления Azure API
* Службы приложений Azure

В этой статье показано, как настроить оба типа управляемых удостоверений для приложения логики. Дополнительные сведения см. в следующих статьях:

* [Триггеры и действия, поддерживающие управляемые удостоверения](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Поддерживаемые типы проверки подлинности для исходящих вызовов](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Ограничения управляемых удостоверений для приложений логики](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Службы Azure, поддерживающие аутентификацию Azure AD с управляемыми удостоверениями](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>предварительные требования

* Подписка Azure. Если у вас нет ее, вы можете [зарегистрироваться для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/). Управляемое удостоверение и целевой ресурс Azure, для которых требуется доступ, должны использовать одну и ту же подписку Azure.

* Чтобы предоставить управляемому удостоверению доступ к ресурсу Azure, необходимо добавить роль в целевой ресурс для этого удостоверения. Чтобы добавить роли, необходимы [разрешения администратора Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) , которые могут назначать роли удостоверениям в соответствующем клиенте Azure AD.

* Целевой ресурс Azure, к которому требуется получить доступ. В этом ресурсе вы добавите роль для управляемого удостоверения, которая помогает приложению логики подлинности получить доступ к целевому ресурсу.

* Приложение логики, в котором вы хотите использовать [триггер или действия, поддерживающие управляемые удостоверения](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Включение управляемого удостоверения

Чтобы настроить управляемое удостоверение, которое вы хотите использовать, перейдите по ссылке для этого удостоверения:

* [Удостоверение, назначенное системой](#system-assigned)
* [Удостоверение, назначенное пользователем](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Включить назначенное системой удостоверение

В отличие от назначенных пользователем удостоверений не требуется вручную создавать назначенные системой удостоверения. Чтобы настроить для приложения логики удостоверение, назначенное системой, можно использовать следующие параметры.

* [Портал Azure](#azure-portal-system-logic-app)
* [Шаблоны диспетчера ресурсов Azure](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Включение назначенного системой удостоверения в портал Azure

1. На [портале Azure](https://portal.azure.com) откройте приложение логики в конструкторе приложений логики.

1. В меню приложения логики в разделе **Параметры** выберите **Идентификатор**. Выберите > , **назначенные системой** , **на** > **сохранить**. Когда Azure запросит подтверждение, выберите **Да**.

   ![Включить назначенное системой удостоверение](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Если вы получаете сообщение об ошибке, которое может иметь только одно управляемое удостоверение, приложение логики уже связано с назначенным пользователем удостоверением. Прежде чем можно будет добавить назначенное системой удостоверение, необходимо сначала *Удалить* назначенное ему удостоверение из приложения логики.

   Теперь приложение логики может использовать назначенное системой удостоверение, зарегистрированное в Azure Active Directory и представленное ИДЕНТИФИКАТОРом объекта.

   ![Идентификатор объекта для назначенного системой удостоверения](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Свойство | Значение | Description |
   |----------|-------|-------------|
   | **Идентификатор объекта** | <*identity-resource-ID*> | Глобальный уникальный идентификатор (GUID), представляющий назначенное системой удостоверение для приложения логики в клиенте Azure AD. |
   ||||

1. Теперь выполните [действия, которые предоставляют удостоверению доступ к ресурсу](#access-other-resources) далее в этом разделе.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Включение назначенного системой удостоверения в шаблоне Azure Resource Manager

Чтобы автоматизировать создание и развертывание ресурсов Azure, таких как приложения логики, можно использовать [шаблоны Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Чтобы включить управляемое системой удостоверение для приложения логики в шаблоне, добавьте объект `identity` и свойство дочернего элемента `type` в определение ресурса приложения логики в шаблоне, например:

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

Когда Azure создает определение ресурса приложения логики, объект `identity` получает следующие дополнительные свойства:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Свойство (JSON) | Значение | Description |
|-----------------|-------|-------------|
| `principalId` | <*ИД субъекта*> | Глобальный уникальный идентификатор (GUID) объекта субъекта-службы для управляемого удостоверения, представляющего ваше приложение логики в клиенте Azure AD. Этот идентификатор GUID иногда отображается как "идентификатор объекта" или `objectID`. |
| `tenantId` | <*ИД клиента Azure AD*> | Глобальный уникальный идентификатор (GUID), представляющий клиент Azure AD, в который теперь входит приложение логики. В клиенте Azure AD субъект-служба имеет то же имя, что и экземпляр приложения логики. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Включить назначенное пользователем удостоверение

Чтобы настроить управляемое пользователем удостоверение для приложения логики, необходимо сначала создать это удостоверение в отдельном автономном ресурсе Azure. Ниже приведены параметры, которые можно использовать.

* [Портал Azure](#azure-portal-user-identity)
* [Шаблоны диспетчера ресурсов Azure](#template-user-identity)
* Azure PowerShell
  * [Создать удостоверение, назначенное пользователем](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Добавить назначение ролей](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Создать удостоверение, назначенное пользователем](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Добавить назначение ролей](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* REST API Azure
  * [Создать удостоверение, назначенное пользователем](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Добавить назначение ролей](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Создание назначенного пользователем удостоверения в портал Azure

1. В [портал Azure](https://portal.azure.com)в поле поиска на любой странице введите `managed identities`и выберите **управляемые удостоверения**.

   ![Найдите и выберите "управляемые удостоверения".](./media/create-managed-service-identity/find-select-managed-identities.png)

1. В разделе **управляемые удостоверения**выберите **Добавить**.

   ![Добавить новое управляемое удостоверение](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Укажите сведения об управляемом удостоверении, а затем выберите **создать**, например:

   ![Создание управляемого удостоверения, назначенного пользователем](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Свойство | Обязательно | Значение | Description |
   |----------|----------|-------|-------------|
   | **Имя ресурса** | Да | <" *назначено пользователем-Identity-Name* "> | Имя, присваиваемое пользователю удостоверение. В этом примере используется "Fabrikam-User-Assigned-Identity". |
   | **подписка** | Да | <*Azure-subscription-name*> | Имя подписки Azure, которую нужно использовать. |
   | **группа ресурсов** | Да | <*имя_группы_ресурсов_Azure*> | Имя используемой группы ресурсов. Создайте новую группу или выберите существующую. В этом примере создается новая группа с именем "Fabrikam-Managed-identitys-RG". |
   | **Местоположение** | Да | <*Azure-region*> | Регион Azure, в котором хранятся сведения о ресурсе. В этом примере используется регион "Западная часть США". |
   |||||

   Теперь можно добавить назначенное пользователем удостоверение в приложение логики. В приложение логики нельзя добавить больше одного назначенного пользователем удостоверения.

1. В портал Azure найдите и откройте приложение логики в конструкторе приложений логики.

1. В меню приложения логики в разделе **Параметры**выберите **удостоверение**, а затем выберите **назначенный пользователем** > **Добавить**.

   ![Добавление управляемого удостоверения, назначенного пользователем](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. В области **Добавить назначенное пользователем управляемое удостоверение** в списке **Подписка** выберите подписку Azure, если она еще не выбрана. В списке, где показаны *все* управляемые удостоверения в этой подписке, найдите и выберите нужное удостоверение, назначенное пользователем. Чтобы отфильтровать список, в поле " **назначенные пользователем управляемые удостоверения** " введите имя удостоверения или группы ресурсов. Когда все будет готово, нажмите кнопку **Добавить**.

   ![Выберите назначаемое пользователем удостоверение](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Если вы получаете сообщение об ошибке, которое может иметь только одно управляемое удостоверение, приложение логики уже связано с назначенным системой удостоверением. Перед добавлением удостоверения, назначенного пользователем, необходимо сначала отключить назначенное системой удостоверение для приложения логики.

   Теперь приложение логики связано с управляемым удостоверением, назначенным пользователем.

   ![Сопоставление с удостоверением, назначенным пользователем](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Теперь выполните [действия, которые предоставляют удостоверению доступ к ресурсу](#access-other-resources) далее в этом разделе.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Создание назначенного пользователем удостоверения в шаблоне Azure Resource Manager

Чтобы автоматизировать создание и развертывание ресурсов Azure, таких как приложения логики, можно использовать [шаблоны Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), которые поддерживают [для проверки подлинности назначенные пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). В разделе `resources` шаблона для определения ресурса приложения логики требуются следующие элементы:

* Объект `identity` со свойством `type`, для которого задано значение `UserAssigned`

* Дочерний объект `userAssignedIdentities`, указывающий идентификатор ресурса удостоверения, который является другим дочерним объектом, имеющим свойства `principalId` и `clientId`

В этом примере показано определение ресурса приложения логики для запроса HTTP-размещения и включает непараметризованный `identity` объект. Ответ на запрос размещения и последующие операции GET также имеют следующий объект `identity`:

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

| Свойство (JSON) | Значение | Description |
|-----------------|-------|-------------|
| `principalId` | <*ИД субъекта*> | Глобальный уникальный идентификатор (GUID) для назначаемого пользователем управляемого удостоверения в клиенте Azure AD |
| `clientId` | <*ИД клиента*> | Глобальный уникальный идентификатор (GUID) для нового удостоверения приложения логики, используемого для вызовов во время выполнения |
||||

Если шаблон также включает определение ресурса управляемого удостоверения, можно параметризовать объект `identity`. В этом примере показано, как дочерний объект `userAssignedIdentities` ссылается на переменную `userAssignedIdentity`, определенную в разделе `variables` шаблона. Эта переменная ссылается на идентификатор ресурса для назначенного пользователю удостоверения.

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

| Свойство (JSON) | Значение | Description |
|-----------------|-------|-------------|
| `tenantId` | <*ИД клиента Azure AD*> | Глобальный уникальный идентификатор (GUID), представляющий клиент Azure AD, в котором теперь пользовательское удостоверение является членом. В клиенте Azure AD субъект-служба имеет то же имя, что и имя удостоверения, назначенное пользователем. |
| `principalId` | <*ИД субъекта*> | Глобальный уникальный идентификатор (GUID) для назначаемого пользователем управляемого удостоверения в клиенте Azure AD |
| `clientId` | <*ИД клиента*> | Глобальный уникальный идентификатор (GUID) для нового удостоверения приложения логики, используемого для вызовов во время выполнения |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Предоставление удостоверениям доступа к ресурсам

Прежде чем использовать управляемое удостоверение приложения логики для проверки подлинности, настройте доступ для этого удостоверения в ресурсе Azure, где вы планируете использовать удостоверение. Чтобы выполнить эту задачу, назначьте удостоверение соответствующей роли в целевом ресурсе Azure. Ниже приведены параметры, которые можно использовать.

* [Портал Azure](#azure-portal-assign-access)
* [Шаблон Azure Resource Manager](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-азролеассигнмент](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)). Дополнительные сведения см. в статье [Добавление назначения ролей с помощью Azure RBAC и Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Azure CLI ([AZ Role назначение роли](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)). Дополнительные сведения см. в статье [Добавление назначения ролей с помощью Azure RBAC и Azure CLI](../role-based-access-control/role-assignments-cli.md).
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Назначение доступа в портал Azure

1. В [портал Azure](https://portal.azure.com)перейдите к ресурсу Azure, в котором вы хотите, чтобы управляемое удостоверение имело доступ.

1. В меню ресурса выберите пункт **Управление доступом (IAM)**  > **назначения ролей** , где можно проверить текущие назначения ролей для этого ресурса. На панели инструментов выберите **добавить** > **добавить назначение ролей**.

   ![Выберите "Добавить" > "добавить назначение ролей".](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Если параметр **добавить назначение роли** отключен, скорее всего, у вас нет разрешений. Дополнительные сведения о разрешениях, позволяющих управлять ролями для ресурсов, см. [в разделе разрешения роли администратора в Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. В разделе **Добавление назначения ролей**выберите **роль** , которая предоставит удостоверению необходимый доступ к целевому ресурсу.

   В этом примере для удостоверения требуется [роль, которая может получить доступ к большому двоичному объекту в контейнере службы хранилища Azure](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

   ![Выберите роль "участник данных BLOB-объектов хранилища"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Выполните следующие действия для управляемого удостоверения:

   * **Удостоверение, назначенное системой**

     1. В поле **назначить доступ к** выберите **приложение логики**. Когда отобразится свойство **Подписка** , выберите подписку Azure, связанную с вашим удостоверением.

        ![Выбор доступа для удостоверения, назначенного системой](./media/create-managed-service-identity/assign-access-system.png)

     1. В поле **выбрать** выберите приложение логики из списка. Если список слишком длинный, используйте поле **выбора** для фильтрации списка.

        ![Выбор приложения логики для назначенного системой удостоверения](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Удостоверение, назначенное пользователем**

     1. В поле **назначить доступ к** выберите значение **управляемое удостоверение, назначенное пользователем**. Когда отобразится свойство **Подписка** , выберите подписку Azure, связанную с вашим удостоверением.

        ![Выберите доступ для назначенного пользователю удостоверения.](./media/create-managed-service-identity/assign-access-user.png)

     1. В поле **выбрать** выберите удостоверение из списка. Если список слишком длинный, используйте поле **выбора** для фильтрации списка.

        ![Выбор назначенного пользователем удостоверения](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Когда все будет готово, нажмите кнопку **Сохранить**.

   В списке назначения ролей целевого ресурса теперь отображается выбранное управляемое удостоверение и роль. В этом примере показано, как можно использовать назначенное системой удостоверение для одного приложения логики и назначенное пользователем удостоверение для группы других приложений логики.

   ![В целевой ресурс добавлены управляемые удостоверения и роли](./media/create-managed-service-identity/added-roles-for-identities.png)

   Для получения дополнительных сведений [назначьте управляемому удостоверению доступ к ресурсу с помощью портал Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Теперь выполните [действия по проверке подлинности доступа с помощью удостоверения](#authenticate-access-with-identity) в триггере или действии, поддерживающем управляемые удостоверения.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Проверка подлинности доступа с помощью управляемого удостоверения

После [включения управляемого удостоверения для приложения логики](#azure-portal-system-logic-app) и [предоставления этому удостоверению доступа к целевому ресурсу или сущности](#access-other-resources)можно использовать это удостоверение в [триггерах и действиях, поддерживающих управляемые удостоверения](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Если у вас есть функция Azure, в которой вы хотите использовать назначенное системой удостоверение, сначала [включите проверку подлинности для функций Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

В этих шагах показано, как использовать управляемое удостоверение с триггером или действием с помощью портал Azure. Сведения об указании управляемого удостоверения в базовом определении JSON триггера или действия см. в разделе [Проверка подлинности управляемого удостоверения](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. Откройте приложение логики в конструкторе приложений логики на [портале Azure](https://portal.azure.com).

1. Если вы еще не сделали этого, добавьте [триггер или действие, поддерживающее управляемые удостоверения](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Например, триггер или действие HTTP могут использовать назначенное системой удостоверение, включенное для приложения логики. Как правило, триггер или действие HTTP используют эти свойства для указания ресурса или сущности, к которым требуется получить доступ.

   | Свойство | Обязательно | Description |
   |----------|----------|-------------|
   | **Метод** | Да | Метод HTTP, используемый операцией, которую необходимо выполнить |
   | **URI** | Да | URL-адрес конечной точки для доступа к целевому ресурсу или сущности Azure. Синтаксис URI обычно включает [идентификатор ресурса](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) для ресурса или службы Azure. |
   | **Заголовки** | нет | Любые значения заголовка, которые требуется включить в исходящий запрос, например тип содержимого |
   | **Запросы** | нет | Любые параметры запроса, которые требуется включить в запрос, например параметр для определенной операции или версия API для операции, которую требуется выполнить. |
   | **Аутентификация** | Да | Тип проверки подлинности, используемый для проверки подлинности доступа к целевому ресурсу или сущности |
   ||||

   В качестве конкретного примера предположим, что вы хотите выполнить [операцию создания BLOB-объекта моментального снимка](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) в большом двоичном объекте в учетной записи хранения Azure, где вы ранее настроили доступ к удостоверению. Однако [соединитель хранилища BLOB-объектов Azure](https://docs.microsoft.com/connectors/azureblob/) в настоящее время не поддерживает эту операцию. Вместо этого эту операцию можно выполнить с помощью [действия HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) или другой [службы Blob-объектов REST API операции](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Чтобы получить доступ к учетным записям хранения Azure за брандмауэром с помощью HTTP-запросов и управляемых удостоверений, убедитесь, что вы также настроили учетную запись хранения с [исключением, которое разрешает доступ доверенными службами Майкрософт](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Чтобы запустить [операцию создания BLOB-объекта моментальных снимков](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob), в действии HTTP указываются следующие свойства:

   | Свойство | Обязательно | Пример значения | Description |
   |----------|----------|---------------|-------------|
   | **Метод** | Да | `PUT`| Метод HTTP, используемый операцией BLOB-объекта моментального снимка |
   | **URI** | Да | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Идентификатор ресурса для файла хранилища BLOB-объектов Azure в глобальной (общедоступной) среде Azure, которая использует этот синтаксис. |
   | **Заголовки** | Да, для службы хранилища Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Значения заголовков `x-ms-blob-type` и `x-ms-version`, необходимые для операций службы хранилища Azure. <p><p>**Важно**. в исходящем триггере HTTP и запросах действий для службы хранилища Azure для заголовка требуется свойство `x-ms-version` и версия API для операции, которую требуется выполнить. <p>Дополнительные сведения см. в следующих статьях: <p><p>- [заголовков запроса — BLOB-объект моментальных снимков](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Управление версиями для служб хранилища Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Запросы** | Да, для этой операции | `comp` = `snapshot` | Имя параметра запроса и значение для операции моментального снимка большого двоичного объекта. |
   |||||

   Ниже приведен пример действия HTTP, в котором показаны все эти значения свойств.

   ![Добавление действия HTTP для доступа к ресурсу Azure](./media/create-managed-service-identity/http-action-example.png)

1. Теперь добавьте свойство **authentication** в действие HTTP. В списке **Добавить новый параметр** выберите **Проверка подлинности**.

   ![Добавление свойства "Authentication" в действие HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Не все триггеры и действия поддерживают добавление типа проверки подлинности. Дополнительные сведения см. [в разделе Добавление проверки подлинности для исходящих вызовов](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. В списке **тип проверки подлинности** выберите **управляемое удостоверение**.

   ![В качестве "проверки подлинности" выберите "управляемое удостоверение".](./media/create-managed-service-identity/select-managed-identity.png)

1. В списке управляемое удостоверение выберите доступные параметры в зависимости от вашего сценария.

   * Если вы настроили назначенное системой удостоверение, выберите **система назначено управляемое удостоверение** , если оно еще не выбрано.

     ![Выберите пункт "управляемое удостоверение, назначенное системой".](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Если вы настроили назначенное пользователем удостоверение, выберите это удостоверение, если оно еще не выбрано.

     ![Выберите удостоверение, назначенное пользователем](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   В этом примере сохраняется **управляемое удостоверение, назначенное системой**.

1. Для некоторых триггеров и действий также отображается свойство **аудитория** для задания идентификатора целевого ресурса. Задайте для свойства **аудиторию** [идентификатор ресурса для целевого ресурса или службы](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). В противном случае по умолчанию свойство **аудитория** использует идентификатор ресурса `https://management.azure.com/`, который является идентификатором ресурса для Azure Resource Manager.

   > [!IMPORTANT]
   > Убедитесь, что целевой идентификатор ресурса *точно соответствует* значению, которое требуется Azure Active Directory (AD), включая все обязательные символы косой черты. Например, для идентификатора ресурса для всех учетных записей хранилища BLOB-объектов Azure требуется завершающая косая черта. Однако для идентификатора ресурса конкретной учетной записи хранения не требуется завершающая косая черта. Проверьте [идентификаторы ресурсов для служб Azure, которые поддерживают Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   В этом примере свойству **аудитории** присваивается значение `https://storage.azure.com/`, чтобы маркеры доступа, используемые для проверки подлинности, были допустимы для всех учетных записей хранения. Однако можно также указать URL-адрес корневой службы `https://fabrikamstorageaccount.blob.core.windows.net`для конкретной учетной записи хранения.

   ![Задать для свойства "аудитория" идентификатор целевого ресурса](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Дополнительные сведения об авторизации доступа с помощью Azure AD для службы хранилища Azure см. в следующих разделах:

   * [Авторизация доступа к BLOB-объектам и очередям Azure с помощью Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Авторизация доступа к службе хранилища Azure с помощью Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Продолжайте создавать приложения логики нужным образом.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Отключить управляемое удостоверение

Чтобы не использовать управляемое удостоверение для приложения логики, доступны следующие варианты:

* [Портал Azure](#azure-portal-disable)
* [Шаблоны диспетчера ресурсов Azure](#template-disable)
* Azure PowerShell
  * [Удалить назначение роли](../role-based-access-control/role-assignments-powershell.md)
  * [Удаление назначенного пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Удалить назначение роли](../role-based-access-control/role-assignments-cli.md)
  * [Удаление назначенного пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* REST API Azure
  * [Удалить назначение роли](../role-based-access-control/role-assignments-rest.md)
  * [Удаление назначенного пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Если вы удалите приложение логики, Azure автоматически удалит управляемое удостоверение из Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Отключение управляемого удостоверения в портал Azure

В портал Azure сначала удалите доступ удостоверения к [целевому ресурсу](#disable-identity-target-resource). Затем выключите назначенное системой удостоверение или удалите удостоверение, назначенное пользователем из [приложения логики](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Удаление доступа к удостоверениям из ресурсов

1. В [портал Azure](https://portal.azure.com)перейдите к целевому ресурсу Azure, в котором вы хотите удалить доступ для управляемого удостоверения.

1. В меню целевого ресурса выберите **Управление доступом (IAM)** . На панели инструментов выберите **назначения ролей**.

1. В списке роли выберите управляемые удостоверения, которые требуется удалить. На панели инструментов нажмите кнопку **Удалить**.

   > [!TIP]
   > Если параметр **Удалить** отключен, скорее всего, у вас нет разрешений. Дополнительные сведения о разрешениях, позволяющих управлять ролями для ресурсов, см. [в разделе разрешения роли администратора в Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Управляемое удостоверение теперь удалено и больше не имеет доступа к целевому ресурсу.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Отключить управляемое удостоверение в приложении логики

1. На [портале Azure](https://portal.azure.com) откройте приложение логики в конструкторе приложений логики.

1. В меню приложения логики в разделе **Параметры**выберите **удостоверение**, а затем выполните действия для своего удостоверения.

   * Выберите > , **назначенные системой** , **на** > **сохранить**. Когда Azure запросит подтверждение, выберите **Да**.

     ![Отключить назначенное системой удостоверение](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Выберите **назначенное пользователем** и управляемое удостоверение, а затем щелкните **Удалить**. Когда Azure запросит подтверждение, выберите **Да**.

     ![Удаление назначенного пользователю удостоверения](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Управляемое удостоверение теперь отключено в приложении логики.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Отключение управляемого удостоверения в шаблоне Azure Resource Manager

Если вы создали управляемое удостоверение приложения логики с помощью шаблона Azure Resource Manager, установите для дочернего свойства `type` объекта `identity` значение `None`. Для управляемого системой удостоверения это действие также удаляет идентификатор субъекта из Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Безопасный доступ и данные в Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)