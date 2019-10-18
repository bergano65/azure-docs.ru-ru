---
title: Учебник. Настройка соединителя meta Networks для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в соединителе meta Networks.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 40927597031205b5fb1bcc5869922bcc4f3f265c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518737"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Учебник. Настройка соединителя meta Networks для автоматической подготовки пользователей

Цель этого учебника — продемонстрировать шаги, которые необходимо выполнить в соединителе мета-сетей и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и (или) групп в соединитель meta Networks.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих Microsoft Azure условиях использования предварительных версий функций см. в разделе Дополнительные [условия использования для предварительного просмотра Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Технические условия

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент соединителя мета-сетей](https://www.metanetworks.com/)
* Учетная запись пользователя в соединителе meta Networks с разрешениями администратора.

## <a name="assigning-users-to-meta-networks-connector"></a>Назначение пользователей соединителю meta Networks

Azure Active Directory использует концепцию, называемую *назначениями* , чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, каким пользователям и (или) группам в Azure AD требуется доступ к соединителю meta Networks. После этого можно назначить этих пользователей или группы соединителю meta Networks, выполнив приведенные здесь инструкции.
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Важные советы по назначению пользователей в соединитель meta Networks

* Рекомендуется назначить одного пользователя Azure AD соединителю meta Networks для проверки конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в соединитель meta Networks необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Настройка соединителя meta Networks для подготовки

1. Войдите в [консоль администрирования соединителя meta Networks](https://login.metanetworks.com/login/) , используя название вашей организации. Перейдите в **раздел администрирование > ключи API**.

    ![Консоль администрирования соединителя meta Networks](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Щелкните знак "плюс" в верхней правой части экрана, чтобы создать **ключ API**.

    ![Значок соединителя meta Networks Plus](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Задайте **имя ключа API** и **Описание ключа API**.

    ![Маркер создания соединителя meta Networks](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Включите привилегии **записи** для **групп** и **пользователей**.

    ![Привилегии соединителя meta Networks](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Щелкните **Добавить**. Скопируйте **секрет** и сохраните его, так как это будет единственный момент, когда вы сможете его просмотреть. Это значение будет указано в поле Секретный токен на вкладке Подготовка приложения соединителя meta Networks в портал Azure.

    ![Маркер создания соединителя meta Networks](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  Добавьте IdP, перейдя в **> администрирование параметры > IdP > создать новый**.

    ![Соединитель meta Networks Add IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  На странице **Конфигурация IDP** можно задать **имя** конфигурации IDP и выбрать **значок**.

    ![Имя IdP соединителя meta Networks](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Значок IdP соединителя meta Networks](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  В разделе **Настройка scim** выберите имя ключа API, созданное на предыдущих шагах. Щелкните **Save**(Сохранить).

    ![Соединитель meta Networks configure SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Перейдите в раздел **администрирование > параметры > вкладку IDP**. Щелкните имя конфигурации IdP, созданной на предыдущих шагах, чтобы просмотреть **идентификатор IDP**. Этот **идентификатор** добавляется в конец **URL-адреса клиента** при вводе значения в поле **URL-адрес клиента** на вкладке Подготовка приложения соединителя meta Networks в портал Azure.

    ![Идентификатор IdP соединителя meta Networks](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Добавление соединителя meta Networks из коллекции

Перед настройкой соединителя meta Networks для автоматической подготовки пользователей с помощью Azure AD необходимо добавить соединитель meta Networks из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить соединитель meta Networks из коллекции приложений Azure AD, выполните следующие действия.**

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **соединитель meta Networks**, выберите **соединитель meta Networks** на панели результатов и нажмите кнопку **добавить** , чтобы добавить это приложение.

    ![Meta Networks Connector в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Настройка автоматической подготовки пользователей в соединителе meta Networks 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в соединителе meta-сетей на основе назначений пользователей и групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для соединителя meta Networks, следуя инструкциям, приведенным в [руководстве по единому входу в соединители meta-сетей](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей для соединителя meta Networks в Azure AD, сделайте следующее:

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Meta Networks Connector**.

    ![Ссылка на Meta Networks Connector в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите `https://api.metanetworks.com/v1/scim/<IdP ID>` в поле **URL-адрес клиента**. Введите значение **маркера проверки подлинности scim** , полученное ранее в **маркере секрета**. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к соединителю meta-сетей. Если подключение не выполняется, убедитесь, что у учетной записи соединителя meta Networks есть разрешения администратора, и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Уведомление по электронной почте** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Уведомление по электронной почте](common/provisioning-notification-email.png)

7. В нижней части страницы нажмите кнопку **Save**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с соединителем meta Networks**.

    ![Сопоставления пользователей соединителя meta Networks](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в соединитель meta Networks, в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в соединителе meta Networks для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя соединителя meta Networks](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы с соединителем meta Networks**.

    ![Сопоставления группы соединителей meta Networks](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Проверьте атрибуты группы, которые синхронизированы из Azure AD в соединитель meta Networks, в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в соединителе meta Networks для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты группы соединителей meta Networks](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для соединителя meta Networks, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки выключено](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать к соединителю мета-сетей, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Идет сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов по подготовке, в которых описаны все действия, выполняемые в соединителе службы подготовки Azure AD для соединителя meta Networks.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

