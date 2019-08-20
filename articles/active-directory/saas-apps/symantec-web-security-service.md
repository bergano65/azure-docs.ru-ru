---
title: Учебник. Настройка Symantec Web Security Service (WSS) для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в Symantec Web Security Service (WSS).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: cfdf5487bcd4c33a21fca593fef829c58d2d099d
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576203"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Учебник. Настройка Symantec Web Security Service (WSS) для автоматической подготовки пользователей

Цель этого руководства — продемонстрировать шаги, которые необходимо выполнить в Symantec Web Security Service (WSS) и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и/или групп в Symantec Web Security. Служба (WSS).

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих Microsoft Azure условиях использования предварительных версий функций см. в разделе Дополнительные [условия использования для предварительного просмотра Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент Symantec Web Security Service (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Учетная запись пользователя в Symantec Web Security Service (WSS) с разрешениями администратора.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Назначение пользователей в Symantec Web Security Service (WSS)

Azure Active Directory использует концепцию, называемую назначениями, чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, каким пользователям и (или) группам в Azure AD требуется доступ к Symantec Web Security Service (WSS). После этого вы можете назначить этих пользователей и (или) группы для Symantec Web Security Service (WSS), следуя приведенным ниже инструкциям.
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Важные советы по назначению пользователей в Symantec Web Security Service (WSS)

* Для проверки конфигурации автоматической подготовки пользователей рекомендуется назначить одного пользователя Azure AD в Symantec Web Security Service (WSS). Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Symantec Web Security Service (WSS) необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Настройка Symantec Web Security Service (WSS) для подготовки

Перед настройкой Symantec Web Security Service (WSS) для автоматической подготовки пользователей с помощью Azure AD необходимо включить подготовку SCIM в Symantec Web Security Service (WSS).

1. Войдите в [консоль администратора службы веб-безопасности Symantec](https://portal.threatpulse.com/login.jsp). Перейдите к**службе** **решений** > .

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/service.png)

2. Последовательно выберите пункты **Управление** > учетной записью**Интеграция** > **Новая интеграция**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Выберите сторонние **пользователи & группы синхронизация**. 

    ![Веб-служба безопасности Symantec](media/symantec-web-security-service/third-party-users.png)

4.  Скопируйте **URL-адрес** и **токен**scim. Эти значения будут указаны в поле **URL-адрес клиента** и **секретный токен** на вкладке Подготовка приложения Symantec Web Security Service (WSS) в портал Azure.

    ![Веб-служба безопасности Symantec](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Добавление Symantec Web Security Service (WSS) из коллекции

Чтобы настроить Symantec Web Security Service (WSS) для автоматической подготовки пользователей с помощью Azure AD, необходимо добавить Symantec Web Security Service (WSS) из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Symantec Web Security Service (WSS) из коллекции приложений Azure AD, выполните следующие действия.**

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Symantec Web Security Service**, выберите **Symantec Web Security Service** на панели результатов и нажмите кнопку **добавить** , чтобы добавить это приложение.

    ![Служба Symantec Web Security Service (WSS) в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Настройка автоматической подготовки пользователей в Symantec Web Security Service (WSS)

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в Symantec Web Security Service (WSS) на основе назначений пользователей и групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для Symantec Web Security Service (WSS), следуя инструкциям, приведенным в руководстве по единому [входу в Symantec Web Security Service (WSS)](symantec-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей для Symantec Web Security Service (WSS) в Azure AD, сделайте следующее:

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Symantec Web Security Service**.

    ![Ссылка на Symantec Web Security Service (WSS) в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе Учетные данные администратора введите **URL-адрес scim** и значения токена, полученные ранее в поле **URL-адрес клиента** и **секретный токен** соответственно. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к службе веб-безопасности Symantec. Если подключение не выполняется, убедитесь, что учетная запись Symantec Web Security Service (WSS) имеет разрешения администратора, и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Symantec Web Security Service (WSS)** .

    ![Сопоставления пользователей в службе веб-безопасности Symantec (WSS)](media/symantec-web-security-service/usermapping.png)

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в Symantec Web Security Service (WSS) в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства Matching, используются для сопоставления учетных записей пользователей в Symantec Web Security Service (WSS) для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Сопоставления пользователей в службе веб-безопасности Symantec (WSS)](media/symantec-web-security-service/userattribute.png)

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы с Symantec Web Security Service**.

    ![Сопоставления пользователей в службе веб-безопасности Symantec (WSS)](media/symantec-web-security-service/groupmapping.png)

11. В разделе **сопоставление атрибутов** Проверьте атрибуты группы, которые синхронизированы из Azure AD в Symantec Web Security Service (WSS). Атрибуты, выбранные как свойства Matching, используются для сопоставления групп в Symantec Web Security Service (WSS) для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Сопоставления пользователей в службе веб-безопасности Symantec (WSS)](media/symantec-web-security-service/groupattribute.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для Symantec Web Security Service, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки выключено](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать в Symantec Web Security Service (WSS), выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Идет сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Дополнительные сведения о том, как долго будет выполняться для пользователей и (или) групп, см. в разделе [время, необходимое для предоставления пользователям](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Вы можете использовать **Текущий раздел состояния** для отслеживания хода выполнения и перехода по ссылкам к отчету о действиях по подготовке, в котором описаны все действия, выполняемые службой подготовки Azure AD в Symantec Web Security Service (WSS). Дополнительные сведения см. [в разделе Проверка состояния подготовки пользователей](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Чтобы прочитать журналы подготовки Azure AD, см. раздел [Создание отчетов об автоматической подготовке учетных записей пользователей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Следующие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
