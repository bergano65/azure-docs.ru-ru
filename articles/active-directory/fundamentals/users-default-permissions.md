---
title: Разрешения пользователя по умолчанию в Azure Active Directory | Документация Майкрософт
description: Дополнительные сведения о различных разрешениях пользователя в Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: lizross
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e501860691cccc0578a0df4eec2b161b99b4c
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341368"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Разрешения пользователя по умолчанию в Azure Active Directory
В Azure Active Directory (Azure AD) набор разрешений по умолчанию предоставляется всем пользователям. Доступ пользователей зависит от типа пользователя, [назначений ролей](active-directory-users-assign-role-azure-portal.md) и владения отдельными объектами. В этой статье описываются эти разрешения по умолчанию и содержится сравнение значений по умолчанию для участников и гостевых пользователей. Разрешения пользователя по умолчанию можно изменить только в параметрах пользователя в Azure AD.

## <a name="member-and-guest-users"></a>Участники и гостевые пользователи
Набор полученных разрешений по умолчанию зависит от того, является ли пользователь собственным членом клиента (пользователь-участник) или пользователь перенесен из другого каталога в качестве гостя (пользователь-гость) в рамках совместной работы B2B. Дополнительные сведения о добавлении гостевых пользователей см. в статье [Что представляет собой гостевой доступ в службе совместной работы Azure Active Directory B2B?](../b2b/what-is-b2b.md).
* Пользователи-участники могут регистрировать приложения, управлять фотографией собственного профиля и мобильным номером, изменять пароль и приглашать гостей B2B. Кроме того, пользователи могут считывать все данные каталога (с некоторыми исключениями). 
* Гостевые пользователи имеют ограниченные разрешения каталога. Например, гостевые пользователи не могут просматривать информацию о клиенте, выходящую за рамки сведений профиля. Однако гостевой пользователь может получить сведения о другом пользователе, указав имя участника-пользователя или objectId. Гостевой пользователь может читать свойства групп, к которым он принадлежит, включая членство в группе, независимо от параметра **Разрешения для гостей ограничены**. Гость не может просматривать сведения о любых других объектах клиента.

Стандартные разрешения для гостевых систем ограничены по умолчанию. Гостей можно добавить в роли администраторов, которые предоставят им полные разрешения на чтение и запись, содержащиеся в роли. Есть одно дополнительное ограничение — возможность для гостей приглашать других гостей. Установка значения **Нет** для параметра **Гости могут приглашать других пользователей** позволяет предотвратить приглашение других гостей. Дополнительные сведения см. в статье [Делегирование приглашений для службы совместной работы Azure Active Directory B2B](../b2b/delegate-invitations.md). Чтобы предоставить гостевым пользователям те же разрешения, что и участникам-пользователям, задайте для параметра **Разрешения для гостей ограничены** значение **Нет**. Этот параметр по умолчанию предоставляет все разрешения участника гостевым пользователям, а также позволяет добавлять гостей к административным ролям.

## <a name="compare-member-and-guest-default-permissions"></a>Сравнение стандартных разрешений участника и гостя

**Область** | **Разрешения участника** | **Разрешения гостевого пользователя**
------------ | --------- | ----------
Пользователи и контакты | Чтение всех открытых свойств пользователей и контактов<br>Приглашение гостей<br>Изменение пароля<br>Управление собственным номером мобильного телефона<br>Управление собственными фотографиями<br>Аннуляция собственных токенов обновления | Чтение собственных свойств<br>Чтение отображаемого имени, по электронной почте, войдите в имя, фото, имя участника-пользователя и свойств типа пользователя для других пользователей и контактов<br>Изменение пароля
Группы | Создание групп безопасности<br>Создание групп Office 365<br>Чтение всех свойств групп<br>Чтение не скрытого членства в группах<br>Чтение скрытых членств в группах Office 365 для объединенной группы<br>Управление свойствами, правами владения и членством в группах, которые принадлежат пользователю<br>Добавление гостей в собственные группы<br>Управление параметрами динамического членства<br>Удаление собственных групп<br>Восстановление собственных групп Office 365 | Чтение всех свойств групп<br>Чтение не скрытого членства в группах<br>Чтение скрытых членств в группах Office 365 для объединенных групп<br>Управление собственными группами<br>Добавление гостей в имеющиеся группы, если это допустимо<br>Удаление собственных групп<br>Восстановление собственных групп Office 365<br>Чтение свойств групп, к которым они принадлежат, включая членство.
Приложения | Регистрация (создание) приложения<br>Чтение свойств зарегистрированных и корпоративных приложений.<br>Управление свойствами приложения, назначениями и учетными данными собственных приложений<br>Создание или удаление пароля приложения для пользователя<br>Удаление собственных приложений<br>Восстановление собственных приложений | Чтение свойств зарегистрированных и корпоративных приложений<br>Управление свойствами приложения, назначениями и учетными данными собственных приложений<br>Удаление собственных приложений<br>Восстановление собственных приложений
Устройства | Чтение всех свойств устройств<br>Управление всеми свойствами собственных устройств<br> | Нет разрешений<br>Удаление собственных устройств<br>
Каталог | Чтение всей информации о компании<br>Чтение всех доменов<br>Чтение всех контрактов партнера | Чтение отображаемого имени и проверенных доменов
Роли и области | Чтение всех административных ролей и членств<br>Чтение всех свойств и членств административных единиц | Нет разрешений 
Подписки | Чтение всех подписок<br>Включение участника плана обслуживания | Нет разрешений
Политики | Чтение всех свойств политик<br>Управление всеми свойствами собственной политики | Нет разрешений

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Ограничение разрешений по умолчанию для пользователей-участников

Разрешения по умолчанию для пользователей-участников можно ограничить одним из следующих способов.

Разрешение | Описание параметра
---------- | ------------
Пользователи могут регистрировать приложения | Установка этого параметра значение "Нет" запрещает пользователям создавать регистрации приложений. Возможности могут предоставляться к конкретным лицам, добавив их с ролью разработчика приложения.
Разрешить пользователям подключаться рабочей или учебной учетной записи с LinkedIn | Установка этого параметра значение "Нет" запрещает пользователям подключаться своей рабочей или учебной учетной записью со своей учетной записью LinkedIn. Дополнительные сведения см. в разделе [совместный доступ к данным подключения и разрешения учетной записи LinkedIn](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Возможность создания групп безопасности | Если установить для этого параметра значение "Нет", пользователи не смогут создавать группы безопасности. Глобальные администраторы и Администраторы пользователей по-прежнему могут создавать группы безопасности. Дополнительные сведения см. в статье [Настройка параметров групп с помощью командлетов Azure Active Directory](../users-groups-roles/groups-settings-cmdlets.md).
Возможность создавать группы Office 365 | Если установить для этого параметра значение "Нет", пользователи не смогут создавать группы Office 365. Если установить для этого параметра значение "Некоторые", можно выбрать набор пользователей для создания групп Office 365. Глобальные администраторы и Администраторы пользователей по-прежнему будут иметь возможность создавать группы Office 365. Дополнительные сведения см. в статье [Настройка параметров групп с помощью командлетов Azure Active Directory](../users-groups-roles/groups-settings-cmdlets.md).
Ограничить доступ к порталу администрирования Azure AD | Установка этого параметра значение Да запрещает пользователям доступ к Azure Active Directory через портал Azure.
Возможность чтения других пользователей | Этот параметр доступен только в PowerShell. Присвоить этому флагу значение $false запрещает без прав администратора чтения сведений о пользователе из каталога. Этот флаг не запрещает чтение информации о пользователях в другие службы Майкрософт, таким как Exchange Online. Этот параметр предназначен для особых случаев, и присвоить этому флагу значение $false не рекомендуется.

## <a name="object-ownership"></a>Владелец объекта

### <a name="application-registration-owner-permissions"></a>Разрешения владельца при регистрации приложения
Когда пользователь регистрирует приложение, он автоматически добавляется в качестве владельца приложения. Владелец может управлять метаданными приложения, такими как имя, и разрешениями приложения. Он также может управлять конфигурацией конкретного клиента приложения, например конфигурацией единого входа и назначениями пользователей. Владелец также может добавлять или удалять других владельцев. В отличие от глобальных администраторов, владельцы могут управлять только приложениями, которыми они владеют.

### <a name="enterprise-application-owner-permissions"></a>Разрешения владельца приложения Enterprise
Когда пользователь добавляет новый корпоративное приложение, они автоматически добавляется в качестве владельца. Как владелец они могут управлять конфигурацией конкретного клиента приложения, например SSO configuration, подготовки и назначения пользователей. Владелец также может добавлять или удалять других владельцев. В отличие от глобальных администраторов владельцы могут управлять только приложения, которыми они владеют.

### <a name="group-owner-permissions"></a>Разрешения владельца группы
Когда пользователь создает группу, он автоматически добавляется в качестве владельца этой группы. Владелец может управлять свойствами группы, например именем, а также членством группы. Владелец также может добавлять или удалять других владельцев. В отличие от глобальных администраторов и администраторов пользователей владельцы могут управлять только группами, которыми они владеют. Сведения о назначении владельца группы см. в [этой статье](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Разрешения владельца
В следующих таблицах описываются конкретные разрешения в Azure Active Directory пользователей-участников имели собственные объекты. Пользователь должен только эти разрешения на объекты, которыми они владеют.

#### <a name="owned-application-registrations"></a>Регистрации собственных приложений
Пользователи могут выполнять следующие действия с регистрации собственных приложений.

| **Действия** | **Описание** |
| --- | --- |
| microsoft.directory/applications/audience/update | Изменение свойства applications.audience в Azure Active Directory. |
| Microsoft.Directory/Applications/Authentication/Update | Изменение свойства applications.authentication в Azure Active Directory. |
| microsoft.directory/applications/basic/update | Изменение базовых свойств приложений в Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Изменение свойства applications.credentials в Azure Active Directory. |
| microsoft.directory/applications/delete | Удаление приложений в Azure Active Directory. |
| Microsoft.Directory/Applications/Owners/Update | Изменение свойства applications.owners в Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Изменение свойства applications.permissions в Azure Active Directory. |
| microsoft.directory/applications/policies/update | Изменение свойства applications.policies в Azure Active Directory. |
| microsoft.directory/applications/restore | Восстановление приложений в Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Принадлежащий корпоративных приложений
Пользователи могут выполнять следующие действия на собственных корпоративных приложений. Корпоративное приложение состоит из субъекта-службы, одной или нескольких политик приложения и иногда объект приложения, в том же клиенте субъекта-службы.

| **Действия** | **Описание** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Чтение всех свойств (включая привилегированные) в auditLogs в Azure Active Directory. |
| microsoft.directory/policies/basic/update | Обновление базовых свойств политик в Azure Active Directory. |
| microsoft.directory/policies/delete | Удаление политик в Azure Active Directory. |
| Microsoft.Directory/policies/Owners/Update | Изменение свойства policies.owners в Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Изменение свойства servicePrincipals.appRoleAssignedTo в Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Изменение свойства users.appRoleAssignments в Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Обновление свойства servicePrincipals.owners в Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Обновление свойства servicePrincipals.authentication в Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Обновление базовых свойств для объектов ServicePrincipal в Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Обновление свойства servicePrincipals.credentials в Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Удаление объектов servicePrincipal в Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Изменение свойства servicePrincipals.owners в Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Обновление свойства servicePrincipals.permissions в Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Изменение свойства servicePrincipals.policies в Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Чтение всех свойств (включая привилегированные) в signInReports в Azure Active Directory. |

#### <a name="owned-devices"></a>Принадлежащие компании устройства
Пользователи могут выполнять следующие действия с собственных устройств.

| **Действия** | **Описание** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Чтение свойства devices.bitLockerRecoveryKeys в Azure Active Directory. |
| Microsoft.Directory/Devices/Disable | Отключение устройств в Azure Active Directory. |

#### <a name="owned-groups"></a>Собственных групп
Пользователи могут выполнять следующие действия на собственных групп.

| **Действия** | **Описание** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Изменение свойства groups.appRoleAssignments в Azure Active Directory. |
| microsoft.directory/groups/basic/update | Обновление базовых свойств в группах в Azure Active Directory. |
| microsoft.directory/groups/delete | Удаление групп в Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Изменение свойства groups.dynamicMembershipRule в Azure Active Directory. |
| Microsoft.Directory/Groups/Members/Update | Изменение свойства groups.members в Azure Active Directory. |
| Microsoft.Directory/Groups/Owners/Update | Изменение свойства groups.owners в Azure Active Directory. |
| Microsoft.Directory/Groups/RESTORE | Восстановление групп в Azure Active Directory. |
| microsoft.directory/groups/settings/update | Изменение свойства groups.settings в Azure Active Directory. |

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о назначении административных ролей в Azure AD см. в статье [Назначение пользователю ролей администратора в Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Дополнительные сведения о том, как осуществляется доступ к ресурсам в Microsoft Azure, см. в статье [Основные сведения о доступе к ресурсам в Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).
* Дополнительные сведения о связи Azure Active Directory с подпиской Azure см. в статье [Связь между подписками Azure и Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Управление пользователями](add-users-azure-active-directory.md)
