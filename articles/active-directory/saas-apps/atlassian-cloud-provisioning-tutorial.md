---
title: 'Учебник: Налаживайте Atlassian Cloud для автоматического подготовки пользователей с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей в Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059374"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Учебник: Налаживание Атласского Облака для автоматического подготовки пользователей

Цель этого руководства – продемонстрировать шаги, которые будут выполняться в Active Directory Atlassian Cloud и Azure (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователям и/или группам в Atlassian Cloud.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Арендатор Атласского Облака](https://www.atlassian.com/licensing/cloud)
* Учетная запись пользователя в Облаке Atlassian с разрешениями Admin.

> [!NOTE]
> Интеграция Azure AD основана на **API Atlassian Cloud SCIM,** который доступен для групп Atlassian Cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Добавление Atlassian Cloud из коллекции

Прежде чем настроить Atlassian Cloud для автоматического предоставления пользовательского интерфейса с помощью Azure AD, необходимо добавить Atlassian Cloud из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Atlassian Cloud из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Atlassian Cloud,** выберите **Atlassian Cloud** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![Atlassian Cloud в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Назначение пользователей Atlassian Cloud

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к Atlassian Cloud. После решения вы можете назначить этих пользователей и/или групп в Atlassian Cloud, следуя инструкциям здесь:

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Важные советы по назначению пользователей в Atlassian Cloud

* Для тестирования автоматической конфигурации подготовки пользователей рекомендуется назначить одного пользователя Azure AD Виссианское облако. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя Atlassian Cloud необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге о назначении. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Настройка автоматической подготовки пользователя к Atlassian Cloud 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в Облаке Atlassian на основе пользовательских и/или групповых заданий в Azure AD.

> [!TIP]
> Вы также можете включить ОДИНочный знак на основе SAML для Atlassian Cloud, следуя инструкциям, представленным в [учебнике Atlassian Cloud с одним набором.](atlassian-cloud-tutorial.md) Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Для настройки автоматической подготовки пользователей для Atlassian Cloud в Azure AD:

1. Вопийте на [портале Azure](https://portal.azure.com) и выберите **корпоративные приложения,** выберите **все приложения,** затем выберите **Atlassian Cloud.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Atlassian Cloud**.

    ![Ссылка на Atlassian Cloud в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Перейдите к [менеджеру Atlassian Organization](https://admin.atlassian.com) **> выберите каталог org >.**

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Нажмите **На кнопку «Обеспечение пользователей»** и нажмите на **«Создайте каталог».** Копируйте **URL-адрес базы каталогов** и **маркер ынапредъяви** в **полях Url-адресов арендатора** и **секретных токенов** соответственно.

    ![Атласское облако,](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![резервирующее Атласское](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![облачное обеспечение Атласского Облака](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. В разделе **Admin Credentials** ввейди **URL-адрес арендатора** и **секретный маркер** учетной записи Atlassian Cloud. Примеры этих значений:

   * В поле **URL-адреса tenant** заполните конкретную конечную точку клиента, полученную от Atlassian, как описано в шаге 6. Например: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * В поле **Secret Token** наполните секретный маркер, как описано в Шаге 6.

8. После заполнения полей, показанных в шаге 7, щелкните **тестовое соединение,** чтобы обеспечить подключение Azure AD к Atlassian Cloud. Если соединение не удается, убедитесь, что ваша учетная запись Atlassian Cloud имеет разрешения Admin и повторите попытку.

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Нажмите **Сохранить**.

11. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в Atlassian Cloud.**

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с Atlassian Cloud в разделе **Атрибуты.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Atlassian Cloud для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. В разделе **Картпинги** выберите **синхронизацию активных групп каталогов Azure в Atlassian Cloud.**

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Просмотрите атрибуты группы, синхронизированные с Azure AD с Atlassian Cloud в разделе **Атрибуты Mapping.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для соответствия группам в Облаке Atlassian для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

16. Чтобы включить службу предоставления AD Azure AD для Atlassian Cloud, измените **статус подготовки** в разделе **«В** **настройках».**

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Определите пользователей и/или группы, которые вы хотели бы предоставить Atlassian Cloud, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка атласового облака](./media/atlassian-cloud-provisioning-tutorial/save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD в Атласском облаке.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Atlassian Cloud позволяет предоставлять пользователям только [проверенные домены.](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)
* Atlassian Cloud не поддерживает переимены группы сегодня. Это означает, что любые изменения в displayName группы в Azure AD не будут обновляться и отражаться в облаке Atlassian.
* Значение атрибута **почтового** пользователя в Azure AD заполняется только в том случае, если у пользователя есть почтовый ящик обмена Майкрософт. Если у пользователя его нет, рекомендуется сопоставить другой желаемый атрибут **атрибута электронной почты** в Atlassian Cloud.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png