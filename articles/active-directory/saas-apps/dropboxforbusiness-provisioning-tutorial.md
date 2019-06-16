---
title: Руководство по Настройка Dropbox for Business для автоматической подготовки пользователей с Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Dropbox для бизнеса.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: zchia
ms.openlocfilehash: c95346ff9026b7fc8c9141234fb3cde9c0809d23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053503"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Руководство по Настройка автоматической подготовки пользователей в Dropbox for Business

Цель данного учебника — продемонстрировать действия, выполняемые в Dropbox для бизнеса и Azure Active Directory (Azure AD) для настройки Azure AD для автоматической подготовки и отзыва пользователей и групп в Dropbox для бизнеса.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Технические условия

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент Dropbox for Business](https://www.dropbox.com/business/pricing)
* Учетная запись пользователя в Dropbox for Business с разрешениями администратора.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Добавить Dropbox for Business из коллекции

Прежде чем настроить Dropbox для бизнеса для автоматической подготовки пользователей в Azure AD, необходимо добавить Dropbox for Business из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Dropbox for Business из коллекции приложений Azure AD, выполните следующие действия:**

1. В  **[портала Azure](https://portal.azure.com)** , на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите **новое приложение** кнопку в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Dropbox for Business**выберите **Dropbox for Business** в панели результатов и нажмите кнопку **добавить** кнопку, чтобы добавить это приложение.

    ![Dropbox for Business в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Назначение пользователей в Dropbox for Business

Azure Active Directory используется концепция, называемая *назначения* для определения, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только те пользователи и группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, нужно решить, какие пользователи или группы в Azure AD требуется доступ к Dropbox для бизнеса. Сделав это, можно назначить этих пользователей и групп в Dropbox for Business, следуя инструкциям ниже:

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Важные рекомендации по назначению пользователей для приложения Dropbox for Business

* Рекомендуется одного пользователя Azure AD, назначенные Dropbox for Business для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Dropbox for Business, необходимо выбрать действительную роль конкретного приложения (если доступно) в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Настройка автоматической подготовки пользователей в Dropbox для бизнеса 

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD для создания, изменения и отключения пользователей и групп в Dropbox для бизнеса на основе назначений в Azure AD.

> [!TIP]
> Также можно включить на основе SAML единого входа для Dropbox for Business, следуйте инструкциям, указанным на [Dropbox for Business входа в руководстве по настройке единого](dropboxforbusiness-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Dropbox для бизнеса в Azure AD.

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Dropbox for Business**.

    ![Ссылка на Dropbox for Business в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Инициализация](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Инициализация](common/provisioning-automatic.png)

5. В разделе **Учетные данные администратора** щелкните **Авторизовать**. Откроется диалоговое окно входа в Dropbox for Business в новом окне браузера.

    ![Подготовка ](common/provisioning-oauth.png)

6. На **вход в Dropbox для бизнеса для связи с Azure AD** диалоговое окно, войдите в клиент Dropbox для бизнеса и подтвердить свою личность.

    ![Dropbox для бизнеса входа в систему](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. После завершения работы шаги 5 и 6, щелкните **проверить подключение** и убедиться, что Azure AD может подключиться к Dropbox для бизнеса. Если подключение отсутствует, убедитесь, что Dropbox for Business учетная запись имеет разрешения администратора и повторите попытку.

    ![по маркеру](common/provisioning-testconnection-oauth.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Уведомление по электронной почте](common/provisioning-notification-email.png)

9. Выберите команду **Сохранить**.

10. В разделе **сопоставления** выберите **синхронизировать пользователей Azure Active Directory с Dropbox**.

    ![Сопоставления пользователей Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Dropbox в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления учетных записей пользователей в Dropbox для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. В разделе **сопоставления** выберите **синхронизировать группы Azure Active Directory с Dropbox**.

    ![Сопоставление групп Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Просмотрите атрибуты группы, которые синхронизированы из Azure AD в Dropbox в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления групп в Dropbox для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты группы Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Чтобы включить подготовки службы для Dropbox в Azure AD, измените **состояние подготовки** для **на** в **параметры** раздел.

    ![Состояние подготовки на противоположное](common/provisioning-toggle-on.png)

16. Определите пользователей или группы, которые вы хотите для подготовки в Dropbox, выбрав нужные значения в **область** в **параметры** раздел.

    ![Инициализация области](common/provisioning-scope.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Идет сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для подготовки отчетов действия, в которых зафиксированы все действия, выполняемые службой Dropbox подготовки Azure AD.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя
 
* Dropbox не поддерживает Приостановка приглашенных пользователей. Если Приглашенный пользователь приостановлена, этого пользователя будут удалены.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

