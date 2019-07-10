---
title: Руководство по Интеграция Azure Active Directory с приложением Wdesk | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 0f614838ae44b5c4263bc9eac81e43fd13f87baa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087290"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Руководство по Интеграция Azure Active Directory с Wdesk

В этом руководстве описано, как интегрировать Wdesk с Azure Active Directory (Azure AD).
Интеграция приложения Wdesk с Azure AD обеспечивает следующие преимущества.

* С помощью Azure Active Directory вы можете контролировать, у кого есть доступ к приложению Wdesk.
* Вы можете включить автоматический вход для пользователей во Wdesk (единый вход) с помощью учетных записей Azure Active Directory.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Wdesk, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка Wdesk с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Приложение Wdesk поддерживает единый вход, инициированный **поставщиком услуг** и **поставщиком удостоверений**.

## <a name="adding-wdesk-from-the-gallery"></a>Добавление Wdesk из коллекции

Чтобы настроить интеграцию приложения Wdesk с Azure AD, вам нужно добавить это приложение из коллекции в свой список управляемых приложений SaaS.

**Добавление приложения Wdesk из коллекции**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Wdesk**, выберите **Wdesk** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Wdesk в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure Active Directory в приложение Wdesk с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure Active Directory и соответствующим пользователем во Wdesk.

Чтобы настроить и проверить единый вход Azure AD в Wdesk, выполните следующие действия:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа во Wdesk](#configure-wdesk-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Wdesk](#create-wdesk-test-user)** требуется для того, чтобы в приложении Wdesk существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure Active Directory.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure Active Directory во Wdesk, сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Wdesk** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Wdesk](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Wdesk](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Эти значения вы можете получить на портале Wdesk при настройке единого входа.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Требуемый URL-адрес можно скопировать из раздела **Настройка Wdesk**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-wdesk-single-sign-on"></a>Настройка единого входа во Wdesk

1. В другом окне браузера войдите в приложение Wdesk с правами администратора безопасности.

2. В нижнем левом углу щелкните **Admin** (Администрирование) и выберите **Account Admin** (Администрирование учетных записей):
 
     ![Настройка единого входа](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. В разделе администрирования Wdesk откройте **Security** (Безопасность), затем **SAML** > **SAML Settings** (Параметры SAML):

    ![Настройка единого входа](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. В разделе **General Settings** (Общие параметры) установите флажок **Enable SAML Single Sign On** (Включить единый вход SAML):

    ![Настройка единого входа](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. В разделе **Service Provider Details** (Сведения о поставщике SAML) выполните следующие действия:

    ![Настройка единого входа](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Скопируйте значение **Login URL** (URL-адрес входа) и вставьте его в текстовое поле **URL-адрес входа** на портале Azure.
   
      b. Скопируйте значение **Metadata Url** (URL-адрес метаданных) и вставьте его в текстовое поле **Идентификатор** на портале Azure.
       
      c. Скопируйте значение **Consumer URL** (URL-адрес потребителя) и вставьте его в текстовое поле **URL-адрес ответа** на портале Azure.
   
      d. На портале Azure нажмите кнопку **Сохранить**, чтобы сохранить изменения.      

6. Щелкните **Configure IdP Settings** (Настройка параметров поставщика удостоверений), чтобы открыть диалоговое окно **изменения параметров поставщика удостоверений**. Щелкните **Choose File** (Выбрать файл) и найдите файл **Metadata.xml**, который вы сохранили с портала Azure, а затем отправьте его.
    
    ![Настройка единого входа](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Нажмите кнопку **Сохранить изменения**.

    ![Настройка единого входа](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

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

В этом разделе мы разрешим пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Wdesk.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Wdesk**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Wdesk**.

    ![Ссылка на Wdesk в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-wdesk-test-user"></a>Создание тестового пользователя Wdesk

Чтобы пользователи Azure Active Directory могли выполнять вход во Wdesk, их следует подготовить во Wdesk. В Wdesk подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в приложение Wdesk с правами администратора безопасности.

2. Последовательно выберите **Admin** (Администрирование)  > **Account Admin** (Администрирование учетных записей).

     ![Настройка единого входа](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Щелкните элемент **Members** (Участники) в разделе **People** (Люди).

4. Теперь щелкните **Add Member** (Добавить участника), чтобы открыть диалоговое окно **Add Member** (Добавление участника). 
   
    ![Создание тестового пользователя Azure AD](./media/wdesk-tutorial/createuser1.png)  

5. В текстовом поле **Пользователь** введите имя пользователя, например brittasimon@contoso.com, и нажмите кнопку **Продолжить**.

    ![Создание тестового пользователя Azure AD](./media/wdesk-tutorial/createuser3.png)

6.  Введите необходимые данные, как показано ниже:
  
    ![Создание тестового пользователя Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. В текстовом поле **Адрес электронной почты** введите адрес электронной почты пользователя, например brittasimon@contoso.com.

    b. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    c. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

7. Щелкните кнопку **Save Member** (Сохранить участника).  

    ![Создание тестового пользователя Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Wdesk на Панели доступа, вы автоматически войдете в приложение Wdesk, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

