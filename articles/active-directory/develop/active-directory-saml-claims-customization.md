---
title: Настройка утверждений, выпущенных в токене SAML для корпоративных приложений в Azure AD | Документация Майкрософт
description: Узнайте, как настраивать утверждения, выпущенные в токене SAML для корпоративных приложений в Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2018
ms.author: celested
ms.reviewer: luleon, jeedes
ms.custom: aaddev
ms.openlocfilehash: 4e80f5cb85a53281da9ec50a02d089f46e97dfde
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466722"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Настройка утверждений, выпущенных в токене SAML для корпоративных приложений

Сегодня Azure Active Directory (Azure AD) поддерживает единый вход в большинстве корпоративных приложений, включая предварительно интегрированные приложения в коллекции приложений Azure AD, а также пользовательских приложениях. Когда пользователь проходит аутентификацию для приложения в Azure AD с помощью протокола SAML 2.0, Azure AD отправляет токен в приложение (через запрос HTTP POST). Затем приложение проверяет и использует маркер для входа пользователя вместо запроса имени пользователя и пароля. Эти маркеры SAML содержат элементы информации о пользователе, которые называются "утверждениями".

*Утверждение* представляет собой информацию, предложенную поставщиком удостоверений, о пользователе в составе токена, выпущенного для этого пользователя. В [токене SAML](http://en.wikipedia.org/wiki/SAML_2.0)эти данные обычно содержит оператор атрибута SAML. А уникальный идентификатор пользователя, как правило, представлен в субъекте SAML, который также называют идентификатором имени.

По умолчанию Azure AD выпускает токен SAML для приложения, которое содержит утверждение NameIdentifier, с указанием имени пользователя (также называемого именем участника-пользователя) в Azure AD. Это значение обеспечивает уникальную идентификацию пользователя. Маркер SAML включает также дополнительные утверждения, содержащие адрес электронной почты, имя и фамилию пользователя.

Чтобы просмотреть или изменить утверждения, выданные приложению в токене SAML, откройте приложение на портале Azure. Затем установите флажок **Просмотреть и изменить все другие атрибуты пользователей** в разделе **Атрибуты пользователя** приложения.

![Раздел "Атрибуты пользователя"][1]

Изменение утверждений, выданных в маркере SAML, может потребоваться по двум основным причинам:
* Приложение требует другого набора URI утверждений или значений утверждений.
* Приложение развернуто таким образом, что утверждение NameIdentifier должно содержать данные, отличные от имени пользователя (также называемого именем участника-пользователя), которое хранится в Azure AD.

Вы можете изменять в утверждении любые значения, используемые по умолчанию. Выберите строку утверждения в таблице атрибутов токена SAML. При этом откроется раздел **Изменить атрибут**, и вы сможете изменить имя утверждения, значение и пространство имен, связанное с утверждением.

![Изменение атрибута пользователя][2]

С помощью контекстного меню, которое открывается при нажатии значка **...**, можно удалить утверждения (кроме утверждения NameIdentifier). А с помощью кнопки **Добавить атрибут** также можно добавить новые утверждения.

![Изменение атрибута пользователя][3]

## <a name="editing-the-nameidentifier-claim"></a>Редактирование утверждения NameIdentifier

Чтобы решить проблему, связанную с тем, что приложение было развернуто с указанием другого имени пользователя, выберите раскрывающийся список **Идентификатор пользователя** в разделе **Атрибуты пользователя**. Это действие выводит диалоговое окно с несколькими различными параметрами:

![Изменение атрибута пользователя][4]

### <a name="attributes"></a>Атрибуты

Выберите нужный источник для утверждения `NameIdentifier` (или NameID). Вы можете выбирать из следующих параметров.

| ИМЯ | ОПИСАНИЕ |
|------|-------------|
| Email | Адрес электронной почты пользователя |
| userprincipalName | Имя участника-пользователя (UPN) этого пользователя |
| onpremisessamaccount | Имя учетной записи SAM, синхронизированное из локального Azure AD |
| objectID | ObjectID пользователя в Azure AD |
| EmployeeID | EmployeeID пользователя |
| Расширения каталогов | Расширения каталогов, [синхронизированные из локальной Active Directory с помощью синхронизации Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Атрибуты расширения 1–15 | Локальные атрибуты расширения, используемые для расширения схемы Azure AD |

### <a name="transformations"></a>Преобразования

Также можно использовать специальные функции преобразования утверждений.

| Функция | ОПИСАНИЕ |
|----------|-------------|
| **ExtractMailPrefix()** | Удаляет суффикс домена из адреса электронной почты, имени учетной записи SAM или имени участника-пользователя. В таком случае будет извлекаться только первая часть передаваемого имени пользователя (например, joe_smith вместо joe_smith@contoso.com). |
| **join()** | Присоединяет атрибут к проверенному домену. Если выбранное значение идентификатора пользователя имеет домен, он извлечет имя пользователя для добавления выбранного проверенного домена. Например, если в качестве значения идентификатора пользователя вы выберите адрес электронной почты (joe_smith@contoso.com), а в качестве проверенного домена выберите contoso.onmicrosoft.com, то в результате получите joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Преобразует символы выбранного атрибута в символы нижнего регистра. |
| **ToUpper()** | Преобразует символы выбранного атрибута в символы верхнего регистра. |

## <a name="adding-claims"></a>Добавление утверждений

При добавлении утверждения можно указать имя атрибута (которое не обязательно соответствует шаблону URI согласно спецификации SAML). Задайте значение любого атрибута пользователя, который хранится в каталоге.

![Добавление атрибута пользователя][7]

Например, необходимо отправить имя подразделения организации, к которому принадлежит пользователь (возьмем отдел продаж), в виде утверждения. Введите имя утверждения, ожидаемое приложением, а затем выберите значение **user.department**.

> [!NOTE]
> Если для заданного пользователя нет сохраненного значения выбранного атрибута, это утверждение не будет выпущено в маркере.

> [!TIP]
> Значения **user.onpremisesecurityidentifier** и **user.onpremisesamaccountname** поддерживаются только при синхронизации данных пользователя из локального каталога Active Directory с помощью [инструмента Azure AD Connect](../hybrid/whatis-hybrid-identity.md).

## <a name="restricted-claims"></a>Утверждения с ограниченным доступом

В SAML существует ряд утверждений с ограниченным доступом. Если добавить эти утверждения, то Azure AD не будет их отправлять. Ниже приведен набор утверждений SAML с ограниченным доступом.

    | Тип утверждения (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>Дополнительная информация

* [Управление приложениями с помощью Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Настройка федеративного единого входа для приложения не из коллекции](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Устранение неполадок единого входа на основе SAML](howto-v1-debug-saml-sso-issues.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
