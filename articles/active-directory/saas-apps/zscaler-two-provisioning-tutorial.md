---
title: Учебник. Настройка Zscaler Two для автоматической подготовки пользователей с Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Zscaler два два.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0a250fcd-6ca1-47c2-a780-7a6278186a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: d7b0828dc4cb37afa9dda647c4407b4039ca4f73
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273572"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Учебник. Настройка Zscaler Two для автоматической подготовки пользователей

Цель данного учебника — продемонстрировать действия, выполняемые в Zscaler Two и Azure Active Directory (Azure AD) для настройки Azure AD настроить автоматическую подготовку и отмену подготовки пользователей и групп в Zscaler Two.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Сейчас этот соединитель доступен в общедоступной предварительной версии. Дополнительные сведения о общие Microsoft Azure условия использования предварительных версий функций, см. в разделе [дополнительным условиям использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Необходимые компоненты

Сценарий, описанный в этом руководстве, предполагает, что у вас уже имеется:

* клиент Azure AD;
* Клиент Zscaler Two
* Учетную запись пользователя в Zscaler Two с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от двух API SCIM Zscaler, которая доступна разработчикам Zscaler Two для учетных записей с пакет предприятия.

## <a name="adding-zscaler-two-from-the-gallery"></a>Добавление Zscaler Two из коллекции

Перед настройкой Zscaler Two для автоматической подготовки пользователей в Azure AD, необходимо добавить Zscaler Two из коллекции приложений Azure AD, в список управляемых приложений SaaS.

**Чтобы добавить Zscaler Two из коллекции приложений Azure AD, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Zscaler Two**, выберите **Zscaler Two** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Zscaler Two в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-two"></a>Назначение пользователей в Zscaler Two

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, следует решить, какие пользователи или группы в Azure AD требуется доступ к Zscaler Two. Сделав это, можно назначить этих пользователей и групп в Zscaler Two, следуя инструкциям ниже:

* [Назначение пользователя или группы корпоративному приложению](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Важные рекомендации по назначению пользователей в Zscaler Two

* Рекомендуется одного пользователя Azure AD назначается Zscaler Two для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Zscaler Two, необходимо выбрать действительную роль конкретного приложения (если доступно) в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-zscaler-two"></a>Настройка автоматической подготовки пользователей в Zscaler Two

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD для создания, изменения и отключения пользователей или групп в Zscaler Two, основанный на пользователя "или" назначения групп в Azure AD.

> [!TIP]
> Вы также можете включить на основе SAML единого входа для Zscaler Two, следуйте инструкциям, указанным на [Zscaler Two входа в руководстве по настройке единого](zscaler-two-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-two-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Zscaler Two в Azure AD.

1. Войдите в [портала Azure](https://portal.azure.com) и выберите **корпоративные приложения**выберите **все приложения**, а затем выберите **Zscaler Two**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Zscaler Two**.

    ![Ссылка на Zscaler Two в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. В разделе **учетные данные администратора** введите **URL-адрес клиента** и **секретный токен** учетной Zscaler Two, как описано на шаге 6.

6. Для получения **URL-адрес клиента** и **секретный токен**, перейдите к **Администрирование > Параметры проверки подлинности** в Zscaler Two пользовательского интерфейса портала и щелкнуть  **SAML** под **тип проверки подлинности**.

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Щелкните **настроить SAML** открыть **конфигурации SAML** параметры.

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Выберите **подготовки Enable SCIM-Based** извлекаемого **базовый URL-адрес** и **маркера носителя**, затем сохраните параметры. Копировать **базовый URL-адрес** для **URL-адрес клиента** и **маркера носителя** для **секретный токен** на портале Azure.

7. После заполнения полей, указанных в шаге 5, щелкните **проверить подключение** и убедиться, что Azure AD может подключиться к Zscaler Two. Если подключение отсутствует, убедитесь, что учетная Zscaler Two запись имеет разрешения администратора и повторите попытку.

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Нажмите кнопку **Сохранить**.

10. В разделе **сопоставления** выберите **синхронизировать пользователей Azure Active Directory с Zscaler Two**.

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Zscaler Two в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления учетных записей пользователей в Zscaler Two для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. В разделе **сопоставления** выберите **синхронизировать группы Azure Active Directory в Zscaler Two**.

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Просмотрите атрибуты группы, которые синхронизированы из Azure AD в Zscaler Two в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления групп в Zscaler Two для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](./../active-directory-saas-scoping-filters.md).

15. Чтобы включить службу Zscaler Two для подготовки Azure AD, измените **состояние подготовки** для **на** в **параметры** раздел.

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Определите пользователей или группы, которые вы хотите подготовить для Zscaler Two, выбрав нужные значения в **область** в **параметры** раздел.

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Zscaler Two Подготовка](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов, который описывает все действия, выполняемые с Zscaler Two службой подготовки Azure AD о подготовке.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте, как просматривать журналы и получать отчеты о действиях по подготовке](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
