---
title: Руководство. Настройка программного обеспечения Officespace Software для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в программном обеспечении Officespace Software.
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
ms.openlocfilehash: 74135ccfe3bf8eb9625a8970a27b11c2a0e7b992
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134909"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Учебник. Настройка программного обеспечения Officespace Software для автоматической подготовки пользователей

Цель этого руководства — продемонстрировать шаги, которые необходимо выполнить в Officespace Software Software и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и (или) групп в Officespace Software Software.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>предварительным требованиям

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент Officespace Software Software](https://www.officespacesoftware.com/)
* Учетная запись пользователя в Officespace Software Software с разрешениями администратора.

## <a name="assigning-users-to-officespace-software"></a>Назначение пользователей для Officespace Software Software

Azure Active Directory использует концепцию, называемую *назначениями* , чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, какие пользователи и (или) группы в Azure AD должны иметь доступ к программному обеспечению Officespace Software. После принятия решения вы можете назначить этих пользователей и (или) группы для Officespace Software программного обеспечения, следуя приведенным ниже инструкциям.
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Важные советы по назначению пользователей в Officespace Software Software

* Рекомендуется назначить одного пользователя Azure AD в Officespace Software Software для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя Officespace Software Software необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="set-up-officespace-software-for-provisioning"></a>Настройка программного обеспечения Officespace Software для подготовки

1. Войдите в [консоль администратора программного обеспечения officespace Software](https://support.officespacesoftware.com/hc). Перейдите в раздел **параметры > соединители**.

    ![Консоль администрирования программного обеспечения Officespace Software](media/officespace-software-provisioning-tutorial/settings.png)

2.  Перейдите в **Каталог синхронизация > scim**.

    ![Officespace Software Software Add SCIM](media/officespace-software-provisioning-tutorial/scim.png)

3.  Скопируйте **маркер проверки подлинности scim**. Это значение будет указано в поле Секретный токен на вкладке Подготовка приложения Officespace Software Software на портал Azure.

    ![Токен создания программного обеспечения Officespace Software](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>Добавление программного обеспечения Officespace Software из коллекции

Перед настройкой программного обеспечения Officespace Software для автоматической подготовки пользователей с помощью Azure AD необходимо добавить Officespace Software Software из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить программное обеспечение Officespace Software из коллекции приложений Azure AD, выполните следующие действия.**

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Officespace Software Software**, выберите **officespace Software Software** на панели результатов и нажмите кнопку **добавить** , чтобы добавить это приложение.

    ![OfficeSpace Software в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Настройка автоматической подготовки пользователей в Officespace Software Software 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в Officespace Software Software на основе назначений пользователей и групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для Officespace Software Software, следуя инструкциям в [руководстве по единому входу в Officespace Software Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-tutorial). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей для программного обеспечения Officespace Software в Azure AD, сделайте следующее:

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **OfficeSpace Software**.

    ![Ссылка на OfficeSpace Software в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите `https://<subdomain>.officespacesoftware.com/api/scim/v2/` формате URL-адреса в поле **URL-адрес клиента**. Пример: `https://contoso.officespacesoftware.com/api/scim/v2/`. Введите значение **маркера проверки подлинности scim** , полученное ранее в **маркере секрета**. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключаться к программному обеспечению officespace Software. В случае сбоя подключения убедитесь, что учетная запись программного обеспечения Officespace Software имеет разрешения администратора, и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **уведомление по электронной почте** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, и установите флажок **Отправить уведомление по электронной почте при возникновении сбоя** .

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Выберите команду **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с officespace Software Software**.

    ![Сопоставления пользователей с программным обеспечением Officespace Software](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в Officespace Software Software в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в officespace Software Software для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя Officespace Software Software](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Чтобы включить службу подготовки Azure AD для Officespace Software Software, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

13. Определите пользователей и (или) группы, которые вы хотите подготавливать для Officespace Software Software, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

14. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов по подготовке, в которых описаны все действия, выполняемые службой подготовки Azure AD в officespace Software Software.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

