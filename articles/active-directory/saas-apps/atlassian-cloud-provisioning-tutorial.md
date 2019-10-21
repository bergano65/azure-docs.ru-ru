---
title: Руководство. Настройка Atlassian Cloud для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в Atlassian Cloud.
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
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0c3173841de25a30b84870332c7334a81773e84d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "68561582"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Руководство. Настройка Atlassian Cloud для автоматической подготовки пользователей

Цель этого руководства — продемонстрировать шаги, которые необходимо выполнить в Atlassian Cloud и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и (или) групп в Atlassian Cloud.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).


## <a name="prerequisites"></a>Технические условия

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент Atlassian Cloud](https://www.atlassian.com/licensing/cloud)
* Учетная запись пользователя в Atlassian Cloud с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от **Atlassian Cloud scim API**, который доступен для облачных команд Atlassian.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Добавление Atlassian Cloud из коллекции

Перед настройкой Atlassian Cloud для автоматической подготовки пользователей с помощью Azure AD необходимо добавить Atlassian Cloud из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Atlassian Cloud из коллекции приложений Azure AD, выполните следующие действия.**

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Atlassian Cloud**, выберите **Atlassian Cloud** на панели результатов и нажмите кнопку **добавить** , чтобы добавить это приложение.

    ![Atlassian Cloud в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Назначение пользователей в Atlassian Cloud

Azure Active Directory использует концепцию, называемую *назначениями* , чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, каким пользователям и (или) группам в Azure AD требуется доступ к Atlassian Cloud. После принятия решения вы можете назначить этих пользователей и (или) группы для Atlassian Cloud, следуя приведенным ниже инструкциям.

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Важные советы по назначению пользователей в Atlassian Cloud

* Рекомендуется назначить одного пользователя Azure AD в Atlassian Cloud для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Atlassian Cloud необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Настройка автоматической подготовки пользователей в Atlassian Cloud 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в Atlassian Cloud на основе назначений пользователей и групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для Atlassian Cloud, следуя инструкциям, приведенным в [руководстве по единому входу Atlassian Cloud](atlassian-cloud-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Atlassian Cloud в Azure AD, сделайте следующее:

1. Войдите в [портал Azure](https://portal.azure.com) и выберите **корпоративные приложения**, выберите **все приложения**, а затем выберите **Atlassian Cloud**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Atlassian Cloud**.

    ![Ссылка на Atlassian Cloud в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. В разделе **учетные данные администратора** введите **URL-адрес клиента** и **маркер секрета** учетной записи облака Atlassian. Примеры этих значений:

   * В поле **URL-адрес клиента** Введите конкретную конечную точку клиента, полученную от Atlassian, как описано в шаге 6. Например: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * В поле **маркер секрета** введите маркер секрета, как описано в шаге 6.

6. Перейдите в [Atlassian Organization Manager](https://admin.atlassian.com) **> подготовки пользователей** и щелкните **создать маркер**. Скопируйте **базовый URL-адрес каталога** и **токен носителя** в поля **URL-адрес клиента** и **секретный токен** соответственно.

    ![Atlassian подготовки облака ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian подготовки облака ](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. После заполнения полей, показанных на шаге 5, нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к Atlassian Cloud. В случае сбоя подключения убедитесь, что у учетной записи Atlassian Cloud есть разрешения администратора, и повторите попытку.

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. В поле **Уведомление по электронной почте** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. В нижней части страницы нажмите кнопку **Save**.

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Atlassian Cloud**.

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Проверьте пользовательские атрибуты, которые синхронизированы из Azure AD в Atlassian Cloud, в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Atlassian Cloud для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. В разделе **сопоставления** выберите **синхронизировать Azure Active Directoryные группы с Atlassian Cloud**.

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Проверьте атрибуты группы, которые синхронизированы из Azure AD в Atlassian Cloud в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в Atlassian Cloud для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Чтобы включить службу подготовки Azure AD для Atlassian Cloud, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Определите пользователей и (или) группы, которые вы хотите подготавливать в Atlassian Cloud, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка облака Atlassian](./media/atlassian-cloud-provisioning-tutorial/save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов по подготовке, в которых описаны все действия, выполняемые службой подготовки Azure AD в Atlassian Cloud.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Atlassian Cloud позволяет подготавливать пользователей только из [проверенных доменов](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud не поддерживает групповые переименования прямо сейчас. Это означает, что любые изменения displayName группы в Azure AD не будут обновлены и отражены в Atlassian Cloud.
* Значение атрибута **почтового** пользователя в Azure AD заполняется только в том случае, если у пользователя есть почтовый ящик Microsoft Exchange. Если у пользователя нет такой учетной записи, рекомендуется сопоставлять другой требуемый **атрибут с атрибутом** emails в Atlassian Cloud.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
