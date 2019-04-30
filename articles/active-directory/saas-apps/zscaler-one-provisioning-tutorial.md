---
title: Руководство по Настройка Zscaler One для автоматической подготовки пользователей с Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory настроить автоматическую подготовку и отзыв учетных записей пользователей в Zscaler One.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 5319b0ac06c4ddf1a7627a4e7fe0bfb2694f79f6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114616"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Руководство по Настройка Zscaler One для автоматической подготовки пользователей

Этот учебник демонстрирует шаги в Zscaler One и Azure Active Directory (Azure AD) для настройки Azure AD настроить автоматическую подготовку и отзыв пользователей и групп в Zscaler One.

> [!NOTE]
> В данном учебнике соединитель, который является надстройкой служба подготовки пользователей Azure AD. Сведения о назначении эта служба, как это работает и часто задаваемые вопросы см. в разделе [Автоматическая подготовка пользователей и ее отзыв для приложений программного обеспечения как услуга (SaaS) с Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Этот соединитель доступен в настоящее время доступна в предварительной версии. Дополнительные сведения о общие Microsoft Azure условия использования предварительных версий функций, см. в разделе [дополнительные условия использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Технические условия

Сценарий, описанный в этом руководстве предполагается, что у вас есть:

* Клиент Azure AD.
* Клиент Zscaler One.
* Учетная запись пользователя в Zscaler One с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от Zscaler One SCIM API. Этот API доступен разработчикам Zscaler One для учетных записей с пакет предприятия.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Добавление Zscaler One из Azure Marketplace

Перед настройкой Zscaler One для автоматической подготовки пользователей в Azure AD, добавьте Zscaler One из Azure Marketplace в список управляемых приложений SaaS.

Чтобы добавить Zscaler One из Marketplace, выполните следующие действия.

1. В [портала Azure](https://portal.azure.com), в области навигации слева выберите **Azure Active Directory**.

    ![Значок Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Zscaler One** и выберите **Zscaler One** на панели результатов. Чтобы добавить это приложение, выберите **добавить**.

    ![Zscaler One в списке результатов](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Назначить пользователей Zscaler One

Azure Active Directory используется концепция, называемая *назначения* для определения, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи или группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, решите, каким пользователям или группам в Azure AD требуется доступ к Zscaler One. Чтобы назначить этим пользователям или группам в Zscaler One, следуйте инструкциям в [назначение пользователя или группы корпоративному приложению](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Важные рекомендации по назначению пользователей в Zscaler One

* Мы рекомендуем назначить одного пользователя Azure AD в Zscaler One для тестирования конфигурации автоматической подготовки пользователей. Позже можно назначить дополнительных пользователей или групп.

* При назначении пользователя в Zscaler One, выберите действительную роль для конкретного приложения, если он доступен, в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Настройка автоматической подготовки пользователей в Zscaler One

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD. Используйте его для создания, обновления и отключения пользователей или групп в Zscaler One на основе пользователя или группы назначений в Azure AD.

> [!TIP]
> Вы также можете включить на основе SAML единого входа для Zscaler One. Следуйте инструкциям в [Zscaler One входа в руководстве по настройке единого](zscaler-One-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, несмотря на то, что эти две возможности дополняют друг друга.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Zscaler One в Azure AD

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения** > **все приложения** > **Zscaler One**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Zscaler One**.

    ![Ссылка на Zscaler One в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Zscaler One Подготовка](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Режим подготовки один Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. В разделе **учетные данные администратора** заполните **URL-адрес клиента** и **секретный токен** поля с параметрами вашей Zscaler One для учетной записи, как описано на шаге 6.

6. Для получения клиента, URL-адрес и маркер секрета, перейдите к **администрирования** > **параметры проверки подлинности** в Zscaler One пользовательском Интерфейсе портала. В разделе **тип проверки подлинности**выберите **SAML**.

    ![Параметры проверки подлинности в Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Выберите **настроить SAML** открыть **настроить SAML** параметры.

    ![Zscaler One Настройка SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    2. Выберите **подготовки Enable SCIM-Based** получить параметры **базовый URL-адрес** и **маркера носителя**. Затем сохраните параметры. Копировать **базовый URL-адрес** присвоить **URL-адрес клиента** на портале Azure. Копировать **маркера носителя** присвоить **секретный токен** на портале Azure.

7. После ввода в поля, указанного в шаге 5, выберите **проверить подключение** и убедитесь, что Azure AD может подключиться к Zscaler One. Если подключение отсутствует, убедитесь, что у учетной записи Zscaler One, есть разрешения администратора и повторите попытку.

    ![Соединение один тест Zscaler](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. В **уведомление по электронной почте** поле, введите адрес электронной почты пользователя или группу, чтобы получать уведомления об ошибках подготовки. Выберите **отправить уведомление по электронной почте при сбое** "флажок".

    ![Zscaler One уведомление по электронной почте](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Щелкните **Сохранить**.

10. В разделе **сопоставления** выберите **синхронизировать пользователей Azure Active Directory с Zscaler One**.

    ![Zscaler One синхронизации пользователей](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Zscaler One в **сопоставления атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления учетных записей пользователей в Zscaler One для операций обновления. Чтобы сохранить изменения, выберите **Сохранить**.

    ![Zscaler One сопоставления атрибутов пользователя](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. В разделе **сопоставления** выберите **синхронизировать группы Azure Active Directory в Zscaler One**.

    ![Zscaler One группы синхронизации, выполните следующие действия.](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Просмотрите атрибуты группы, которые синхронизированы из Azure AD в Zscaler One в **сопоставления атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления групп в Zscaler One для операций обновления. Чтобы сохранить изменения, выберите **Сохранить**.

    ![Zscaler One сопоставления атрибутов группы](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Чтобы настроить фильтры области, следуйте инструкциям в [области руководстве по фильтрам](./../active-directory-saas-scoping-filters.md).

15. Для включения подготовки службы для Zscaler One в Azure AD **параметры** измените **состояние подготовки** для **на**.

    ![Состояние подготовки один Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Определите пользователей или группы, необходимые для подготовки в Zscaler One. В **параметры** выберите значения в **область**.

    ![Область Zscaler One](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Когда вы будете готовы для подготовки, выберите **Сохранить**.

    ![Сохранить Zscaler](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

После этого начнется начальная синхронизация всех пользователей или группы, определенные в **область** в **параметры** раздел. Начальная синхронизация занимает больше времени, чем более поздней версии синхронизации. Они происходят примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для подготовки отчета о действиях. Отчет зафиксированы все действия, выполняемые с Zscaler One службой подготовки Azure AD.

Сведения о журналах подготовки Azure AD см. в руководстве [по отчетам об автоматической подготовке учетных записей пользователей](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Managing user account provisioning for enterprise apps in the Azure portal](../manage-apps/configure-automatic-user-provisioning-portal.md) (Управление подготовкой учетных записей пользователей для корпоративных приложений на портале Azure)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
