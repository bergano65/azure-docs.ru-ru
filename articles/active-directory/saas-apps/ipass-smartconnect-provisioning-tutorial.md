---
title: Учебник. Настройка Ипасс Смартконнект для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в Ипасс Смартконнект.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 3141a32437e56bcefe830bc94109e93208e0258d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232156"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Учебник. Настройка Ипасс Смартконнект для автоматической подготовки пользователей

Цель этого руководства — продемонстрировать шаги, которые необходимо выполнить в Ипасс Смартконнект и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и (или) групп в Ипасс Смартконнект.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих Microsoft Azure условиях использования предварительных версий функций см. в разделе Дополнительные [условия использования для предварительного просмотра Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* Клиент Azure AD.
* [Клиент Ипасс смартконнект](https://www.ipass.com/buy-ipass/).
* Учетная запись пользователя в Ипасс Смартконнект с разрешениями администратора.

## <a name="assigning-users-to-ipass-smartconnect"></a>Назначение пользователей для Ипасс Смартконнект

Azure Active Directory использует концепцию, называемую назначениями, чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, какие пользователи и (или) группы в Azure AD должны иметь доступ к Ипасс Смартконнект. После принятия решения вы можете назначить этих пользователей и (или) группы для Ипасс Смартконнект, следуя приведенным ниже инструкциям.
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Важные советы по назначению пользователей в Ипасс Смартконнект

* Рекомендуется назначить одного пользователя Azure AD в Ипасс Смартконнект для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Ипасс Смартконнект необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Настройка Ипасс Смартконнект для подготовки

Перед настройкой Ипасс Смартконнект для автоматической подготовки пользователей с помощью Azure AD необходимо получить сведения о конфигурации из консоли администрирования Ипасс Смартконнект:

1. Чтобы получить токен носителя, который необходим для проверки подлинности в конечной точке Ипасс Смартконнект SCIM, ознакомьтесь с первой настройкой Ипасс Смартконнект, так как это значение предоставляется только после этого. 
2. Если у вас нет токена носителя, обратитесь к [группе поддержки Ипасс смартконнект](mailto:help@ipass.com) , чтобы получить новый.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Добавление Ипасс Смартконнект из коллекции

Чтобы настроить Ипасс Смартконнект для автоматической подготовки пользователей с помощью Azure AD, необходимо добавить Ипасс Смартконнект из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Ипасс Смартконнект из коллекции приложений Azure AD, выполните следующие действия.**

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Ипасс смартконнект**, выберите **ипасс смартконнект** на панели результатов и нажмите кнопку **добавить** , чтобы добавить это приложение.

    ![iPass SmartConnect в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Настройка автоматической подготовки пользователей в Ипасс Смартконнект 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в Ипасс Смартконнект на основе назначений пользователей и групп в Azure AD.

> [!TIP]
>  Вы также можете включить единый вход на основе SAML для BitaBIZ, следуя инструкциям, приведенным в руководстве по единому [входу Ипасс смартконнект](ipasssmartconnect-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Ипасс Смартконнект в Azure AD, сделайте следующее:

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **iPass SmartConnect**.

    ![Ссылка iPass SmartConnect в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите `https://openmobile.ipass.com/moservices/scim/v1` **URL-адрес клиента**. Введите токен носителя, полученный ранее в **маркере секрета**. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к ипасс смартконнект. В случае сбоя подключения убедитесь, что учетная запись Ипасс Смартконнект имеет разрешения администратора, и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с ипасс смартконнект**.

    ![Сопоставления пользователей Ипасс Смартконнект](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в Ипасс Смартконнект в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства Matching, используются для сопоставления учетных записей пользователей в ипасс смартконнект для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Сопоставления пользователей Ипасс Смартконнект](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу подготовки Azure AD для Ипасс Смартконнект, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки выключено](common/provisioning-toggle-on.png)

12. Определите пользователей и (или) группы, которые вы хотите подготавливать к Ипасс Смартконнект, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Идет сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов по подготовке, в которых описаны все действия, выполняемые службой подготовки Azure AD на ипасс смартконнект.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Ипасс Смартконнект принимает только имена пользователей, имена доменов которых зарегистрированы в консоли администрирования Ипасс Смартконнект.  

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Следующие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
