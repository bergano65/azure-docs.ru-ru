---
title: Учебник. Настройка Proxyclick для автоматической подготовки пользователей с Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Proxyclick.
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
ms.openlocfilehash: c1656e6cc0c690e5a2bccfd2efab02aa843875b8
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672888"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Учебник. Настройка Proxyclick для автоматической подготовки пользователей

Цель данного учебника — продемонстрировать действия, выполняемые в Proxyclick и Azure Active Directory (Azure AD) для настройки Azure AD для автоматической подготовки и отзыва пользователей и групп в Proxyclick.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения о общие Microsoft Azure условия использования предварительных версий функций, см. в разделе [дополнительным условиям использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент Proxyclick](https://www.proxyclick.com/pricing)
* Учетная запись пользователя в Proxyclick с разрешениями администратора.

## <a name="add-proxyclick-from-the-gallery"></a>Добавление Proxyclick из коллекции

Перед настройкой Proxyclick для автоматической подготовки пользователей в Azure AD, необходимо добавить Proxyclick из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Proxyclick из коллекции приложений Azure AD, выполните следующие действия:**

1. В  **[портала Azure](https://portal.azure.com)** , на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите **новое приложение** кнопку в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Proxyclick**выберите **Proxyclick** в панели результатов и нажмите кнопку **добавить** кнопку, чтобы добавить это приложение.

    ![Proxyclick в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Назначение пользователей в Proxyclick

Azure Active Directory используется концепция, называемая *назначения* для определения, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только те пользователи и группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, следует решить, какие пользователи или группы в Azure AD требуется доступ к Proxyclick. Сделав это, можно назначить этих пользователей и групп, чтобы Proxyclick следуя приведенным ниже указаниям:

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Важные рекомендации по назначению пользователей в Proxyclick

* Рекомендуется один назначенный пользователем Azure AD Proxyclick для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Proxyclick, необходимо выбрать действительную роль конкретного приложения (если доступно) в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Настройка автоматической подготовки пользователей для Proxyclick 

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD для создания, изменения и отключения пользователей и групп в Proxyclick на основе назначений в Azure AD.

> [!TIP]
> Вы также можете включить на основе SAML единого входа для Proxyclick, следуйте инструкциям, указанным на [Proxyclick входа в руководстве по настройке единого](proxyclick-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Proxyclick в Azure AD.

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Proxyclick**.

    ![Ссылка на Proxyclick в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Инициализация](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Инициализация](common/provisioning-automatic.png)

5. Для получения **URL-адрес клиента** и **секретный токен** учетной записи Proxyclick, следуйте указаниям пошагового руководства, как описано на шаге 6.

6. Войдите в ваш [консоли администрирования Proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Перейдите к **параметры** > **интеграции** > **просматривать Marketplace**.

    ![Параметры Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxyclick интеграции](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Выберите **Azure AD**. Нажмите кнопку **установить сейчас**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Установка Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Выберите **подготовки пользователей** и нажмите кнопку **начать интеграцию**. 

    ![Подготовка пользователей Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    Соответствующие параметры конфигурации, пользовательского интерфейса должны отображаться в разделе **параметры** > **интеграции**. Выберите **параметры** под **Azure AD (Подготовка пользователя)** .

    ![Создание Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Можно найти **URL-адрес клиента** и **секретный токен** здесь.

    ![Proxyclick создание токена](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. После заполнения полей, указанных в шаге 5, щелкните **проверить подключение** и убедиться, что Azure AD может подключиться к Proxyclick. Если подключение отсутствует, убедитесь, что учетная запись Proxyclick имеет разрешения администратора и повторите попытку.

    ![по маркеру](common/provisioning-testconnection-tenanturltoken.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Уведомление по электронной почте](common/provisioning-notification-email.png)

9. Нажмите кнопку **Сохранить**.

10. В разделе **сопоставления** выберите **синхронизировать пользователей Azure Active Directory с Proxyclick**.

    ![Сопоставления пользователей Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Proxyclick в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления учетных записей пользователей в Proxyclick для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Чтобы включить службу для Proxyclick подготовки Azure AD, измените **состояние подготовки** для **на** в **параметры** раздел.

    ![Состояние подготовки на противоположное](common/provisioning-toggle-on.png)

15. Определите пользователей или группы, которые вы хотите для подготовки к Proxyclick, выбрав нужные значения в **область** в **параметры** раздел.

    ![Инициализация области](common/provisioning-scope.png)

16. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Идет сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для подготовки отчетов действия, в которых зафиксированы все действия, выполняемые службой Proxyclick подготовки Azure AD.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Требуется Proxyclick **сообщений электронной почты** и **userName** иметь то же значение источника. Все обновления, чтобы какие-либо атрибуты изменит другое значение.
* Proxyclick не поддерживает подготовку для групп.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Следующие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

