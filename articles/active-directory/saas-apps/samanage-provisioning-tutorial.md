---
title: Руководство по настройке Samanage для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и депонировать учетные записи пользователей в Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060508"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Руководство по настройке Samanage для автоматической подготовки пользователей

В этом уроке показаны шаги для выполнения в Samanage и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и деформатива пользователей и групп Samanage.

> [!NOTE]
> В этом уроке описывается разъем, построенный поверх службы предоставления пользовательского обеспечения Azure AD. Для получения информации о том, что эта служба делает, как она работает, и часто задаваемые вопросы, см [Автоматизировать пользовательские подготовки и деобеспечения программного обеспечения как службы (SaaS) приложений с Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Предварительные требования

Сценарий, изложенный в этом учебнике предполагает, что у вас есть:

* Клиент Azure AD.
* Арендатор [Samanage](https://www.samanage.com/pricing/) с пакетом Professional.
* Учетная запись пользователя в Samanage с разрешениями админа.

> [!NOTE]
> Интеграция Azure AD основана на [API Samanage Rest.](https://www.samanage.com/api/) Этот API доступен разработчикам Samanage для учетных записей с пакетом Professional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Добавить Samanage из рынка Azure

Перед настройкой Samanage для автоматического предоставления пользовательского запроса с помощью Azure AD добавьте Samanage из Azure Marketplace в список управляемых приложений SaaS.

Чтобы добавить Samanage из Marketplace, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com)в навигационном стеле слева выберите **Active Directory Azure.**

    ![Значок активного каталога Azure](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Samanage** и выберите **Samanage** из панели результатов. Чтобы добавить приложение, **выберите Добавить**.

    ![Samanage в списке результатов](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Назначить пользователей Samanage

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи или группы, назначенные приложению Azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, решите, каким пользователям или группам в Azure AD нужен доступ к Samanage. Чтобы назначить этих пользователей или групп в Samanage, следуйте инструкциям в [назначить пользователя или группу корпоративному приложению.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Важные советы по назначению пользователей в Samanage

*    Сегодня роли Samanage автоматически и динамично заполняются на uI портала Azure. Прежде чем назначить роли Samanage пользователям, убедитесь, что начальная синхронизация завершена против Samanage для получения последних ролей в вашем арендаторе Samanage.

*    Мы рекомендуем назначить одному пользователю Azure AD Samanage для тестирования начальной автоматической конфигурации подготовки пользователей. Вы можете назначить дополнительных пользователей и группы позже после успешного проведения тестов.

*    При назначении пользователя Ва-Управлению выберите любую действительную роль, конкретную для приложения, если она имеется, в поле диалога назначения. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Настройка автоматического обеспечения пользователя для Samanage

В этом разделе вы проведете шаги по настройке службы обеспечения AD Azure AD. Используйте его для создания, обновления и отработки пользователей или групп в Samanage на основе пользовательских или групповых заданий в Azure AD.

> [!TIP]
> Вы также можете включить SAML на основе одного знака для Samanage. Следуйте инструкциям в [Samanage одного знака на учебник](samanage-tutorial.md). Единый набор может быть настроен независимо от автоматического подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Настройка автоматического подготовки пользователей для Samanage в Azure AD

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения** > **Все приложения** > **Samanage.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Samanage**.

    ![Ссылка Samanage в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** ввейди URL-адрес Samanage **Tenant** и **секретный токен.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к Samanage. Если установить подключение не удалось, убедитесь, что у учетной записи Samanage есть разрешения администратора, и повторите попытку.

    ![Подключение к тесту Samanage](./media/samanage-provisioning-tutorial/provisioning.png)

6. В поле **электронной почты уведомления** введите адрес электронной почты человека или группы для получения уведомлений об ошибке. Выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой** флажка.

    ![Samanage Уведомление Email](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure для Samanage.**

    ![Синхронизация пользователей Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

9. В разделе **Сопоставления атрибутов** просмотрите пользовательские атрибуты, синхронизированные из Azure AD в Samanage. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Samanage для операций обновления. Чтобы сохранить все изменения, выберите **Сохранить**.

    ![Samanage, соответствующие атрибуты пользователя](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Чтобы включить сопоставление групп, в разделе **Сопоставления** выберите **Synchronize Azure Active Directory Groups to Samanage** (Синхронизировать группы Azure Active Directory с Samanage).

    ![Синхронизация группы Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. В столбце **Включено** выберите значение **Да** для синхронизации групп. В разделе **Сопоставления атрибутов** просмотрите атрибуты группы, синхронизированные из Azure AD в Samanage. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Samanage для операций обновления. Чтобы сохранить все изменения, выберите **Сохранить**.

    ![Совпадение атрибутов группы Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Для настройки фильтров для обсажания следуйте инструкциям в [учебнике по отслеживанию фильтров.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Чтобы включить службу предоставления AD Azure AD для Samanage в разделе **«Настройки»,** измените **статус «Обеспечение»** на **On.**

    ![Статус обеспечения Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Определите пользователей или группы, которые вы хотите предоставить Samanage. В разделе **Настройки** выберите значения, которые вы хотите в **области области.** При выборе **опции Sync все пользователи и группы** рассмотреть ограничения, описанные в следующем разделе "Ограничения коннектора".

    ![Сфера управления](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Когда вы будете готовы к предоставлению, выберите **Сохранить**.

    ![Samanage Сохранить](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Эта операция запускает начальную синхронизацию всех пользователей или групп, определенных в **разделе Область** в разделе **Настройки.** Начальная синхронизация занимает больше времени, чем более поздняя синхронизация. Они возникают примерно каждые 40 минут при запуске службы обеспечения AD Azure AD. 

Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности подготовки. В отчете описаны все действия, выполняемые службой предоставления AD Azure AD на Samanage.

Сведения о журналах подготовки Azure AD см. в руководстве [по отчетам об автоматической подготовке учетных записей пользователей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

При выборе опции **Sync всех пользователей и групп** и настройки значения для атрибута **ролей** Samanage значение под **значением по умолчанию, если нулевое (необязательное)** поле должно быть выражено в следующем формате:

- "DisplayName":"роль", где роль значение по умолчанию вы хотите.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
