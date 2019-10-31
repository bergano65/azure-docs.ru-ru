---
title: Проверка подлинности с помощью управляемых удостоверений — Azure Logic Apps
description: Доступ к ресурсам в других клиентах Azure Active Directory без входа с использованием учетных данных или секретов с помощью управляемого удостоверения
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: 2d1dbde2499dbe793a895f894e5ae83c36c54449
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200624"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Проверка подлинности доступа к ресурсам Azure с помощью управляемых удостоверений в Azure Logic Apps

Чтобы получить доступ к ресурсам в других клиентах Azure Active Directory (Azure AD) и проверить подлинность личности без входа, приложение логики может использовать [управляемое системой удостоверение](../active-directory/managed-identities-azure-resources/overview.md) (прежнее название — управляемое удостоверение службы или MSI), а не учетные данные или секреты. Azure управляет этим удостоверением за вас и помогает защитить учетные данные, потому что вам не нужно предоставлять или сменять секреты. В этой статье показано, как настроить и использовать управляемое системой удостоверение в приложении логики. В настоящее время управляемые удостоверения работают только с [конкретными встроенными триггерами и действиями, а](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-to-outbound-calls)не с управляемыми соединителями или соединениями.

Дополнительные сведения см. в следующих статьях:

* [Триггеры и действия, поддерживающие управляемые удостоверения](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Службы Azure, поддерживающие аутентификацию Azure AD с управляемыми удостоверениями](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [Поддерживаемые типы проверки подлинности для исходящих вызовов](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Ограничения управляемых удостоверений для приложений логики](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас еще ее нет, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/). Управляемое удостоверение и целевой ресурс Azure, для которых требуется доступ к одной и той же подписке Azure.

* [Разрешения администратора Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) , которые могут назначать роли управляемым удостоверениям в том же клиенте Azure AD, что и целевой ресурс. Чтобы предоставить управляемому удостоверению доступ к ресурсу Azure, необходимо добавить роль для этого удостоверения в целевом ресурсе.

* Целевой ресурс Azure, к которому требуется получить доступ

* Приложение логики, которое использует [триггеры и действия, поддерживающие управляемые удостоверения](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>Включить назначенное системой удостоверение

В отличие от назначенных пользователем удостоверений не требуется вручную создавать назначенные системой удостоверения. Чтобы настроить удостоверение, назначенное системой для приложения логики, можно использовать следующие параметры.

* [Портал Azure](#azure-portal-system-logic-app)
* [Шаблоны диспетчера ресурсов Azure](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Включение назначенного системой удостоверения в портал Azure

1. На [портале Azure](https://portal.azure.com) откройте приложение логики в конструкторе приложений логики.

1. В меню приложения логики в разделе **Параметры**выберите **удостоверение** > **системой назначено**. В **разделе Состояние**выберите ** > ** **сохранить** > **Да**.

   ![Включить назначенное системой удостоверение](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   Теперь приложение логики может использовать назначенное системой удостоверение, зарегистрированное в Azure Active Directory и представленное ИДЕНТИФИКАТОРом объекта.

   ![Идентификатор объекта для назначенного системой удостоверения](./media/create-managed-service-identity/object-id.png)

   | Свойство | Value | Описание |
   |----------|-------|-------------|
   | **Идентификатор объекта** | <*identity-resource-ID*> | Глобальный уникальный идентификатор (GUID), представляющий назначенное системой удостоверение для приложения логики в клиенте Azure AD. |
   ||||

1. Теперь выполните [действия, которые придают удостоверению доступ к ресурсу](#access-other-resources).

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Включение назначенного системой удостоверения в шаблоне Azure Resource Manager

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

| Свойство (JSON) | Value | Описание |
|-----------------|-------|-------------|
| `principalId` | <*ИД субъекта*> | Глобальный уникальный идентификатор (GUID) объекта субъекта-службы для управляемого удостоверения, представляющего ваше приложение логики в клиенте Azure AD. Этот идентификатор GUID иногда отображается как "идентификатор объекта" или `objectID`. |
| `tenantId` | <*ИД клиента Azure AD*> | Глобальный уникальный идентификатор (GUID), представляющий клиент Azure AD, в который теперь входит приложение логики. В клиенте Azure AD субъект-служба имеет то же имя, что и экземпляр приложения логики. |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Предоставление удостоверениям доступа к ресурсам

После настройки управляемого удостоверения для приложения логики можно [предоставить этому удостоверению доступ к другим ресурсам Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Затем это удостоверение можно использовать для проверки подлинности.

1. В [портал Azure](https://portal.azure.com)перейдите к ресурсу Azure, в котором вы хотите, чтобы управляемое удостоверение имело доступ.

1. В меню ресурса выберите пункт **Управление доступом (IAM)**  > **назначения ролей** , где можно проверить текущие назначения ролей для этого ресурса. На панели инструментов выберите **добавить** > **добавить назначение ролей**.

   ![Выберите "Добавить" > "добавить назначение ролей".](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Если параметр **добавить назначение роли** отключен, скорее всего, у вас нет разрешений. Дополнительные сведения о разрешениях, позволяющих управлять ролями для ресурсов, см. [в разделе разрешения роли администратора в Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. В разделе **Добавление назначения ролей**выберите **роль** , которая предоставит удостоверению необходимый доступ к целевому ресурсу.

   В этом примере для удостоверения требуется [роль, которая может получить доступ к большому двоичному объекту в контейнере службы хранилища Azure](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights):

   ![Выберите роль "участник данных BLOB-объектов хранилища"](./media/create-managed-service-identity/assign-role.png)

1. В поле **Назначить доступ** выберите **Пользователь, группа или субъект-служба Azure AD**.

   ![Выбор доступа для удостоверения, назначенного системой](./media/create-managed-service-identity/assign-access-system.png)

1. В поле **выбрать** найдите и выберите свое приложение логики.

   ![Выбор приложения логики для назначенного системой удостоверения](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Когда все будет готово, нажмите кнопку **Сохранить**.

   В списке назначения ролей целевого ресурса теперь отображается выбранное управляемое удостоверение и роль.

   ![В целевой ресурс добавлены управляемые удостоверения и роли](./media/create-managed-service-identity/added-roles-for-identities.png)

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

   | Свойство | Обязательно для заполнения | Описание |
   |----------|----------|-------------|
   | **Метод** | ДА | Метод HTTP, используемый операцией, которую необходимо выполнить |
   | **URI** | ДА | URL-адрес конечной точки для доступа к целевому ресурсу или сущности Azure. Синтаксис URI обычно включает [идентификатор ресурса](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) для ресурса или службы Azure. |
   | **Заголовки** | Нет | Любые значения заголовка, которые требуется включить в исходящий запрос, например тип содержимого |
   | **Запросы** | Нет | Любые параметры запроса, которые требуется включить в запрос, например параметр для определенной операции или версия API для операции, которую требуется выполнить. |
   | **Authentication** (Аутентификация) | ДА | Тип проверки подлинности, используемый для проверки подлинности доступа к целевому ресурсу или сущности |
   ||||

   В качестве конкретного примера предположим, что вы хотите выполнить [операцию создания BLOB-объекта моментального снимка](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) в большом двоичном объекте в учетной записи хранения Azure, где вы ранее настроили доступ к удостоверению. Однако [соединитель хранилища BLOB-объектов Azure](https://docs.microsoft.com/connectors/azureblob/) в настоящее время не поддерживает эту операцию. Вместо этого эту операцию можно выполнить с помощью [действия HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) или другой [службы Blob-объектов REST API операции](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Чтобы получить доступ к учетным записям хранения Azure за брандмауэром с помощью HTTP-запросов и управляемых удостоверений, убедитесь, что вы также настроили учетную запись хранения с [исключением, которое разрешает доступ доверенными службами Майкрософт](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Чтобы запустить [операцию создания BLOB-объекта моментальных снимков](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob), в действии HTTP указываются следующие свойства:

   | Свойство | Обязательно для заполнения | Пример значения | Описание |
   |----------|----------|---------------|-------------|
   | **Метод** | ДА | `PUT`| Метод HTTP, используемый операцией BLOB-объекта моментального снимка |
   | **URI** | ДА | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Идентификатор ресурса для файла хранилища BLOB-объектов Azure в глобальной (общедоступной) среде Azure, которая использует этот синтаксис. |
   | **Заголовки** | Да, для службы хранилища Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Значения заголовков `x-ms-blob-type` и `x-ms-version`, необходимые для операций службы хранилища Azure. <p><p>**Важно**. в исходящем триггере HTTP и запросах действий для службы хранилища Azure для заголовка требуется свойство `x-ms-version` и версия API для операции, которую требуется выполнить. <p>Дополнительные сведения см. в следующих статьях: <p><p>- [заголовков запроса — BLOB-объект моментальных снимков](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Управление версиями для служб хранилища Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Запросы** | Да, для этой операции | `comp` = `snapshot` | Имя параметра запроса и значение для операции моментального снимка большого двоичного объекта. |
   | **Authentication** (Аутентификация) | ДА | `Managed Identity` | Тип проверки подлинности, используемый для проверки подлинности доступа к большому двоичному объекту Azure |
   |||||

   Ниже приведен пример действия HTTP, в котором показаны все эти значения свойств.

   ![Добавление действия HTTP для доступа к ресурсу Azure](./media/create-managed-service-identity/http-action-example.png)

   Дополнительные сведения обо всех доступных операциях REST API Azure см. в [справочнике по azure REST API](https://docs.microsoft.com/rest/api/azure/).

1. В списке **Проверка подлинности** выберите **Управляемое удостоверение**. Если свойство [ **Проверка подлинности** поддерживается](logic-apps-securing-a-logic-app.md#add-authentication-outbound) , но скрыто, откройте список **Добавить новый параметр** и выберите **Проверка подлинности**.

   > [!NOTE]
   > Не все триггеры и действия позволяют выбрать тип проверки подлинности. Дополнительные сведения см. [в разделе Добавление проверки подлинности для исходящих вызовов](logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![В свойстве "Проверка подлинности" выберите "управляемое удостоверение".](./media/create-managed-service-identity/select-managed-identity.png)

1. После выбора **управляемого удостоверения**для некоторых триггеров и действий отображается свойство **аудитория** . Если свойство **аудитория** поддерживается, но скрыто, откройте список **Добавить новый параметр** и выберите **аудитория**.

1. Убедитесь, что в качестве значения **аудитории** задан идентификатор ресурса для целевого ресурса или службы. В противном случае по умолчанию свойство **аудитория** использует идентификатор ресурса `https://management.azure.com/`, который является идентификатором ресурса для Azure Resource Manager.

   > [!IMPORTANT]
   > Убедитесь, что целевой идентификатор ресурса *точно соответствует* значению, которое требуется Azure Active Directory (AD), включая все обязательные символы косой черты. Например, для идентификатора ресурса для всех учетных записей хранилища BLOB-объектов Azure требуется завершающая косая черта. Однако для идентификатора ресурса конкретной учетной записи хранения не требуется завершающая косая черта. Проверьте [идентификаторы ресурсов для служб Azure, которые поддерживают Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   В этом примере свойству **аудитории** присваивается значение `https://storage.azure.com/`, чтобы маркеры доступа, используемые для проверки подлинности, были допустимы для всех учетных записей хранения. Однако можно также указать URL-адрес корневой службы `https://fabrikamstorageaccount.blob.core.windows.net`для конкретной учетной записи хранения.

   ![Укажите идентификатор целевого ресурса в свойстве "аудитория"](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Дополнительные сведения об авторизации доступа с помощью Azure AD для службы хранилища Azure см. в следующих разделах:

   * [Авторизация доступа к BLOB-объектам и очередям Azure с помощью Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Авторизация доступа к службе хранилища Azure с помощью Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>Удалить назначенное системой удостоверение

Чтобы не использовать назначенное системой удостоверение для приложения логики, доступны следующие варианты:

* [Портал Azure](#azure-portal-disable)
* [Шаблоны диспетчера ресурсов Azure](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Azure CLI](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

Если вы удалите приложение логики, Azure автоматически удалит управляемое удостоверение из Azure AD.

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>Удаление назначенного системой удостоверения в портал Azure

В портал Azure удалите назначенное системой удостоверение [из приложения логики](#disable-identity-logic-app) и получите доступ к этому [ресурсу из целевого ресурса](#disable-identity-target-resource).

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>Удалить назначенное системой удостоверение из приложения логики

1. На [портале Azure](https://portal.azure.com) откройте приложение логики в конструкторе приложений логики.

1. В меню приложения логики в разделе **Параметры**выберите **удостоверение** > **системой назначено**. В разделе **состояние**выберите **выкл** . > **сохранить** > **Да**.

   ![Запретить с использованием назначенного системой удостоверения](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Удаление доступа к удостоверениям из ресурсов

1. В [портал Azure](https://portal.azure.com)перейдите к целевому ресурсу Azure, в котором вы хотите удалить доступ для управляемого удостоверения.

1. В меню целевого ресурса выберите **Управление доступом (IAM)** . На панели инструментов выберите **назначения ролей**.

1. В списке роли выберите управляемые удостоверения, которые требуется удалить. На панели инструментов нажмите кнопку **Удалить**.

   > [!TIP]
   > Если параметр **Удалить** отключен, скорее всего, у вас нет разрешений. Дополнительные сведения о разрешениях, позволяющих управлять ролями для ресурсов, см. [в разделе разрешения роли администратора в Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Управляемое удостоверение теперь удалено и больше не имеет доступа к целевому ресурсу.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Отключение управляемого удостоверения в шаблоне Azure Resource Manager

Если вы включили управляемое системой удостоверение приложения логики с помощью шаблона Azure Resource Manager, установите для дочернего свойства `type` объекта `identity` значение `None`. Это действие также удаляет идентификатор участника для управляемого системой удостоверения из Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Безопасный доступ и данные в Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
