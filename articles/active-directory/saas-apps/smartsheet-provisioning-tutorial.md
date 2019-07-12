---
title: Учебник. Настройка Smartsheet для автоматической подготовки пользователей с Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Smartsheet.
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
ms.openlocfilehash: f0ca2dfa90e1312db664962e7ffbe6b3f4dd96e1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670937"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Учебник. Настройка Smartsheet для автоматической подготовки пользователей

Цель данного учебника — продемонстрировать действия, выполняемые в Smartsheet и Azure Active Directory (Azure AD) для настройки Azure AD для автоматической подготовки и отзыва пользователей и групп в Smartsheet.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения о общие Microsoft Azure условия использования предварительных версий функций, см. в разделе [дополнительным условиям использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент Smartsheet](https://www.smartsheet.com/pricing)
* Учетная запись пользователя в плане Smartsheet Enterprise или Enterprise Premier с разрешениями системного администратора.

## <a name="assign-users-to-smartsheet"></a>Назначить пользователей к Smartsheet

Azure Active Directory используется концепция, называемая *назначения* для определения, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только те пользователи и группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, следует решить, какие пользователи или группы в Azure AD требуется доступ к Smartsheet. Сделав это, можно назначить этих пользователей и групп, к Smartsheet следуя приведенным ниже указаниям:

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Важные рекомендации по назначению пользователей в Smartsheet

* Рекомендуется один назначенный пользователем Azure AD Smartsheet для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Smartsheet, необходимо выбрать действительную роль конкретного приложения (если доступно) в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

* Чтобы обеспечить четность в назначениях ролей пользователя между Smartsheet и Azure AD, рекомендуется использовать же назначения ролей в полный список пользователей Smartsheet. Чтобы получить этот список пользователей из Smartsheet, перейдите к **администратор учетной записи > Управление пользователями > Дополнительные действия > скачать список пользователей (csv)** .

* Для доступа к определенным возможностям в приложении, Smartsheet требуется использовать несколько ролей пользователя. Дополнительные сведения о типах пользователей и разрешения в Smartsheet, перейдите к [вводимые пользователем и разрешения](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Если у пользователя есть несколько ролей, назначенных в Smartsheet, вы **необходимо** убедитесь, что эти назначения ролей, реплицируются в Azure AD, чтобы избежать ситуации, где пользователи могут утратить доступ к объектам Smartsheet без возможности восстановления. Каждой уникальной роли в Smartsheet **необходимо** назначаться в другую группу в Azure AD. Пользователь **необходимо** каждой из групп, соответствующих ролям, которые требуется добавить. 

## <a name="set-up-smartsheet-for-provisioning"></a>Настройка Smartsheet для подготовки

Перед настройкой Smartsheet для автоматической подготовки пользователей в Azure AD, необходимо включить подготовку SCIM в Smartsheet.

1. Войдите в систему как **SysAdmin** в **[портала Smartsheet](https://app.smartsheet.com/b/home)** и перейдите к **администратор учетной записи**.

    ![Администратор учетной записи Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Перейдите к **средства управления безопасностью > пользователя автоматической подготовки > изменить**.

    ![Средства управления безопасностью Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Добавить и проверить домены электронной почты для пользователей, которые планируется Подготовка из Azure AD в Smartsheet. Выберите **не включена** убедитесь, что все действия подготовки исходить только из Azure AD и также убедитесь, что Smartsheet списка пользователей в соответствии с назначением Azure AD.

    ![Подготовка пользователей Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. После завершения проверки необходимо будет активировать домена. 

    ![Smartsheet активировать домена](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Создать **секретный токен** необходимые для настройки автоматической подготовки пользователей с Azure AD, перейдя по адресу **приложений и интеграции**.

    ![Установка Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Выберите **доступ через API**. Нажмите кнопку **создать новый токен доступа**.

    ![Установка Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Определение имени токена доступа API. Нажмите кнопку **ОК**.

    ![Установка Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Скопируйте токен доступа API и сохраните его, так как это будет единственный случай, когда вы можете просмотреть ее. Это необходимо в **секретный токен** в Azure AD.

    ![Токен Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Добавить Smartsheet из коллекции

Чтобы настроить Smartsheet для автоматической подготовки пользователей в Azure AD, необходимо добавить Smartsheet из коллекции приложений Azure AD в список управляемых приложений SaaS.

1. В  **[портала Azure](https://portal.azure.com)** , на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите **новое приложение** кнопку в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Smartsheet**выберите **Smartsheet** на панели результатов. 

    ![Smartsheet в списке результатов](common/search-new-app.png)

5. Выберите **регистрации для Smartsheet** кнопки, который вы будете перенаправлены на страницу входа в Smartsheet. 

    ![Добавить Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Как Smartsheet — это приложение OpenIDConnect, выберите для входа в систему к Smartsheet с помощью рабочей учетной записи Майкрософт.

    ![Имя входа Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. После успешной проверки подлинности примите запрос согласия для страница согласия. Приложения будут автоматически добавляться к вашему клиенту, и вы будете перенаправлены к вашей учетной записи Smartsheet.

    ![Smartsheet OIDc согласия](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Настройка автоматической подготовки пользователей к Smartsheet 

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD для создания, изменения и отключения пользователей и групп в Smartsheet на основе назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Smartsheet в Azure AD.

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Smartsheet**.

    ![Ссылка на Smartsheet в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Инициализация](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Инициализация](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите `https://scim.smartsheet.com/v2/` в **URL-адрес клиента**. Входные данные, полученные и сохраненные ранее из Smartsheet в значение **секретный токен**. Нажмите кнопку **проверить подключение** и убедиться, что Azure AD может подключиться к Smartsheet. Если подключение отсутствует, убедитесь, что у учетной записи Smartsheet есть разрешения системного администратора и повторите попытку.

    ![по маркеру](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Уведомление по электронной почте](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать пользователей Azure Active Directory к Smartsheet**.

    ![Сопоставления пользователей Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Smartsheet в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления учетных записей пользователей в Smartsheet для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу для Smartsheet подготовки Azure AD, измените **состояние подготовки** для **на** в **параметры** раздел.

    ![Состояние подготовки на противоположное](common/provisioning-toggle-on.png)

12. Определите пользователей или группы, которые вы хотите для подготовки к Smartsheet, выбрав нужные значения в **область** в **параметры** раздел.

    ![Инициализация области](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Идет сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для подготовки отчетов действия, в которых зафиксированы все действия, выполняемые службой Smartsheet подготовки Azure AD.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Smartsheet не поддерживает удаляется обратимо. Когда у пользователя **active** атрибут имеет значение False, Smartsheet удаляет пользователя без возможности восстановления.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Следующие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
