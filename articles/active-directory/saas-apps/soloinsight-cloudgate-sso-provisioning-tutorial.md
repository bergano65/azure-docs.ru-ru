---
title: 'Учебник: Настройте Soloinsight-CloudGate SSO для автоматического предоставления пользовательских услуг с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей в Soloinsight-CloudGate SSO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 6ab90a6aea262d5c7067f9f41b9ddfc090b7371d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063201"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Учебник: Настройка Soloinsight-CloudGate SSO для автоматического подготовки пользователей

Цель этого руководства состоит в том, чтобы продемонстрировать шаги, которые будут выполнены в Soloinsight-CloudGate SSO и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователей и/или групп В Soloinsight-CloudGate SSO.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Арендатор SSO Soloinsight-CloudGate](https://www.soloinsight.com/)
* Учетная запись пользователя в Soloinsight-CloudGate SSO с разрешениями Admin.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Назначение пользователей Soloinsight-CloudGate SSO

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настраивать и включать автоматическую подготовку пользователей, необходимо решить, каким пользователям и/или группам в Azure AD необходим доступ к Soloinsight-CloudGate SSO. После решения вы можете назначить этих пользователей и/или групп в Soloinsight-CloudGate SSO, следуя инструкциям здесь:
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Важные советы по назначению пользователей Soloinsight-CloudGate SSO

* Для тестирования автоматической конфигурации подготовки пользователей рекомендуется назначить одного пользователя Azure AD ВАЙСу Soloinsight-CloudGate SSO. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя Soloinsight-CloudGate SSO необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге о назначении. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Настройка Soloinsight-CloudGate SSO для подготовки

1. Восприкажитесь на консоль [Soloinsight-CloudGate SSO Admin.](https://soloinsight.sigateway.com/login) Перейдите к **настройкам системы администрирования >.**

    ![Консоль индивидуального анализа-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Перейдите к **общему**.

    ![Soloinsight-Cloudgate SSO Добавить SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Прокрутите вниз до конца страницы, чтобы получить **URL-адрес арендатора** и **секретный токен.** Копировать **секретный токен**. Это значение будет введено в поле Secret Token в вкладке «Обеспечение вашего приложения Soloinsight-CloudGate SSO» на портале Azure.

    ![Soloinsight-Cloudgate SSO Создать токен](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Добавить Soloinsight-CloudGate SSO из галереи

Прежде чем настроить Soloinsight-CloudGate SSO для автоматического оформления данных с Помощью Azure AD, необходимо добавить Soloinsight-CloudGate SSO из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Soloinsight-CloudGate SSO из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Soloinsight-CloudGate SSO**, выберите **Soloinsight-CloudGate SSO** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![Soloinsight-CloudGate SSO в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Настройка автоматической подготовки пользователя к Soloinsight-CloudGate SSO 

В этом разделе вы проведет шаги по настройке службы предоставления Azure AD для создания, обновления и отработки пользователей и/или групп в Soloinsight-CloudGate SSO на основе пользовательских и/или групповых заданий в Azure AD.

> [!TIP]
> Вы также можете включить SAML на основе одного знака для Soloinsight-CloudGate SSO , следуя инструкциям, представленным в [Soloinsight-CloudGate SSO Единый знак на учебник](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). Единый подгон может быть настроен независимо от автоматического подготовки пользователей, хотя эти две функции дополняют друг друга

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Для настройки автоматической подготовки пользователя для Soloinsight-CloudGate SSO в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Soloinsight CloudGate SSO**.

    ![Ссылка на Soloinsight-CloudGate SSO в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** `https://sigateway.com/scim/v2/sync/serviceproviderconfig` ввод **URL-адреса клиента**. Ввейте значение **токенов подлинности SCIM,** извлеченное ранее в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к Soloinsight-CloudGate SSO. Если подключение не удается, убедитесь, что ваша учетная запись Soloinsight-CloudGate SSO имеет разрешения admin и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в Soloinsight-CloudGate SSO.**

    ![Картирование пользователей Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD на Soloinsight-CloudGate SSO в разделе **Атрибуткартирования.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Soloinsight-CloudGate SSO для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. В разделе **Картпинги** выберите **синхронизацию групп активных каталогов Azure в Soloinsight-CloudGate SSO.**

    ![Картирование группы Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с Soloinsight-CloudGate SSO в разделе **Атрибуткарт.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для соответствия группам в Soloinsight-CloudGate SSO для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты группы Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления AD Azure AD для Soloinsight-CloudGate SSO, измените **статус подготовки** в разделе **«В** **настройках».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить Soloinsight-CloudGate SSO, выбрав желаемые значения в разделе **Область** в разделе **Настройки.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD на Soloinsight-CloudGate SSO.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

