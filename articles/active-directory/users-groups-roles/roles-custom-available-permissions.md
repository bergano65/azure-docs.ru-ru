---
title: Доступные пользовательские разрешения роли админ - Azure AD Документы Майкрософт
description: Разрешения на роль пользовательского администратора для делегирования управления идентификацией.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025149"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Подтипы регистрации приложений и разрешения в Active Directory Azure

Эта статья содержит доступные в настоящее время разрешения на регистрацию приложений для пользовательских определений ролей в Active Directory Azure (Azure AD).

## <a name="permissions-for-managing-single-directory-applications"></a>Разрешения на управление приложениями с одним каталогом

При выборе разрешений для пользовательской роли у вас есть возможность предоставить доступ к управлению только приложениями с одним каталогом. Приложения с одним каталогом доступны только пользователям в организации Azure AD, где приложение зарегистрировано. Приложения с одним каталогом определяются как имеющие **поддерживаемые типы учетных записей,** установленные только в этом каталоге организации. В API Graph приложения с одним каталогом имеют свойство InAudience, установленное на "AzureADMyOrg".

Чтобы предоставить доступ к управлению только приложениями с одним каталогом, используйте приведенные ниже разрешения с **помощью подтипных приложений.myOrganization**. Например, microsoft.directory/applications.myOrganization/basic/update.

Ознакомьтесь с [пользовательским обзором ролей,](roles-custom-overview.md) чтобы объяснить, что означают подтип, разрешение и набор свойств. Следующая информация специфична для регистрации заявлений.

### <a name="create-and-delete"></a>Создание и удаление

Существует два разрешения на предоставление возможности создания регистрации приложений, каждый из которых имеет различное поведение:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

Назначение этого разрешения приводит к добавлению создателя в качестве первого владельца созданной регистрации приложения, а регистрация созданного приложения будет учитываться против квоты 250 созданных объектов создателя.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

Назначение этого разрешения приводит к тому, что создатель не будет добавлен в качестве первого владельца созданной регистрации приложения, а регистрация созданного приложения не будет учитываться против квоты 250 созданных объектов создателя. Используйте это разрешение осторожно, потому что ничто не мешает назначенику создавать регистрации приложений до тех пор, пока не будет получена квота уровня каталога. Если назначены оба разрешения, это разрешение имеет приоритет.

Если оба разрешения назначены, разрешение /создать будет иметь приоритет. Хотя разрешение /createAsOwner автоматически не добавляет создателя в качестве первого владельца, владельцы могут быть указаны при создании регистрации приложения при использовании Графических AIS или смдлетов PowerShell.

Создание разрешений предоставляет доступ к новой команде **регистрации.**

[Эти разрешения предоставляют доступ к команде портала «Новая регистрация»](./media/roles-create-custom/new-custom-role.png)

Существует два разрешения на предоставление возможности удалять регистрации приложений:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Предоставляет возможность удалять регистрации приложений независимо от подтипа; то есть, как одно-арендаторов и мульти-арендатор приложений.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganization/delete

Предоставляет возможность удалять регистрации приложений только для тех, которые доступны только для учетных записей в вашей организации или приложений с одним арендатором (подтип myOrganization).

![Эти разрешения предоставляют доступ к команде регистрации приложения Delete](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> При назначении роли, содержащей разрешения на создание, назначение роли должно быть выполнено в области каталога. Разрешение на создание, назначенное в области ресурсов, не дает возможности создавать регистрации приложений.

### <a name="read"></a>Чтение

Все пользователи-члены организации могут читать информацию о регистрации приложений по умолчанию. Тем не менее, гостевые пользователи и директора служб приложений не могут. Если вы планируете назначить роль гостевому пользователю или приложению, необходимо включить соответствующие разрешения на чтение.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

Возможность читать все свойства приложений с одним арендатором и мультиарендаторами за пределами свойств, которые не могут быть прочитаны в любой ситуации, как учетные данные.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

Предоставляет те же разрешения, что и microsoft.directory/applications/allProperties/read, но только для приложений с одним арендатором.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

Предоставляет возможность читать имущество владельцев по однотенантным и мультитенантным приложениям. Предоставляет доступ ко всем полям на странице владельцев заявок:

![Это разрешение предоставляет доступ к странице владельцев регистрации приложений](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.directory/applications/standard/read

Предоставляет доступ к свойствам регистрации стандартных приложений. Это включает свойства на страницах регистрации приложений.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

Предоставляет те же разрешения, что и microsoft.directory/applications/standard/read, но только для приложений с одним арендатором.

### <a name="update"></a>Update

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

Возможность обновления всех свойств в приложениях с одним каталогом и мультикаталогами.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

Предоставляет те же разрешения, что и microsoft.directory/applications/allProperties/update, но только для приложений с одним арендатором.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Возможность обновления поддерживаемого типа учетной записи (signInAudience) свойства на однокаталогичных и мультикаталогных приложений.

![Это разрешение предоставляет доступ к зарегистрированию приложения поддерживается тип счета собственности на странице проверки подлинности](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

Предоставляет те же разрешения, что и microsoft.directory/applications/audience/update, но только для приложений с одним арендатором.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Возможность обновления URL-адреса ответа, URL-адреса регистрации, неявного потока и свойств доменов издателя в приложениях с одним арендатором и несколькими тенантами. Предоставляет доступ ко всем полям на странице проверки подлинности регистрации приложения, за исключением поддерживаемых типов учетных записей:

![Предоставляет доступ к проверке подлинности регистрации приложений, но не поддерживает типы учетных записей](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

Предоставляет те же разрешения, что и microsoft.directory/applications/authentication/update, но только для приложений с одним арендатором.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Возможность обновления имени, логотипа, URL-адреса домашней страницы, URL-адреса службы и URL-свойств ВЫПИСКи конфиденциальности в приложениях с одним арендатором и несколькими тенантами. Предоставляет доступ ко всем полям на странице брендинга регистрации приложений:

![Это разрешение предоставляет доступ к странице брендинга приложения](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

Предоставляет те же разрешения, что и microsoft.directory/applications/basic/update, но только для приложений с одним арендатором.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Возможность обновления сертификатов и свойств секретов клиентов в приложениях с одним арендатором и несколькими арендаторами. Предоставляет доступ ко всем полям на странице регистрации заявок & страницы секретов:

![Это разрешение предоставляет доступ к свидетельствам о регистрации приложения & страницы секретов](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

Предоставляет те же разрешения, что и microsoft.directory/applications/credentials/update, но только для приложений с одним каталогом.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Возможность обновления имущества владельца на одно-арендатора и мультиарендатора. Предоставляет доступ ко всем полям на странице владельцев заявок:

![Это разрешение предоставляет доступ к странице владельцев регистрации приложений](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

Предоставляет те же разрешения, что и microsoft.directory/applications/owners/update, но только для приложений с одним арендатором.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

Возможность обновления делегированных разрешений, разрешений на применение, авторизованных клиентских приложений, требуемых разрешений и предоставления свойств согласия по заявкам с одним арендатором и несколькими арендаторами. Не дает возможности выполнить согласие. Предоставляет доступ ко всем полям на разрешениях API регистрации приложения и экспозиция страниц API:

![Это разрешение предоставляет доступ к странице разрешений API регистрации приложения](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Это разрешение предоставляет доступ к регистрации приложения Разоблачить страницу API](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

Предоставляет те же разрешения, что и microsoft.directory/applications/permissions/update, но только для приложений с одним арендатором.

## <a name="required-license-plan"></a>Обязательный лицензионный план

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Дальнейшие действия

- Создавайте пользовательские роли с [помощью портала Azure, Azure AD PowerShell и Графического API](roles-create-custom.md)
- [Просмотр заданий для пользовательской роли](roles-view-assignments.md)
