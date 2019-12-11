---
title: Предоставление дополнительных утверждений для приложений Azure AD | Службы
titleSuffix: Microsoft identity platform
description: Добавление настраиваемых или дополнительных утверждений в токены SAML 2,0 и JSON Web tokens (JWT), выданные Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/08/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcaf347eb91f8777b56bb2ea4d26985b2d75f645
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967254"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Руководство. предоставление необязательных утверждений для приложения Azure AD

Разработчики приложений могут использовать необязательные утверждения в своих приложениях Azure AD, чтобы указать, какие утверждения они хотят в токенах, отправляемых в приложение. 

Необязательные утверждения можно использовать, чтобы:

- выбрать дополнительные утверждения для включения в токены для приложения;
- изменить поведение определенных утверждений в токенах, возвращаемых Azure AD;
- добавлять пользовательские утверждения для приложения и обращаться к ним.

Список стандартных утверждений см. в документации [маркер доступа](access-tokens.md) и [id_token](id-tokens.md) утверждений. 

Хотя необязательные утверждения поддерживаются в маркерах формата v 1.0 и v 2.0, а также в маркерах SAML, они предоставляют большую часть их значений при переходе с версии 1.0 на версию 2.0. Одной из целей [конечной точки платформы идентификации Майкрософт версии 2.0](active-directory-appmodel-v2-overview.md) являются меньшие размеры маркеров для обеспечения оптимальной производительности клиентов. В результате нескольких утверждений, ранее включенных в маркеры доступа и идентификаторов, больше нет в токенах версии 2.0 и их нужно запрашивать специально для каждого приложения.

**Таблица 1. Применимость**

| Тип учетной записи | маркеры версии 1.0; | маркеры версии 2.0.  |
|--------------|---------------|----------------|
| Личная учетная запись Майкрософт  | Н/Д  | Поддерживается |
| Учетная запись Azure AD      | Поддерживается | Поддерживается |

## <a name="v10-and-v20-optional-claims-set"></a>набор утверждений v 1.0 и v 2.0

Набор необязательных утверждений доступен по умолчанию для использования приложениями, перечисленными ниже. Чтобы добавить настраиваемые необязательные утверждения для приложения, ознакомьтесь с разделом [Расширения каталога](#configuring-directory-extension-optional-claims) ниже. При добавлении утверждений в **маркер доступа**это будет применяться к маркерам доступа, запрошенным *для* приложения (веб-API), а не *по* приложениям. Это гарантирует, что независимо от того, получил ли клиент доступ к вашему API, в маркере доступа, используемом при аутентификации с помощью API, будут присутствовать правильные данные.

> [!NOTE]
> Большую часть этих утверждений можно включить в JWT для токенов версии 1.0 и 2.0, кроме токенов SAML, за исключением оговоренных в столбце типов токенов. Учетные записи потребителей поддерживают подмножество этих утверждений, помеченных в столбце "тип пользователя".  Многие из перечисленных утверждений не применяются к пользователям-получателям (у них нет клиента, поэтому `tenant_ctry` не имеет значения).  

**Таблица 2. необязательный набор утверждений v 1.0 и v 2.0**

| Name                       |  Описание   | Тип маркера | Тип пользователя | Заметки  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Время, когда пользователь последний раз проходил аутентификацию. Ознакомьтесь со спецификацией OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Регион клиента ресурса | JWT        |           | |
| `home_oid`                 | Для гостевых пользователей идентификатор объекта пользователя — это домашний клиент пользователя.| JWT        |           | |
| `sid`                      | Идентификатор сеанса, используемый для выхода пользователя из сеанса. | JWT        |  Личные и учетные записи Azure AD.   |         |
| `platf`                    | Платформа устройства    | JWT        |           | Только для управляемых устройств, которые могут проверить тип устройства.|
| `verified_primary_email`   | Источник: PrimaryAuthoritativeEmail пользователя      | JWT        |           |         |
| `verified_secondary_email` | Источник: SecondaryAuthoritativeEmail пользователя   | JWT        |           |        |
| `enfpolids`                | Идентификаторы применяемой политики. Список идентификаторов политик, оцененных для текущего пользователя. | JWT |  |  |
| `vnet`                     | Сведения об описателе виртуальной сети. | JWT        |           |      |
| `fwd`                      | IP-адрес.| JWT    |   | Добавляет исходный IPv4-адрес запрашивающего клиента (при использовании внутри виртуальной сети) |
| `ctry`                     | Страна пользователя | JWT |  | Azure AD возвращает необязательное утверждение `ctry`, если оно имеется, и значением утверждения является стандартный двухбуквенный код страны, например FR, JP, SZ и т. д. |
| `tenant_ctry`              | Страна ресурса клиента | JWT | | |
| `xms_pdl`          | Предпочтительное расположение данных   | JWT | | Для нескольких географических клиентов это код из трех букв, отображающий географический регион, в котором находится пользователь. Дополнительные сведения см. в [Azure AD Connect документации о предпочтительном расположении данных](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Например: `APC` для Азиатско-Тихоокеанского региона. |
| `xms_pl`                   | Предпочитаемый язык пользователя  | JWT ||Предпочитаемый язык пользователя, если задан. Источник: домашний клиент пользователя в сценариях гостевого доступа. Формат: ЯЯ-СС ("en-us"). |
| `xms_tpl`                  | Предпочитаемый язык клиента| JWT | | Предпочитаемый язык клиента ресурса, если задан. Формат: ЯЯ ("en"). |
| `ztdid`                    | Идентификатор развертывания без участия пользователя | JWT | | Идентификатор устройства, используемый для [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Доступный адрес электронной почты для этого пользователя (если имеется).  | JWT, SAML | MSA, Azure AD | Это значение добавляется по умолчанию, если пользователь является гостем в клиенте.  Для управляемых пользователей (в клиенте) это значение должно запрашиваться посредством необязательного утверждения или, в версии 2.0, с использованием области OpenID.  Адреса электронной почты для управляемых пользователей должны быть заданы на [портале администрирования Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Необязательное форматирование для групповых утверждений |JWT, SAML| |Используется в сочетании с параметром GroupMembershipClaims в [манифесте приложения](reference-app-manifest.md), который также должен быть установлен. Дополнительные сведения см. в разделе [групповые утверждения](#configuring-groups-optional-claims) ниже. Дополнительные сведения об утверждениях группы см. [в статье Настройка групповых утверждений](../hybrid/how-to-connect-fed-group-claims.md) .
| `acct`             | Состояние учетной записи пользователя в клиенте. | JWT, SAML | | Если пользователь является членом клиента, это значение равно `0`. Если он является гостем, это значение равно `1`. |
| `upn`                      | Утверждение UserPrincipalName. | JWT, SAML  |           | Несмотря на то, что это утверждение автоматически включается, можно указать его в качестве необязательного утверждения для присоединения дополнительных свойств, чтобы изменить его поведение в варианте использования гостевым пользователем.  |

## <a name="v20-specific-optional-claims-set"></a>Версия 2.0 — заданный дополнительный набор утверждений

Эти утверждения всегда включаются в токены Azure AD версии 1.0, но не включены в маркеры версии 2.0, если это не требуется. Эти утверждения применимы только для JWT (токены идентификации и маркеры доступа). 

**Таблица 3. v 2.0 — только необязательные утверждения**

| Утверждение JWT     | Name                            | Описание                                | Заметки |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-адрес                      | IP-адрес, с которого клиент вошел в систему.   |       |
| `onprem_sid`  | Локальный идентификатор безопасности |                                             |       |
| `pwd_exp`     | Срок действия пароля        | Дата и время истечения срока действия пароля. |       |
| `pwd_url`     | Изменить URL-адрес пароля             | URL-адрес, перейдя по которому пользователь может изменить свой пароль.   |   |
| `in_corp`     | В корпоративной сети        | Посылает сигнал, если клиент входит в корпоративную сеть. В противном случае утверждение не включается.   |  На основе параметров [надежных IP-адресов](../authentication/howto-mfa-mfasettings.md#trusted-ips) в MFA.    |
| `nickname`    | Псевдоним                        | Дополнительное имя для пользователя, отдельное от имени или фамилии. | 
| `family_name` | Фамилия                       | Предоставляет фамилию, фамилию или имя семейства пользователей, как определено в объекте User. <br>"family_name":"Miller" | Поддерживается в MSA и Azure AD   |
| `given_name`  | Имя                      | Предоставляет первое или "заданное имя пользователя, заданное для объекта пользователя.<br>"given_name": "Frank"                   | Поддерживается в MSA и Azure AD  |
| `upn`         | User Principal Name | Идентификатор пользователя, который можно использовать с параметром username_hint.  Не является долговременным идентификатором для пользователя и не должен использоваться для ключевых данных. | См. в разделе [Дополнительные свойства необязательных утверждений](#additional-properties-of-optional-claims) указанном ниже для конфигурации утверждения. |

### <a name="additional-properties-of-optional-claims"></a>Дополнительные свойства необязательных утверждений

Некоторые необязательные утверждения можно настроить так, чтобы изменить способ возврата утверждения. Эти дополнительные свойства используются в основном для переноса локальных приложений с различными требованиями к данным (например, `include_externally_authenticated_upn_without_hash` помогает при работе с клиентами, которые не обрабатывают метки хэширования (`#`) в имени участника-пользователя).

**Таблица 4. значения для настройки необязательных утверждений**

| Имя свойства  | Имя дополнительного свойства | Описание |
|----------------|--------------------------|-------------|
| `upn`          |                          | Может использоваться для ответов SAML и JWT и для токенов v1.0 и v2.0. |
|                | `include_externally_authenticated_upn`  | Включает гостевое имя участника-пользователя, сохраненное в клиенте ресурса. Например `foo_hometenant.com#EXT#@resourcetenant.com`. |             
|                | `include_externally_authenticated_upn_without_hash` | Аналогично предыдущему примеру, за исключением того, что метки хэширования (`#`) будут заменены символами нижнего подчеркивания (`_`), например `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Пример дополнительных свойств:

    ```json
        "optionalClaims": 
         {
             "idToken": [ 
            { 
                      "name": "upn", 
                      "essential": false,
                  "additionalProperties": [ "include_externally_authenticated_upn"]  
                    }
                 ]
        }
    ```

Этот объект OptionalClaims вызывает маркер идентификатора, возвращаемый клиенту, для включения другого имени участника-пользователя с дополнительными сведениями о домашнем клиенте и клиенте ресурса. Он может изменить утверждение `upn` в маркере, только если пользователь является гостем в клиенте (который использует другой поставщик удостоверений для проверки подлинности). 

## <a name="configuring-optional-claims"></a>Настройка необязательных утверждений

> [!IMPORTANT]
> Маркеры доступа **всегда** создаются с помощью манифеста ресурса, а не клиента.  Итак, в запросе `...scope=https://graph.microsoft.com/user.read...` ресурс является графом.  Таким же маркер доступа создается с помощью манифеста графа, а не манифеста клиента.  Изменение манифеста для приложения никогда не приведет к тому, что маркеры для Graph будут выглядеть иначе.  Чтобы проверить, вступают ли в действие изменения в `accessToken`, запросите маркер для вашего приложения, а не другого приложения.  

Можно настроить необязательные утверждения для приложения с помощью пользовательского интерфейса или манифеста приложения.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Пройдя аутентификацию, выберите клиент Azure AD, щелкнув его в правом верхнем углу страницы.
1. Выберите **Azure Active Directory** в меню слева.
1. В разделе **Управление** выберите **Регистрация приложений**.
1. В списке выберите приложение, для которого нужно настроить необязательные утверждения.

**Настройка необязательных утверждений через пользовательский интерфейс:**

[![показано, как настроить необязательные утверждения с помощью пользовательского интерфейса](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. В разделе **Управление** выберите **Конфигурация токена (Предварительная версия)** .
2. Выберите **добавить необязательное утверждение**.
3. Выберите тип токена, который требуется настроить.
4. Выберите необязательные утверждения для добавления.
5. Щелкните **Добавить**.

**Настройка необязательных утверждений с помощью манифеста приложения:**

[![показано, как настроить необязательные утверждения с помощью манифеста приложения.](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. В разделе **Управление** выберите **Манифест**. Откроется редактор манифестов на основе веб-интерфейсов, позволяющий изменить манифест. При необходимости можно выбрать **Скачать** и изменить манифест локально, а затем повторно применить его для приложения с помощью команды **Отправить**. Дополнительные сведения о манифесте приложения см. в [статье основные сведения о манифесте приложения Azure AD](reference-app-manifest.md).

    Следующая запись манифеста приложения добавляет необязательные утверждения auth_time, reduce и UPN к маркерам ID, доступа и SAML.

    ```json
        "optionalClaims":  
           {
              "idToken": [
                    {
                          "name": "auth_time", 
                          "essential": false
                     }
              ],
              "accessToken": [
                     {
                            "name": "ipaddr", 
                            "essential": false
                      }
              ],
              "saml2Token": [
                      {
                            "name": "upn", 
                            "essential": false
                       },
                       {
                            "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                            "source": "user", 
                            "essential": false
                       }
               ]
           }
       ```

2. When finished, click **Save**. Now the specified optional claims will be included in the tokens for your application.    


### OptionalClaims type

Declares the optional claims requested by an application. An application can configure optional claims to be returned in each of three types of tokens (ID token, access token, SAML 2 token) it can receive from the security token service. The application can configure a different set of optional claims to be returned in each token type. The OptionalClaims property of the Application entity is an OptionalClaims object.

**Table 5: OptionalClaims type properties**

| Name        | Type                       | Description                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Collection (OptionalClaim) | The optional claims returned in the JWT ID token. |
| `accessToken` | Collection (OptionalClaim) | The optional claims returned in the JWT access token. |
| `saml2Token`  | Collection (OptionalClaim) | The optional claims returned in the SAML token.   |

### OptionalClaim type

Contains an optional claim associated with an application or a service principal. The idToken, accessToken, and saml2Token properties of the [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) type is a collection of OptionalClaim.
If supported by a specific claim, you can also modify the behavior of the OptionalClaim using the AdditionalProperties field.

**Table 6: OptionalClaim type properties**

| Name                 | Type                    | Description                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | The name of the optional claim.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | The source (directory object) of the claim. There are predefined claims and user-defined claims from extension properties. If the source value is null, the claim is a predefined optional claim. If the source value is user, the value in the name property is the extension property from the user object. |
| `essential`            | Edm.Boolean             | If the value is true, the claim specified by the client is necessary to ensure a smooth authorization experience for the specific task requested by the end user. The default value is false.                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Additional properties of the claim. If a property exists in this collection, it modifies the behavior of the optional claim specified in the name property.                                                                                                                                               |
## Configuring directory extension optional claims

In addition to the standard optional claims set, you can also configure tokens to include directory schema extensions. For more info, see [Directory schema extensions](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). This feature is useful for attaching additional user information that your app can use – for example, an additional identifier or important configuration option that the user has set. See the bottom of this page for an example.

> [!NOTE]
> - Directory schema extensions are an Azure AD-only feature, so if your application manifest requests a custom extension and an MSA user logs into your app, these extensions will not be returned.
> - Azure AD optional claims only work with Azure AD Graph extensions and do not work with Microsoft Graph directory extensions. Both APIs require the `Directory.ReadWriteAll` permission, which can only be consented by admins.

### Directory extension formatting

When configuring directory extension optional claims using the application manifest, use the full name of the extension (in the format: `extension_<appid>_<attributename>`). The `<appid>` must match the ID of the application requesting the claim. 

Within the JWT, these claims will be emitted with the following name format:  `extn.<attributename>`.

Within the SAML tokens, these claims will be emitted with the following URI format: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## Configuring groups optional claims

   > [!NOTE]
   > The ability to emit group names for users and groups synced from on-premises is Public Preview.

This section covers the configuration options under optional claims for changing the group attributes used in group claims from the default group objectID to attributes synced from on-premises Windows Active Directory. You can configure groups optional claims for your application through the UI or application manifest.

> [!IMPORTANT]
> See [Configure group claims for applications with Azure AD](../hybrid/how-to-connect-fed-group-claims.md) for more details including important caveats for the public preview of group claims from on-premises attributes.

**Configuring groups optional claims through the UI:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Under the **Manage** section, select **App registrations**
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Token configuration (preview)**
2. Select **Add groups claim**
3. Select the group types to return (**All Groups**, **SecurityGroup** or **DirectoryRole**). The **All Groups** option includes **SecurityGroup**, **DirectoryRole** and **DistributionList**
4. Optional: click on the specific token type properties to modify the groups claim value to contain on premises group attributes or to change the claim type to a role
5. Click **Save**

**Configuring groups optional claims through the application manifest:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Manifest**
3. Add the following entry using the manifest editor:

   The valid values are:

   - "All" (this option includes SecurityGroup, DirectoryRole and DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   For example:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   По умолчанию группы ObjectID будут выдаваться в значении утверждения группы.  Чтобы изменить значение утверждения в соответствии с атрибутами локальной группы или изменить тип утверждения на Role, используйте конфигурацию OptionalClaims следующим образом:

3. Задайте необязательные утверждения для конфигурации имени группы.

   Если вы хотите, чтобы группы в токене содержали атрибуты on локальной группы AD в разделе необязательные утверждения, укажите, к какому типу токена следует применить обязательное утверждение, имя запрошенного необязательного утверждения и дополнительные свойства.  В списке можно указать несколько типов токенов:

   - idToken для токена идентификатора OIDC
   - accessToken для маркера доступа OAuth/OIDC
   - Saml2Token для токенов SAML.

   > [!NOTE]
   > Тип Saml2Token применяется к маркерам формата SAML 1.1 и SAML 2.0.

   Для каждого соответствующего типа токена измените утверждение групп, чтобы в манифесте использовался раздел OptionalClaims. Схема OptionalClaims выглядит следующим образом:

    ```json
       {
       "name": "groups",
       "source": null,
       "essential": false,
       "additionalProperties": []
       }
    ```

   | Схема необязательных утверждений | Value |
   |----------|-------------|
   | **безымян** | Должно быть "groups" |
   | **источника** | Не используется. Опустить или укажите значение null |
   | **очень** | Не используется. Опустить или укажите значение false |
   | **AdditionalProperties** | Список дополнительных свойств.  Допустимые значения: "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   В additionalProperties требуется только один из них: "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Если указано более одного, используется первый, а остальные игнорируются.

   Некоторые приложения занимают сведения о группе пользователя в утверждении роли.  Чтобы изменить тип утверждения с утверждения группы на утверждение роли, добавьте "emit_as_roles" к дополнительным свойствам.  Значения группы будут выдаваться в заявке роли.

   > [!NOTE]
   > Если используется "emit_as_roles", все роли приложения, настроенные для назначения пользователя, не будут отображаться в заявке роли

**Примеры**

1) Выдавать группы в виде имен групп в маркерах доступа OAuth в формате Днсдомаиннаме\самаккаунтнаме

    
    **Конфигурация пользовательского интерфейса:**

    [![показано, как настроить необязательные утверждения с помощью пользовательского интерфейса](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Запись манифеста приложения:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Выдавать имена групп, возвращаемых в формате Нетбиосдомаин\самаккаунтнаме в качестве утверждения ролей в маркерах ИДЕНТИФИКАТОРов SAML и OIDC

    **Конфигурация пользовательского интерфейса:**

    [![показано, как настроить необязательные утверждения с помощью пользовательского интерфейса](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Запись манифеста приложения:**
    
    ```json
        "optionalClaims": {
        "saml2Token": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }],
        "idToken": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }]
    ``` 
     

## <a name="optional-claims-example"></a>Пример необязательного утверждения

В этом разделе можно пошагово выполнить сценарий, чтобы узнать, как можно использовать возможность необязательных утверждений для приложения.
Есть несколько доступных вариантов обновления свойств в конфигурации удостоверения приложения, позволяющих включить и настроить необязательные утверждения:
-    Вы можете использовать пользовательский интерфейс **настройки маркера (Предварительная версия)** (см. пример ниже).
-    Вы можете использовать **Манифест** (см. пример ниже). Ознакомьтесь со статьей [Манифест приложения Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest), чтобы получить общие сведения о манифесте.
-   Вы также можете написать приложение, в котором для обновления используется [API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api). [Справочник по сущностям и сложным типам](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) в справочнике по API Graph может помочь вам с конфигурацией необязательных утверждений.

**Пример:** В приведенном ниже примере вы будете использовать пользовательский интерфейс **конфигурации маркера (Предварительная версия)** и **Манифест** для добавления необязательных утверждений к МАРКЕРам доступа, идентификатору и SAML, предназначенным для вашего приложения. В каждый тип токена, который может получить приложение, будут добавлены различные необязательные утверждения:
-    Теперь токены идентификации содержат имена участников-пользователей федеративных пользователей в полной форме (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    Маркеры доступа, запрашиваемые другими клиентами для этого приложения, теперь включают утверждение auth_time
-    Токен SAML будет содержать расширение схемы каталога skypeId (в этом примере идентификатор этого приложения — ab603c56068041afb2f6832e2a17e237). Токен SAML представит идентификатор Skype как `extension_skypeId`.

**Конфигурация пользовательского интерфейса:**

1. Войдите на [портал Azure](https://portal.azure.com)

1. Пройдя аутентификацию, выберите клиент Azure AD, щелкнув его в правом верхнем углу страницы.

1. Выберите **Azure Active Directory** в меню слева.

1. В разделе **Управление** выберите **Регистрация приложений**.

1. Найдите в списке приложение, для которого нужно настроить необязательные утверждения, и щелкните его.

1. В разделе **Управление** щелкните **Конфигурация токена (Предварительная версия)** .

1. Выберите **добавить необязательное утверждение**, выберите тип маркера **идентификатор** , выберите **UPN** в списке утверждений и нажмите кнопку **Добавить**.

1. Выберите **добавить необязательное утверждение**, выберите тип маркера **доступа** , выберите **auth_time** в списке заявок, а затем нажмите кнопку **Добавить**.

1. На экране "Обзор конфигурации токенов" щелкните значок карандаша рядом с **именем участника-пользователя**, выберите переключатель с **внешней проверкой подлинности** и нажмите кнопку **сохранить**.

1. Выберите **добавить необязательное утверждение**, выберите тип токена **SAML** , выберите **екстн. skypeID** из списка утверждений (применимо, только если вы создали объект пользователя Azure AD с именем skypeID) и нажмите кнопку **Добавить**.

    [![показано, как настроить необязательные утверждения с помощью пользовательского интерфейса](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Конфигурация манифеста:**
1. Войдите на [портале Azure](https://portal.azure.com).
1. Пройдя аутентификацию, выберите клиент Azure AD, щелкнув его в правом верхнем углу страницы.
1. Выберите **Azure Active Directory** в меню слева.
1. Найдите в списке приложение, для которого нужно настроить необязательные утверждения, и щелкните его.
1. В разделе **Управление** щелкните **Манифест** , чтобы открыть встроенный редактор манифеста.
1. Можно напрямую изменить манифест с помощью этого редактора. Манифест соответствует схеме для [сущности приложения]. (https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) и автоматическое форматирование манифеста после сохранения. В свойство `OptionalClaims` будут добавлены новые элементы.

    ```json
            "optionalClaims": {
                "idToken": [ 
                     { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                     }
                     ],
                "accessToken": [ 
                      {
                        "name": "auth_time", 
                        "essential": false
                      }
                     ],
            "saml2Token": [ 
                  { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
                  }
                 ]
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
