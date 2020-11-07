---
title: Учебник. Настройка единого входа Soloinsight-CloudGate для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей для Soloinsight-CloudGate единого входа.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 1b992150f6013fd597a3e56b243bd6236289b710
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358003"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Учебник. Настройка единого входа Soloinsight-CloudGate для автоматической подготовки пользователей

Цель этого учебника — продемонстрировать шаги, которые необходимо выполнить в Soloinsight-CloudGate SSO и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и (или) групп для Soloinsight-CloudGate единого входа.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент Soloinsight-CloudGate SSO](https://www.soloinsight.com/)
* Учетная запись пользователя в Soloinsight-CloudGate SSO с разрешениями администратора.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Назначение пользователей для Soloinsight-CloudGate единого входа

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая *назначением*. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, каким пользователям и (или) группам в Azure AD требуется доступ к Soloinsight-CloudGate SSO. После принятия решения вы можете назначить этих пользователей и (или) группы для Soloinsight-CloudGate единого входа, следуя приведенным ниже инструкциям.
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Важные советы по назначению пользователей Soloinsight-CloudGate единого входа

* Рекомендуется назначить одного пользователя Azure AD Soloinsight-CloudGate единого входа для проверки конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя Soloinsight-CloudGate SSO необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Настройка Soloinsight-CloudGate единого входа для подготовки

1. Войдите в [консоль администрирования солоинсигхт-КЛАУДГАТЕ SSO](https://soloinsight.sigateway.com/login). Перейдите в раздел **администрирование > параметры системы**.

    ![Консоль администрирования Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Перейдите к разделу **Общие**.

    ![Добавление SCIM Soloinsight-CloudGate единого входа](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Прокрутите страницу вниз до конца страницы, чтобы получить **URL-адрес** и **маркер секрета** клиента. Скопируйте **маркер секрета**. Это значение будет указано в поле Секретный токен на вкладке Подготовка приложения Soloinsight-CloudGate SSO в портал Azure.

    ![Создание маркера Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Добавление Soloinsight-CloudGate единого входа из коллекции

Перед настройкой Soloinsight-CloudGate единого входа для автоматической подготовки пользователей с помощью Azure AD необходимо добавить Soloinsight-CloudGate SSO из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Soloinsight-CloudGate SSO из коллекции приложений Azure AD, выполните следующие действия.**

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **солоинсигхт-КЛАУДГАТЕ SSO** , выберите **СОЛОИНСИГХТ-клаудгате SSO** на панели результатов и нажмите кнопку **добавить** , чтобы добавить это приложение.

    ![Soloinsight-CloudGate SSO в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Настройка автоматической подготовки пользователей для Soloinsight-CloudGate единого входа 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в Soloinsight-CloudGate SSO на основе назначений пользователей и групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для Soloinsight-CloudGate единого входа, следуя инструкциям, приведенным в [руководстве по единому входу в солоинсигхт-КЛАУДГАТЕ SSO](./soloinsight-cloudgate-sso-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Soloinsight-CloudGate SSO в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения** , а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Soloinsight CloudGate SSO**.

    ![Ссылка на Soloinsight-CloudGate SSO в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Снимок экрана параметров управления с вызываемым параметром подготовки.](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Снимок экрана: раскрывающийся список режима подготовки с вызываемым автоматическим параметром.](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите `https://sigateway.com/scim/v2/sync/serviceproviderconfig` **URL-адрес клиента**. Введите значение **маркера проверки подлинности scim** , полученное ранее в **маркере секрета**. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к Soloinsight-CloudGate SSO. Если подключение не выполняется, убедитесь, что у учетной записи Soloinsight-CloudGate SSO есть разрешения администратора, и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Выберите команду **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Soloinsight-CloudGate единого входа**.

    ![Сопоставления пользователей Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Проверьте пользовательские атрибуты, которые синхронизированы из Azure AD, чтобы Soloinsight-CloudGate единый вход в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Soloinsight-CloudGate SSO для операций обновления. Нажмите кнопку **Сохранить** , чтобы зафиксировать все изменения.

    ![Soloinsight-CloudGate пользовательские атрибуты единого входа](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы, чтобы Soloinsight-CloudGate единый вход**.

    ![Сопоставления групп Soloinsight-CloudGate единого входа](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Проверьте атрибуты группы, которые синхронизированы из Azure AD, чтобы Soloinsight-CloudGate единый вход в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в Soloinsight-CloudGate SSO для операций обновления. Нажмите кнопку **Сохранить** , чтобы зафиксировать все изменения.

    ![Атрибуты группы Soloinsight-CloudGate единого входа](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для Soloinsight-CloudGate SSO, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать для Soloinsight-CloudGate единого входа, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов по подготовке, в которых описаны все действия, выполняемые службой подготовки Azure AD на Soloinsight-CloudGate единого входа.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)