---
title: Руководство по Настройка Cornerstone OnDemand для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory настроить автоматическую подготовку и отзыв учетных записей пользователей в Cornerstone OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85ddcf3aff7d15c946230cedb0da190bca6aeab7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721942"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Руководство по Настройка Cornerstone OnDemand для автоматической подготовки пользователей

Этот учебник демонстрирует шаги в Cornerstone OnDemand и Azure Active Directory (Azure AD) для настройки Azure AD настроить автоматическую подготовку и отзыв пользователей или групп в Cornerstone OnDemand.

> [!NOTE]
> В данном учебнике соединитель, который является надстройкой служба подготовки пользователей Azure AD. Сведения о назначении эта служба, как это работает и часто задаваемые вопросы см. в разделе [Автоматическая подготовка пользователей и ее отзыв для приложений программного обеспечения как услуга (SaaS) с Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Технические условия

Сценарий, описанный в этом руководстве предполагается, что у вас есть:

* Клиент Azure AD.
* Клиент Cornerstone OnDemand.
* Учетная запись пользователя в Cornerstone OnDemand с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от [веб-службы Cornerstone OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Эта служба доступна командам Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Добавить Cornerstone OnDemand из Azure Marketplace

Перед настройкой Cornerstone OnDemand для автоматической подготовки пользователей в Azure AD добавьте Cornerstone OnDemand из Marketplace в список управляемых приложений SaaS.

Чтобы добавить Cornerstone OnDemand из Marketplace, выполните следующие действия.

1. В [портала Azure](https://portal.azure.com), в области навигации слева выберите **Azure Active Directory**.

    ![Значок Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Cornerstone OnDemand** и выберите **Cornerstone OnDemand** на панели результатов. Чтобы добавить это приложение, выберите **добавить**.

    ![Cornerstone OnDemand в списке результатов](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Назначить пользователей Cornerstone OnDemand

Azure Active Directory используется концепция, называемая *назначения* для определения, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи или группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, решите, каким пользователям или группам в Azure AD требуется доступ к Cornerstone OnDemand. Чтобы назначить этих пользователей или групп в Cornerstone OnDemand, следуйте инструкциям в [назначение пользователя или группы корпоративному приложению](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Важные рекомендации по назначению пользователей в Cornerstone OnDemand

* Мы рекомендуем назначить одного пользователя Azure AD в Cornerstone OnDemand для тестирования конфигурации автоматической подготовки пользователей. Позже можно назначить дополнительных пользователей или групп.

* При назначении пользователя в Cornerstone OnDemand, выберите действительную роль для конкретного приложения, если он доступен, в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Настройка автоматической подготовки пользователей в Cornerstone OnDemand

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD. Используйте его для создания, обновления и отключения пользователей или групп в Cornerstone OnDemand на основе пользователя или группы назначений в Azure AD.

Чтобы настроить автоматическую подготовку пользователей Cornerstone OnDemand в Azure AD, выполните следующие действия.

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения** > **все приложения** > **Cornerstone OnDemand**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Cornerstone OnDemand**.

    ![Ссылка на Cornerstone OnDemand в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Автоматически**.

    ![Режим подготовки к работе cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **учетные данные администратора** введите имя пользователя администратора, пароль администратора и домен учетной записи Cornerstone OnDemand:

    * В **имя пользователя администратора** окне введите домен или имя пользователя учетной записи администратора в клиенте Cornerstone OnDemand. Например, contoso\admin.

    * В **пароль администратора** окне введите пароль, соответствующий имени пользователя администратора.

    * В **домена** окне введите URL-адрес службы web клиента Cornerstone OnDemand. Например, служба находится в `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, и для Contoso используется домен `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Дополнительные сведения о том, как получить URL веб-службы см. в разделе [этот PDF-файл](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. После ввода в поля, указанного в шаге 5, выберите **проверить подключение** чтобы убедиться в том, что Azure AD может подключиться к Cornerstone OnDemand. Если подключение отсутствует, убедитесь, что учетной записи Cornerstone OnDemand есть разрешения администратора и повторите попытку.

    ![Cornerstone OnDemand проверить подключение](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. В **уведомление по электронной почте** поле, введите адрес электронной почты пользователя или группу, чтобы получать уведомления об ошибках подготовки. Выберите **отправить уведомление по электронной почте при сбое** "флажок".

    ![Уведомление по электронной почте cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Щелкните **Сохранить**.

9. В разделе **сопоставления** выберите **Synchronize Azure Active Directory Users to Cornerstone OnDemand** (Синхронизировать пользователей Azure Active Directory с Cornerstone OnDemand).

    ![Cornerstone OnDemand синхронизации](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Cornerstone OnDemand в **сопоставления атрибутов** раздел. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления учетных записей пользователей в Cornerstone OnDemand для операций обновления. Чтобы сохранить изменения, выберите **Сохранить**.

    ![Сопоставления cornerstone OnDemand атрибутов](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Чтобы настроить фильтры области, следуйте инструкциям в [области руководстве по фильтрам](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Для включения подготовки службы для Cornerstone OnDemand, в Azure AD **параметры** измените **состояние подготовки** для **на**.

    ![Состояние подготовки cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Определите пользователей или группы, необходимые для подготовки в Cornerstone OnDemand. В **параметры** выберите значения в **область**.

    ![Область cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Когда вы будете готовы для подготовки, выберите **Сохранить**.

    ![Сохранить cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

После этого начнется начальная синхронизация всех пользователей или группы, определенные в **область** в **параметры** раздел. Начальная синхронизация занимает больше времени, чем более поздней версии синхронизации. Они происходят примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для подготовки отчета о действиях. Отчет описаны все действия, выполняемые с приложением Cornerstone OnDemand службой подготовки Azure AD.

Сведения о журналах подготовки Azure AD см. в руководстве [по отчетам об автоматической подготовке учетных записей пользователей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

Атрибут **Position** в Cornerstone OnDemand ожидает значение, соответствующее ролям на портале Cornerstone OnDemand. Чтобы получить список допустимых **позиции** значения, перейдите к **изменить запись пользователя > структура организации > позиции** на портале Cornerstone OnDemand.

![Подготовка cornerstone OnDemand изменить запись пользователя](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Позиция Подготовка cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Список-позиции cornerstone OnDemand Подготовка](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Managing user account provisioning for enterprise apps in the Azure portal](../manage-apps/configure-automatic-user-provisioning-portal.md) (Управление подготовкой учетных записей пользователей для корпоративных приложений на портале Azure)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
