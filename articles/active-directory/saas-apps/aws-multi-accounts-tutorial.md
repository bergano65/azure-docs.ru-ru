---
title: Руководство по интеграции Azure Active Directory с Amazon Web Services (AWS) для подключения нескольких учетных записей | Документация Майкрософт
description: Узнайте, как настроить единый всконь между Azure AD и Amazon Web Services (AWS) (Legacy Tutorial).
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
ms.topic: article
ms.date: 04/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a6ab13dea1a1db96cbb2f2ac70b9779eca60591
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885389"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Учебник: Активная интеграция каталога Azure с веб-службами Amazon (AWS) (Наследие)

В этом уроке вы узнаете, как интегрировать активный каталог Azure (Azure AD) с веб-службами Amazon (AWS) (Legacy Tutorial).

Интеграция Azure AD с приложением Amazon Web Services обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Amazon Web Services.
- Вы можете включить автоматический вход пользователей в Amazon Web Services (AWS) (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно на портале Azure.

Если вы хотите узнать более подробную информацию об интеграции приложений SaaS с Azure AD, узнайте, [что такое доступ к приложениям и единый вход в каталог Azure Active.](../manage-apps/what-is-single-sign-on.md)

![Amazon Web Services (AWS) в списке результатов](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Учтите, что подключение одного приложения AWS ко всем учетным записям AWS не рекомендуется. Вместо этого мы рекомендуем использовать [этот](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) подход, чтобы настроить несколько экземпляров учетной записи AWS для нескольких экземпляров приложений AWS в Azure AD. Этот [подход](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) следует использовать только в том случае, если в нем очень меньше учетных записей и ролей AWS. [эта](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) модель не масштабируется по мере роста учетных записей и ролей AWS в этих учетных записях. Кроме того, [этот](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) подход не использует функциональность импорта aWS Role с помощью обеспечения пользователей Azure AD, поэтому вам придется вручную добавлять/обновлять/удалять роли. Для других ограничений на [этот](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) подход, пожалуйста, смотрите подробности ниже.

**Обратите внимание на то, что мы не рекомендуем использовать этот подход по следующим причинам.**

* Вы должны использовать подход Microsoft Graph Explorer, чтобы исправить все роли в приложении. Мы не рекомендуем использовать файлы манифестов.

* Мы видели отчеты клиентов, свидетельствующие о том, что после добавления примерно 1200 ролей для одного приложения AWS любая операция с приложением порождала ошибки, связанные с размером. Установлено жесткое ограничение размера объекта приложения.

* Необходимо вручную обновлять роль по мере добавления ролей в любые учетные записи. К сожалению, это требует замены сведений, а не добавления. Кроме того, если число учетных записей растет, то между учетными записями и ролями устанавливается связь "N к N".

* Все учетные записи AWS будут использовать один и тот же XML-файл метаданных федерации, и во время смены сертификата будет непросто одновременно обновить сертификат для всех учетных записей AWS.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Amazon Web Services, вам потребуется следующее:

* Подписка Azure AD. (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка с поддержкой единого входа в Amazon Web Services (AWS)

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Amazon Web Services (AWS) поддерживает **SP и InP** инициированных SSO
* После настройки Amazon Web Services (AWS) можете применить функцию управления сеансом, которая защищает от хищения и несанкционированного доступа к конфиденциальным данным вашей организации в реальном времени. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad).

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Добавление Amazon Web Services из коллекции

Чтобы настроить интеграцию Amazon Web Services с Azure AD, вам потребуется добавить Amazon Web Services из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Amazon Web Services (AWS)**.
1. Выберите **Amazon Web Services (AWS)** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

1. Как только приложение добавлено, перейдите на страницу **Свойства** и скопируйте **идентификатор объекта**.

    ![Amazon Web Services (AWS) в списке результатов](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Amazon Web Services (AWS) с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Amazon Web Services соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Amazon Web Services.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Amazon Web Services (AWS).

Чтобы настроить и проверить единый вход Azure AD в Amazon Web Services, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Тестируйте один очный знак](#test-single-sign-on)** - чтобы проверить, работает ли конфигурация.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Amazon Web Services (AWS).

**Чтобы настроить единый вход Azure AD в Amazon Web Services, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Amazon Web Services (AWS)** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Изменение базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Basic SAML Configuration** пользователю не нужно выполнять никаких шагов, так как приложение уже предварительно интегрировано с Azure и нажмите **Кнопка Сохранить**.

5. Приложение Amazon Web Services (AWS) ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **User Attributes & Claims** (Атрибуты пользователя и утверждения) на странице интеграции приложения. На странице **Set up Single Sign-On with SAML** (Настройка единого входа с помощью SAML) нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **User Attributes & Claims** (Атрибуты пользователя и утверждения).

    ![Изображение](common/edit-attribute.png)

6. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.

    | Имя  | Исходный атрибут  | Пространство имен |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Роль            | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration             | "Укажите значение от 900 секунд (15 минут) до 43 200 секунд (12 часов)." |  `https://aws.amazon.com/SAML/Attributes` |

    а. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![Изображение](common/new-save-attribute.png)

    ![Изображение](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой записи.

    c. В текстовом ящике **Namespace** введите значение Namespace, отображаемое для этой строки.

    d. В качестве источника выберите **Атрибут**.

    д) В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    е) Нажмите **Ok**

    ж. Выберите команду **Сохранить**.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку **Скачать**, чтобы скачать **XML метаданных федерации** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Настройка единого входа в Amazon Web Services (AWS)

1. В другом окне браузера войдите на сайт Amazon Web Services компании в качестве администратора.

1. Щелкните **AWS Home** (Домашняя страница AWS).

    ![Настройка главной страницы единого входа][11]

1. Щелкните **Identity and Access Management**(Управление удостоверениями и доступом).

    ![Настройка удостоверения для единого входа][12]

1. Щелкните **Identity Providers** (Поставщики удостоверений), затем щелкните **Create Provider** (Создать поставщик).

    ![Настройка поставщика единого входа][13]

1. На странице диалогового окна **Configure Provider** (Настройка поставщика) выполните следующие действия.

    ![Настройка диалогового окна единого входа][14]

    а. Выберите для параметра **Тип поставщика** значение **SAML**.

    b. В текстовом поле **Имя поставщика** введите имя поставщика (например, *WAAD*).

    c. Чтобы отправить **файл метаданных**, скачанный с портала Azure, нажмите кнопку **Choose File** (Выбрать файл).

    d. Щелкните **Следующий шаг**.

1. На странице диалогового окна **Verify Provider Information** (Проверка сведений о поставщике) щелкните **Create** (Создать).

    ![Настройка проверки единого входа][15]

1. Щелкните **Roles** (Роли), а затем нажмите кнопку **Create role** (Создать роль).

    ![Настройка ролей для единого входа][16]

1. На странице **Create role** (Создание роли) выполните следующие действия.  

    ![Настройка доверия для единого входа][19]

    а. В разделе **Select type of trusted entity** (Выберите тип доверенной сущности) выберите **SAML 2.0 federation** (Федерация SAML 2.0).

    b. В разделе **Choose a SAML 2.0 Provider** (Выберите поставщик SAML 2.0) выберите **поставщик SAML**, созданный ранее (например, *WAAD*).

    c. Установите флажок **Allow programmatic and AWS Management Console access** (Разрешить программный доступ и доступ через консоль управления AWS).
  
    d. Щелкните **Next: Permissions** (Далее: разрешения).

1. Поиск **Администратор Доступ** в панели поиска и выберите **Checkbox AdministratorAccess,** а затем нажмите **Далее: Теги**.

    ![Выберите доступ к администратору](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. В разделе **Добавление тегов (необязательно)** выполните следующие действия:

    ![Выберите доступ к администратору](./media/aws-multi-accounts-tutorial/config2.png)

    а. В **ключевом** текстовом ящике введите ключевое имя для ex: Azureadtest.

    b. В текстовом ящике **значения (необязательный)** `accountname-aws-admin`введите ключевое значение, используя следующий формат. Имя учетной записи должно быть во всех нижних регистрах.

    c. Нажмите **дальше: Обзор**.

1. В диалоговом окне **Review** (Обзор) выполните следующие действия.

    ![Проверка настройки единого входа][34]

    а. В текстовом ящике **имени имени** роли `accountname-aws-admin`введите значение в следующем шаблоне.

    b. В текстовом ящике **описания роли** введите то же значение, которое вы использовали для имени роли.

    c. Щелкните **Создать роль**.

    d. Создайте необходимое количество ролей и сопоставьте их с поставщиком удостоверений.

    > [!NOTE]
    > Аналогичным образом создать оставшиеся другие роли, такие как имя учетной записи-финансы-админ, имя учетной записи читать только пользователь, имя учетной записи-devops-пользователя, имя учетной записи-tpm-пользователя с различными политиками, которые будут прикреплены. Позже также эти ролевые политики могут быть изменены в соответствии с требованиями по учетной записи AWS, но всегда лучше сохранить одни и те же политики для каждой роли в учетных записях AWS.

1. Пожалуйста, обратите внимание на идентификатор учетной записи для этой учетной записи AWS либо из свойств EC2, либо из панели мониторинга IAM, как показано ниже:

    ![Выберите доступ к администратору](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Теперь зайдите на [портал Azure](https://portal.azure.com/) и перейдите к **группам**.

1. Создавайте новые группы с тем же именем, что и роли IAM, созданные ранее, и записывайте **идентиобразование объектов** этих новых групп.

    ![Выберите доступ к администратору](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Выйдите из текущей учетной записи AWS и войдите в систему с другой учетной записью, в которой нужно настроить единый вход с помощью Azure AD.

1. После создания всех ролей в учетных записях они отображаются в соответствующем списке **ролей**.

    ![Настройка ролей](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. Необходимо зафиксировать все ARN ролей и доверенные сущности для всех ролей в учетных записях, которые необходимо вручную сопоставлять с приложением Azure AD.

1. Щелкните роли, чтобы скопировать значения **ARN роли** и **доверенных сущностей**. Эти значения нужны всем ролям, которые необходимо создать в Azure AD.

    ![Настройка ролей](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Выполните то же самое для всех ролей во всех учетных записях и сохраните их всех в формате **ARN роли,доверенные сущности** в блокноте.

1. Откройте [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) в другом окне.

    а. Зарегистрируйтесь на сайте Microsoft Graph Explorer, используя учетные данные Global Admin/Co-admin для вашего клиента.

    b. У вас должно быть достаточно разрешений для создания ролей. Щелкните **Изменить разрешения**, чтобы получить требуемые разрешения.

    ![Microsoft Graph Explorer диалоговые окна](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Выберите следующие разрешения из списка (если у вас их еще нет) и щелкните "Изменить разрешения". 

    ![Microsoft Graph Explorer диалоговые окна](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Вам необходимо снова войти в систему и принять запрос. После получения согласия вы снова вошли в Microsoft Graph Explorer.

    д) В раскрывающемся списке измените версию на **Бета**. Чтобы получить все субъекты-службы клиента, используйте следующий запрос:

    `https://graph.microsoft.com/beta/servicePrincipals`

    При использовании нескольких каталогов можно использовать следующий шаблон, в котором есть основной домен: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.

    ![Microsoft Graph Explorer диалоговые окна](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    е) В полученном списке субъектов-служб найдите ту, в которую нужно внести изменения. Также для поиска приложений в списке субъектов-служб можно использовать сочетание клавиш CTRL+F. Следующий запрос можно использовать, используя **идентификатор объекта,** который вы скопировали со страницы Azure AD Properties, чтобы перейти к соответствующему директору службы.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Microsoft Graph Explorer диалоговые окна](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    ж. Извлеките свойство appRoles из объекта субъекта-службы.

    ![Microsoft Graph Explorer диалоговые окна](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

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
    > Вы можете добавлять новые роли только после **msiam_access** для операции исправления. Вы можете добавить столько ролей, сколько нужно вашей организации. Azure AD отправит **значение** этих ролей в качестве значения претензии в ответ SAML.

    j. Вернитесь к Microsoft Graph Explorer и измените метод с **GET** на **PATCH.** Примените к объекту субъекта-службы нужные роли, обновив свойство appRoles, как показано в примере выше. Щелкните **Выполнить запрос**, чтобы выполнить операцию исправления. Сообщение об успешном выполнении подтвердит создание роли для приложения Amazon Web Services

    ![Microsoft Graph Explorer диалоговые окна](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Добавив новые роли в субъект-службу, вы сможете назначить эти роли и группы пользователям. Для этого перейдите на портал и найдите приложение Amazon Web Services. Щелкните вкладку **Пользователи и группы** в верхней части окна.

1. Мы рекомендуем создавать группы для каждой роли AWS, чтобы вы могли присвоить определенную роль этой группе. Обратите внимание, что группа сопоставляется с ролью по принципу "один к одному". Затем можно добавить участников, которые принадлежат к этой группе.

1. После создания групп выберите группу и присвойте ее приложению.

    ![Добавление атрибута для настройки единого входа](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Вложенные группы не поддерживаются при назначении групп.

1. Чтобы присвоить роль группе, выберите роль и нажмите кнопку **Присвоить** в нижней части страницы.

    ![Добавление атрибута для настройки единого входа](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Обратите внимание, что вам необходимо обновить сеанс на портале Azure, чтобы увидеть новые роли.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Amazon Web Services на панели доступа, вы перейдете на страницу приложения Amazon Web Services с возможностью выбрать роль.

![Добавление атрибута для настройки единого входа](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Также можно проверить ответ SAML, чтобы увидеть роли, передаваемые как утверждения.

![Добавление атрибута для настройки единого входа](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

См. дополнительные сведения о [панели доступа](../active-directory-saas-access-panel-introduction.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Как настроить подготовку с помощью AIS MS Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [Как защитить Amazon Web Services (AWS) с улучшенной видимостью и контролем](https://docs.microsoft.com/cloud-app-security/protect-aws)

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
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/