---
title: Руководство. Интеграция Azure Active Directory с Promapp | Документация Майкрософт
description: Из этого руководства вы узнаете, как настроить единый вход между Azure Active Directory и Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: e91351d4571eaa084865c5a179ed05e6c773b952
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240414"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Руководство по Интеграция Azure Active Directory с Promapp

В этом руководстве описано, как интегрировать Promapp с Azure Active Directory (Azure AD).
Такая интеграция обеспечивает следующие преимущества.

* Контроль доступа к Promapp с помощью Azure AD.
* Автоматический вход пользователей (единый вход) в Promapp с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Promapp, вам потребуется:

* подписка Azure AD Если у вас нет среды Azure AD, вы можете получить [пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).
* подписка Promapp с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure Active Directory в тестовой среде.

* Promapp поддерживает единый вход, инициируемый поставщиком службы и поставщиком удостоверений.

* Promapp поддерживает JIT-подготовку пользователей.

## <a name="add-promapp-from-the-gallery"></a>Добавление Promapp из коллекции

Чтобы настроить интеграцию Promapp с Azure AD, необходимо добавить Promapp из коллекции в список управляемых приложений SaaS.

1. На [портале Azure](https://portal.azure.com) в области слева щелкните **Azure Active Directory**.

    ![Выберите Azure Active Directory.](common/select-azuread.png)

2. Выберите **Корпоративные приложения** > **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите **Новое приложение** в верхней части окна.

    ![Выбор элемента "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Promapp**. В результатах поиска выберите **Promapp**, а затем нажмите **Добавить**.

     ![Результаты поиска](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Promapp с использованием тестового пользователя Britta Simon.
Для работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Promapp.

Чтобы настроить и проверить единый вход Azure AD в Promapp, вам потребуется выполнить следующие действия:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настроить единый вход в Promapp](#configure-promapp-single-sign-on)** на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить ему использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure Active Directory в Promapp, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением Promapp выберите **Единый вход**.

    ![Выбор параметра "Единый вход"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Выбор метода единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Значок "Изменить"](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом поставщиком удостоверений, в диалоговом окне **Базовая конфигурация SAML** выполните следующие действия.

    ![Диалоговое окно "Базовая конфигурация SAML"](common/idp-intiated.png)

    1. В поле **Идентификатор** введите URL-адрес в следующем формате:

       | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > При интеграции Azure AD с Promapp сейчас поддерживается только аутентификация, инициированная службой. (То есть переход по URL-адресу Promapp запускает процесс аутентификации.) При этом поле **URL-адрес ответа** является обязательным.

    1. В поле **URL-адрес ответа** введите URL-адрес в следующем формате:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

5. Если вы хотите настроить приложение для работы в режиме, инициируемом поставщиком услуг, выберите **Задать дополнительные URL-адреса**. В поле **URL-адрес для входа** введите URL-адрес в следующем формате:

      `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    ![Сведения о домене и URL-адресах единого входа приложения Promapp](common/metadata-upload-additional-signon.png)

   

    > [!NOTE]
    > Эти значения представляют собой заполнители. Вместо них нужно указать фактический идентификатор, URL-адрес ответа и URL-адрес для входа. Чтобы получить эти значения, обратитесь в [службу поддержки Promapp](https://www.promapp.com/about-us/contact-us/). Можно также ознакомиться с шаблонами в диалоговом окне **Базовая конфигурация SAML** на портале Azure.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните ссылку **Скачать** рядом с нужным **сертификатом (Base64)** и сохраните сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

7. Требуемые URL-адреса можно скопировать из раздела **Настройка Promapp**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    1. **URL-адрес входа**.

    1. **Идентификатор Azure AD**

    1. **URL-адрес выхода**.

### <a name="configure-promapp-single-sign-on"></a>Настройка единого входа в Promapp

1. Войдите на корпоративный сайт Promapp с правами администратора.

2. В меню в верхней части окна нажмите **Admin** (Администрирование).
   
    ![Выбор меню администрирования][12]

3. Выберите **Configure** (Настроить):
   
    ![Выбор пункта меню настроек][13]

4. В диалоговом окне **Security** (Безопасность) сделайте следующее:
   
    ![Диалоговое окно безопасности][14]
    
    1. В поле **SSO Login URL** (URL-адрес для единого входа) вставьте **URL-адрес входа**, скопированный на портале Azure.
    
    1. В списке **SSO — Single Sign-on Mode** (Режим единого входа) выберите **Optional** (Необязательно). Щелкните **Сохранить**.

       > [!NOTE]
       > Режим Optional (Необязательно) предназначен только для тестирования. После настройки задайте для параметра **SSO — Single Sign-on Mode** (Режим единого входа) значение **Required** (Обязательно), чтобы аутентификация выполнялась в Azure AD для всех пользователей.

    1. Откройте в Блокноте сертификат, скачанный на портала Azure. Скопируйте содержимое сертификата без первой и последней строки ( **---BEGIN CERTIFICATE---** и **---END CERTIFICATE---** ). Вставьте содержимое в сертификата в поле **SSO-x.509 Certificate** (Сертификат единого входа x.509) и нажмите **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Выбор "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Выбор "Новый пользователь"](common/new-user.png)

3. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    1. В поле **Имя** введите **BrittaSimon**.
  
    1. В поле **Имя пользователя** введите **BrittaSimon @\<ваш_домен>.\<доменная_зона>** . (Например, BrittaSimon@contoso.com).

    1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и как предоставить такому пользователю доступ к Promapp.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Promapp**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Promapp**.

    ![Список приложений](common/all-applications.png)

3. В области слева выберите **Пользователи и группы**.

    ![Выбор параметра "Пользователи и группы"](common/users-groups-blade.png)

4. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** — **Пользователи и группы**.

    ![Выбор элемента "Добавить пользователя"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка. В нижней части экрана нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** выберите **Назначить**.

### <a name="just-in-time-user-provisioning"></a>JIT-подготовка пользователей

Promapp поддерживает JIT-подготовку пользователей. Эта функция включена по умолчанию. Если пользователь еще не существует в Promapp, он создается после проверки подлинности.

### <a name="test-single-sign-on"></a>Проверка единого входа

Теперь необходимо проверить конфигурацию единого входа Azure AD с помощью Панели доступа.

Щелкнув плитку Promapp на Панели доступа, вы автоматически войдете в экземпляр Promapp, для которого настроили единый вход. Дополнительные сведения о Панели доступа см. в статье [Access and use apps on the My Apps portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Доступ к приложениям и их использование на портале "Мои приложения").

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png
