---
title: Руководство. Настройка частного доступа Zscaler (ZPA) для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в Zscaler закрытый доступ (ZPA).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: c5274f3c38782394b01f8f29c56823753783307c
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287727"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Руководство. Настройка частного доступа Zscaler (ZPA) для автоматической подготовки пользователей

Цель этого учебника — продемонстрировать шаги, которые необходимо выполнить в Zscaler Private Access (ZPA) и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и (или) групп для Zscaler закрытого доступа (ZPA).

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>предварительным требованиям

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент Zscaler Private Access (ZPA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Учетная запись пользователя в Zscaler Private Access (ZPA) с разрешениями администратора.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Назначение пользователей Zscaler закрытому доступу (ZPA)

Azure Active Directory использует концепцию, называемую *назначениями* , чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, каким пользователям и (или) группам в Azure AD требуется доступ к Zscaler Private Access (ZPA). После принятия решения эти пользователи и (или) группы можно будет назначить Zscaler закрытому доступу (ZPA), следуя приведенным ниже инструкциям.
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Важные советы по назначению пользователей для Zscaler закрытого доступа (ZPA)

* Рекомендуется назначить одного пользователя Azure AD в Zscaler Private Access (ZPA) для проверки конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Zscaler Private Access (ZPA) необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Настройка частного доступа Zscaler (ZPA) для подготовки

1. Войдите в [консоль администрирования Zscaler Private Access (ZPA)](https://admin.private.zscaler.com/). Перейдите в раздел **администрирование > конфигурация IDP**.

    ![Консоль администрирования Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Убедитесь, что настроен IdP для **единого входа** . Если IdP не настроен, добавьте его, щелкнув значок "плюс" в правом верхнем углу экрана.

    ![Zscaler частный доступ (ZPA) Add SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Чтобы добавить IdP, выполните действия, описанные в мастере **добавления конфигурации IDP** . Оставьте в поле **единого входа** значение **User**. Укажите **имя** и выберите **домены** из раскрывающегося списка. Нажмите кнопку **Далее** , чтобы вернуться к следующему окну.

    ![Zscaler частный доступ (ZPA) Add IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Скачайте **сертификат поставщика услуг**. Нажмите кнопку **Далее** , чтобы вернуться к следующему окну.

    ![Сертификат Zscaler частного доступа (ZPA)](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. В следующем окне отправьте **сертификат поставщика услуг** , скачанный ранее.

    ![Сертификат для отправки частного доступа Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Прокрутите вниз, чтобы указать **URL-адрес единого входа** и **идентификатор сущности IDP**.

    ![Zscaler частный доступ (ZPA) идентификатор IdP](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Прокрутите вниз, чтобы **включить синхронизацию scim**. Нажмите кнопку " **создать новый токен** ". Скопируйте **токен носителя**. Это значение будет указано в поле Секретный токен на вкладке Подготовка приложения Zscaler Private Access (ZPA) в портал Azure.

    ![Zscaler частный доступ (ZPA) создание токена](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Чтобы найти **URL-адрес клиента** , перейдите в раздел **Администрирование > Конфигурация IDP**. Щелкните имя вновь добавленной конфигурации IdP, указанной на странице.

    ![Имя IDP закрытого доступа Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Прокрутите вниз, чтобы просмотреть **конечную точку поставщика услуг scim** в конце страницы. Скопируйте **конечную точку поставщика службы scim**. Это значение будет указано в поле URL-адрес клиента на вкладке Подготовка приложения Zscaler Private Access (ZPA) в портал Azure.

    ![URL-адрес SCIM закрытого доступа Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Добавление частного доступа Zscaler (ZPA) из коллекции

Перед настройкой частного доступа Zscaler (ZPA) для автоматической подготовки пользователей с помощью Azure AD необходимо добавить Zscaler частный доступ (ZPA) из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Zscaler Private Access (ZPA) из коллекции приложений Azure AD, выполните следующие действия.**

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Zscaler частный доступ (ZPA)** , выберите **Zscaler частный доступ (ZPA)** на панели результатов и нажмите кнопку **добавить** , чтобы добавить это приложение.

    ![Zscaler Private Access (ZPA) в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Настройка автоматической подготовки пользователей для Zscaler закрытого доступа (ZPA) 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в Zscaler Private Access (ZPA) на основе назначений пользователей и групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для Zscaler Private Access (ZPA), следуя инструкциям, приведенным в [руководстве по единому входу в Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две функции дополняют друг друга.

> [!NOTE]
> Дополнительные сведения о конечной точке SCIM для закрытого доступа Zscaler см. [здесь](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Zscaler Private Access (ZPA) в Azure AD, сделайте следующее:

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Zscaler Private Access (ZPA)** .

    ![Ссылка на Zscaler Private Access (ZPA) в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите значение **конечной точки поставщика службы scim** , полученное ранее в поле **URL-адрес клиента**. Введите значение **токена носителя** , полученное ранее в **маркере секрета**. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к Zscaler закрытому доступу (ZPA). В случае сбоя подключения убедитесь, что учетная запись Zscaler Private Access (ZPA) имеет разрешения администратора, и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Выберите команду **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Zscaler закрытым доступом (ZPA)** .

    ![Сопоставления пользователей Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в Zscaler Private Access (ZPA) в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Zscaler Private Access (ZPA) для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. В разделе " **сопоставления** " выберите **синхронизировать Azure Active Directory группы к Zscaler закрытый доступ (ZPA)** .

    ![Сопоставления группы Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Проверьте атрибуты группы, которые синхронизированы из Azure AD в Zscaler Private Access (ZPA) в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в Zscaler Private Access (ZPA) для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты группы Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для Zscaler Private Access (ZPA), измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать к Zscaler закрытому доступу (ZPA), выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов по подготовке, в которых описаны все действия, выполняемые службой подготовки Azure AD на Zscaler Private Access (ZPA).

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

