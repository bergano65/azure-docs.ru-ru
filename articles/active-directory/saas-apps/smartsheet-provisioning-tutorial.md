---
title: Руководство. Настройка Smartsheet для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в Smartsheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063209"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Учебник. Настройка Smartsheet для автоматической подготовки пользователей

Цель этого руководства — продемонстрировать шаги, которые необходимо выполнить в Smartsheet и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и групп в Smartsheet.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные условия

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент Smartsheet](https://www.smartsheet.com/pricing)
* Учетная запись пользователя на корпоративном плане Smartsheet Enterprise или Enterprise с разрешениями системного администратора.

## <a name="assign-users-to-smartsheet"></a>Назначение пользователей Smartsheet

Azure Active Directory использует концепцию, называемую *назначениями* , чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, какие пользователи и (или) группы в Azure AD должны иметь доступ к Smartsheet. После принятия решения вы можете назначить этих пользователей и (или) группы для Smartsheet, следуя приведенным ниже инструкциям.

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Важные советы по назначению пользователей в Smartsheet

* Рекомендуется назначить одного пользователя Azure AD в Smartsheet для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Smartsheet необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью **доступа по умолчанию** исключаются из подготовки.

* Чтобы обеспечить четность в назначениях ролей пользователей между Smartsheet и Azure AD, рекомендуется использовать те же назначения ролей, заполненные в списке пользователей Full Smartsheet. Чтобы получить этот список пользователей из Smartsheet, перейдите к **администратору учетной записи > Управление пользователями > другие действия > скачать список пользователей (CSV)**.

* Для доступа к определенным функциям в приложении Smartsheet требуется, чтобы пользователь имел несколько ролей. Чтобы узнать больше о типах пользователей и разрешениях в Smartsheet, перейдите к разделу [типы и разрешения пользователей](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Если у пользователя есть несколько ролей, назначенных в Smartsheet, **необходимо** убедиться, что эти назначения ролей реплицируются в Azure AD, чтобы избежать ситуации, когда пользователи могут навсегда потерять доступ к объектам Smartsheet. Каждая уникальная роль в Smartsheet **должна** быть назначена другой группе в Azure AD. После этого пользователь **должен** быть добавлен в каждую из групп, соответствующих нужным ролям. 

## <a name="set-up-smartsheet-for-provisioning"></a>Настройка Smartsheet для подготовки

Перед настройкой Smartsheet для автоматической подготовки пользователей с помощью Azure AD необходимо включить подготовку SCIM для Smartsheet.

1. Войдите в систему как **sysadmin** на **[портале Smartsheet](https://app.smartsheet.com/b/home)** и перейдите к **администратору учетной записи**.

    ![Администратор учетной записи Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Перейдите в раздел средства **управления безопасностью > автоматической подготовки пользователей > изменить**.

    ![Средства управления безопасностью Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Добавьте и проверьте домены электронной почты для пользователей, которые планируется подготавливать из Azure AD в Smartsheet. Выберите **не включено** , чтобы убедиться, что все действия по подготовке созданы только из Azure AD, а также убедитесь, что список пользователей Smartsheet синхронизирован с назначениями Azure AD.

    ![Подготовка пользователей Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. После завершения проверки потребуется активировать домен. 

    ![Smartsheet активировать домен](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Создайте **маркер секрета** , необходимый для настройки автоматической подготовки пользователей с помощью Azure AD, перейдя к **приложениям и интеграции**.

    ![Установка Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Выберите **доступ через API**. Щелкните **создать новый маркер доступа**.

    ![Установка Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Определите имя маркера доступа API. Нажмите кнопку **ОК**.

    ![Установка Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Скопируйте маркер доступа API и сохраните его, так как это будет единственный момент, когда вы сможете его просмотреть. Это необходимо в поле **секретного токена** в Azure AD.

    ![Токен Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Добавление Smartsheet из коллекции

Чтобы настроить Smartsheet для автоматической подготовки пользователей с помощью Azure AD, необходимо добавить Smartsheet из коллекции приложений Azure AD в список управляемых приложений SaaS.

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в раздел **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Smartsheet**, выберите **Smartsheet** на панели результатов. 

    ![Smartsheet в списке результатов](common/search-new-app.png)

5. Выберите кнопку **Регистрация для Smartsheet** , которая перенаправит вас на страницу входа Smartsheet. 

    ![Добавление Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Так как Smartsheet — это приложение OpenIDConnect, выберите вход в Smartsheet с помощью рабочей учетной записи Майкрософт.

    ![Имя входа OIDC Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. После успешной проверки подлинности примите запрос согласия на страницу согласия. Затем приложение будет автоматически добавлено в клиент, и вы будете перенаправлены в учетную запись Smartsheet.

    ![Smartsheet OIDc согласие](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Настройка автоматической подготовки пользователей в Smartsheet 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в Smartsheet на основе назначений пользователей и групп в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Smartsheet в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Smartsheet**.

    ![Ссылка на Smartsheet в списке "приложения"](common/all-applications.png)

3. Перейдите на вкладку **Подготовка** .

    ![Вкладка "подготовка"](common/provisioning.png)

4. Установите для **режима подготовки** значение **автоматически**.

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите `https://scim.smartsheet.com/v2/` **URL-адрес клиента**. Введите значение, полученное и сохраненное ранее из Smartsheet в **маркере секрета**. Щелкните **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к Smartsheet. Если подключение не выполняется, убедитесь, что у учетной записи Smartsheet есть разрешения SysAdmin, и повторите попытку.

    ![Токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Smartsheet**.

    ![Сопоставления пользователей Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в Smartsheet в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Smartsheet для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу подготовки Azure AD для Smartsheet, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей и (или) группы, которые вы хотите подготавливать к Smartsheet, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов по подготовке, в которых описаны все действия, выполняемые службой подготовки Azure AD в Smartsheet.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Smartsheet не поддерживает обратимое удаление. Если атрибуту **активного** пользователя присвоено значение false, Smartsheet удаляет пользователя навсегда.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)
