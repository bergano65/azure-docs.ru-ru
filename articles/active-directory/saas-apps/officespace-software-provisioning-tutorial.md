---
title: 'Учебник: Налаживание программного обеспечения OfficeSpace для автоматического предоставления пользовательского обеспечения с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей OfficeSpace Software.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f832a0a6-ad0a-453f-a747-9cd717e11181
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: 3d472b300400cf230773ba01f3f4362988c34e81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063431"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Учебник: Настройка программного обеспечения OfficeSpace для автоматического подготовки пользователей

Цель этого руководства состоит в том, чтобы продемонстрировать шаги, которые должны быть выполнены в OfficeSpace Software и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователям и/или группам OfficeSpace Software.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Арендатор программного обеспечения OfficeSpace](https://www.officespacesoftware.com/)
* Учетная запись пользователя в программном обеспечении OfficeSpace с разрешениями Admin.

## <a name="assigning-users-to-officespace-software"></a>Назначение пользователей в программное обеспечение OfficeSpace

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к программному обеспечению OfficeSpace. После решения, вы можете назначить этих пользователей и / или групп OfficeSpace программного обеспечения, следуя инструкциям здесь:
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Важные советы по назначению пользователей в программное обеспечение OfficeSpace

* Для тестирования автоматической конфигурации подготовки пользователей рекомендуется назначить одного пользователя Azure AD. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в OfficeSpace Software необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге о назначении. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="set-up-officespace-software-for-provisioning"></a>Настройка программного обеспечения OfficeSpace для подготовки

1. Войти на [консоль OfficeSpace Software Admin Console.](https://support.officespacesoftware.com/hc) Перейдите к **настройкам > соединителям.**

    ![Консоль для управления программным обеспечением](media/officespace-software-provisioning-tutorial/settings.png)

2.  Перейдите к **синхронизации каталога > SCIM.**

    ![Программное обеспечение OfficeSpace Добавить SCIM](media/officespace-software-provisioning-tutorial/scim.png)

3.  Копируйте **токен проверки подлинности SCIM**. Это значение будет введено в поле Secret Token в вкладке «Обеспечение программного обеспечения OfficeSpace» на портале Azure.

    ![Программное обеспечение Officespace Создает токен](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>Добавить программное обеспечение OfficeSpace из галереи

Прежде чем настроить программное обеспечение OfficeSpace для автоматической подготовки пользователей с помощью Azure AD, необходимо добавить программное обеспечение OfficeSpace из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить программное обеспечение OfficeSpace из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **OfficeSpace Software**, выберите **OfficeSpace Software** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![OfficeSpace Software в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Настройка автоматической подготовки пользователя к программному обеспечению OfficeSpace 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в программном обеспечении OfficeSpace на основе пользовательских и/или групповых заданий в Azure AD.

> [!TIP]
> Вы также можете включить SAML на основе одного знака для OfficeSpace программного обеспечения, следуя инструкциям, представленным в [OfficeSpace программного обеспечения Единый набор.](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-tutorial) Единый набор может быть настроен независимо от автоматического подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Для настройки автоматической подготовки пользователей для программного обеспечения OfficeSpace в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **OfficeSpace Software**.

    ![Ссылка на OfficeSpace Software в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** `https://<subdomain>.officespacesoftware.com/api/scim/v2/` формат ввода URL в **URL-адресе Tenant.** Например, `https://contoso.officespacesoftware.com/api/scim/v2/`. Ввейте значение **токенов подлинности SCIM,** извлеченное ранее в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к программному обеспечению OfficeSpace. Если подключение не удается, убедитесь, что ваша учетная запись OfficeSpace Software имеет разрешения для админов и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **"Уведомление email"** введите адрес электронной почты человека или группы, которые должны получать уведомления об ошибке и выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой.**

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure с программным обеспечением OfficeSpace.**

    ![Картирование пользователей программного обеспечения OfficeSpace](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с программным обеспечением OfficeSpace в разделе **«Отображение атрибутов».** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Программном обеспечении OfficeSpace для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты программного обеспечения OfficeSpace](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Чтобы включить службу предоставления Azure AD программного обеспечения OfficeSpace, измените **статус подготовки** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

13. Определите пользователей и/или группы, которые вы хотели бы предоставить OfficeSpace Software, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

14. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD в программном обеспечении OfficeSpace.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

