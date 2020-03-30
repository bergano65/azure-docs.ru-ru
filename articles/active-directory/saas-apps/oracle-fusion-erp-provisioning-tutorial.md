---
title: 'Учебник: Налаживайте ERP Oracle Fusion для автоматического предоставления пользовательского запроса с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей Oracle Fusion ERP.
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
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061205"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Учебник: Налаживайте ERP Oracle Fusion для автоматического подготовки пользователей

Цель этого руководства – продемонстрировать шаги, которые будут выполняться в Oracle Fusion ERP и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователям и/или группам Oracle Fusion ERP.

> [!NOTE]
>  В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель доступен в режиме предварительной версии. Для получения дополнительной информации об общих условиях использования Microsoft Azure для функций Preview [см.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* Арендатор [Oracle Fusion ERP](https://www.oracle.com/applications/erp/).
* Учетная запись пользователя в ERP Oracle Fusion с разрешениями Admin.

## <a name="assign-users-to-oracle-fusion-erp"></a>Назначить пользователей Oracle Fusion ERP 
Активный каталог Azure использует концепцию, называемую назначениями, чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к ERP Oracle Fusion. После решения вы можете назначить этих пользователей и/или групп Oracle Fusion ERP, следуя инструкциям здесь:
 
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Важные советы по назначению пользователей в ErP Oracle Fusion 

 * Рекомендуется, чтобы один пользователь Azure AD был назначен erP Oracle Fusion для тестирования автоматической конфигурации подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя ERP Oracle Fusion необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге о назначении. Пользователи с ролью доступа по умолчанию исключены из подготовки.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Настройка ERP Oracle Fusion для подготовки

Прежде чем настроить Oracle Fusion ERP для автоматического предоставления пользовательского запроса с Помощью Azure AD, необходимо включить SCIM в подготовку к ERP Oracle Fusion.

1. Вопийте на консоль [Oracle Fusion ERP Admin](https://cloud.oracle.com/sign-in)

2. Нажмите навигатор на верхнем верхнем углу верхнего верха. Под **инструментами**, выберите **консоль безопасности**.

    ![Oracle Fusion ERP Добавить SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Перейдите к **пользователям**.
    
    ![Oracle Fusion ERP Добавить SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Сохраните имя пользователя и пароль для учетной записи пользователя admin, которые вы будете использовать для входа в консоль Oracle Fusion ERP. Эти значения должны быть введены в поля имени пользователя и **пароля** **Admin** в вкладке «Подготовка» приложения Oracle Fusion ERP на портале Azure.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Добавить Oracle Fusion ERP из галереи

Чтобы настроить Oracle Fusion ERP для автоматического предоставления пользовательского запроса с помощью Azure AD, необходимо добавить ERP Oracle Fusion из галереи приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить ERP Oracle Fusion из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Oracle Fusion ERP**, выберите **Oracle Fusion ERP** в панели результатов.

    ![Oracle Fusion ERP в списке результатов](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Настройка автоматического пкей кулачку для ErP Oracle Fusion 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в ERP Oracle Fusion на основе пользовательских и/или групповых заданий в Azure AD.

> [!TIP]
> Вы также можете включить SAML на основе одного знака для Oracle Fusion ERP, следуя инструкциям, представленным в [Oracle Fusion ERP Единый набор.](oracle-fusion-erp-tutorial.md) Единый набор может быть настроен независимо от автоматического подготовки пользователей, хотя эти две функции дополняют друг друга.

> [!NOTE]
> Чтобы узнать больше о прыге SCIM-класса ORACLE Fusion ERP, обратитесь к [REST API для общих функций в облаке приложений Oracle.](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Для настройки автоматической подготовки пользователя для взрывателя в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Oracle Fusion ERP**.

    ![Ссылка на Oracle Fusion ERP в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` ввод **URL-адреса клиента**. Введите имя пользователя и пароль admin, полученные ранее, в поля имени пользователя и **пароля** **Admin.** Нажмите на **тестовое соединение** между Azure AD и ERP Oracle Fusion. 

    ![Oracle Fusion ERP Добавить SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в ErP Oracle Fusion.**

    ![Oracle Fusion ERP Добавить SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с ERP Oracle Fusion в разделе **Атрибут-Картирование.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в ERP Oracle Fusion для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Oracle Fusion ERP Добавить SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. В разделе **Картпинги** выберите **синхронизацию активных групп каталогов Azure в ERP Oracle Fusion.**

    ![Картирование группы Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с ERP Oracle Fusion в разделе **Атрибуткартирования.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для соответствия группам в ERP Oracle Fusion для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты Oracle Fusion ERP Group](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления Azure AD для ERP Oracle Fusion, измените **статус подготовки** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить Oracle Fusion ERP, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

    После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD на ERP Oracle Fusion.

    Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Oracle Fusion ERP поддерживает только базовую аутентификацию для своей конечной точки SCIM.
* Oracle Fusion ERP не поддерживает групповое подготовка.
* Роли в ERP Oracle Fusion отображаются для групп в Azure AD. Чтобы назначить роли пользователям в ErP Oracle Fusion из Azure AD, необходимо назначить пользователей желаемым группам Azure AD, названным в честь ролей в ERP Oracle Fusion.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)
