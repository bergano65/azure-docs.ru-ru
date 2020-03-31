---
title: Руководство по настройке Cornerstone OnDemand для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и декора счетов пользователей в Cornerstone OnDemand.
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058450"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Руководство по настройке Cornerstone OnDemand для автоматической подготовки пользователей

В этом уроке показаны шаги, которые необходимо выполнить в Корнерном OnDemand и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и деобеспечения пользователей или групп в Cornerstone OnDemand.

> [!NOTE]
> В этом уроке описывается разъем, построенный поверх службы предоставления пользовательского обеспечения Azure AD. Для получения информации о том, что эта служба делает, как она работает, и часто задаваемые вопросы, см [Автоматизировать пользовательские подготовки и деобеспечения программного обеспечения как службы (SaaS) приложений с Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Предварительные требования

Сценарий, изложенный в этом учебнике предполагает, что у вас есть:

* Клиент Azure AD.
* Арендатор Cornerstone OnDemand.
* Учетная запись пользователя в Cornerstone OnDemand с разрешениями админа.

> [!NOTE]
> Интеграция Azure AD основана на [веб-сервисе Cornerstone OnDemand.](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf) Эта услуга доступна командам Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Добавить cornerstone OnDemand с рынка Azure

Перед настройкой Cornerstone OnDemand для автоматического предоставления пользовательской подготовки с помощью Azure AD добавьте Cornerstone OnDemand из Marketplace в список управляемых приложений SaaS.

Чтобы добавить Cornerstone OnDemand из Marketplace, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com)в навигационном стеле слева выберите **Active Directory Azure.**

    ![Значок активного каталога Azure](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Cornerstone OnDemand** и выберите **Cornerstone OnDemand** из панели результатов. Чтобы добавить приложение, **выберите Добавить**.

    ![Cornerstone OnDemand в списке результатов](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Назначить пользователей Cornerstone OnDemand

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи или группы, назначенные приложению Azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, решите, каким пользователям или группам в Azure AD нужен доступ к Cornerstone OnDemand. Чтобы назначить этих пользователей или групп в Cornerstone OnDemand, следуйте инструкциям в [присваивать пользователю или группе корпоративное приложение.](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Важные рекомендации по назначению пользователей в Cornerstone OnDemand

* Мы рекомендуем назначить одного пользователя Azure AD в Cornerstone OnDemand для тестирования автоматической конфигурации подготовки пользователей. Вы можете назначить дополнительных пользователей или групп ы позже.

* При назначении пользователя ВCornerstone OnDemand выберите любую действительную роль, конкретную для приложения, если она имеется, в поле диалога назначения. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Настройка автоматического предоставления пользовательу для Cornerstone OnDemand

В этом разделе вы проведете шаги по настройке службы обеспечения AD Azure AD. Используйте его для создания, обновления и отработки пользователей или групп в Cornerstone OnDemand на основе пользовательских или групповых заданий в Azure AD.

Чтобы настроить автоматическую подготовку пользователя для Cornerstone OnDemand в Azure AD, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения** > **Все приложения** > **Cornerstone OnDemand.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Cornerstone OnDemand**.

    ![Ссылка Cornerstone OnDemand в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Подготовка Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Автоматически**.

    ![Режим подготовки Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **Admin Credentials** введите имя пользователя, пароль админа и домен учетной записи Cornerstone OnDemand:

    * В поле **Admin Username** заполните домен или имя пользователя учетной записи админ на вашем арендаторе Cornerstone OnDemand. Примером может быть contoso-admin.

    * В поле **Admin Password** заполните пароль, соответствующий имени пользователя.

    * В поле **домена** заполните URL-адрес веб-сервиса арендатора Cornerstone OnDemand. Например, услуга находится `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`на, и для Contoso домен `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Для получения дополнительной информации о том, как получить URL веб-службы, смотрите [этот PDF](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. После заполнения коробок, отображаемых в шаге 5, выберите **тестовое соединение,** чтобы убедиться, что Azure AD может подключиться к Cornerstone OnDemand. Если соединение не удается, убедитесь, что ваша учетная запись Cornerstone OnDemand имеет разрешения для админовов и повторите попытку.

    ![Угловой камень на спрос ею](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. В поле **электронной почты уведомления** введите адрес электронной почты человека или группы для получения уведомлений об ошибке. Выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой** флажка.

    ![Корнерстоун OnDemand Уведомление Email](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Нажмите кнопку **Сохранить**.

9. В разделе **сопоставления** выберите **Synchronize Azure Active Directory Users to Cornerstone OnDemand** (Синхронизировать пользователей Azure Active Directory с Cornerstone OnDemand).

    ![Синхронизация Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с Cornerstone OnDemand в разделе **Атрибут Карт.** Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления учетных записей пользователей в Cornerstone OnDemand для операций обновления. Чтобы сохранить все изменения, выберите **Сохранить**.

    ![Угловой камень OnDemand Атрибут Картпинги](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Для настройки фильтров для обсажания следуйте инструкциям в [учебнике по отслеживанию фильтров.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

12. Чтобы включить службу предоставления Azure AD для Cornerstone OnDemand в разделе **Настройки,** измените **статус обеспечения** на **On.**

    ![Краеугольный камень OnDemand Обеспечение Статус](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Определите пользователей или группы, которые вы хотите предоставить Cornerstone OnDemand. В разделе **Настройки** выберите значения, которые вы хотите в **области области.**

    ![Область углового на спросе](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Когда вы будете готовы к предоставлению, выберите **Сохранить**.

    ![Краеугольный камень на спрос сохранить](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Эта операция запускает начальную синхронизацию всех пользователей или групп, определенных в **разделе Область** в разделе **Настройки.** Начальная синхронизация занимает больше времени, чем более поздняя синхронизация. Они возникают примерно каждые 40 минут при запуске службы обеспечения AD Azure AD. 

Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности подготовки. В отчете описаны все действия, выполняемые службой предоставления Azure AD на Cornerstone OnDemand.

Сведения о журналах подготовки Azure AD см. в руководстве [по отчетам об автоматической подготовке учетных записей пользователей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

Атрибут **Position** в Cornerstone OnDemand ожидает значение, соответствующее ролям на портале Cornerstone OnDemand. Чтобы получить список допустимых значений **позиции,** перейдите на ссылку > **организации организации, которая** > позиции на портале Cornerstone OnDemand.

![Краеугольный камень OnDemand Подготовка Отсечение Записи пользователей](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Корнерстоун OnDemand Положение о предоставлении](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Список позиций Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
