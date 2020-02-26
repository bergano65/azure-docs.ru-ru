---
title: Руководство по настройке Cornerstone OnDemand для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отмены предоставления учетных записей пользователей в ходе работы по запросу.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058450"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Руководство по настройке Cornerstone OnDemand для автоматической подготовки пользователей

В этом руководстве описываются шаги, которые необходимо выполнить в ходе выполнения OnDemand и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической подготовки и отмены наполнения пользователей или групп для выполнения OnDemand.

> [!NOTE]
> В этом руководстве описывается соединитель, созданный на основе службы подготовки пользователей Azure AD. Сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. [в статье Автоматизация подготовки пользователей и ее отработки в приложения SaaS (программное обеспечение как услуга) с Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>предварительные требования

В сценарии, описанном в этом учебнике, предполагается, что у вас есть:

* Клиент Azure AD.
* Клиент «по запросу OnDemand».
* Учетная запись пользователя в ходе по запросу OnDemand с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от [веб-службы](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)"по запросу". Эта служба доступна для групповых команд OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Добавление в Azure Marketplace своей собственной службы OnDemand

Перед настройкой программы OnDemand для автоматической подготовки пользователей в Azure AD добавьте в список управляемых приложений SaaS программное обеспечение OnDemand из Marketplace.

Чтобы добавить в Marketplace процедуру OnDemand, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)в области навигации слева выберите **Azure Active Directory**.

    ![Значок Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите значение « **по запросу OnDemand** » и выберите в панели «результат» пункт « **по запросу** ». Чтобы добавить приложение, нажмите кнопку **Добавить**.

    ![Cornerstone OnDemand в списке результатов](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Назначение пользователей в ходе OnDemand

Azure Active Directory использует концепцию, называемую *назначениями* , чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только те пользователи или группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей решите, каким пользователям или группам в Azure AD нужен доступ к этой системе. Чтобы назначить этих пользователей или группы в ходе выполнения OnDemand, следуйте инструкциям в статье [Назначение пользователя или группы корпоративному приложению](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Важные рекомендации по назначению пользователей в Cornerstone OnDemand

* Рекомендуется назначить одного пользователя Azure AD в ходе OnDemand, чтобы протестировать конфигурацию автоматической подготовки пользователей. Позднее можно назначить дополнительных пользователей или группы.

* При назначении пользователя в ходе OnDemand выберите любую допустимую роль приложения, если она доступна, в диалоговом окне Назначение. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Настройка автоматической подготовки пользователей в ходе OnDemand

В этом разделе описывается процедура настройки службы подготовки Azure AD. Используйте его для создания, обновления и отключения пользователей или групп в ходе работы по запросу в соответствии с назначениями пользователей или групп в Azure AD.

Чтобы настроить автоматическую подготовку пользователей в ходе выполнения OnDemand в Azure AD, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения** > **все приложения > программной** **необходимости**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Cornerstone OnDemand**.

    ![Ссылка для программного перехода на OnDemand в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Автоматически**.

    ![Режим подготовки к запуску по требованию](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **учетные данные администратора** введите имя пользователя администратора, пароль администратора и домен учетной записи для своей задачи OnDemand:

    * В поле **имя администратора** введите домен или имя пользователя учетной записи администратора в клиенте по запросу OnDemand. Пример: контосо\админ.

    * В поле **пароль администратора** введите пароль, соответствующий имени администратора.

    * В поле **Domain (домен** ) введите URL-адрес Web Service клиента по запросу OnDemand. Например, служба находится по адресу `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, а для Contoso домен — `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Дополнительные сведения о том, как получить URL веб-службы, см. в [этом PDF-документе](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. После заполнения полей, показанных на шаге 5, выберите **проверить подключение** , чтобы убедиться, что Azure AD может подключаться к испытательной мере по запросу. В случае сбоя подключения убедитесь, что используемая учетная запись OnDemand имеет разрешения администратора, и повторите попытку.

    ![Тестовое подключение по запросу](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. В поле e-mail **Notification (уведомление** ) введите адрес электронной почты пользователя или группы для получения уведомлений об ошибках подготовки. Установите флажок **отправлять уведомление по электронной почте при возникновении сбоя** .

    ![Сообщение электронной почты об уведомлении OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Щелкните **Сохранить**.

9. В разделе **сопоставления** выберите **Synchronize Azure Active Directory Users to Cornerstone OnDemand** (Синхронизировать пользователей Azure Active Directory с Cornerstone OnDemand).

    ![Синхронизация по запросу](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Изучите пользовательские атрибуты, которые синхронизированы из Azure AD, с помощью по запросу в разделе **сопоставления атрибутов** . Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления учетных записей пользователей в Cornerstone OnDemand для операций обновления. Чтобы сохранить изменения, нажмите кнопку **сохранить**.

    ![Сопоставленные с атрибутами OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Чтобы настроить фильтры области, следуйте инструкциям в [руководстве по фильтрации областей](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Чтобы включить службу подготовки Azure AD для использования в ходе выполнения OnDemand, в разделе **Параметры** измените значение параметра **состояние подготовки** на **включено**.

    ![Состояние подготовки к запуску по требованию](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Определите пользователей или группы, которые вы хотите подготавливать к работе по запросу. В разделе **Параметры** выберите нужные значения в **области**.

    ![Область действия по запросу](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Когда вы будете готовы к подготовке к работе, нажмите кнопку **сохранить**.

    ![Сохранение по запросу](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Эта операция запускает начальную синхронизацию всех пользователей или групп, определенных в **области** , в разделе **параметров** . Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Они происходят примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам в отчет о действиях по подготовке. В этом отчете описываются все действия, выполняемые службой подготовки Azure AD в ходе выполнения OnDemand.

Сведения о журналах подготовки Azure AD см. в руководстве [по отчетам об автоматической подготовке учетных записей пользователей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

Атрибут **Position** в Cornerstone OnDemand ожидает значение, соответствующее ролям на портале Cornerstone OnDemand. Чтобы получить список допустимых значений **позиций** , перейдите в раздел **изменение записи пользователя > Структура организации > расположение** на портале программы OnDemand.

![Учетная запись изменения пользовательской подготовки по запросу](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Нацеленная на подготовку к запуску запрос](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Список позиций подготовки к запуску по запросу](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Managing user account provisioning for enterprise apps in the Azure portal](../app-provisioning/configure-automatic-user-provisioning-portal.md) (Управление подготовкой учетных записей пользователей для корпоративных приложений на портале Azure)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
