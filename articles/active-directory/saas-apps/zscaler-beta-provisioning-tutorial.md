---
title: Руководство по Настроить для автоматической подготовки пользователей Azure Active Directory с Zscaler Beta | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Zscaler Beta.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 54cf2924a94dda1d29baf048c866f019b82e1402
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256844"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Руководство по Настройка Zscaler Beta для автоматической подготовки пользователей

Цель данного учебника — продемонстрировать действия, выполняемые в Zscaler Beta и Azure Active Directory (Azure AD) для настройки Azure AD для автоматической подготовки и отзыва пользователей и групп в Zscaler Beta.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Сейчас этот соединитель доступен в общедоступной предварительной версии. Дополнительные сведения о общие Microsoft Azure условия использования предварительных версий функций, см. в разделе [дополнительным условиям использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Технические условия

Сценарий, описанный в этом руководстве, предполагает, что у вас уже имеется:

* клиент Azure AD;
* Клиент Zscaler Beta
* Учетную запись пользователя в Zscaler Beta с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от Zscaler Beta SCIM API, которая доступна разработчикам Zscaler Beta для учетных записей с помощью пакета Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Добавление Zscaler Beta из коллекции

Перед настройкой Zscaler Beta для автоматической подготовки пользователей в Azure AD, необходимо добавить Zscaler Beta из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Zscaler Beta из коллекции приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Zscaler Beta**, выберите **Zscaler Beta** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Zscaler Beta в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Назначение пользователей в Zscaler Beta

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, следует решить, какие пользователи или группы в Azure AD требуется доступ к Zscaler Beta. Сделав это, можно назначить этих пользователей и групп в Zscaler Beta, следуя инструкциям ниже:

* [Назначение корпоративному приложению пользователя или группы](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Важные рекомендации по назначению пользователей в Zscaler Beta

* Рекомендуется одного пользователя Azure AD, назначается для тестирования конфигурации автоматической подготовки пользователей в Zscaler Beta. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Zscaler Beta, необходимо выбрать действительную роль конкретного приложения (если доступно) в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Настройка автоматической подготовки пользователей в Zscaler Beta

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD для создания, изменения и отключения пользователей и групп в Zscaler Beta на основе назначений в Azure AD.

> [!TIP]
> Вы также можете включить на основе SAML единого входа для Zscaler Beta, следуйте инструкциям, указанным на [Zscaler Beta входа в руководстве по настройке единого](zscaler-beta-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Zscaler Beta в Azure AD.

1. Войдите в [портала Azure](https://portal.azure.com) и выберите **корпоративные приложения**выберите **все приложения**, а затем выберите **Zscaler Beta**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Zscaler Beta**.

    ![Ссылка на Zscaler Beta в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. В разделе **учетные данные администратора** введите **URL-адрес клиента** и **секретный токен** учетной Zscaler Beta, как описано на шаге 6.

6. Для получения **URL-адрес клиента** и **секретный токен**, перейдите к **Администрирование > Параметры проверки подлинности** в Zscaler Beta пользовательского интерфейса портала и щелкнуть  **SAML** под **тип проверки подлинности**.

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Щелкните **настроить SAML** открыть **конфигурации SAML** параметры.

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Выберите **подготовки Enable SCIM-Based** извлекаемого **базовый URL-адрес** и **маркера носителя**, затем сохраните параметры. Копировать **базовый URL-адрес** для **URL-адрес клиента**, и **маркера носителя** для **секретный токен** на портале Azure.

7. После заполнения полей, указанных в шаге 5, щелкните **проверить подключение** и убедиться, что Azure AD может подключиться к Zscaler Beta. Если подключение отсутствует, убедитесь, что учетная запись Zscaler Beta имеет разрешения администратора и повторите попытку.

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Выберите команду **Сохранить**.

10. В разделе **сопоставления** выберите **синхронизировать пользователей Azure Active Directory с Zscaler Beta**.

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Zscaler Beta в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления учетных записей пользователей в Zscaler Beta для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. В разделе **сопоставления** выберите **синхронизировать группы Azure Active Directory в Zscaler Beta**.

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Просмотрите атрибуты группы, которые синхронизированы из Azure AD в Zscaler Beta в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления групп в Zscaler Beta для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](./../active-directory-saas-scoping-filters.md).

15. Чтобы включить службу для Zscaler Beta подготовки Azure AD, измените **состояние подготовки** для **на** в **параметры** раздел.

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Определите пользователей или группы, которые вы хотите подготовить для Zscaler Beta, выбрав нужные значения в **область** в **параметры** раздел.

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Zscaler Beta Подготовка](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов, которых зафиксированы все действия, выполняемые службой Zscaler Beta подготовки Azure AD о подготовке.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
