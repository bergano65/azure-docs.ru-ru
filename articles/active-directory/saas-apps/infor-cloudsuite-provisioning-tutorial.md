---
title: 'Учебник: Налаживайте Infor CloudSuite для автоматического предоставления пользовательского запроса с помощью active-каталога Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей Infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 7b91b8418580717afaf8ddf176f934b3ff1d0c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057553"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Учебник: Налаживание Infor CloudSuite для автоматического подготовки пользователей

Цель этого руководства – продемонстрировать шаги, которые будут выполняться в Infor CloudSuite и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-обеспечения пользователей и/или групп Infor CloudSuite.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Арендатор Infor CloudSuite](https://www.infor.com/products/infor-os)
* Учетная запись пользователя в Infor CloudSuite с разрешениями Admin.

## <a name="assigning-users-to-infor-cloudsuite"></a>Назначение пользователей Infor CloudSuite

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к Infor CloudSuite. После решения вы можете назначить этих пользователей и/или групп Infor CloudSuite, следуя инструкциям здесь:
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Важные советы по назначению пользователей Infor CloudSuite

* Для тестирования автоматической конфигурации подготовки пользователей рекомендуется назначить одного пользователя Azure AD Infor CloudSuite. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя Infor CloudSuite необходимо выбрать любую действительную роль приложения (если она доступна) в диалоге о назначении. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Настройка Infor CloudSuite для подготовки

1. Вопийте на [консоль Infor CloudSuite.](https://www.infor.com/customer-center) Нажмите на значок пользователя, а затем перейти к **управлению пользователем.**

    ![Консоль для админ-консоли Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Нажмите на значок меню в левом верхнем углу экрана. Нажмите на **Управление**.

    ![Infor CloudSuite Добавить SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Перейдите к **учетным записям SCIM**.

    ![Учетная запись Infor CloudSuite SCIM](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Добавьте пользователя-админ, нажав на значок plus. Предоставьте **пароль SCIM** и введите тот же пароль под **подтвердите пароль**. Нажмите на значок папки, чтобы сохранить пароль. Затем вы увидите **идентификатор пользователя,** созданный для пользователя-админ.

    ![Пользователь Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Пароль Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Идентификатор Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. Для создания маркера носителя скопируйте **идентификатор пользователя** и **пароль SCIM.** Вставьте их в блокнот, разделенный толстой кишке. Закодировать значение строки, перемещаясь на **плагины > MIME Tools > Basic64 Encode.** 

    ![Идентификатор Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Скопируйте маркер носителя. Это значение будет введено в поле Secret Token в вкладке «Подготовка» приложения Infor CloudSuite на портале Azure.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Добавить Infor CloudSuite из галереи

Прежде чем настроить Infor CloudSuite для автоматического предоставления пользовательского запроса с помощью Azure AD, необходимо добавить Infor CloudSuite из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Infor CloudSuite из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Infor CloudSuite,** выберите **Infor CloudSuite** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![Infor CloudSuite в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Настройка автоматического предоставления пользовательу Infor CloudSuite 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в Infor CloudSuite на основе пользовательских и/или групповых заданий в Azure AD.

> [!TIP]
> Вы также можете включить SAML на основе одного включения для Infor CloudSuite , следуя инструкциям, представленным в [Infor CloudSuite Единый набор для регистрации](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

> [!NOTE]
> Чтобы узнать больше о прыге infor CloudSuite в SCIM, обратитесь к [этому](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Для настройки автоматического набора пользователей для Infor CloudSuite в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Infor CloudSuite**.

    ![Ссылка на Infor CloudSuite в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` ввод **URL-адреса клиента**. Ввейте значение маркера предъявителя, полученное ранее в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к Infor CloudSuite. Если подключение не удается, убедитесь, что ваша учетная запись Infor CloudSuite имеет разрешения admin и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в Infor CloudSuite.**

    ![Картирование пользователей Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD на Infor CloudSuite в разделе **Атрибуты.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Infor CloudSuite для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. В разделе **Картпинги** выберите **синхронизацию активных групп каталогов Azure в Infor CloudSuite.**

    ![Картирование группы Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с Infor CloudSuite в разделе **Атрибуты Mapping.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для соответствия группам infor CloudSuite для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты группы Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления AD Azure AD для Infor CloudSuite, измените **статус подготовки** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить Infor CloudSuite, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD в Infor CloudSuite.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)