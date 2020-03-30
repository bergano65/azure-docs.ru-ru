---
title: 'Учебник: Настройка Storegate для автоматического предоставления пользовательского интерфейса с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей Storegate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 72903a36f88f9092ce1d203b557003083407320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064263"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Учебник: Настройка Storegate для автоматического подготовки пользователей

Цель этого руководства состоит в том, чтобы продемонстрировать шаги, которые будут выполнены в Storegate и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователям и/или группам Storegate.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Арендатор Storegate](https://www.storegate.com)
* Учетная запись пользователя в Storegate с разрешениями администратора.

## <a name="assign-users-to-storegate"></a>Назначить пользователей Storegate

Активный каталог Azure использует концепцию, называемую назначениями, чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к Storegate. После решения вы можете назначить этих пользователей и/или групп Storegate, следуя инструкциям здесь:

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Важные советы по назначению пользователей Storegate

* Для тестирования автоматической конфигурации подготовки пользователей рекомендуется назначить одного пользователя Azure AD Storegate. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя Storegate необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге заданий. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="set-up-storegate-for-provisioning"></a>Настройка Storegate для подготовки

Перед настройкой Storegate для автоматического предоставления пользовательского запроса с Azure AD необходимо получить некоторую информацию о подготовке из Storegate.

1. Войдите в консоль [Storegate Admin](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) И перейдите к настройкам, нажав на значок пользователя в правом верхнем углу и выберите **настройки учетной записи.**

    ![Storegate Добавить SCIM](media/storegate-provisioning-tutorial/admin.png)

2. В настройках перейдите к **настройкам > и** убедитесь, что переключатель переключателя включен в **разделе Единого знака.**

    ![Настройки хранения](media/storegate-provisioning-tutorial/team.png)

    ![Кнопка переключения storegate](media/storegate-provisioning-tutorial/sso.png)

3. Копирование **URL-адреса арендатора** и **маркера**. Эти значения будут введены в поля **URL-адреса арендатора** и **секретные маркеры** соответственно в вкладке «Подготовка» приложения Storegate на портале Azure. 

    ![Storegate Создать токен](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Добавить Storegate из галереи

Для настройки Storegate для автоматического предоставления пользовательского запроса с помощью Azure AD необходимо добавить Storegate из галереи приложений Azure AD в список управляемых приложений SaaS.

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Storegate,** выберите **Storegate** в панели результатов. 

    ![Хранение в списке результатов](common/search-new-app.png)

5. Выберите кнопку **«Регистрация» для Storegate,** которая перенаправит вас на страницу входа Виран. 

    ![Storegate OIDC Добавить](media/storegate-provisioning-tutorial/signup.png)

6.  Войдите в консоль [Storegate Admin](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) И перейдите к настройкам, нажав на значок пользователя в правом верхнем углу и выберите **настройки учетной записи.**

    ![Войти в Магазингейт](media/storegate-provisioning-tutorial/admin.png)

7. В настройках перейдите к настройкам > команды и нажмите на переключатель **переключения** в разделе Единый знак, это запустит поток согласия. Нажмите на **активацию**.

    ![Команда Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Storegate sso](media/storegate-provisioning-tutorial/sso.png)

    ![Активировать хранение](media/storegate-provisioning-tutorial/activate.png)

8. Поскольку Storegate является приложением OpenIDConnect, выберите вход в Storegate с помощью рабочей учетной записи Майкрософт.

    ![Логин Storegate OIDC](media/storegate-provisioning-tutorial/login.png)

9. После успешной проверки подлинности примите запрос согласия на страницу согласия. Приложение будет автоматически добавлено к вашему арендатору, и вы будете перенаправлены на ваш аккаунт Storegate.

    ![Хранение OIDc Согласие](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Настройка автоматического обеспечения пользователя для Storegate 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в Storegate на основе пользовательских и/или групповых заданий в Azure AD.

> [!NOTE]
> Чтобы узнать больше о прыге storegate SCIM, [обратитесь](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)к этому .

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Настройка автоматического обеспечения пользовательского обеспечения Storegate в Azure AD

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Storegate**.

    ![Ссылка Storegate в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** `https://dialpad.com/scim` ввод **URL-адреса клиента**. Ввейте значение, которое вы извлекли и сохранено ранее из Storegate в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к Storegate. Если соединение не удается, убедитесь, что ваша учетная запись Storegate имеет разрешения Admin и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure для Storegate.**

    ![Картографические данные пользователей Storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD для Storegate в разделе **Атрибуты.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Storegate для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя Storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу обеспечения Storegate ВАЗа, измените **статус подготовки** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей и/или группы, которые вы хотели бы предоставить Storegate, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD в Storegate.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)
