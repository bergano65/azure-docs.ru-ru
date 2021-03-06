---
title: Учебник. Настройка Воркгрид для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в Воркгрид.
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
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: c714fa736375e36d68a528dfea82c2c4b7ef448f
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69908216"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>Учебник. Настройка Воркгрид для автоматической подготовки пользователей

Цель этого руководства — продемонстрировать шаги, которые необходимо выполнить в Воркгрид и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и групп в Воркгрид.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих Microsoft Azure условиях использования предварительных версий функций см. в разделе Дополнительные [условия использования для предварительного просмотра Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* Клиент Azure AD.
* [Клиент Воркгрид](https://www.workgrid.com/)
* Учетная запись пользователя в Воркгрид с разрешениями администратора.

## <a name="assigning-users-to-workgrid"></a>Назначение пользователей в Воркгрид 

Azure Active Directory использует концепцию, называемую назначениями, чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, какие пользователи и (или) группы в Azure AD должны иметь доступ к Воркгрид. После принятия решения вы можете назначить этих пользователей и (или) группы для Воркгрид, следуя приведенным ниже инструкциям.
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>Важные советы по назначению пользователей в Воркгрид 

* Рекомендуется назначить одного пользователя Azure AD в Воркгрид для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Воркгрид необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="set-up-workgrid-for-provisioning"></a>Настройка Воркгрид для подготовки

Перед настройкой Воркгрид для автоматической подготовки пользователей с помощью Azure AD необходимо включить подготовку SCIM для Воркгрид.

1. Войдите в Воркгрид. Перейдите к **пользователям > подготовки пользователей**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/user.png)

2. В разделе **API управления учетными записями**щелкните **создать учетные данные**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/scim.png)

3. Скопируйте значения **конечной точки scim** и **маркера доступа** . Они будут указаны в поле **URL-адрес клиента** и **секретный токен** на вкладке подготовка приложения воркгрид в портал Azure.

    ![Workgrid](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>Добавление Воркгрид из коллекции

Чтобы настроить Воркгрид для автоматической подготовки пользователей с помощью Azure AD, необходимо добавить Воркгрид из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Воркгрид из коллекции приложений Azure AD, выполните следующие действия.**

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **воркгрид**, выберите **воркгрид** на панели результатов и нажмите кнопку **добавить** , чтобы добавить это приложение.

    ![Воркгрид в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Настройка автоматической подготовки пользователей в Воркгрид  

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в Воркгрид на основе назначений пользователей и групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для Воркгрид, следуя инструкциям, приведенным в руководстве по единому [входу воркгрид](Workgrid-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Воркгрид в Azure AD, сделайте следующее:

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Workgrid**.

    ![Ссылка на Воркгрид в списке "приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе Учетные данные администратора введите **конечную точку scim** и значения **маркера доступа** , полученные ранее в поле **URL-адрес клиента** и **секретный токен** соответственно. Щелкните **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к воркгрид. Если подключение не выполняется, убедитесь, что у учетной записи Воркгрид есть разрешения администратора, и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с воркгрид**.

    ![Сопоставления пользователей воркгрид](media/Workgrid-provisioning-tutorial/usermapping.png)

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в Воркгрид в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства Matching, используются для сопоставления учетных записей пользователей в воркгрид для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя воркгрид](media/Workgrid-provisioning-tutorial/userattribute.png)

10. В разделе **сопоставления** выберите **синхронизировать группы Azure Active Directory с воркгрид** .

    ![Сопоставления пользователей воркгрид](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. Проверьте атрибуты группы, которые синхронизированы из Azure AD в Воркгрид в разделе " **сопоставление атрибутов** ". Атрибуты, выбранные как свойства Matching, используются для сопоставления учетных записей пользователей в воркгрид для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Сопоставления пользователей воркгрид](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Чтобы включить службу подготовки Azure AD для Воркгрид, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки выключено](common/provisioning-toggle-on.png)

15. Определите пользователей и (или) группы, которые вы хотите подготавливать к Воркгрид, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область подготовки](common/provisioning-scope.png)

16. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Идет сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Дополнительные сведения о том, как долго будет выполняться для пользователей и (или) групп, см. в разделе [время, необходимое для предоставления пользователям](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Вы можете использовать **Текущий раздел состояния** для отслеживания хода выполнения и перехода по ссылкам к отчету о действиях по подготовке, в котором описаны все действия, выполняемые службой подготовки Azure AD в воркгрид. Дополнительные сведения см. [в разделе Проверка состояния подготовки пользователей](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Чтобы прочитать журналы подготовки Azure AD, см. раздел [Создание отчетов об автоматической подготовке учетных записей пользователей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Следующие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
