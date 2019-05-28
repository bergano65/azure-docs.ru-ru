---
title: Руководство по Интеграция Azure Active Directory со Screencast-O-Matic | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Screencast-O-Matic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 836ef454e5bdb14fcc3f519f9c6d9f6dfb4b5703
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686315"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>Руководство по Интеграция Azure Active Directory со Screencast-O-Matic

В этом руководстве описано, как интегрировать Screencast-O-Matic с Azure Active Directory (Azure AD).
Интеграция Screencast-O-Matic с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Screencast-O-Matic.
* Вы можете включить автоматический вход пользователей в Screencast-O-Matic (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD со Screencast-O-Matic, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка на Screencast-O-Matic с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Screencast-O-Matic поддерживает единый вход, инициированный **пакетом обновления**.
* Screencast-O-Matic поддерживает **JIT**-подготовку пользователей.

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Добавление Screencast-O-Matic из коллекции

Чтобы настроить интеграцию Screencast-O-Matic с Azure AD, необходимо добавить Screencast-O-Matic из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Screencast-O-Matic из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Screencast-O-Matic**, выберите **Screencast-O-Matic** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Screencast-O-Matic в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в Screencast-O-Matic с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Screencast-O-Matic.

Чтобы настроить и проверить единый вход Azure AD в Screencast-O-Matic, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Screencast-O-Matic](#configure-screencast-o-matic-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Screencast-O-Matic](#create-screencast-o-matic-test-user)** требуется для того, чтобы в Screencast-O-Matic существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Screencast-O-Matic, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Screencast-O-Matic** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Screencast-O-Matic](common/sp-signonurl.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://screencast-o-matic.com/<InstanceName>`.

    > [!NOTE]
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов Screencast-O-Matic](mailto:support@screencast-o-matic.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. В разделе **Настройка Screencast-O-Matic** скопируйте нужные URL-адреса.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-screencast-o-matic-single-sign-on"></a>Настройка единого входа Screencast-O-Matic

1. В другом окне веб-браузера войдите в приложение Screencast-O-Matic с правами администратора.

2. Выберите пункт **Подписка**.

    ![Подписка](./media/screencast-tutorial/tutorial_screencast_sub.png)

3. В разделе **Страница доступа** нажмите кнопку **Настроить**.

    ![Доступ](./media/screencast-tutorial/tutorial_screencast_setup.png)

4. На экране **Страница настройки доступа** выполните следующие действия.

   * В разделе **URL-адрес** в указанном текстовом поле введите имя экземпляра.

    ![Доступ](./media/screencast-tutorial/tutorial_screencast_access.png)

   * В разделе **SAML User Restriction (optional)** (Ограничение пользователя SAML (необязательно)) установите флажок **Require Domain User** (Требовать пользователя домена).

   * В разделе **Upload IDP Metadata XML File** (Отправка XML-файла метаданных поставщика удостоверений) щелкните **Выбрать файл**, чтобы отправить файл метаданных, скачанный на портале Azure.

   * Последовательно выберите **ОК**.

    ![Доступ](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon@yourcompanydomain.extension`. Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Screencast-O-Matic.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Screencast-O-Matic**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Screencast-O-Matic**.

    ![Ссылка на Screencast-O-Matic в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-screencast-o-matic-test-user"></a>Создание тестового пользователя Screencast-O-Matic

В этом разделе вы создадите тестового пользователя с именем Britta Simon в Screencast-O-Matic. Приложение Screencast-O-Matic поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Screencast-O-Matic, то он создается после проверки подлинности. Чтобы создать учетную запись пользователя вручную, обратитесь в  [группу поддержки Screencast-O-Matic](mailto:support@screencast-o-matic.com).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Screencast-O-Matic на Панели доступа, вы автоматически войдете в приложение Screencast-O-Matic, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
