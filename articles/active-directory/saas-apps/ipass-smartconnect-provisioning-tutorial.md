---
title: 'Учебник: Налаживайте iPass SmartConnect для автоматического предоставления пользовательского запроса с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей в iPass SmartConnect.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: fb3a5d03f390b88f9856f03bdc97a35b845874ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057547"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Учебник: Налаживайте iPass SmartConnect для автоматического подготовки пользователей

Цель этого руководства состоит в том, чтобы продемонстрировать шаги, которые будут выполнены в iPass SmartConnect и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-обеспечения пользователей и/или групп iPass SmartConnect.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* Клиент Azure AD.
* [Арендатор iPass SmartConnect](https://www.ipass.com/buy-ipass/).
* Учетная запись пользователя в iPass SmartConnect с разрешениями Admin.

## <a name="assigning-users-to-ipass-smartconnect"></a>Назначение пользователей iPass SmartConnect

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к iPass SmartConnect. После принятия решения вы можете назначить этих пользователей и/или групп iPass SmartConnect, следуя инструкциям здесь:
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Важные советы по назначению пользователей iPass SmartConnect

* Для тестирования автоматической конфигурации подготовки пользователей рекомендуется назначить одного пользователя Azure AD iPass SmartConnect. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя iPass SmartConnect необходимо выбрать любую действительную роль приложения (если она доступна) в диалоге о назначении. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Настройка iPass SmartConnect для подготовки

Перед настройкой iPass SmartConnect для автоматического предоставления пользовательского запроса с помощью Azure AD необходимо получить информацию о конфигурации с консоли admin iPass SmartConnect:

1. Чтобы получить маркер носителя, необходимый для проверки подлинности на вашей цели iPass SmartConnect SCIM, обратитесь к самому первому разу, когда вы создали iPass SmartConnect, поскольку это значение предоставляется только тогда. 
2. Если у вас нет маркера носителя, опередите [группу поддержки iPass SmartConnect,](mailto:help@ipass.com) чтобы получить новый.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Добавить iPass SmartConnect из галереи

Чтобы настроить iPass SmartConnect для автоматического предоставления пользовательского запроса с помощью Azure AD, необходимо добавить iPass SmartConnect из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить iPass SmartConnect из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **iPass SmartConnect,** выберите **iPass SmartConnect** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![iPass SmartConnect в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Настройка автоматической подготовки пользователя к iPass SmartConnect 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отключения пользователей и/или групп в iPass SmartConnect на основе пользовательских и/или групповых заданий в Azure AD.

> [!TIP]
>  Вы также можете включить SAML на основе одного знака для BitaBI , следуя инструкциям, представленным в [iPass SmartConnect Единый набор.](ipasssmartconnect-tutorial.md) Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Для настройки автоматического пользовательского обеспечения для iPass SmartConnect в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **iPass SmartConnect**.

    ![Ссылка iPass SmartConnect в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** `https://openmobile.ipass.com/moservices/scim/v1` ввод **URL-адреса клиента**. Введите маркер носителя, полученный ранее в **Secret Token**. Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к iPass SmartConnect. Если подключение не удается, убедитесь, что ваша учетная запись iPass SmartConnect имеет разрешения для админов и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в iPass SmartConnect.**

    ![IPass SmartConnect Пользовательские картографы](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD на iPass SmartConnect в разделе **Атрибуткартирования.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в iPass SmartConnect для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![IPass SmartConnect Пользовательские картографы](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу предоставления AD Azure AD для iPass SmartConnect, измените **статус подготовки** **в** разделе **Настройки.**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей и/или группы, которые вы хотели бы предоставить iPass SmartConnect, выбрав желаемые значения в **разделе Область** в разделе **Настройки.**

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD на iPass SmartConnect.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* iPass SmartConnect принимает только имена пользователей, которые имеют свои домены, зарегистрированные в консоли iPass SmartConnect.  

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)
