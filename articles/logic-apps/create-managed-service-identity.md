---
title: Аутентификация на основе управляемых удостоверений
description: Доступ к ресурсам, защищенным Azure Active Directory, без входа с использованием учетных данных или секретов с помощью управляемого удостоверения
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: f9c5de4fb4e38d3f9ccb79c89be988fe0bbebc3c
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760300"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Проверка подлинности и получение доступа к ресурсам Azure с помощью управляемых удостоверений в Azure Logic Apps.

Чтобы получить доступ к ресурсам в других клиентах Azure Active Directory (Azure AD) и выполнить аутентификацию без входа, приложение логики может использовать [управляемое удостоверение](../active-directory/managed-identities-azure-resources/overview.md), известное ранее как "Управляемое удостоверение службы" (MSI), а не учетные данные или секреты. Azure управляет этим удостоверением за вас и помогает защитить учетные данные, потому что вам не нужно предоставлять или сменять секреты.

Azure Logic Apps поддерживает [*назначенные системой*](../active-directory/managed-identities-azure-resources/overview.md) и [*назначенные пользователем*](../active-directory/managed-identities-azure-resources/overview.md) управляемые удостоверения. Приложение логики может использовать либо назначенное системой удостоверение, либо *одно* назначенное пользователем удостоверение (которое можно совместно использовать в группе приложений логики), но только одно из двух. В настоящее время только [определенные встроенные триггеры и действия](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) поддерживают управляемые удостоверения, а не управляемые соединители или соединения, например:

* HTTP
* Функции Azure
* Cлужба управления Azure API
* Службы приложений Azure

В этой статье показано, как настроить оба типа управляемых удостоверений для приложения логики. Дополнительные сведения см. в следующих статьях:

* [Триггеры и действия, поддерживающие управляемые удостоверения](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Поддерживаемые для исходящих вызовов типы аутентификации](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Ограничения управляемого удостоверения для приложений логики](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Службы, которые поддерживают управляемые удостоверения для проверки подлинности Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет ее, вы можете [зарегистрироваться для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/). Как назначенное системой управляемое удостоверение, так и ресурс, к которому необходимо назначить доступ, должны находиться в одной подписке Azure.

* Чтобы предоставить управляемому удостоверению доступ к ресурсу Azure, необходимо добавить роль в целевой ресурс для этого удостоверения. Чтобы добавить роли, необходимо иметь [разрешения администратора Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md), которые могут назначать роли удостоверениям в соответствующем клиенте Azure AD.

* Целевой ресурс Azure, к которому требуется получить доступ. В этом ресурсе вы добавите роль для управляемого удостоверения, которая помогает приложению логики подтвердить доступ к целевому ресурсу.

* Приложение логики, в котором вы хотите использовать [триггер или действия, поддерживающие управляемые удостоверения.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Включение управляемого удостоверения

Чтобы настроить управляемое удостоверение, которое вы хотите использовать, перейдите по ссылке для этого удостоверения:

* [Назначаемое системой удостоверение](#system-assigned)
* [Назначаемое пользователем удостоверение](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Включить назначаемое системой удостоверение

В отличие от назначаемых пользователем удостоверений, создаваемое системой удостоверение не нужно создавать самостоятельно. Чтобы настроить для приложения логики удостоверение, назначенное системой, можно использовать следующие варианты:

* [Портал Azure](#azure-portal-system-logic-app)
* [Шаблоны диспетчера ресурсов Azure](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Включить назначаемое системой удостоверение через портал Azure

1. На [портале Azure](https://portal.azure.com) откройте приложение логики в конструкторе приложений логики.

1. В меню приложения логики в разделе **Параметры** выберите **Идентификатор**. Выберите **Назначенное системой** > **Включено** > **Сохранить**. Когда Azure запросит подтверждение, выберите **Да**.

   ![Включение назначаемого системой удостоверения](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Если вы получаете сообщение об ошибке, уведомляющее, что может быть активно только одно управляемое удостоверение, это значит, что приложение логики уже связано с назначенным пользователем удостоверением. Прежде чем можно будет добавить назначенное системой удостоверение, необходимо сначала *удалить* назначенное пользователем удостоверение из приложения логики.

   Теперь приложение логики может использовать назначенное системой удостоверение, зарегистрированное в Azure Active Directory и представленное идентификатором объекта.

   ![Идентификатор объекта для назначенного системой удостоверения](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Свойство | Значение | Описание |
   |----------|-------|-------------|
   | **Идентификатор объекта** | <*identity-resource-ID*> | Глобальный уникальный идентификатор (GUID), который представляет присвоенное системой управляемое удостоверение приложения логики в клиенте Azure AD |
   ||||

1. Теперь выполните действия [, которые предоставляют удостоверению доступ к ресурсу](#access-other-resources), описанные далее в этом разделе.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Включение назначенного системой удостоверения в шаблоне Azure Resource Manager

Чтобы автоматизировать создание и развертывание таких ресурсов Azure, как приложения логики, можно настроить [шаблоны Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Чтобы включить управляемое системой удостоверение в приложение логики в шаблоне, добавьте объект `identity` и свойство дочернего элемента `type` в определение ресурса приложения логики в шаблоне, например:

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

При создании Azure определения ресурса для приложения логики объект `identity` получает следующие дополнительные свойства:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Свойство (JSON) | Значение | Описание |
|-----------------|-------|-------------|
| `principalId` | <*ИД субъекта*> | Глобальный уникальный идентификатор (GUID) объекта субъекта-службы для управляемого удостоверения, представляющего ваше приложение логики в клиенте Azure AD. Этот идентификатор GUID иногда отображается как "идентификатор объекта" или `objectID`. |
| `tenantId` | <*ИД клиента Azure AD*> | Глобальный уникальный идентификатор (GUID), представляющий клиент Azure AD, участником которого теперь является приложение логики. В клиенте Azure AD субъект-служба имеет то же имя, что и экземпляр приложения логики. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Подключение назначенного пользователем удостоверения

Чтобы настроить назначенное пользователем удостоверение для приложения логики, необходимо сначала создать это удостоверение в отдельном автономном ресурсе Azure. Воспользоваться можно следующими вариантами.

* [Портал Azure](#azure-portal-user-identity)
* [Шаблоны диспетчера ресурсов Azure](#template-user-identity)
* Azure PowerShell
  * [Создание назначаемого пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Добавление назначения роли](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Создание назначаемого пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Добавление назначения роли](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* REST API Azure
  * [Создание назначаемого пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Добавление назначения роли](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Создание назначаемого пользователем управляемого удостоверения через портал Azure

1. На [портале Azure](https://portal.azure.com) в поле поиска на любой странице введите `managed identities` и затем выберите пункт **Управляемые удостоверения**.

   ![Найдите и выберите пункт "Управляемые удостоверения".](./media/create-managed-service-identity/find-select-managed-identities.png)

1. В разделе **Управляемые удостоверения** нажмите **Добавить**.

   ![Добавление управляемого удостоверения](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Укажите сведения об управляемом удостоверении, а затем выберите **Создать**. Вот пример:

   ![Создание управляемого удостоверения, назначаемого пользователем](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Свойство | Обязательно | Значение | Описание |
   |----------|----------|-------|-------------|
   | **Имя ресурса** | Да | <*user-assigned-identity-name*> | Имя, присваиваемое удостоверению. В этом примере используется "Fabrikam-User-Assigned-Identity". |
   | **Подписка** | Да | <*Azure-subscription-name*> | Имя подписки Azure, которую нужно использовать. |
   | **Группа ресурсов** | Да | <*имя_группы_ресурсов_Azure*> | Имя, используемое группой ресурсов. Создайте группу или выберите имеющуюся. В этом примере создается новая группа с именем "Fabrikam-Managed-identitys-RG". |
   | **Расположение** | Да | <*Azure-region*> | Регион Azure, в котором будут храниться сведения о приложении логики. В этом примере используется регион "Западная часть США". |
   |||||

   Теперь можно добавить назначенное пользователем удостоверение в приложение логики. Невозможно добавить более одного назначенного пользователем удостоверения в приложение логики.

1. На портале Azure найдите ваше приложение логики и откройте его в конструкторе приложений логики.

1. В меню приложения логики в разделе **Параметры** выберите пункт **Удостоверение**, затем нажмите на **Назначенное пользователем** > **Добавить**.

   ![Добавление назначенного пользователем управляемого удостоверения](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. Выберите подписку Azure из списка **Подписки** (если она уже не была выбрана) на панели **Добавление назначенного пользователем управляемого удостоверения**. Выберите необходимое назначенное пользователем управляемое удостоверение из списка, в котором отображаются *все* управляемые удостоверения на данной подписке. Чтобы отфильтровать список, в поле поиска **Назначенные пользователем управляемые удостоверения** нужно будет ввести имя удостоверения или группы ресурсов. Закончив, нажмите кнопку **Добавить**.

   ![Выберите назначаемое пользователем удостоверение для использования](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Если вы получаете сообщение об ошибке, уведомляющее, что может быть активно только одно управляемое удостоверение, это значит, что приложение логики уже связано с назначенным системой удостоверением. Прежде чем можно будет добавить назначенное пользователем удостоверение, необходимо сначала удалить назначенное системой удостоверение из приложения логики.

   Теперь приложение логики связано с управляемым удостоверением, назначенным пользователем.

   ![Привязка назначаемого пользователем удостоверения](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Теперь выполните действия [, которые предоставляют удостоверению доступ к ресурсу](#access-other-resources), описанные далее в этом разделе.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Создание назначенного пользователем удостоверения в шаблоне Azure Resource Manager

Если вы хотите автоматизировать создание и развертывание таких ресурсов Azure, как приложения логики, можно настроить [шаблоны Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), поддерживающие [установленные пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) для аутентификации. В разделе `resources` шаблона вам потребуются следующие элементы для определения ресурса приложения логики:

* Объект `identity` со свойством `type`, для которого задано значение `UserAssigned`.

* Дочерний `userAssignedIdentities` объект, указывающий назначенный пользователю ресурс и имя

В этом примере показано определение ресурса приложения логики для запроса HTTP-размещения, а также объект `identity` без параметров. Ответ на запрос размещения и последующие операции GET также имеют объект `identity`:

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
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
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
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Предоставление удостоверению доступа к ресурсам

Прежде чем использовать управляемое удостоверение приложения логики для аутентификации, настройте доступ для этого удостоверения в ресурсе Azure, где вы планируете использовать удостоверение. Чтобы выполнить эту задачу, назначьте удостоверению соответствующую роль в целевом ресурсе Azure. Воспользоваться можно следующими вариантами.

* [Портал Azure](#azure-portal-assign-access)
* [Шаблон Azure Resource Manager](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)). Дополнительные сведения см. в разделе [Добавление назначения ролей с помощью Azure RBAC и Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Azure CLI ([az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)). Дополнительные сведения см. в разделе [Добавление назначения ролей с помощью Azure RBAC и Azure CLI](../role-based-access-control/role-assignments-cli.md).
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Назначение доступа на портале Azure

1. На [портале Azure](https://portal.azure.com) перейдите к ресурсу Azure, к которому вы хотите назначить доступ для управляемого удостоверения.

1. В меню ресурса выберите **Управление доступом (IAM)**  > **Назначение ролей**. Там можно будет проверить текущие назначения ролей для этого ресурса. Выберите на панели инструментов **Добавить** > **Добавить назначение ролей**.

   ![Выберите "Добавить" > "Добавить назначение ролей"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Если параметр **Добавить назначение ролей** отключен, скорее всего, у вас нет разрешений. Дополнительные сведения о различных ролях администраторов см. в статье [Разрешения роли администратора в Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. В разделе **Добавить назначение ролей** выберите **роль**, которая предоставит удостоверению необходимый доступ к целевому ресурсу.

   В этом примере для удостоверения требуется роль [, которая может получить доступ к большому двоичному объекту в контейнере службы хранилища Azure](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

   ![Выберите роль "Участник хранилища для больших двоичных объектов"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Выполните следующие действия для управляемого удостоверения.

   * **Назначаемое системой удостоверение**

     1. В поле **Назначить доступ к** выберите **Приложение логики**. Когда появится свойство **Подписка**, выберите подписку Azure, связанную с вашим удостоверением.

        ![Выбор доступа для назначенного системой удостоверения](./media/create-managed-service-identity/assign-access-system.png)

     1. В поле **Выбор** выберите приложение логики из списка. Если список слишком длинный, воспользуйтесь полем **Выбор**, чтобы отфильтровать список.

        ![Выбор приложения логики для назначенного системой удостоверения](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Назначаемое пользователем удостоверение**

     1. В поле**Назначить доступ** выберите **Управляемое удостоверение, назначаемое пользователем**. Когда появится свойство **Подписка**, выберите подписку Azure, связанную с вашим удостоверением.

        ![Выбор доступа для назначаемого пользователем удостоверения](./media/create-managed-service-identity/assign-access-user.png)

     1. В поле **Выбор** выберите удостоверение из списка. Если список слишком длинный, воспользуйтесь полем **Выбор**, чтобы отфильтровать список.

        ![Выберите назначаемое пользователем удостоверение](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Когда все будет готово, нажмите кнопку **Сохранить**.

   Список ролей целевого ресурса теперь будет отображать выбранное удостоверение и роль. В этом примере показано, как можно использовать назначенное системой удостоверение для одного приложения логики и назначенное пользователем удостоверение для группы других приложений логики.

   ![В целевой ресурс добавлены управляемые удостоверения и роли](./media/create-managed-service-identity/added-roles-for-identities.png)

   Чтобы узнать больше, [назначьте управляемому удостоверению доступ к ресурсу через портал Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Теперь выполните [действия, чтобы проверить подлинность доступа с помощью удостоверения](#authenticate-access-with-identity) в триггере или действии, поддерживающем управляемые удостоверения.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Подтверждение подлинности доступа на основе управляемых удостоверений

После [включения управляемого удостоверения в приложение логики](#azure-portal-system-logic-app) и [предоставления этому удостоверению доступа к целевому ресурсу или сущности](#access-other-resources) вы можете использовать это удостоверение в [триггерах и действиях, поддерживающих управляемые удостоверения](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Если у вас есть функция Azure, в которой вы хотите использовать назначенное системой удостоверение, сначала [включите проверку подлинности для функций Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

В этой пошаговой инструкции показано, как использовать управляемое удостоверение с триггером или действием через портал Azure. Сведения об указании управляемого удостоверения в базовом определении JSON триггера или действия см. в разделе [проверки подлинности управляемого удостоверения](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. Откройте приложение логики в конструкторе приложений логики на [портале Azure](https://portal.azure.com).

1. Если вы еще не сделали этого, добавьте [триггер или действие, поддерживающее управляемые удостоверения](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Например, триггер или действие HTTP могут использовать назначенное системой удостоверение, включенное для приложения логики. Как правило, триггер или действие HTTP используют эти свойства для указания ресурса или сущности, к которым требуется получить доступ:

   | Свойство | Обязательно | Описание |
   |----------|----------|-------------|
   | **Метод** | Да | Метод HTTP, используемый операцией, которую необходимо выполнить |
   | **URI** | Да | URL-адрес конечной точки для доступа к целевому ресурсу или сущности Azure. Синтаксис URI обычно включает в себя [идентификатор ресурса](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) для ресурса или службы Azure. |
   | **Заголовки** | нет | Любые значения заголовка, которые требуется включить в исходящий запрос, например тип содержимого |
   | **Запросы** | нет | Любые параметры запроса, которые требуется включить в запрос, например параметр для определенной операции или версия API для операции, которую требуется выполнить. |
   | **Аутентификация** | Да | Тип проверки подлинности, используемый для аутентификации доступа к целевому ресурсу или сущности |
   ||||

   В качестве конкретного примера предположим, что вы хотите выполнить операцию [со снимком BLOB-объекта](/rest/api/storageservices/snapshot-blob) на большом двоичном объекте в учетной записи хранилища Azure, где вы ранее настроили доступ к удостоверению. Однако [соединитель хранилища BLOB-объектов](/connectors/azureblob/) Azure в настоящее время не поддерживает эту операцию. Вместо этого операцию можно выполнить с помощью [действия HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) или другой операции [REST API службы BLOB-объектов](/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Чтобы получить доступ к учетным записям хранения Azure за брандмауэром с помощью HTTP-запросов и управляемых удостоверений, убедитесь, что вы также настроили учетную запись хранения с [исключением, которое разрешает доступ доверенным службам от Майкрософт](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Для запуска [операции снимка BLOB-объекта](/rest/api/storageservices/snapshot-blob) в действии HTTP указываются следующие свойства:

   | Свойство | Обязательно | Пример значения | Описание |
   |----------|----------|---------------|-------------|
   | **Метод** | Да | `PUT`| Метод HTTP, используемый операцией снимка BLOB-объекта |
   | **URI** | Да | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Идентификатор ресурса для файла хранилища BLOB-объектов Azure в глобальной (общедоступной) среде Azure, который использует этот синтаксис |
   | **Заголовки** | Да, для службы хранилища Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Значения заголовков `x-ms-blob-type` и `x-ms-version`, необходимые для операций службы хранилища Azure. <p><p>**Важно!** В исходящих HTTP-триггерах и запросах действий для службы хранилища Azure для заголовка требуется свойство `x-ms-version` и версия API для операции, которую требуется выполнить. <p>Дополнительные сведения см. в следующих статьях: <p><p>- [Заголовок запроса — снимок BLOB-объекта](/rest/api/storageservices/snapshot-blob#request) <br>- [Управление версиями для служб хранилища Azure](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Запросы** | Да, для данной операции | `comp` = `snapshot` | Имя параметра запроса и значение для операции снимка BLOB-объекта. |
   |||||

   Ниже приведен пример действия HTTP, в котором показаны все эти значения свойств.

   ![Добавьте действие HTTP для доступа к ресурсу Azure](./media/create-managed-service-identity/http-action-example.png)

1. Теперь добавьте **свойство проверки подлинности** в действие HTTP. Из списка **Добавить новый параметр** выберите **Проверка подлинности**.

   ![Добавьте свойство "Проверка подлинности" в действие HTTP.](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Не все триггеры и действия поддерживают добавление типа проверки подлинности. Дополнительные сведения см. в разделе [Добавление проверки подлинности в исходящие вызовы](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) далее в этой статье.

1. В списке **Проверка подлинности** выберите **Управляемое удостоверение**.

   ![В списке "Проверка подлинности" выберите "Управляемое удостоверение".](./media/create-managed-service-identity/select-managed-identity.png)

1. В списке управляемых удостоверений выберите необходимые параметры из списка доступных для нужд вашего сценария.

   * Если вы настроили назначенное системой удостоверение, выберите пункт **Назначенное системой управляемое удостоверение**, если он еще не выбран.

     ![Выберите "Управляемое удостоверение, назначенное системой"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Если вы настроили назначенное пользователем удостоверение, выберите это удостоверение, если оно еще не выбрано.

     ![Выберите назначаемое пользователем удостоверение](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Этот пример продолжится с **управляемым удостоверением, назначаемым системой**.

1. Для некоторых триггеров и действий также отображается свойство **Аудитория** для задания идентификатора целевого ресурса. Задайте для свойства **аудитории** значение [идентификатора ресурса](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) целевого ресурса или службы. В противном случае по умолчанию свойство **аудитории** будет использовать идентификатор ресурса `https://management.azure.com/`, который представляет собой идентификатор ресурса для Azure Resource Manager.

   > [!IMPORTANT]
   > Убедитесь, что значение идентификатора ресурса *в точности совпадает* со значением, ожидаемым Azure AD, включая все необходимые знаки косой черты. Например, для идентификатора ресурса для всех учетных записей хранилища BLOB-объектов Azure требуется завершающая косая черта. Но для идентификатора ресурса конкретной учетной записи хранения завершающая косая черта не требуется. Сведения о том, как найти эти идентификаторы ресурсов, см. в разделе [Службы Azure, которые поддерживают Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   В этом примере свойству **аудитории** присваивается значение `https://storage.azure.com/`, чтобы маркеры доступа, используемые для проверки подлинности, были допустимы для всех учетных записей хранения. Однако можно также указать URL-адрес корневой службы для конкретной учетной записи хранения, `https://fabrikamstorageaccount.blob.core.windows.net`.

   ![Задание для свойства "Аудитория" идентификатора целевого ресурса](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Дополнительные сведения об авторизации доступа с помощью Azure AD для службы хранилища Azure см. в следующих разделах:

   * [Авторизация доступа к BLOB-объектам и очередям с помощью Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Авторизация доступа к хранилищу Azure с помощью Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Продолжите создание приложения логики нужным образом.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Отключение управляемого удостоверения

Чтобы прекратить использование управляемого удостоверения в приложении логики, можно воспользоваться следующими вариантами действий.

* [Портал Azure](#azure-portal-disable)
* [Шаблоны диспетчера ресурсов Azure](#template-disable)
* Azure PowerShell
  * [Удаление назначений ролей](../role-based-access-control/role-assignments-powershell.md)
  * [Удаление назначаемого пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Удаление назначений ролей](../role-based-access-control/role-assignments-cli.md)
  * [Удаление назначаемого пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* REST API Azure
  * [Удаление назначений ролей](../role-based-access-control/role-assignments-rest.md)
  * [Удаление назначаемого пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

При удалении приложения логики Azure автоматически удаляет присвоенный системой идентификатор приложения логики из Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Отключение управляемого удостоверения на портале Azure

На портале Azure сначала удалите доступ удостоверения к [целевому ресурсу](#disable-identity-target-resource). Затем отключите назначенное системой удостоверение или удалите назначаемое пользователем удостоверение из [приложения логики](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Удаление доступа удостоверения к ресурсам

1. На [портале Azure](https://portal.azure.com) перейдите к ресурсу Azure, у которого вы хотите удалить доступ для управляемого удостоверения.

1. В меню целевого ресурса выберите **Управление доступом (IAM)** . Выберите под панелью инструментов пункт **Назначение ролей**.

1. В списке ролей выберите управляемые удостоверения, которые требуется удалить. На панели инструментов нажмите кнопку **Удалить**.

   > [!TIP]
   > Если кнопка **Удалить** недоступна, скорее всего, у вас нет разрешений. Дополнительные сведения о различных ролях администраторов см. в статье [Разрешения роли администратора в Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Управляемое удостоверение теперь удалено и больше не имеет доступа к целевому ресурсу.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Отключение управляемого удостоверения в приложении логики

1. На [портале Azure](https://portal.azure.com) откройте приложение логики в конструкторе приложений логики.

1. В меню приложения логики в разделе **Параметры** выберите пункт **Удостоверение**, затем выполните следующее.

   * Выберите **Назначенное системой** > **Включено** > **Сохранить**. Когда Azure запросит подтверждение, выберите **Да**.

     ![Отключение назначаемого системой удостоверения](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Выберите **Назначаемое пользователем** и нужное управляемое удостоверение, а затем нажмите **Удалить**. Когда Azure запросит подтверждение, выберите **Да**.

     ![Удаление назначаемого пользователем удостоверения](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Управляемое удостоверение теперь отключено в приложении логики.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Отключение управляемого удостоверения с помощью шаблона Resource Manager

Если вы создали управляемое удостоверение приложения логики с помощью шаблона развертывания Azure Resource Manager, задайте свойству `type` элемента `identity` значение `None`.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Безопасный доступ и данные в Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
