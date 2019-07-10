---
title: Руководство. Интеграция Azure Active Directory с DocuSign | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 607e5aded52375190878de6b48ffa4aa2ab49767
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67104087"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Руководство. Интеграция Azure Active Directory с DocuSign

В этом руководстве описано, как интегрировать DocuSign с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением DocuSign обеспечивает перечисленные ниже преимущества.

* С помощью Azure AD вы можете контролировать доступ к DocuSign.
* Вы можете включить автоматический вход пользователей в DocuSign (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с DocuSign, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка DocuSign с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* DocuSign поддерживает единый вход, инициированный **поставщиком службы**.

* DocuSign поддерживает **JIT**-подготовку пользователей.

## <a name="adding-docusign-from-the-gallery"></a>Добавление DocuSign из коллекции

Чтобы настроить интеграцию DocuSign с Azure AD, необходимо добавить DocuSign из коллекции в список управляемых приложений SaaS.

**Чтобы добавить DocuSign из коллекции, выполните указанные ниже действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **DocuSign**, выберите **DocuSign** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![DocuSign в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение DocuSign для тестового пользователя **Britta Simon**.
Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в DocuSign.

Чтобы настроить и проверить единый вход Azure AD в DocuSign, вам потребуется выполнить действия в перечисленных ниже стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в DocuSign](#configure-docusign-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя DocuSign](#create-docusign-test-user)** требуется для создания в DocuSign пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в DocuSign, выполните следующее действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **DocuSign** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения DocuSign](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените эти значения фактическими URL-адресом для входа и идентификатором, как описано ниже в пункте о разделе **View SAML 2.0 Endpoints** (Просмотр конечных точек SAML 2.0) в этом руководстве.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Скопируйте требуемый URL-адрес из раздела **Настройка DocuSign**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-docusign-single-sign-on"></a>Настройка единого входа DocuSign

1. В другом окне браузера войдите на **портал администрирования DocuSign** с правами администратора.

2. В правом верхнем углу страницы щелкните **логотип профиля** и выберите пункт **Go to Admin** (Перейти на страницу администратора).
  
    ![Настройка единого входа][51]

3. На странице решений домена щелкните **Domains** (Домены).

    ![Настройка единого входа][50]

4. В разделе **Domains** (Домены) щелкните **Claim domain** (Зарезервировать домен).

    ![Настройка единого входа][52]

5. В диалоговом окне **Claim a domain** (Резервирование домена) в текстовом поле **Domain Name** (Доменное имя) введите соответствующее доменное имя своей компании и нажмите кнопку **Claim** (Зарезервировать). Обязательно проверьте домен. Состояние домена должно быть "Активный".

    ![Настройка единого входа][53]

6. На странице решений домена щелкните **Identity Providers** (Поставщики удостоверений).
  
    ![Настройка единого входа][54]

7. В разделе **Identity Providers** (Поставщики удостоверений) щелкните **Add identity provider** (Добавление поставщика удостоверений). 

    ![Настройка единого входа][55]

8. На странице **параметров поставщика удостоверений** выполните следующие действия.

    ![Настройка единого входа][56]

    a. В соответствующее текстовое поле введите уникальное **имя** конфигурации. Не используйте пробелы.

    b. В текстовое поле **Identity Provider Issuer** (Издатель поставщика удостоверений) вставьте значение **идентификатора Azure AD**, скопированное на портале Azure.

    c. В текстовое поле **Identity Provider Login URL** (URL-адрес для входа поставщика удостоверений) вставьте **URL-адрес входа**, скопированный на портале Azure.

    d. В текстовое поле **Identity Provider Logout URL** (URL-адрес выхода поставщика удостоверений) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    д. Выберите **Sign AuthN Request** (Подпись запроса авторизации).

    Е. Для параметра **Send AuthN request by** (Как отправлять запрос авторизации) выберите значение **POST**.

    ж. Для параметра **Send logout request by** (Как отправлять запрос на выход) выберите значение **GET**.

    h. В разделе **Custom Attribute Mapping** (Сопоставление настраиваемого атрибута) выберите **Add new mapping** (Добавить новое сопоставление).

    ![Настройка единого входа][62]

    i. Выберите поле, которое нужно сопоставить с утверждением Azure AD. В этом примере утверждение **emailaddress** сопоставляется со значением **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . Это имя утверждения по умолчанию из Azure AD, которое используется для утверждения на электронную почту, а затем щелкните **Save** (Сохранить).

    ![Настройка единого входа][57]

    > [!NOTE]
    > Чтобы сопоставить пользователя Azure AD с пользователем Docusign, используйте соответствующий **идентификатор пользователя**. Выберите соответствующее поле и введите подходящее значение на основе параметров вашей организации.

    j. В разделе **Identity Provider Certificates** (Сертификаты поставщика удостоверений) нажмите кнопку **Add Certificate** (Добавить сертификат) и отправьте сертификат, который вы скачали на портале Azure AD, а затем щелкните **Save** (Сохранить).

    ![Настройка единого входа][58]

    k. В разделе **Identity Providers** (Поставщики удостоверений) щелкните **Actions** (Действия), а затем выберите **Endpoints** (Конечные точки).

    ![Настройка единого входа][59]

    l. На **портале администрирования DocuSign** в разделе **View SAML 2.0 Endpoints** (Просмотр конечных точек SAML 2.0) выполните указанные ниже действия.

    ![Настройка единого входа][60]

    * Скопируйте значение **Service Provider Issuer URL** (URL-адрес издателя поставщика службы), а затем вставьте его в текстовое поле **Идентификатор** в разделе **Базовая конфигурация SAML** на портале Azure.

    * Скопируйте значение **Service Provider Login URL** (URL-адрес для входа поставщика службы), а затем вставьте его в текстовое поле **URL-адрес для входа** в разделе **Базовая конфигурация SAML** на портале Azure.

    * В нижней части страницы нажмите кнопку **Close**

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите brittasimon@yourcompanydomain.extension. Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к DocuSign.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **DocuSign**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **DocuSign**.

    ![Ссылка на DocuSign в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-docusign-test-user"></a>Создание тестового пользователя DocuSign

В этом разделе вы создадите в DocuSign пользователя с именем Britta Simon. Приложение DocuSign поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в DocuSign, он создается после проверки подлинности.

>[!Note]
>Чтобы создать пользователя вручную, обратитесь в [службу поддержки DocuSign](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку DocuSign на панели доступа, вы автоматически войдете в приложение DocuSign, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
