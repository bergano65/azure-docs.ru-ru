---
title: Руководство. Интеграция Azure Active Directory с Amazon Web Services (AWS) для подключения нескольких учетных записей | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и несколькими учетными записями Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23ceb9c7b18de5262a38fc724aad9605e82a615a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238425"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Руководство. Интеграция Azure Active Directory с несколькими учетными записями Amazon Web Services (AWS)

В этом руководстве описано, как интегрировать приложение Azure Active Directory (Azure AD) с несколькими учетными записями Amazon Web Services (AWS).

Интеграция Azure AD с приложением Amazon Web Services обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Amazon Web Services.
- Вы можете включить автоматический вход пользователей в Amazon Web Services (AWS) (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) в списке результатов](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Учтите, что подключение одного приложения AWS ко всем учетным записям AWS не рекомендуется. Вместо этого мы рекомендуем использовать [этот](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) подход, чтобы настроить несколько экземпляров учетной записи AWS для нескольких экземпляров приложений AWS в Azure AD.

**Обратите внимание на то, что мы не рекомендуем использовать этот подход по следующим причинам.**

* Вам нужно исправлять все роли для приложения с помощью Graph Explorer. Мы не рекомендуем использовать файлы манифестов.

* Мы видели отчеты клиентов, свидетельствующие о том, что после добавления примерно 1200 ролей для одного приложения AWS любая операция с приложением порождала ошибки, связанные с размером. Установлено жесткое ограничение размера объекта приложения.

* Необходимо вручную обновлять роль по мере добавления ролей в любые учетные записи. К сожалению, это требует замены сведений, а не добавления. Кроме того, если число учетных записей растет, то между учетными записями и ролями устанавливается связь "N к N".

* Все учетные записи AWS будут использовать один и тот же XML-файл метаданных федерации, и во время смены сертификата будет непросто одновременно обновить сертификат для всех учетных записей AWS.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Amazon Web Services, вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка с поддержкой единого входа в Amazon Web Services (AWS)

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Amazon Web Services (AWS) поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Добавление Amazon Web Services из коллекции

Чтобы настроить интеграцию Amazon Web Services с Azure AD, вам потребуется добавить Amazon Web Services из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Amazon Web Services (AWS) из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Amazon Web Services (AWS)**, выберите **Amazon Web Services (AWS)** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Amazon Web Services (AWS) в списке результатов](common/search-new-app.png)

5. Добавив приложение, перейдите к странице **Свойства** и скопируйте **идентификатор объекта**.

    ![Amazon Web Services (AWS) в списке результатов](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Amazon Web Services (AWS) с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Amazon Web Services соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Amazon Web Services.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Amazon Web Services (AWS).

Чтобы настроить и проверить единый вход Azure AD в Amazon Web Services, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Amazon Web Services (AWS).

**Чтобы настроить единый вход Azure AD в Amazon Web Services, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Amazon Web Services (AWS)** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure.

    ![изображение](common/preintegrated.png)

5. Приложение Amazon Web Services (AWS) ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **User Attributes & Claims** (Атрибуты пользователя и утверждения) на странице интеграции приложения. На странице **Set up Single Sign-On with SAML** (Настройка единого входа с помощью SAML) нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **User Attributes & Claims** (Атрибуты пользователя и утверждения).

    ![изображение](common/edit-attribute.png)

6. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.

    | ИМЯ  | Исходный атрибут  | Пространство имен |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Роль            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "Укажите значение от 900 секунд (15 минут) до 43 200 секунд (12 часов)." |  https://aws.amazon.com/SAML/Attributes |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](common/new-save-attribute.png)

    ![изображение](common/new-attribute-details.png)

    б) В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В текстовом поле **Пространство имен** введите значение пространства имен, показанное для этой строки.

    4.3. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку **Скачать**, чтобы скачать **XML метаданных федерации** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Настройка единого входа в Amazon Web Services (AWS)

1. В другом окне браузера войдите на сайт Amazon Web Services компании в качестве администратора.

2. Щелкните **AWS Home** (Домашняя страница AWS).

    ![Настройка главной страницы единого входа][11]

3. Щелкните **Identity and Access Management**(Управление удостоверениями и доступом).

    ![Настройка удостоверения для единого входа][12]

4. Щелкните **Identity Providers** (Поставщики удостоверений), затем щелкните **Create Provider** (Создать поставщик).

    ![Настройка поставщика единого входа][13]

5. На странице диалогового окна **Configure Provider** (Настройка поставщика) выполните следующие действия.

    ![Настройка диалогового окна единого входа][14]

    a. Выберите для параметра **Provider Type** (Тип поставщика) значение **SAML**.

    б) В текстовом поле **Имя поставщика** введите имя поставщика (например, *WAAD*).

    c. Чтобы отправить **файл метаданных**, скачанный с портала Azure, нажмите кнопку **Choose File** (Выбрать файл).

    4.3. Щелкните **Следующий шаг**.

6. На странице диалогового окна **Verify Provider Information** (Проверка сведений о поставщике) щелкните **Create** (Создать).

    ![Настройка проверки единого входа][15]

7. Щелкните **Roles** (Роли), а затем нажмите кнопку **Create role** (Создать роль).

    ![Настройка ролей для единого входа][16]

8. На странице **Create role** (Создание роли) выполните следующие действия.  

    ![Настройка доверия для единого входа][19]

    a. В разделе **Select type of trusted entity** (Выберите тип доверенной сущности) выберите **SAML 2.0 federation** (Федерация SAML 2.0).

    б) В разделе **Choose a SAML 2.0 Provider** (Выберите поставщик SAML 2.0) выберите **поставщик SAML**, созданный ранее (например, *WAAD*).

    c. Установите флажок **Allow programmatic and AWS Management Console access** (Разрешить программный доступ и доступ через консоль управления AWS).
  
    4.3. Щелкните **Далее: разрешения**.

9. В диалоговом окне **Attach Permissions Policies** (Вложить политики разрешений) вложите соответствующую политику согласно вашей организации. Щелкните **Далее: Review** (Далее: проверка).  

    ![Настройка политики единого входа][33]

10. В диалоговом окне **Review** (Обзор) выполните следующие действия.

    ![Проверка настройки единого входа][34]

    a. В текстовом поле **Role name** (Имя роли) введите имя роли.

    б) В текстовом поле **Role description** (Описание роли) введите описание.

    c. Щелкните **Создать роль**.

    4.3. Создайте необходимое количество ролей и сопоставьте их с поставщиком удостоверений.

11. Выйдите из текущей учетной записи AWS и войдите в систему с другой учетной записью, в которой нужно настроить единый вход с помощью Azure AD.

12. Выполните шаги 2–10, чтобы создать несколько ролей, которые необходимо настроить для этой учетной записи. При наличии более двух учетных записей выполните те же шаги для всех учетных записей, чтобы создать для них роли.

13. После создания всех ролей в учетных записях они отображаются в соответствующем списке **ролей**.

    ![Настройка ролей](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Необходимо зафиксировать все ARN ролей и доверенные сущности для всех ролей в учетных записях, которые необходимо вручную сопоставлять с приложением Azure AD.

15. Щелкните роли, чтобы скопировать значения **ARN роли** и **доверенных сущностей**. Эти значения нужны всем ролям, которые необходимо создать в Azure AD.

    ![Настройка ролей](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Выполните то же самое для всех ролей во всех учетных записях и сохраните их всех в формате **ARN роли,доверенные сущности** в блокноте.

17. В другом окне откройте [обозреватель Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

    a. Войдите на сайт обозревателя Graph с учетными данными глобального администратора или соадминистратора вашего клиента.

    б) У вас должно быть достаточно разрешений для создания ролей. Щелкните **Изменить разрешения**, чтобы получить требуемые разрешения.

    ![Диалоговое окно обозревателя Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Выберите следующие разрешения из списка (если у вас их еще нет) и щелкните "Изменить разрешения". 

    ![Диалоговое окно обозревателя Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    4.3. Вам необходимо снова войти в систему и принять запрос. После принятия запроса вы снова войдете в обозреватель Graph.

    д. В раскрывающемся списке измените версию на **Бета**. Чтобы получить все субъекты-службы клиента, используйте следующий запрос:

    `https://graph.microsoft.com/beta/servicePrincipals`

    При использовании нескольких каталогов можно использовать следующий шаблон, в котором есть основной домен: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.

    ![Диалоговое окно обозревателя Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    Е. В полученном списке субъектов-служб найдите ту, в которую нужно внести изменения. Также для поиска приложений в списке субъектов-служб можно использовать сочетание клавиш CTRL+F. Используя **идентификатор объекта**, который был скопирован со страницы свойств Azure AD, выполните следующий запрос для получения соответствующего субъекта-службы.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Диалоговое окно обозревателя Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    ж. Извлеките свойство appRoles из объекта субъекта-службы.

    ![Диалоговое окно обозревателя Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Теперь вам следует создать роли для приложения. 

    i. Расположенный ниже JSON является примером объекта appRoles. Создайте похожий объект, чтобы добавить необходимые роли в приложение.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Вы можете добавлять новые роли только после **msiam_access** для операции исправления. Вы можете добавить столько ролей, сколько нужно вашей организации. Azure Active Directory будет отправлять **значение** этих ролей в качестве значения утверждения в ответе SAML.

    j. Вернитесь к своему обозревателю Graph и измените метод с **GET** на **PATCH**. Примените к объекту субъекта-службы нужные роли, обновив свойство appRoles, как показано в примере выше. Щелкните **Выполнить запрос**, чтобы выполнить операцию исправления. Сообщение об успешном выполнении подтвердит создание роли для приложения Amazon Web Services

    ![Диалоговое окно обозревателя Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. Добавив новые роли в субъект-службу, вы сможете назначить эти роли и группы пользователям. Для этого перейдите на портал и найдите приложение Amazon Web Services. Щелкните вкладку **Пользователи и группы** в верхней части окна.

19. Мы рекомендуем создавать группы для каждой роли AWS, чтобы вы могли присвоить определенную роль этой группе. Обратите внимание, что группа сопоставляется с ролью по принципу "один к одному". Затем можно добавить участников, которые принадлежат к этой группе.

20. После создания групп выберите группу и присвойте ее приложению.

    ![Добавление атрибута для настройки единого входа](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Вложенные группы не поддерживаются при назначении групп.

21. Чтобы присвоить роль группе, выберите роль и нажмите кнопку **Присвоить** в нижней части страницы.

    ![Добавление атрибута для настройки единого входа](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Обратите внимание, что вам необходимо обновить сеанс на портале Azure, чтобы увидеть новые роли.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Amazon Web Services на панели доступа, вы перейдете на страницу приложения Amazon Web Services с возможностью выбрать роль.

![Добавление атрибута для настройки единого входа](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Также можно проверить ответ SAML, чтобы увидеть роли, передаваемые как утверждения.

![Добавление атрибута для настройки единого входа](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
