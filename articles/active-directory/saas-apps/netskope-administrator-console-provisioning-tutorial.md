---
title: 'Учебник: Настройка консоли администратора Netskope для автоматического предоставления пользовательского интерфейса с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей на консоль netskope Administrator Console.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061315"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Учебник: Настройка Консоли администратора Netskope для автоматического подготовки пользователей

Цель этого руководства состоит в том, чтобы продемонстрировать шаги, которые будут выполнены в консоли Netskope Administrator и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователей и/или групп консоли Netskope Administrator Console.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Арендатор консоли администратора Netskope](https://www.netskope.com/)
* Учетная запись пользователя в консоли администратора Netskope с разрешениями администратора.

## <a name="assigning-users-to-netskope-administrator-console"></a>Назначение пользователей консоль администратора Netskope

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к консоли администратора Netskope. После решения вы можете назначить этих пользователей и/или групп консоли Netskope Administrator Console, следуя инструкциям здесь:
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Важные советы по назначению пользователей консоли администратора Netskope

* Для тестирования автоматической конфигурации подготовки пользователей рекомендуется назначить одного пользователя Azure AD консоли Netskope administrator Console. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя консоли администратора Netskope необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге заданий. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Настройка консоли администратора Netskope для подготовки

1. Вопийте на [консоль Администратора Netskope Администратор админ консоли](https://netskope.goskope.com/). Перейдите к **настройкам > дома.**

    ![Netskope Администратор Консоль Администратор Администратор Консоль](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Перейдите к **инструментам**. В меню **Инструменты** перейдите к **каталогу Инструменты > SCIM INTEGRATION**.

    ![Инструменты консоли АдминистраторNetsе](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Консоль Netskope Администратор Добавить SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Прокрутите вниз и нажмите на кнопку **Добавить маркер.** В диалоговом окне **Add OAuth Name Client Name** предоставьте CLIENT **NAME** и нажмите на кнопку **«Сохранить».**

    ![Консоль Администратор Netskope Добавить токен](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope Администратор Консоль CLient Имя](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Копирование **URL-адреса СЕРВЕРа SCIM** и **TOKEN**. Эти значения будут вводиться в поля URL-адресов арендатора и секретные маркеры соответственно в вкладке «Обеспечение безопасности консоли Netskope» на портале Azure.

    ![Консоль Администратор Netskope Создать токен](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Добавление консоли администратора Netskope из галереи

Прежде чем настроить консоль Netskope Administrator Console для автоматического предоставления пользовательской подготовки с помощью Azure AD, необходимо добавить консоль Netskope Administrator Console из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить консоль Netskope Administrator из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **консоль Netskope Administrator Console,** выберите **консоль Netskope Administrator Console** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![Консоль администратора Netskope в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Настройка автоматического обеспечения пользователя на консоль администратора Netskope 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в консоли Netskope Administrator Console на основе пользовательских и/или групповых заданий в Azure AD.

> [!TIP]
> Вы также можете включить SAML на основе одного включения для Netskope Администратор Консоль, следуя инструкциям, представленным в [Netskope Администратор Консоль Одиночный набор.](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial) Единый набор может быть настроен независимо от автоматического подготовки пользователей, хотя эти две функции дополняют друг друга.

> [!NOTE]
> Чтобы узнать больше о МИМ-конечной точке консоли администратора Netskope, [обратитесь](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)к этому .

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Для настройки автоматической подготовки пользователей для консоли Netskope Administrator в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Netskope Administrator Console**.

    ![Консольадминистратор Netskope в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **"Учетные данные администратора"** ввод стоимости **URL-адреса SCIM Server,** извлеченной ранее в **URL-адресе Арендатора.** Ввейте значение **TOKEN,** извлеченное ранее в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к консоли администратора Netskope. Если соединение выходит из строя, убедитесь, что ваша учетная запись Netskope Administrator Console имеет разрешения администратора и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure на консоль Netskope Administrator Console.**

    ![Netskope Администратор Консоль Пользовательские картирования](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с консолью администраторов Netskope в разделе **Атрибуткартирования.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в консоли Netskope Administrator для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты консоли Netskope Администратор](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. В разделе **Картпинги** выберите **синхронизацию групп активных каталогов Azure на консоль Netskope Administrator Console.**

    ![Netskope Администратор Консоль группы Картпинги](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с консолью администраторов Netskope в разделе **Атрибут картирования.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления групп в консоли администратора Netskope для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты консоли Netskope Administrator](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления AD Azure AD для консоли администратора Netskope, измените **статус подготовки** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить консоли администратора Netskope, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD на консоли администратора Netskope.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

