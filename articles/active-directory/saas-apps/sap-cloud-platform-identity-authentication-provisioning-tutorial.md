---
title: Учебник. Настройка SAP Cloud Platform Identity Authentication для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в SAP Cloud Platform Identity Authentication.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f49b5738-c769-403b-8f29-84ddeea7fbf1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 266e68b2378db7148649fd4067f1da6172932367
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833842"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Учебник. Настройка SAP Cloud Platform Identity Authentication для автоматической подготовки пользователей

Цель этого учебника — продемонстрировать шаги, которые необходимо выполнить в SAP Cloud Platform Identity Authentication и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и/или групп в SAP Cloud Platform Identity Authentication.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих Microsoft Azure условиях использования предварительных версий функций см. в разделе Дополнительные [условия использования для предварительного просмотра Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиентское удостоверение для проверки подлинности SAP Cloud Platform](https://cloudplatform.sap.com/pricing.html)
* Учетная запись пользователя в SAP Cloud Platform Identity Authentication с разрешениями администратора.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Назначение пользователей для проверки подлинности с использованием удостоверения облачной платформы SAP

Azure Active Directory использует концепцию, называемую *назначениями* , чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, каким пользователям и (или) группам в Azure AD требуется доступ к SAP Cloud Platform Identity Authentication. После принятия решения вы можете назначить этих пользователей и (или) группы для проверки подлинности SAP Cloud Platform Identity аутентификации, следуя приведенным ниже инструкциям.
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Важные советы по назначению пользователей для аутентификации SAP Cloud Platform Identity

* Для проверки конфигурации автоматической подготовки пользователей рекомендуется назначить одного пользователя Azure AD в качестве удостоверения для аутентификации SAP Cloud Platform. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя для проверки подлинности с помощью удостоверения облачной платформы SAP необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Настройка SAP Cloud Platform Identity аутентификация для подготовки

1. Войдите в [консоль администрирования SAP Cloud Platform Identity Authentication](https://sapmsftintegration.accounts.ondemand.com/admin). Перейдите к **пользователям & авторизации > администраторов**.

    ![Консоль администрирования SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Создайте пользователя с правами администратора и выберите пользователя.  

3.  В разделе Настройка авторизации включите переключатель в положение **Управление пользователями** и **Управление группами**.

    ![SAP облачная платформа Identity аутентификация Add SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Вы получите электронное письмо, чтобы активировать учетную запись и задать пароль для **службы SAP Cloud Platform Identity Authentication**.

4.  Скопируйте **идентификатор пользователя** и **пароль**. Эти значения будут указаны в полях имя администратора и пароль администратора, соответственно на вкладке Подготовка приложения SAP Cloud Platform Identity Authentication в портал Azure.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Добавление приложения SAP Cloud Platform Identity Authentication из коллекции

Перед настройкой удостоверения SAP Cloud Platform для проверки подлинности для автоматической подготовки пользователей с помощью Azure AD необходимо добавить удостоверение SAP Cloud Platform Identity Authentication из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить SAP Cloud Platform Identity Authentication из коллекции приложений Azure AD, выполните следующие действия.**

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **SAP Cloud Platform Identity Authentication**, выберите **SAP Cloud Platform Identity Authentication** на панели результатов и нажмите кнопку **добавить** , чтобы добавить это приложение.

    ![Приложения SAP Cloud Platform Identity Authentication в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Настройка автоматической подготовки учетных записей пользователей в SAP облачной платформе удостоверение аутентификации 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в SAP Cloud Platform Identity Authentication на основе назначений пользователей и групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для проверки подлинности SAP Cloud Platform Identity Authentication, следуя инструкциям, приведенным в [руководстве по единому входу SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей в Azure AD с удостоверением Identity, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **SAP Cloud Platform Identity Authentication**.

    ![Ссылка на SAP Cloud Platform Identity Authentication в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите `https://<tenantID>.accounts.ondemand.com/service/scim ` в поле **URL-адрес клиента**. Введите значения **идентификатора пользователя** и **пароля** , полученные ранее в поле **имя администратора** и **пароль администратора** соответственно. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к SAP Cloud Platform Identity Authentication. В случае сбоя подключения убедитесь, что учетная запись аутентификации SAP Cloud Platform Identity имеет разрешения администратора, и повторите попытку.

    ![URL-адрес клиента + токен](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. В поле **Уведомление по электронной почте** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Уведомление по электронной почте](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе " **сопоставления** " выберите **синхронизировать Azure Active Directory пользователей с SAP облачная платформа удостоверение аутентификации**.

    ![Сопоставления пользователей SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Ознакомьтесь с атрибутами пользователей, которые синхронизированы из Azure AD, с SAP Cloud Platform Identity Authentication в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в SAP Cloud Platform Identity Authentication для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя для проверки подлинности SAP Cloud Platform Identity](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу подготовки Azure AD для проверки подлинности SAP Cloud Platform Identity, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки выключено](common/provisioning-toggle-on.png)

12. Определите пользователей и (или) группы, которые вы хотите настроить для проверки подлинности SAP Cloud Platform Identity Authentication, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Идет сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов по подготовке, в которых описаны все действия, выполняемые службой подготовки Azure AD на платформе SAP Cloud Platform Identity Authentication.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Конечной точке SCIM для SAP Cloud Platform Identity Authentication требуются определенные атрибуты в определенном формате. Дополнительные сведения об этих атрибутах и их конкретном формате можно получить [здесь](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

