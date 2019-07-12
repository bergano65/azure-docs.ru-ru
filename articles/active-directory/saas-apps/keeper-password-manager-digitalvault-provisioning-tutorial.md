---
title: Учебник. Настроить для автоматической подготовки пользователей Azure Active Directory с Keeper Password Manager & Digital Vault | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Keeper Password Manager & Digital Vault.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 74bfe37323a17bde19e4a9bf4ec28c9c3910b37f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666252"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Учебник. Настроить Keeper Password Manager & Digital Vault для автоматической подготовки пользователей

Цель данного руководства является демонстрация действия, выполняемые в Keeper Password Manager & Digital Vault и Azure Active Directory (Azure AD) для настройки Azure AD настроить автоматическую подготовку и отмену подготовки пользователей и групп в Keeper Password Manager & Digital Vault.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения о общие Microsoft Azure условия использования предварительных версий функций, см. в разделе [дополнительным условиям использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент Keeper Password Manager & Digital Vault](https://keepersecurity.com/pricing.html?t=e)
* Учетная запись пользователя в Keeper Password Manager & Digital Vault с разрешениями администратора.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Добавить Keeper Password Manager & Digital Vault из коллекции

Прежде чем настроить Keeper Password Manager & Digital Vault для автоматической подготовки пользователей с Azure AD, необходимо добавить Keeper Password Manager & Digital Vault из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Keeper Password Manager & Digital Vault из коллекции приложений Azure AD, выполните следующие действия:**

1. В  **[портала Azure](https://portal.azure.com)** , на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите **новое приложение** кнопку в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Keeper Password Manager & Digital Vault**выберите **Keeper Password Manager & Digital Vault** в панели результатов и нажмите кнопку **добавить**кнопку, чтобы добавить это приложение.

    ![Keeper Password Manager & Digital Vault в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Назначение пользователей в Keeper Password Manager & Digital Vault

Azure Active Directory используется концепция, называемая *назначения* для определения, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только те пользователи и группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, следует решить, какие пользователи или группы в Azure AD требуется доступ к Keeper Password Manager & Digital Vault. Сделав это, можно назначить этих пользователей и групп, к Keeper Password Manager & Digital Vault следуя приведенным ниже указаниям:

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Важные рекомендации по назначению пользователей в Keeper Password Manager & Digital Vault

* Рекомендуется одного пользователя Azure AD назначается Keeper Password Manager & Digital Vault для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Keeper Password Manager & Digital Vault, необходимо выбрать действительную роль конкретного приложения (если доступно) в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Настройка автоматической подготовки пользователей в Keeper Password Manager & Digital Vault 

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD для создания, изменения и отключения пользователей или групп в Keeper Password Manager & Digital Vault использованием назначений в Azure AD.

> [!TIP]
> Вы также можете включить на основе SAML единого входа для Keeper Password Manager & Digital Vault, следуйте инструкциям, указанным на [Keeper Password Manager & Digital Vault входа в руководстве по настройке единого](keeperpasswordmanager-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Keeper Password Manager & Digital Vault в Azure AD.

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Keeper Password Manager & Digital Vault**.

    ![Keeper Password Manager & Digital Vault в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Инициализация](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Инициализация](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите **URL-адрес клиента** и **секретный токен** из вашей Keeper Password Manager & Digital Vault учетной записи, как описано на шаге 6.

6. Войдите в ваш [консоли администрирования Keeper](https://keepersecurity.com/console/#login). Щелкните **администратора** и выберите существующий узел, или создайте новую. Перейдите к **Подготовка** и выберите **добавить метод**.

    ![Консоль администрирования keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Выберите **SCIM (системы для управления удостоверениями междоменных**.

    ![Keeper добавить SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Нажмите кнопку **Создание подготовки маркер**.

    ![Keeper Создание конечной точки](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Скопируйте значения параметров **URL-адрес** и **маркера** и вставьте их в **URL-адрес клиента** и **секретный токен** в Azure AD. Нажмите кнопку **Сохранить** для завершения подготовки установки на Keeper.

    ![Keeper создание токена](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. После заполнения полей, указанных в шаге 5, щелкните **проверить подключение** и убедиться, что Azure AD может подключиться к Keeper Password Manager & Digital Vault. Если подключение отсутствует, убедитесь, что учетная запись Keeper Password Manager & Digital Vault имеет разрешения администратора и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Уведомление по электронной почте](common/provisioning-notification-email.png)

9. Нажмите кнопку **Сохранить**.

10. В разделе **сопоставления** выберите **синхронизировать пользователей Azure Active Directory с Keeper Password Manager & Digital Vault**.

    ![Keeper сопоставления пользователей](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Keeper Password Manager & Digital Vault в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления учетных записей пользователей в Keeper Password Manager & Digital Vault для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. В разделе **сопоставления** выберите **синхронизировать группы Azure Active Directory к Keeper Password Manager & Digital Vault**.

    ![Сопоставление групп keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Просмотрите атрибуты группы, которые синхронизированы из Azure AD в Keeper Password Manager & Digital Vault в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления групп в Keeper Password Manager & Digital Vault для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты группы keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Чтобы включить службу для Keeper Password Manager & Digital Vault подготовки Azure AD, измените **состояние подготовки** для **на** в **параметры** раздел.

    ![Состояние подготовки на противоположное](common/provisioning-toggle-on.png)

16. Определите пользователей или группы, которые вы хотите для подготовки в Keeper Password Manager & Digital Vault, выбрав нужные значения в **область** в **параметры** раздел.

    ![Инициализация области](common/provisioning-scope.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Идет сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов, описывающий все действия, выполняемые службой на Keeper Password Manager подготовки Azure AD о подготовке & Digital Vault.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Keeper Password Manager & Digital Vault требует **сообщений электронной почты** и **userName** иметь одинаковое значение источника, все обновления, чтобы какие-либо атрибуты изменит другое значение.
* Keeper Password Manager & Digital Vault не поддерживает удаление пользователя, только отключить. Отключенных пользователей будет отображаться как заблокированная в пользовательском Интерфейсе консоли администратора Keeper.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Следующие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

