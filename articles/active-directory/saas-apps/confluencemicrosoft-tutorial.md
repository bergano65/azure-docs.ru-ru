---
title: Руководство. Интеграция Azure Active Directory с Confluence SAML SSO by Microsoft | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Confluence SAML SSO by Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c0a7ff6d06325a6e6f631b92335f9a41c2c8c06
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57854812"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Руководство. Интеграция Azure Active Directory с Confluence SAML SSO by Microsoft

В этом руководстве описано, как интегрировать Confluence SAML SSO by Microsoft с Azure Active Directory (Azure AD).
Интеграция Azure AD с Confluence SAML SSO by Microsoft обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Confluence SAML SSO by Microsoft.
* Для пользователей можно включить автоматический вход пользователей в Confluence SAML SSO by Microsoft (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="description"></a>Описание:

Используйте учетную запись Microsoft Azure Active Directory с сервером Atlassian Confluence, чтобы включить единый вход. Таким образом все пользователи вашей организации смогут использовать учетные данные Azure AD для входа в приложение Confluence. Этот подключаемый модуль для федерации использует SAML 2.0.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Confluence SAML SSO by Microsoft, вам потребуется следующее:

- подписка Azure AD;
- серверное приложение Confluence, установленное на 64-разрядной версии сервера Windows (в локальной среде или облачной инфраструктуре IaaS);
- поддержка HTTPS на сервере Confluence;
- поддерживаемые версии подключаемого модуля Confluence указаны в разделе ниже;
- сервер Confluence должен быть доступен через Интернет, в частности, на странице входа Azure AD для аутентификации, и должен иметь возможность получать маркер из Azure AD;
- в Confluence должны быть настроены учетные данные администратора;
- в Confluence должна быть отключена поддержка WebSudo;
- в серверном приложении Confluence должен быть создан тестовый пользователь.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду Confluence для проверки действий в этом учебнике. Сначала протестируйте интеграцию в среде разработки или промежуточной среде приложения, а затем используйте ее в рабочей среде.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по этой ссылке: [пробная версия](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Поддерживаемые версии Confluence

Сейчас поддерживаются следующие версии Confluence:

- Confluence: От 5.0 до 5.10
- Confluence: 6.0.1
- Confluence: 6.1.1
- Confluence: 6.2.1
- Confluence: 6.3.4
- Confluence: 6.4.0
- Confluence: 6.5.0
- Confluence: 6.6.2
- Confluence: 6.7.0
- Confluence: 6.8.1
- Confluence: 6.9.0
- Confluence: 6.10.0
- Confluence: 6.11.0
- Confluence: 6.12.0

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Confluence SAML SSO by Microsoft поддерживает единый вход инициированного **пакета обновления**.

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Добавление Confluence SAML SSO by Microsoft из коллекции

Чтобы настроить интеграцию Confluence SAML SSO by Microsoft с Azure AD, необходимо добавить Confluence SAML SSO by Microsoft из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Confluence SAML SSO by Microsoft из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Confluence SAML SSO by Microsoft**, выберите **Confluence SAML SSO by Microsoft** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Confluence SAML SSO by Microsoft в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описывается настройка и проверка единого входа Azure AD в Confluence SAML SSO by Microsoft с использованием тестового пользователя **Britta Simon**.
Чтобы единый вход работал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Confluence SAML SSO by Microsoft.

Чтобы настроить и проверить единый вход Microsoft Azure AD в Confluence SAML SSO by Microsoft, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Confluence SAML SSO by Microsoft](#configure-confluence-saml-sso-by-microsoft-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Confluence SAML SSO by Microsoft](#create-confluence-saml-sso-by-microsoft-test-user)** требуется для того, чтобы в Confluence SAML SSO by Microsoft существовал пользователь Britta Simon, связанный с представлением пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Confluence SAML SSO by Microsoft, сделайте следующее.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Confluence SAML SSO by Microsoft** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Confluence SAML SSO by Microsoft](common/sp-identifier-reply.png)

    a. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<domain:port>/plugins/servlet/saml/auth`.

    b. В поле **Идентификатор** введите URL-адрес в следующем формате: `https://<domain:port>/`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<domain:port>/plugins/servlet/saml/auth`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Если это именованный URL-адрес, то порт указывать необязательно. Эти значения предоставляются во время настройки подключаемого модуля Confluence, которая описывается далее в этом руководстве.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы копировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

### <a name="configure-confluence-saml-sso-by-microsoft-single-sign-on"></a>Настройка единого входа в Confluence SAML SSO by Microsoft

1. В другом окне браузера войдите в свой экземпляр Confluence в качестве администратора.

2. Наведите указатель мыши на шестеренку и щелкните **Add-ons** (Надстройки).

    ![Настройка единого входа](./media/confluencemicrosoft-tutorial/addon1.png)

3. Скачайте подключаемый модуль из [Центра загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=56503). Вручную передайте подключаемый модуль, предоставленный корпорацией Майкрософт, воспользовавшись меню **Upload add-on** (Отправка надстройки). Загрузка подключаемого модуля выполняется в соответствии с [соглашением об обслуживании Майкрософт](https://www.microsoft.com/servicesagreement/).

    ![Настройка единого входа](./media/confluencemicrosoft-tutorial/addon12.png)

4. После установки подключаемый модуль появится в разделе **Управление надстройками**, подраздел **Установленные пользователем**. Щелкните **Configure** (Настройка), чтобы настроить новый подключаемый модуль.

    ![Настройка единого входа](./media/confluencemicrosoft-tutorial/addon13.png)

5. Выполните следующие действия на странице настройки:

    ![Настройка единого входа](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Убедитесь, что с приложением сопоставлен только один сертификат, чтобы при разрешении метаданных не возникла ошибка. Если имеется несколько сертификатов, то при разрешении метаданных администратор увидит сообщение об ошибке.

    a. В текстовое поле **URL-адрес метаданных** вставьте значение **URL-адреса метаданных федерации приложений**, скопированное на портале Azure, и нажмите кнопку **Разрешить**. Будет прочитан URL-адрес метаданных поставщика удостоверений, а также будут заполнены все поля сведений.

    b. Скопируйте значения **идентификатора, URL-адреса ответа и URL-адреса входа** и вставьте их в соответствующие поля **идентификатора, URL-адреса ответа и URL-адреса входа** в разделе **Базовая конфигурация SAML** на портале Azure.

    c. В поле **Имя кнопки входа** введите имя кнопки, которую должны видеть на экране входа пользователи вашей организации.

    d. Для параметра **SAML User ID Locations** (Расположения идентификатора пользователя SAML) укажите значение **User ID is in the NameIdentifier element of the Subject statement** (Идентификатор пользователя указан в элементе NameIdentifier утверждения Subject) или **User ID is in an Attribute element** (Идентификатор пользователя указан в элементе Attribute).  Этим идентификатором должен быть идентификатор пользователя Confluence. Если идентификатор пользователя не совпадет, система не позволит пользователям выполнить вход. 

    > [!Note]
    > По умолчанию идентификатор пользователя SAML указан в идентификаторе имени. Его можно заменить атрибутом и ввести имя соответствующего атрибута.
    
    д. Если выбран параметр **Идентификатор пользователя указан в элементе Attribute**, то в текстовом поле **Имя атрибута** введите имя атрибута, в котором ожидается идентификатор пользователя. 

    Е. При использовании федеративного домена (например, AD FS и т. д.) для Azure AD выберите параметр **Включить обнаружение домашней области** и настройте **доменное имя**.
    
    ж. В поле **Доменное имя** введите доменное имя, если вы используете вход на основе AD FS.

    h. Установите флажок **Включить единый выход**, если после выхода пользователя из Confluence требуется выходить и из Azure AD. 

    i. Нажмите кнопку **Сохранить**, чтобы сохранить изменения.

    > [!NOTE]
    > Дополнительные сведения об установке и устранении неполадок см. в [руководстве администратора соединителя единого входа MS Confluence](../ms-confluence-jira-plugin-adminguide.md) и в [часто задаваемых вопросах](../ms-confluence-jira-plugin-faq.md).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@<домен_вашей_компании>.<доменная_зона>**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Confluence SAML SSO by Microsoft.

1. На портале Azure последовательно выберите **Корпоративные приложения**, **Все приложения**, **Confluence SAML SSO by Microsoft**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Confluence SAML SSO by Microsoft**.

    ![Ссылка на Confluence SAML SSO by Microsoft в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Создание тестового пользователя Confluence SAML SSO by Microsoft

Чтобы пользователи Azure AD могли входить на локальный сервер Confluence, для них необходимо выполнить подготовку в Confluence SAML SSO by Microsoft. Для Confluence SAML SSO by Microsoft подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на локальный сервер Confluence с правами администратора.

2. Наведите указатель мыши на шестеренку и щелкните **User management** (Управление пользователями).

    ![Добавление сотрудника](./media/confluencemicrosoft-tutorial/user1.png) 

3. В разделе "Users" (Пользователи) выберите вкладку **Add users** (Добавление пользователей). На диалоговой странице **Add a User** (Добавление пользователя) выполните следующее.

    ![Добавление сотрудника](./media/confluencemicrosoft-tutorial/user2.png) 

    a. В текстовом поле **Username** (Имя пользователя) введите электронный адрес пользователя, например Britta Simon.

    b. В текстовом поле **Full Name** (Полное имя) введите полное имя пользователя, например Britta Simon.

    c. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.

    d. В текстовом поле **Password** (Пароль) введите пароль пользователя Britta Simon.

    д. Щелкните **Confirm Password** (Подтвердить пароль) и повторно введите пароль.

    Е. Нажмите кнопку **Добавить**.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Confluence SAML SSO by Microsoft" на панели доступа, вы автоматически войдете в Confluence SAML SSO by Microsoft, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

