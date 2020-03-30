---
title: 'Учебник: Налаживайте Signagelive для автоматического подготовки пользователей с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей Signagelive.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: bbd0d3c60c5cc4056d5cbadfc7c6d90ae2a37ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063260"
---
# <a name="tutorial-configure-signagelive--for-automatic-user-provisioning"></a>Учебник: Настройка Signagelive для автоматического подготовки пользователей

Цель этого руководства состоит в том, чтобы продемонстрировать шаги, которые должны быть выполнены в Signagelive и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователям и/или группам Signagelive.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* Клиент Azure AD.
* [Вывескиliveт арендатора](https://signagelive.com/pricing/)
* Учетная запись пользователя в Signagelive с разрешениями Admin.

## <a name="assigning-users-to-signagelive"></a>Назначение пользователей Signagelive   

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к Signagelive. После того, как решение, вы можете назначить этих пользователей и / или групп Signagelive, следуя инструкциям здесь:
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-signagelive"></a>Важные советы по назначению пользователей Signagelive   

* Для тестирования автоматической конфигурации подготовки пользователей рекомендуется назначить одного пользователя Azure AD Signagelive. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя Signagelive необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге о назначении. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="setup-signagelive--for-provisioning"></a>Настройка Signagelive для подготовки

Перед настройкой Signagelive для автоматического подготовки пользователей с Azure AD необходимо включить SCIM-подготовку на Signagelive.

  Обращайся к [Signagelive,](mailto:development@signagelive.com) чтобы получить секретный токен, необходимый для настройки SCIM подготовки.

## <a name="add-signagelive-from-the-gallery"></a>Добавление Signagelive из коллекции

Чтобы настроить Signagelive для автоматического оформления пользовательского интерфейса с помощью Azure AD, необходимо добавить Signagelive из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Signagelive из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска, введите **Signagelive**, выберите **Signagelive** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![Signagelive в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-signagelive"></a>Настройка автоматического пользовательского подготовки к Signagelive    

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отключения пользователей и/или групп в Signagelive на основе пользовательских и/или групповых заданий в Azure AD.

> [!TIP]
>  Вы также можете включить SAML основе одного знака для Signagelive, следуя инструкциям, представленным в [Signagelive Один знак на учебник](Signagelive-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-signagelive--in-azure-ad"></a>Для настройки автоматического пользовательского обеспечения для Signagelive в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Signagelive**.

    ![Ссылка на Signagelive в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе Admin Credentials ` https://samlapi.signagelive.com/scim/v2` ввод **URL-адреса клиента**. В поле **Secret Token** введите значение **token,** предоставленное командой разработки инженерных разработок. Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к Signagelive. Если соединение не удается, убедитесь, что ваша учетная запись Signagelive имеет разрешения admin и повторите попытку.
    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure signagelive.**

    ![Signagelive Пользовательские картографы](media/signagelive-provisioning-tutorial/usermapping.png)

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD на Signagelive в разделе **Атрибуткарт.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Signagelive для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Signagelive Атрибуты пользователей](media/signagelive-provisioning-tutorial/userattribute.png)

10. В разделе **Картпинги** выберите **синхронизацию группы активных каталогов Azure signagelive.**

    ![Signagelive Пользовательские картографы](media/signagelive-provisioning-tutorial/groupmapping.png)

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с Signagelive в разделе **Атрибуткарт.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей группы в Signagelive для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Signagelive Атрибуты пользователей](media/signagelive-provisioning-tutorial/groupattribute.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления Azure AD для Signagelive, измените **статус подготовки** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить Signagelive, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие синхронизации. Для получения дополнительной информации о том, сколько времени потребуется для пользователей и / или групп для предоставления, посмотреть, [сколько времени потребуется для предоставления пользователям](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

Вы можете использовать раздел **Текущее состояние** для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD на Signagelive. Для получения дополнительной [информации, см.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Чтобы прочитать журналы подготовки Azure AD, [см.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)
