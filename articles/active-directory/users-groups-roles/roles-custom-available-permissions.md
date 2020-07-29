---
title: Доступные настраиваемые разрешения роли администратора — Azure AD | Документация Майкрософт
description: Разрешения настраиваемой роли администратора для делегирования управления удостоверениями.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c11723efe3fac236fce49c1f92fa338d4e58b59
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84732112"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Подтипы регистрации приложений и разрешения в Azure Active Directory

Эта статья содержит доступные в настоящее время разрешения регистрации приложений для пользовательских определений ролей в Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-directory-applications"></a>Разрешения для управления приложениями с одним каталогом

При выборе разрешений для настраиваемой роли вы можете предоставить доступ для управления только приложениями с одним каталогом. Приложения с одним каталогом доступны только пользователям в Организации Azure AD, в которой зарегистрировано приложение. Приложения с одним каталогом определяются как имеющие **Поддерживаемые типы учетных** записей "учетные записи только в этом каталоге организации". В API Graph для приложений с одним каталогом свойство Сигнинаудиенце имеет значение "Азуреадмйорг".

Чтобы предоставить доступ для управления только приложениями с одним каталогом, используйте приведенные ниже разрешения с подтипом **Applications. myOrganization**. Например, Microsoft. Directory/Applications. myOrganization/Basic/Update.

В [обзоре пользовательских ролей](roles-custom-overview.md) приведены пояснения о том, что означают общие условия, разрешения и набор свойств. Следующие сведения относятся к регистрации приложений.

### <a name="create-and-delete"></a>Создание и удаление

Существует два разрешения, позволяющие предоставить возможность создания регистраций приложений, в каждой из которых используется другое поведение.

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

Назначение этого разрешения приводит к добавлению создателя в качестве первого владельца созданной регистрации приложения, а созданная регистрация приложения будет считаться для квоты на создание объектов создателя 250.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

Назначение этого разрешения приводит к добавлению создателя в качестве первого владельца созданной регистрации приложения, а созданная регистрация приложения не будет учитываться в квоте на создание объектов создателя 250. Используйте это разрешение с осторожностью, так как в случае его применения ничто не мешает создавать регистрации приложений до тех пор, пока не будет исчерпана квота на уровне каталога. Если назначены оба разрешения, это разрешение имеет приоритет.

Если назначены оба разрешения, разрешение/CREATE будет иметь приоритет. Хотя разрешение/Креатеасовнер не добавляет его в качестве первого владельца автоматически, владельцы могут быть указаны во время создания регистрации приложения при использовании API Graph или командлетов PowerShell.

Разрешения на создание предоставляют доступ к **новой команде регистрации** .

[Эти разрешения предоставляют доступ к новой команде портала регистрации.](./media/roles-create-custom/new-custom-role.png)

Существует два разрешения для предоставления возможности удалять регистрации приложений:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Предоставляет возможность удалять регистрации приложений независимо от подтипа. то есть приложения с одним клиентом и несколькими клиентами.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. myOrganization/Delete

Предоставляет возможность удалять регистрации приложений, ограниченные теми, которые доступны только для учетных записей в организации или приложениях с одним клиентом (myOrganization подтип).

![Эти разрешения предоставляют доступ к команде удаления регистрации приложения.](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> При назначении роли, содержащей разрешения CREATE, назначение роли должно быть выполнено в области каталога. Разрешение CREATE, назначенное в области действия ресурса, не предоставляет возможность создавать регистрации приложений.

### <a name="read"></a>Чтение

Все пользователи, входящие в организацию, могут читать сведения о регистрации приложения по умолчанию. Однако гостевые пользователи и субъекты-службы приложений не могут. Если вы планируете назначить роль гостевому пользователю или приложению, необходимо включить соответствующие разрешения на чтение.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/Applications/Аллпропертиес/Read

Возможность чтения всех свойств приложений с одним клиентом и несколькими клиентами за пределами свойств, которые не могут быть считаны в любой ситуации, например учетные данные.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. myOrganization/Аллпропертиес/Read

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Аллпропертиес/Read, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

Предоставляет возможность чтения свойств владельцев в приложениях с одним клиентом и несколькими клиентами. Предоставляет доступ ко всем полям на странице владельцы регистрации приложения:

![Эти разрешения предоставляют доступ к странице владельцы регистрации приложений.](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>Microsoft. Directory/Applications/Standard/Read

Предоставляет доступ для чтения стандартных свойств регистрации приложения. К ним относятся свойства на страницах регистрации приложений.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. myOrganization/Standard/Read

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Standard/Read, но только для приложений с одним клиентом.

### <a name="update"></a>Update

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/Applications/Аллпропертиес/Update

Возможность обновления всех свойств в приложениях с одним каталогом и несколькими каталогами.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. myOrganization/Аллпропертиес/Update

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Аллпропертиес/Update, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Возможность обновления свойства поддерживаемого типа учетной записи (Сигнинаудиенце) в приложениях с одним каталогом и несколькими каталогами.

![Это разрешение предоставляет доступ к свойству типа учетной записи, поддерживаемой регистрацией приложения на странице проверки подлинности](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. Directory/Applications. myOrganization/аудитория/обновление

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/аудитории/Update, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Возможность обновления URL-адреса ответа, URL-адреса выхода, неявного потока и свойств домена издателя в приложениях с одним клиентом и несколькими клиентами. Предоставляет доступ ко всем полям на странице проверки подлинности регистрации приложения, за исключением поддерживаемых типов учетных записей:

![Предоставление доступа к проверке подлинности регистрации приложений, но не поддерживаемых типов учетных записей](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. myOrganization, проверка подлинности или обновление

Предоставляет те же разрешения, что и Microsoft. каталог/приложения, проверка подлинности или обновление, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Возможность обновления свойств имени, логотипа, URL-адреса домашней страницы, URL-адреса условия службы и заявления о конфиденциальности в приложениях с одним клиентом и несколькими клиентами. Предоставляет доступ ко всем полям на странице фирменной символики регистрации приложения:

![Это разрешение предоставляет доступ к странице фирменной символики регистрации приложения](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. myOrganization/Basic/Update

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Basic/Update, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Возможность обновления свойств сертификатов и секретов клиента в приложениях с одним клиентом и несколькими клиентами. Предоставляет доступ ко всем полям в сертификатах регистрации приложения & секретной странице:

![Это разрешение предоставляет доступ к сертификатам регистрации приложений & секретной странице](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. myOrganization/учетные данные/обновление

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Credentials/Update, но только для приложений с одним каталогом.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Возможность обновления свойства Owner в одном клиенте и нескольких клиентах. Предоставляет доступ ко всем полям на странице владельцы регистрации приложения:

![Эти разрешения предоставляют доступ к странице владельцы регистрации приложений.](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. myOrganization/Owners/Update

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Owners/Update, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

Возможность обновления делегированных разрешений, разрешений приложения, разрешенных клиентских приложений, необходимых разрешений и предоставления свойств согласия в приложениях с одним клиентом и несколькими клиентами. Не предоставляет возможность выполнения согласия. Предоставляет доступ ко всем полям в разрешениях API регистрации приложения и предоставляет страницы API:

![Эти разрешения предоставляют доступ к странице разрешений API регистрации приложений.](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Эти разрешения предоставляют доступ к странице API для регистрации приложения.](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. Directory/Applications. myOrganization/разрешения/обновление

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Permissions/Update, но только для приложений с одним клиентом.

## <a name="required-license-plan"></a>Требуемый план лицензирования

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Дальнейшие действия

- Создание настраиваемых ролей с помощью [портал Azure, Azure AD PowerShell и API Graph](roles-create-custom.md)
- [Просмотрите назначения для настраиваемой роли](roles-view-assignments.md).
