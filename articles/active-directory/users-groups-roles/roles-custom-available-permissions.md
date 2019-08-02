---
title: Разрешения роли настраиваемого администратора для управления регистрацией приложений — Azure Active Directory | Документация Майкрософт
description: Разрешения настраиваемой роли администратора для делегирования управления удостоверениями.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ff6755f1391ff19e65df669fb51967a904f4f
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707569"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Подтипы регистрации приложений и разрешения в Azure Active Directory

Эта статья содержит доступные в настоящее время разрешения регистрации приложений для пользовательских определений ролей в Azure Active Directory (Azure AD).

## <a name="single-tenant-v-multi-tenant-permissions"></a>Один клиент v. разрешения для нескольких клиентов

Пользовательские разрешения роли отличаются для приложений с одним клиентом и несколькими клиентами. Приложения с одним клиентом доступны только пользователям в Организации Azure AD, в которой зарегистрировано приложение. Приложения с несколькими клиентами доступны для всех организаций Azure AD. Приложения с одним клиентом определяются как имеющие **Поддерживаемые типы учетных** записей "учетные записи только в этом каталоге организации". В API Graph для приложений с одним клиентом свойство Сигнинаудиенце имеет значение "Азуреадмйорг".

## <a name="application-registration-subtypes-and-permissions"></a>Подтипы и разрешения регистрации приложений

В [обзоре пользовательских ролей](roles-custom-overview.md) приведены пояснения о том, что означают общие условия, разрешения и набор свойств. Следующие сведения относятся к регистрации приложений.

### <a name="subtypes"></a>Подтипы

Существует только один подтип приложения для регистрации — Applications. myOrganization. Например, Microsoft. Directory/Applications. myOrganization/Basic/Update. Этот подтип задается на странице **проверки** подлинности для конкретной регистрации приложения и соответствует присвоению свойству сигнинаудиенце значения "азуреадмйорг" с помощью API Graph или PowerShell. Подтипы ограничивают разрешения для регистрации приложений, которые помечены как доступные только для учетных записей в вашей организации (приложения с одним клиентом).

Ограниченное разрешение можно использовать для предоставления разрешений на чтение или управление только внутренним приложениям без предоставления разрешений на чтение или управление для приложений, доступных учетным записям в других организациях.

Существуют приложения. myOrganization версии всех разрешений на чтение и обновление, а также разрешение DELETE. В настоящее время не существует версии приложения. myOrganization. Стандартные разрешения (например, Microsoft. Directory/Applications/Basic/Update) предоставляют разрешения на чтение или управление для всех типов регистрации приложений.

![Объявление приложения с одним клиентом или приложения с несколькими клиентами](./media/roles-custom-available-permissions/supported-account-types.png)

Сведения о следующих разрешениях для предварительной версии пользовательских ролей перечислены в списке [Доступные разрешения пользовательской роли в Azure Active Directory](roles-custom-available-permissions.md).

### <a name="create-and-delete"></a>Создание и удаление

Для предоставления возможности создания регистраций приложений доступны два разрешения.

- **Microsoft. Directory/Applications/Креатеасовнер**
- **Microsoft. Directory/Applications/CREATE**

Если назначены оба разрешения, разрешение на создание будет иметь приоритет. Хотя разрешение Креатеасовнер не добавляет его в качестве первого владельца автоматически, владельцы могут быть указаны во время создания регистрации приложения при использовании API Graph или командлетов PowerShell.

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

Возможность чтения всех свойств приложений с одним клиентом и несколькими клиентами за пределами конфиденциальных свойств, таких как учетные данные.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. myOrganization/Аллпропертиес/Read

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Аллпропертиес/Read, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationsstandardread-grants-access-to-all-fields-on-the-application-registration-branding-page"></a>Microsoft. Directory/Applications/Standard/Read: Предоставление доступа ко всем полям на странице фирменной символики регистрации приложения

![Это разрешение предоставляет доступ к странице фирменной символики регистрации приложения](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. myOrganization/Standard/Read

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Standard/Read, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. Directory/Applications/Owners/Read

Предоставляет возможность чтения свойств владельцев в приложениях с одним клиентом и несколькими клиентами. Предоставляет доступ ко всем полям на странице владельцы регистрации приложения:

![Эти разрешения предоставляют доступ к странице владельцы регистрации приложений.](./media/roles-custom-available-permissions/app-registration-owners.png)

Предоставляет доступ к следующим свойствам сущности приложения:

- алловактасфораллклиентс
- алловпасссраугхусерс
- AppAddress
- аппбрандинжелементс
- аппкатегори
- аппкреатеддатетиме
- Папка
- AppId
- аппинформатионалурл
- аппликатионтаг
- AppLogoUrl
- аппметадата
- аппоптионс
- бинарекстенсионаттрибуте
- булеанекстенсионаттрибуте
- каунтриесблоккедформинорс
- CreatedOnBehalfOf
- датетимикстенсионаттрибуте
- DisplayName
- екстенсионаттрибутедефинитион
- интежерекстенсионаттрибуте
- KnownClientApplications
- ларжеинтежерекстенсионаттрибуте
- легалажеграупруле
- локализедаппбрандинжелементс
- маинлого
- мсааппид
- ресаурцеаппликатионсет
- сервицедисковерендпоинт
- стринжекстенсионаттрибуте
- трустедцертификатесубжект
- WebApi
- WebApp
- WwwHomepage

### <a name="update"></a>Обновление

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/Applications/Аллпропертиес/Update

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. myOrganization/Аллпропертиес/Update

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Аллпропертиес/Update, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>Microsoft. Directory, приложения, аудитория и обновление

Предоставляет доступ ко всем полям на странице проверки подлинности регистрации приложения:

![Это разрешение предоставляет доступ к странице проверки подлинности регистрации приложения](./media/roles-custom-available-permissions/supported-account-types.png)

Предоставляет доступ к следующим свойствам ресурса приложения:

- AvailableToOtherTenants
- сигнинаудиенце

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. Directory/Applications. myOrganization/аудитория/обновление

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/аудитории/Update, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>Microsoft. Directory/приложения, проверка подлинности или обновление

Возможность обновления URL-адреса ответа, URL-адреса выхода, неявного потока и свойств домена издателя в приложениях с одним клиентом и несколькими клиентами. Предоставляет доступ ко всем полям на странице проверки подлинности регистрации приложения, за исключением поддерживаемых типов учетных записей:

![Предоставление доступа к проверке подлинности регистрации приложений, но не поддерживаемых типов учетных записей](./media/roles-custom-available-permissions/supported-account-types.png)

 Предоставляет доступ к следующим свойствам ресурса приложения:

- AcceptMappedClaims
- акцесстокенакцептедверсион
- AddIns
- GroupMembershipClaims
- исдевицеонляуссуппортед
- OAuth2LegacyUrlPathMatching
- оаусоидкреспонсеполицибитмап
- OptionalClaims
- оргрестриктионс
- PublicClient
- усекустомтокенсигнингкэй

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. myOrganization, проверка подлинности или обновление

Предоставляет те же разрешения, что и Microsoft. каталог/приложения, проверка подлинности или обновление, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>Microsoft. Directory/Applications/Basic/Update

Возможность обновления свойств имени, логотипа, URL-адреса домашней страницы, URL-адреса условия службы и заявления о конфиденциальности в приложениях с одним клиентом и несколькими клиентами. Предоставляет доступ ко всем полям на странице фирменной символики регистрации приложения:

![Это разрешение предоставляет доступ к странице фирменной символики регистрации приложения](./media/roles-custom-available-permissions/app-registration-branding.png)

Предоставляет доступ к следующим свойствам ресурса приложения:

- алловактасфораллклиентс
- алловпасссраугхусерс
- AppAddress
- аппбрандинжелементс
- аппкатегори
- Папка
- AppId
- аппинформатионалурл
- аппликатионтаг
- AppLogoUrl
- аппметадата
- аппоптионс
- бинарекстенсионаттрибуте
- булеанекстенсионаттрибуте
- каунтриесблоккедформинорс
- CreatedOnBehalfOf
- датетимикстенсионаттрибуте
- DisplayName
- екстенсионаттрибутедефинитион
- интежерекстенсионаттрибуте
- KnownClientApplications
- ларжеинтежерекстенсионаттрибуте
- легалажеграупруле
- локализедаппбрандинжелементс
- маинлого
- мсааппид
- ресаурцеаппликатионсет
- сервицедисковерендпоинт
- стринжекстенсионаттрибуте
- трустедцертификатесубжект
- WebApi
- WebApp
- WwwHomepage

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. myOrganization/Basic/Update

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Basic/Update, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>Microsoft. Directory/приложения/учетные данные/обновление

Возможность обновления свойств сертификатов и секретов клиента в приложениях с одним клиентом и несколькими клиентами. Предоставляет доступ ко всем полям в сертификатах регистрации приложения & секретной странице:

![Это разрешение предоставляет доступ к сертификатам регистрации приложений & секретной странице](./media/roles-custom-available-permissions/app-registration-secrets.png)

Предоставляет доступ к следующим свойствам ресурса приложения:
- AsymmetricKey
- енкриптедсекреткэй
- кэйдескриптион
- шаредкэйреференце
- токененкриптионкэйид

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. myOrganization/учетные данные/обновление

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Credentials/Update, но только для приложений с одним каталогом.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>Microsoft. Directory, приложения, владельцы и обновление

Возможность обновления свойства Owner в одном клиенте и нескольких клиентах. Предоставляет доступ ко всем полям на странице владельцы регистрации приложения:

![Эти разрешения предоставляют доступ к странице владельцы регистрации приложений.](./media/roles-custom-available-permissions/app-registration-owners.png)

Предоставляет доступ к следующим свойствам ресурса приложения:
- Владельцы

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. myOrganization/Owners/Update

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Owners/Update, но только для приложений с одним клиентом.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>Microsoft. Directory/приложения/разрешения/обновление

Возможность обновления делегированных разрешений, разрешений приложения, разрешенных клиентских приложений, необходимых разрешений и предоставления свойств согласия в приложениях с одним клиентом и несколькими клиентами. Не предоставляет возможность выполнения согласия. Предоставляет доступ ко всем полям в разрешениях API регистрации приложения и предоставляет страницы API:

![Эти разрешения предоставляют доступ к странице разрешений API регистрации приложений.](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Эти разрешения предоставляют доступ к странице API для регистрации приложения.](./media/roles-custom-available-permissions/app-registration-expose-api.png)

Предоставляет доступ к следующим свойствам ресурса приложения:

- AppIdentifierUri
- Права
- преаусоризедаппликатионс
- RecordConsentConditions
- RequiredResourceAccess

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. Directory/Applications. myOrganization/разрешения/обновление

Предоставляет те же разрешения, что и Microsoft. Directory/Applications/Permissions/Update, но только для приложений с одним клиентом.

## <a name="required-license-plan"></a>Требуемый план лицензирования

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Следующие шаги

- Создание настраиваемых ролей с помощью [портал Azure, Azure AD PowerShell и API Graph](roles-create-custom.md)
- [Просмотр назначений для пользовательской роли](roles-view-assignments.md)
