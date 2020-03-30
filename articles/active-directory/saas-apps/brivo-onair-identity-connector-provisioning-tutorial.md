---
title: 'Учебник: Накондиция Brivo Onair Identity Connector для автоматического предоставления пользовательского интерфейса с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей в Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: f3c1e7337c0ce07b7fbebb9f954deeb75f0b9584
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246659"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Учебник: Налаживание разъема для идентификации Brivo Onair для автоматического подготовки пользователей

Цель этого руководства состоит в том, чтобы продемонстрировать шаги, которые будут выполнены в Brivo Onair Identity Connector и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователей и/или групп в Brivo Onair Identity Connector.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Арендатор разъема для идентификации Brivo Onair](https://www.brivo.com/lp/quote)
* Учетная запись пользователя в подъемике идентификации Brivo Onair с разрешениями старшего администратора.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Назначение пользователей в разъем идентификации Brivo Onair

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к разъединению идентификации Brivo Onair. После решения вы можете назначить этих пользователей и/или групп в разъем идентификации Brivo Onair, следуя инструкциям здесь:
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Важные советы по назначению пользователей в Разъем идентификации Brivo Onair

* Для тестирования автоматической конфигурации подготовки пользователей рекомендуется назначить одного пользователя Azure AD в подъемике идентификации Brivo Onair. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Brivo Onair Identity Connector необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге о назначении. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Настройка Brivo Onair Identity Connector для подготовки

1.    Войти на [консоль для подключения к разъединению Brivo Onair Identity.](https://acs.brivo.com/login/) Перейдите к **настройкам учетной записи > учетной записи.**

    ![Бриво Onair Identity Разъем Админ консоль](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2.  Нажмите на вкладку **Azure AD.** На странице данных **Azure AD** повторно введите пароль вашей учетной записи старшего администратора. Нажмите на **отправку**.

    ![Brivo Onair Identity Connector лазурный](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3.    Нажмите на кнопку **Copy Token** и сохраните **секретный маркер.** Это значение будет введено в поле Secret Token в вкладке «Подготовка» приложения Brivo Onair Identity Connector на портале Azure.

    ![Токен подключаемого ключа Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Добавить Brivo Onair Identity Connector из галереи

Прежде чем настроить подъезжающий к идентификационным данным Brivo Onair для автоматического оформления приложений с Azure AD, необходимо добавить разъем идентификационных данных Brivo Onair из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить разъем идентификационных данных Brivo Onair из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **подъезжающий к идентификационным данным Brivo Onair,** выберите **подъему для идентификации Brivo Onair** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![Разъем идентификации Brivo Onair в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Настройка автоматического подключения пользователя к разъединению идентификации Brivo Onair 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отключения пользователей и/или групп в подъеме brivo Onair на основе пользовательских и/или групповых заданий в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Для настройки автоматического подключения пользователей к разъединению идентификации Brivo Onair в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **подъезжающий к идентификационным вопросам Brivo Onair.**

    ![Ссылка на идентификационный соединитель Brivo Onair в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** `https://scim.brivo.com/ActiveDirectory/v2/` ввод **URL-адреса клиента**. Ввейте значение **токенов подлинности SCIM,** извлеченное ранее в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к identity-коннектору Brivo Onair. Если подключение не удается, убедитесь, что ваша учетная запись Brivo Onair Identity Connector имеет разрешения admin и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в Brivo Onair Identity Connector.**

    ![Brivo Onair Identity Connector Пользовательские карты](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с разъемом идентификации Brivo Onair в разделе **Атрибуткарт.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в подъемике идентификации Brivo Onair для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя подключаемых удостоверений личности Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. В разделе **Картпинги** выберите **синхронизацию активных групп каталогов Azure в brivo Onair Identity Connector.**

    ![Brivo Onair Identity Connector Group Картпинги](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с разъемом идентификации Brivo Onair в разделе **Атрибуткарт.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для соответствия группам в разъеме идентификации Brivo Onair для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты группы подключаемых удостоверений Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления AD Azure AD для разъема идентификации Brivo Onair, измените **статус подготовки** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить разъему для идентификации Brivo Onair, выбрав желаемые значения в **разделе Область** в разделе **Настройки.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD на разъеме идентификации Brivo Onair.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

