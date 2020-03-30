---
title: 'Учебник: Настройка Keeper Password Manager & Digital Vault для автоматического предоставления пользовательского интерфейса с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей Keeper Password Manager & Digital Vault.
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
ms.openlocfilehash: 236527a9889879f872ef8c3867a7ec3c1b1ba0a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057537"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Учебник: Настройка Keeper Password Manager & Digital Vault для автоматического подготовки пользователей

Цель этого руководства состоит в том, чтобы продемонстрировать шаги, которые должны быть выполнены в Keeper Password Manager & Digital Vault и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователей и/или групп менеджеру по защите паролей & Digital Vault.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Менеджер по паролям Keeper & арендатор digital Vault](https://keepersecurity.com/pricing.html?t=e)
* Учетная запись пользователя в Keeper Password Manager & Digital Vault с разрешениями администратора.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Добавить Keeper Пароль менеджер & цифровое убежище из галереи

Перед настройкой Keeper Password Manager & Digital Vault для автоматического предоставления пользовательского обеспечения с помощью Azure AD необходимо добавить диспетчера паролей Хранителя & Digital Vault из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Keeper Password Manager & Digital Vault из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Keeper Password Manager & Digital Vault,** выберите Keeper Password Manager & Digital **Vault** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![Keeper Password Manager & Digital Vault в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Назначение пользователей диспетчером паролем Keeper & Digital Vault

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD должны получить доступ к диспетчеру стержней хранителя & Digital Vault. После решения, вы можете назначить этих пользователей и / или групп Keeper Password Manager & Digital Vault, следуя инструкциям здесь:

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Важные советы по назначению пользователей диспетчеру паролем Keeper & Digital Vault

* Рекомендуется, чтобы один пользователь Azure AD был назначен диспетчеру парольной & Digital Vault для тестирования автоматической конфигурации обеспечения пользователя. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя keeper Password Manager & Digital Vault необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге о назначении. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Настройка автоматической подготовки пользователя к диспетчеру паролем Keeper & Digital Vault 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в Keeper Password Manager & Digital Vault на основе пользовательских и/или групповых заданий в Azure AD.

> [!TIP]
> Вы также можете включить SAML основе одного знака для Keeper Password Manager & Digital Vault, следуя инструкциям, представленным в [Keeper Password Manager & Цифровой Vault одного знака на учебник.](keeperpasswordmanager-tutorial.md) Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователя для Keeper Password Manager & Digital Vault в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Keeper Password Manager & Digital Vault**.

    ![Keeper Password Manager & Digital Vault в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Учетные данные администратора** ввейте **URL-адрес арендатора** и **секретный маркер** вашего keeper Password Manager & учетную запись Digital Vault, как описано в шаге 6.

6. Войти на [консоль Keeper Admin.](https://keepersecurity.com/console/#login) Нажмите на **Admin** и выберите существующий узло или создайте новый. Перейдите на вкладку «Подготовка» и выберите **«Метод** **добавления».**

    ![Хранитель Администратор Консоль](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Выберите **SCIM (Система управления кросс-доменами идентификации.**

    ![Keeper Добавить SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Нажмите **Создать Обеспечение токенов**.

    ![Keeper Создать конечную точку](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Копируйте значения **для URL** и **маркеров** и вставьте их в **URL-адрес арендатора** и **секретный токен** в Azure AD. Нажмите **Сохранить** для завершения подготовки установки на Keeper.

    ![Keeper Create Token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. После заполнения полей, показанных в шаге 5, щелкните **тестовое соединение,** чтобы обеспечить подключение Azure AD к диспетчеру парольной & Digital Vault. Если соединение не удается, убедитесь, что ваш Keeper Password Manager & учетную запись Digital Vault имеет разрешения администратора и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

9. Нажмите **Сохранить**.

10. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure to Keeper Password Manager & Digital Vault.**

    ![Картирование пользователей Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с Keeper Password Manager & Digital Vault в разделе **«Отображение атрибутов».** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Keeper Password Manager & Digital Vault для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. В разделе **Картпинги** выберите **синхронизацию групп активных каталогов Azure для диспетчера парольных & Digital Vault.**

    ![Картирование Keeper Group](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Просмотрите атрибуты группы, синхронизированные с Azure AD с диспетчером парольKeeper & Digital Vault в разделе **Атрибут картирования.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для соответствия группам в Keeper Password Manager & Digital Vault для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты keeper Group](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Чтобы включить службу предоставления Azure AD для диспетчера парольных & Digital Vault, измените **статус подготовки** **в** разделе **"Настройки".**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

16. Определите пользователей и/или группы, которые вы хотели бы предоставить диспетчеру паролем Keeper & Digital Vault, выбрав желаемые значения в **разделе Область** в разделе **Настройки.**

    ![Область действия подготовки](common/provisioning-scope.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD на Keeper Password Manager & Digital Vault.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Keeper Password Manager & Digital Vault требует, чтобы **электронные письма** и **имя пользователя** имели такое же исходное значение, так как любые обновления для любых атрибутов изменят другое значение.
* Keeper Password Manager & Digital Vault не поддерживает удаление пользователем, а только отключает. Пользователи-инвалиды будут отображаться как заблокированные в пользовательном доступе консоли Keeper Admin.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

