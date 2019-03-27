---
title: Руководство по Интеграция Azure Active Directory с Euromonitor Passport | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Euromonitor Passport.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7740905c-04c4-4d8c-ad90-523a6cd1e206
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cdf8f78a54c89ac89441a87ca0ba34d61a3d2a2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57888320"
---
# <a name="tutorial-azure-active-directory-integration-with-euromonitor-passport"></a>Руководство. Интеграция Azure Active Directory с Euromonitor Passport

Из этого руководства вы узнаете, как интегрировать Euromonitor Passport с Azure Active Directory (Azure AD).
Интеграция Euromonitor Passport с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Euromonitor Passport.
* Можно включить автоматический вход пользователей в Euromonitor Passport (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Euromonitor Passport, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка Euromonitor Passport с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Euromonitor Passport поддерживает единый вход, инициируемый **поставщиком услуг и поставщиком удостоверений**.

## <a name="adding-euromonitor-passport-from-the-gallery"></a>Добавление Euromonitor Passport из коллекции

Чтобы настроить интеграцию Euromonitor Passport с Azure AD, необходимо добавить Euromonitor Passport из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Euromonitor Passport из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Euromonitor Passport**, выберите **Euromonitor Passport** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Euromonitor Passport в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Euromonitor Passport с использованием тестового пользователя **Britta Simon**.
Чтобы обеспечить единый вход, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Euromonitor Passport.

Чтобы настроить и проверить единый вход Azure AD в Euromonitor Passport, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройку единого входа в Euromonitor Passport](#configure-euromonitor-passport-single-sign-on)** необходимо выполнить, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Euromonitor Passport](#create-euromonitor-passport-test-user)** требуется для того, чтобы в Euromonitor Passport существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Euromonitor Passport, сделайте следующее:

1. На странице интеграции с приложением **Euromonitor Passport** [портала Azure](https://portal.azure.com/) щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure.

    ![Сведения о домене и URL-адресах единого входа для приложения Euromonitor Passport](common/preintegrated.png)

5. Если вы хотите настроить приложение в режиме, инициированном **поставщиком услуг**, вам нужно получить URL-адрес входа в [службе поддержки Euromonitor Passport](mailto:passport.support@euromonitor.com). Получив этот URL-адрес, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Euromonitor Passport](common/metadata-upload-additional-signon.png)

    Вставьте полученное значение URL-адреса входа, полученного от службы поддержки Euromonitor Passport, в текстовое поле URL-адреса входа.

6. Для приложения Euromonitor Passport требуется передавать оператор утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![изображение](./media/euromonitor-passport-tutorial/attribute1.png)

7. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** измените утверждения, щелкнув значок **Изменить**, или добавьте утверждение, нажав кнопку **Добавить новое утверждение**, чтобы настроить атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.

    | ИМЯ | Исходный атрибут|
    | ---------------| --------- |
    | Значение идентификатора имени | user.userprincipalname |
    | emailaddress | user.mail |

    > [!NOTE]
    > Администраторы клиента могут добавить или изменить атрибуты в зависимости от поставленной задачи.

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](common/new-save-attribute.png)

    ![изображение](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы копировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

### <a name="configure-euromonitor-passport-single-sign-on"></a>Настройка единого входа в приложении Euromonitor Passport.

Чтобы настроить единый вход на стороне **Euromonitor Passport**, необходимо отправить созданный **URL-адрес метаданных федерации приложений** в [службу технической поддержки Euromonitor Passport](mailto:passport.support@euromonitor.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@домен_вашей_компании.доменная_зона**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В рамках этого раздела вы предоставите пользователю Britta Simon доступ к Euromonitor Passport для использования единого входа Azure.

1. На портале Azure последовательно выберите **Корпоративные приложения**, **Все приложения** и **Euromonitor Passport**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Euromonitor Passport**.

    ![Ссылка на Euromonitor Passport в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-euromonitor-passport-test-user"></a>Создание тестового пользователя в приложении Euromonitor Passport

В рамках этого раздела вы создадите пользователя Britta Simon в приложении Euromonitor Passport. Обратитесь в  [службу технической поддержки Euromonitor Passport](mailto:passport.support@euromonitor.com), чтобы добавить пользователей на платформу Euromonitor Passport. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Euromonitor Passport на панели доступа, вы автоматически войдете в приложение Euromonitor Passport, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

