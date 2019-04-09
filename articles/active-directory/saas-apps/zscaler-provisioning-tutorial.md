---
title: Руководство по Настроить для автоматической подготовки пользователей Azure Active Directory с Zscaler | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Zscaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 3ea502477cc5b380c99a183d9270c2b2e94375a8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275490"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Руководство по Настройка Zscaler для автоматической подготовки пользователей

Цель данного учебника — продемонстрировать действия, выполняемые в Zscaler и Azure Active Directory (Azure AD) для настройки Azure AD для автоматической подготовки и отзыва пользователей и групп в Zscaler.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Сейчас этот соединитель доступен в общедоступной предварительной версии. Дополнительные сведения о общие Microsoft Azure условия использования предварительных версий функций, см. в разделе [дополнительным условиям использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Технические условия

Сценарий, описанный в этом руководстве, предполагает, что у вас уже имеется:

* клиент Azure AD;
* Клиента Zscaler
* Учетную запись пользователя в Zscaler с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от Zscaler SCIM API, которая доступна разработчикам Zscaler для учетных записей с помощью пакета Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Добавление Zscaler из коллекции

Перед настройкой Zscaler для автоматической подготовки пользователей в Azure AD, необходимо добавить Zscaler из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Zscaler из коллекции приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Zscaler**, выберите **Zscaler** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Zscaler в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Назначение пользователей в Zscaler

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, следует решить, какие пользователи или группы в Azure AD требуется доступ к Zscaler. Сделав это, можно назначить этих пользователей и групп в Zscaler, следуя инструкциям ниже:

* [Назначение пользователя или группы корпоративному приложению](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Важные рекомендации по назначению пользователей в Zscaler

* Рекомендуется один назначенный пользователем Azure AD Zscaler для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Zscaler, необходимо выбрать действительную роль конкретного приложения (если доступно) в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Настройка автоматической подготовки пользователей в Zscaler

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD для создания, изменения и отключения пользователей и групп в Zscaler на основе назначений в Azure AD.

> [!TIP]
> Вы также можете включить на основе SAML единого входа для Zscaler, следуйте инструкциям, указанным на [Zscaler входа в руководстве по настройке единого](zscaler-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Zscaler в Azure AD.

1. Войдите в [портала Azure](https://portal.azure.com) и выберите **корпоративные приложения**выберите **все приложения**, а затем выберите **Zscaler**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Zscaler**.

    ![Ссылка на Zscaler в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. В разделе **учетные данные администратора** введите **URL-адрес клиента** и **секретный токен** учетной Zscaler, как описано на шаге 6.

6. Для получения **URL-адрес клиента** и **секретный токен**, перейдите к **Администрирование > Параметры проверки подлинности** в Zscaler пользовательского интерфейса портала и щелкнуть **SAML** под **тип проверки подлинности**.

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Щелкните **настроить SAML** открыть **конфигурации SAML** параметры.

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Выберите **подготовки Enable SCIM-Based** извлекаемого **базовый URL-адрес** и **маркера носителя**, затем сохраните параметры. Копировать **базовый URL-адрес** для **URL-адрес клиента**, и **маркера носителя** для **секретный токен** на портале Azure.

7. После заполнения полей, указанных в шаге 5, щелкните **проверить подключение** и убедиться, что Azure AD может подключиться к Zscaler. Если подключение отсутствует, убедитесь, что учетная запись Zscaler имеет разрешения администратора и повторите попытку.

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/test-connection.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/notification.png)

9. Выберите команду **Сохранить**.

10. В разделе **сопоставления** выберите **синхронизировать пользователей Azure Active Directory с Zscaler**.

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Zscaler в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления учетных записей пользователей в Zscaler для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. В разделе **сопоставления** выберите **синхронизировать группы Azure Active Directory в Zscaler**.

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Просмотрите атрибуты группы, которые синхронизированы из Azure AD в Zscaler в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления групп в Zscaler для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](./../active-directory-saas-scoping-filters.md).

15. Чтобы включить службу для Zscaler подготовки Azure AD, измените **состояние подготовки** для **на** в **параметры** раздел.

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Определите пользователей или группы, которые вы хотите подготовить для Zscaler, выбрав нужные значения в **область** в **параметры** раздел.

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/scoping.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Zscaler Подготовка](./media/zscaler-provisioning-tutorial/save-provisioning.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для подготовки отчетов действия, в которых зафиксированы все действия, выполняемые службой Zscaler подготовки Azure AD.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте, как просматривать журналы и получать отчеты о действиях по подготовке](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
