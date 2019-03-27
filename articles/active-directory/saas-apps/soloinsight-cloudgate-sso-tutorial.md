---
title: Руководство. Интеграция Azure Active Directory с Soloinsight-CloudGate SSO | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e8b2b4d1a660fe2f1289bba6fa596d08ec824b8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57847262"
---
# <a name="tutorial-azure-active-directory-integration-with-soloinsight-cloudgate-sso"></a>Руководство. Интеграция Azure Active Directory с Soloinsight-CloudGate SSO

В этом руководстве описано, как интегрировать Soloinsight-CloudGate SSO с Azure Active Directory (Azure AD).
Интеграция Azure AD с Soloinsight-CloudGate SSO обеспечивает следующие преимущества:

* С помощью Azure AD вы можете управлять доступом к Soloinsight-CloudGate SSO.
* Вы можете включить автоматический вход для пользователей в Soloinsight-CloudGate SSO (единый вход) с помощью их учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Soloinsight-CloudGate SSO, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Soloinsight-CloudGate SSO с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Soloinsight-CloudGate SSO поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Добавление Soloinsight-CloudGate SSO из коллекции

Чтобы настроить интеграцию Soloinsight-CloudGate SSO с Azure AD, необходимо добавить Soloinsight-CloudGate SSO из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Soloinsight-CloudGate SSO из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Soloinsight-CloudGate SSO**, выберите **Soloinsight-CloudGate SSO** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Soloinsight-CloudGate SSO в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в Soloinsight-CloudGate SSO с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Soloinsight-CloudGate SSO.

Чтобы настроить и проверить единый вход Azure AD в Soloinsight-CloudGate SSO, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Soloinsight-CloudGate SSO](#create-soloinsight-cloudgate-sso-test-user)** требуется для того, чтобы в Soloinsight-CloudGate SSO существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Soloinsight-CloudGate SSO, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Soloinsight-CloudGate SSO** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для Soloinsight-CloudGate SSO](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.sigateway.com/login`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.sigateway.com/process/sso`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора и URL-адреса входа, которые описываются далее в разделе **Настройка единого входа Soloinsight-CloudGate SSO**.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Из раздела **Настройка Soloinsight-CloudGate SSO** скопируйте требуемые URL-адреса.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-soloinsight-cloudgate-sso-single-sign-on"></a>Настройка единого входа Soloinsight-CloudGate SSO

1. Чтобы получить значения, которые нужно вставить на портале Azure при настройке базовой конфигурации SAML, войдите на веб-портал CloudGate со своими учетными данными и откройте параметры единого входа, выбрав **Home > Administration > System settings > General** (Главная страница > Администрирование > Параметры системы > Общие).

    ![Параметры CloudGate SSO](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

2. **URL-адрес потребителя SAML**

    * Скопируйте ссылки напротив полей **URL-адреса потребителя SAML** и **URL-адреса перенаправления** и вставьте их в разделе **Базовая конфигурация SAML** портала Azure Portal в полях **Идентификатор (код сущности)** и **URL-адрес ответа** соответственно.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

3. **Сертификат подписи SAML**

    * Перейдите к источнику файла сертификата (Base64), который был скачан из списков сертификатов подписи SAML на портале Azure, и щелкните его правой кнопкой мыши. Выберите **Изменить с помощью Notepad++** в списке. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Скопируйте его содержимое в файл сертификата (Base64) с помощью Notepad++.

        ![Копия сертификата](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Вставьте содержимое в поле **Certificate** (Сертификат) параметров единого входа на веб-портале CloudGate и нажмите кнопку Save (Сохранить).

        ![Портал сертификатов](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

4. **Группа по умолчанию**

    * Выберите **Business Admin** (Администратор бизнеса) в раскрывающемся списке параметра **Default Group** (Группа по умолчанию) на веб-портале CloudGate.

        ![Группа по умолчанию](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

5. **Идентификатор AD и URL-адрес для входа**

    * Скопированный **URL-адрес для входа** из конфигураций **Настройка Soloinsight-CloudGate SSO** на портале Azure нужно ввести в раздел параметров единого входа на веб-портале CloudGate. 

    * Вставьте ссылку **URL-адрес для входа** с портала Azure в поле **URL-адреса для входа в AD** на веб портале CloudGate.
     
    * Вставьте ссылку **Идентификатор Azure AD** с портала Azure в поле **идентификатора AD** на веб-портале CloudGate.

        ![Вход в AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@<домен_вашей_компании>.<доменная_зона>**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставляя доступ к CloudGate SSO.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Soloinsight-CloudGate SSO**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Soloinsight CloudGate SSO**.

    ![Ссылка на Soloinsight-CloudGate SSO в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Создание тестового пользователя Soloinsight CloudGate SSO

Чтобы создать тестового пользователя, выберите **Employees** (Сотрудники) в главном меню веб-портала CloudGate и заполните форму для добавления нового сотрудника. Тестовому пользователю назначается уровень полномочий **Business Admin** (Администратор бизнеса). Нажмите кнопку **Create** (Создать) после заполнения всех нужных полей.

![Проверка сотрудника](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Soloinsight-CloudGate SSO на панели доступа, вы автоматически войдете в приложение Soloinsight-CloudGate SSO, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

