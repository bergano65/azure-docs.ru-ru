---
title: Руководство. Настройка бета-версии Zscaler для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в бета-версии Zscaler.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 001d139e7fc140f41fa94ea0c6f32d0b08036a9a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519783"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Руководство. Настройка бета-версии Zscaler для автоматической подготовки пользователей

Цель этого руководства — продемонстрировать шаги, которые необходимо выполнить в Zscaler Beta и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и/или групп в Zscaler Beta.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>


## <a name="prerequisites"></a>Обязательные условия

Сценарий, описанный в этом руководстве, предполагает, что у вас уже имеется:

* клиент Azure AD;
* Клиент Zscaler Beta
* Учетная запись пользователя в Zscaler Beta с разрешениями администратора

> [!NOTE]
> Интеграция подготовки Azure AD зависит от API-интерфейса Zscaler Beta SCIM, который доступен для Zscaler Beta-разработчиков для учетных записей с корпоративным пакетом.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Добавление Zscaler Beta из коллекции

Перед настройкой Zscaler Beta для автоматической подготовки пользователей с помощью Azure AD необходимо добавить Zscaler Beta из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Zscaler Beta из коллекции приложений Azure AD, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Zscaler Beta**, выберите **Zscaler Beta** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Zscaler Beta в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Назначение пользователей для Zscaler Beta

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, каким пользователям и (или) группам в Azure AD требуется доступ к Zscaler Beta. После принятия решения вы можете назначить этих пользователей и (или) группы для Zscaler бета-версии, следуя приведенным ниже инструкциям.

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Важные советы по назначению пользователей в Zscaler Beta

* Рекомендуется назначить одного пользователя Azure AD в Zscaler Beta для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Zscaler Beta необходимо выбрать допустимую роль конкретного приложения (если она доступна) в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Настройка автоматической подготовки пользователей в Zscaler Beta

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в Zscaler Beta на основе назначений пользователей и групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для Zscaler Beta, следуя инструкциям, приведенным в [руководстве по использованию Zscaler бета-версии единого входа](zscaler-beta-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Zscaler Beta в Azure AD, сделайте следующее:

1. Войдите в [портал Azure](https://portal.azure.com) и выберите **корпоративные приложения**, выберите **все приложения**, а затем выберите **Zscaler Beta**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Zscaler Beta**.

    ![Ссылка на Zscaler Beta в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. В разделе **учетные данные администратора** введите **URL-адрес клиента** и **маркер секрета** вашей учетной записи Zscaler Beta, как описано в шаге 6.

6. Чтобы получить **URL-адрес** и **маркер секрета**клиента, перейдите в раздел **Администрирование > параметры проверки подлинности** в пользовательском интерфейсе портала Zscaler Beta и щелкните **SAML** в разделе **тип проверки подлинности**.

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Щелкните **настроить SAML** , чтобы открыть параметры **SAML конфигурации** .

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Выберите **включить подготовку SCIM-Based** , чтобы получить **базовый URL-адрес** и **токен носителя**, а затем сохраните параметры. Скопируйте **базовый URL-адрес** в **URL-адрес клиента**и **токен носителя**  в **секретный токен** в портал Azure.

7. После заполнения полей, показанных на шаге 5, нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к бета-версии Zscaler. Если подключение не выполняется, убедитесь, что у учетной записи Zscaler Beta есть разрешения администратора, и повторите попытку.

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Нажмите кнопку **Сохранить**.

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Zscaler Beta**.

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Ознакомьтесь с атрибутами пользователей, которые синхронизированы из Azure AD, с Zscaler Beta в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Zscaler Beta для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы для Zscaler Beta**.

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. В разделе **сопоставление атрибутов** Проверьте атрибуты группы, которые синхронизированы из Azure AD в Zscaler Beta. Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в Zscaler Beta для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Чтобы включить службу подготовки Azure AD для Zscaler Beta, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Определите пользователей и (или) группы, которые вы хотите подготавливать для Zscaler Beta, выбрав нужные значения в поле **область** в разделе **Параметры** .

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка бета-версии Zscaler](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов по подготовке, в которых описаны все действия, выполняемые службой подготовки Azure AD в Zscaler Beta.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png