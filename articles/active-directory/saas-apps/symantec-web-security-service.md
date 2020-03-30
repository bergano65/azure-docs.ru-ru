---
title: 'Учебник: Настройте службу веб-безопасности Symantec (WSS) для автоматического предоставления пользовательских данных с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей Symantec Web Security Service (WSS).
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
ms.openlocfilehash: fbd105ca1623512a3c16f3b609374f5c4055898b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063124"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Учебник: Настройте Службу веб-безопасности Symantec (WSS) для автоматического подготовки пользователей

Цель этого руководства состоит в том, чтобы продемонстрировать шаги, которые будут выполнены в Symantec Web Security Service (WSS) и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователей и/или групп ВSymantec Web Security Service (WSS).

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Арендатор службы веб-безопасности Symantec (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Учетная запись пользователя в Symantec Web Security Service (WSS) с разрешениями админа.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Назначение пользователей службой веб-безопасности Symantec (WSS)

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к Службе веб-безопасности Symantec (WSS). После решения вы можете назначить этих пользователей и/или групп в Службу веб-безопасности Symantec (WSS), следуя инструкциям здесь:
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Важные советы по назначению пользователей службе веб-безопасности Symantec (WSS)

* Для тестирования автоматической конфигурации подготовки пользователей рекомендуется назначить одного пользователя Azure AD службе веб-безопасности Symantec Web Security Service (WSS). Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя службе web-безопасности Symantec (WSS) необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге о назначении. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Настройка Symantec Web Security Service (WSS) для подготовки

Прежде чем настроить Службу веб-безопасности Symantec (WSS) для автоматического предоставления пользовательских данных с Azure AD, необходимо включить SCIM-подготовку на Службе веб-безопасности Symantec (WSS).

1. Восвай на [консоль Symantec Web Security Service.](https://portal.threatpulse.com/login.jsp) Перейдите к**службе** **решений** > .

    ![Служба веб-безопасности Symantec (WSS)](media/symantec-web-security-service/service.png)

2. Перейдите к интеграции > обслуживания**учетной записи** > **Новая интеграция.** **Account Maintenance**

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Выберите **сторонних пользователей & синхронизации групп.** 

    ![Symantec Web Security Service](media/symantec-web-security-service/third-party-users.png)

4.  Копирование **URL-адреса SCIM** и **маркера**. Эти значения будут введены в поле **URL-адреса арендатора** и **Secret Token** в вкладке «Обеспечение безопасности веб-службы Symantec» (WSS) на портале Azure.

    ![Symantec Web Security Service](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Добавить Службу веб-безопасности Symantec (WSS) из галереи

Чтобы настроить Службу веб-безопасности Symantec Web Security Service (WSS) для автоматической подготовки пользователей с помощью Azure AD, необходимо добавить службу web-безопасности Symantec Web Security Service (WSS) из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить службу веб-безопасности Symantec (WSS) из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Symantec Web Security Service,** выберите **Службу безопасности Web Symantec** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![Служба Symantec Web Security Service (WSS) в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Настройка автоматической подготовки пользователей к Службе веб-безопасности Symantec (WSS)

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в Symantec Web Security Service (WSS) на основе пользовательских и/или групповых назначений в Azure AD.

> [!TIP]
> Вы также можете включить SAML на основе одного включения для Symantec Web Security Service (WSS), следуя инструкциям, представленным в [Symantec Web Security Service (WSS) Единый учебник по регистрации.](symantec-tutorial.md) Единый набор может быть настроен независимо от автоматического подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Для настройки автоматической подготовки пользователей для Службы веб-безопасности Symantec (WSS) в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **службу веб-безопасности Symantec.**

    ![Ссылка на Symantec Web Security Service (WSS) в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе "Учетные данные админа" введомые значения **URL-адреса SCIM** и **маркеров,** полученные ранее в **URL-адресе Tenant** и **Secret Token** соответственно. Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к службе веб-безопасности Symantec. Если соединение не удается, убедитесь, что ваша учетная запись Symantec Web Security Service (WSS) имеет разрешения Admin и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в Symantec Web Security Service (WSS).**

    ![Symantec Web Security Service (WSS) Пользовательские картографы](media/symantec-web-security-service/usermapping.png)

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с службой веб-безопасности Symantec (WSS) в разделе **«Отображение атрибутов».** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Службе веб-безопасности Symantec (WSS) для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Symantec Web Security Service (WSS) Пользовательские картографы](media/symantec-web-security-service/userattribute.png)

10. В разделе **Картпинги** выберите **синхронизацию активных групп каталогов Azure для службы веб-безопасности Symantec.**

    ![Symantec Web Security Service (WSS) Пользовательские картографы](media/symantec-web-security-service/groupmapping.png)

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с службой веб-безопасности Symantec (WSS) в разделе **«Отображение атрибутов».** Атрибуты, выбранные в качестве свойств **соответствия,** используются для соответствия группам службы веб-безопасности Symantec (WSS) для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Symantec Web Security Service (WSS) Пользовательские картографы](media/symantec-web-security-service/groupattribute.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления Услуг Azure AD для Службы веб-безопасности Symantec, измените **статус обеспечения** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить Службе веб-безопасности Symantec (WSS), выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие синхронизации. Для получения дополнительной информации о том, сколько времени потребуется для пользователей и / или групп для предоставления, посмотреть, [сколько времени потребуется для предоставления пользователям](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Вы можете использовать раздел **Текущее состояние** для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD в Службе веб-безопасности Symantec (WSS). Для получения дополнительной [информации, см.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Чтобы прочитать журналы подготовки Azure AD, [см.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)
