---
title: 'Учебник: Налаживание Proxyclick для автоматического подготовки пользователей с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей в Proxyclick.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 95cb0371c4b2181d8f09991fe6e652c0e939f3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063362"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Учебник: Настройка Proxyclick для автоматического подготовки пользователей

Цель этого руководства состоит в том, чтобы продемонстрировать шаги, которые будут выполнены в Proxyclick и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователям и/или группам Proxyclick.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Арендатор Proxyclick](https://www.proxyclick.com/pricing)
* Учетная запись пользователя в Proxyclick с разрешениями Admin.

## <a name="add-proxyclick-from-the-gallery"></a>Добавление Proxyclick из коллекции

Прежде чем настроить Proxyclick для автоматического предоставления пользовательу с Azure AD, необходимо добавить Proxyclick из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Proxyclick из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Proxyclick,** выберите **Proxyclick** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![Proxyclick в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Назначение пользователей Proxyclick

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к Proxyclick. После того, как решение, вы можете назначить этих пользователей и / или групп proxyclick, следуя инструкциям здесь:

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Важные советы по назначению пользователей Proxyclick

* Для тестирования автоматической конфигурации подготовки пользователя рекомендуется назначить одного пользователя Azure AD. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя Proxyclick необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге о назначении. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Настройка автоматической подготовки пользователя к Proxyclick 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в Proxyclick на основе пользовательских и/или групповых заданий в Azure AD.

> [!TIP]
> Вы также можете включить SAML основе одного включения для Proxyclick, следуя инструкциям, представленным в [proxyclick одного знака на учебник](proxyclick-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Для настройки автоматического пользовательского обеспечения для Proxyclick в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Proxyclick**.

    ![Ссылка на Proxyclick в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. Чтобы получить **URL-адрес арендатора** и **секретный маркер** вашей учетной записи Proxyclick, следуйте пошаге, описанной в шаге 6.

6. Вопийте на [консоль Proxyclick Admin.](https://app.proxyclick.com/login//?destination=%2Fdefault) Перейдите к **настройкам** > **Интеграции** > **Просмотр Marketplace**.

    ![Настройки proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Интеграция proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Выберите **Azure AD**. Нажмите кнопку **Установить**.

    ![Прокси-нажмите Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Установка Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Выберите **интеграцию пользовательского обеспечения** и нажмите **кнопку «Начать»**. 

    ![Прокси-клик Пользователь Обеспечение](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    Теперь uI конфигурации соответствующих параметров должен отображаться в **настройках интеграции.** > **Integrations** Выберите **настройки** в **Azure AD (Обеспечение пользователей)**.

    ![Proxyclick Создание](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Вы можете найти **URL-адрес арендатора** и **секретный токен** здесь.

    ![Proxyclick Создать токен](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. После заполнения полей, показанных в шаге 5, щелкните **тестовое соединение,** чтобы обеспечить подключение Azure AD к Proxyclick. Если соединение не удается, убедитесь, что ваша учетная запись Proxyclick имеет разрешения Admin и повторите попытку.

    ![Токен](common/provisioning-testconnection-tenanturltoken.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

9. Нажмите **Сохранить**.

10. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure для Proxyclick.**

    ![Картирование пользователей Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Просмотрите атрибуты пользователя, синхронизированные с Azure AD, с Proxyclick в разделе **Атрибуткарт.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Proxyclick для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователей Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Чтобы включить службу предоставления Azure AD для Proxyclick, измените **статус подготовки** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

15. Определите пользователей и/или группы, которые вы хотели бы предоставить Proxyclick, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

16. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD на Proxyclick.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Proxyclick **требует,** чтобы электронные письма и **userName** имели одинаковое исходное значение. Любые обновления для любых атрибутов изменят другое значение.
* Proxyclick не поддерживает подготовку для групп.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

