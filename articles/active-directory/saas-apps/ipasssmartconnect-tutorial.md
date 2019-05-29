---
title: Руководство по Интеграция Azure Active Directory с iPass SmartConnect | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 8f8dc8219d65505952f35ad018ef19aeb68d64e9
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989766"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Руководство по интеграции Azure Active Directory с iPass SmartConnect

В этом руководстве описано, как интегрировать iPass SmartConnect с Azure Active Directory (Azure AD).
Интеграция iPass SmartConnect с Azure AD позволяет получить следующие преимущества:

* Вы можете контролировать доступ к iPass SmartConnect с помощью Azure AD.
* Вы можете включить автоматический вход пользователей в iPass SmartConnect (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с iPass SmartConnect, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка iPass SmartConnect с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* iPass SmartConnect поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.
* iPass SmartConnect поддерживает **JIT**-подготовку пользователей.

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Добавление iPass SmartConnect из коллекции

Чтобы настроить интеграцию iPass SmartConnect с Azure AD, необходимо добавить iPass SmartConnect из коллекции в список управляемых приложений SaaS.

**Чтобы добавить iPass SmartConnect из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **iPass SmartConnect**, выберите **iPass SmartConnect** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![iPass SmartConnect в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в iPass SmartConnect с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в iPass SmartConnect.

Чтобы настроить и проверить единый вход Azure AD в iPass SmartConnect, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в iPass SmartConnect](#configure-ipass-smartconnect-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя iPass SmartConnect](#create-ipass-smartconnect-test-user)** требуется для того, чтобы в iPass SmartConnect существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в iPass SmartConnect, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **iPass SmartConnect** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure.

    ![Сведения о домене и URL-адресах единого входа для приложения iPass SmartConnect](common/preintegrated.png)

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения iPass SmartConnect](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`.

6. Приложение iPass SmartConnect ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![image](common/edit-attribute.png)

7. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** измените утверждения, щелкнув значок **Изменить**, или добавьте утверждение, нажав кнопку **Добавить новое утверждение**, чтобы настроить атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.

    | ИМЯ |  Исходный атрибут|
    | ---------------| ----------|
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.userprincipalname |
    | Имя пользователя | user.userprincipalname |
    | | |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

9. Скопируйте требуемый URL-адрес в разделе **Настройка iPass SmartConnect**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-ipass-smartconnect-single-sign-on"></a>Настройка единого входа в iPass SmartConnect

Чтобы настроить единый вход на стороне **iPass SmartConnect**, нужно отправить скачанный **XML-файл метаданных федерации** и соответствующие URL-адреса, скопированные на портале Azure, в [службу поддержки iPass SmartConnect](mailto:help@ipass.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension** .  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к iPass SmartConnect.

1. На портале Azure последовательно выберите **Корпоративные приложения**, **Все приложения**, а затем — **iPass SmartConnect**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **iPass SmartConnect**.

    ![Ссылка iPass SmartConnect в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-ipass-smartconnect-test-user"></a>Создание тестового пользователя iPass SmartConnect

В этом разделе описано, как создать пользователя Britta Simon в приложении iPass SmartConnect. Обратитесь к  [группе поддержки iPass SmartConnect](mailto:help@ipass.com) , чтобы добавить пользователей или домен в список разрешений для платформы iPass SmartConnect. Если служба поддержки добавит домен, пользователи будут автоматически подготовлены для платформы iPass SmartConnect. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

**Чтобы проверить приложение в режиме, инициируемом поставщиком услуг, выполните следующие действия:**

a. Скачайте клиент iPass SmartConnect [здесь](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![Ссылка iPass SmartConnect в списке "Приложения"](./media/ipasssmartconnect-tutorial/testing3.png)

b. Установите и запустите клиент.

c. Нажмите кнопку **Начать работу**.

![Ссылка iPass SmartConnect в списке "Приложения"](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Введите имя пользователя в Azure с доменом. Нажмите кнопку **Продолжить**. Вы перейдете на страницу входа в Azure.

![Ссылка iPass SmartConnect в списке "Приложения"](./media/ipasssmartconnect-tutorial/testing2.png) 

д. После проверки подлинности будет запущена активация клиента. Клиент будет активирован.

**Чтобы проверить приложение в режиме, инициируемом поставщиком удостоверений, выполните следующие действия:**

a. Войдите в [https://myapps.microsoft.com](https://myapps.microsoft.com).

b. Щелкните приложение iPass SmartConnect.

c. Будет открыта страница SSA. Нажмите кнопку **Скачать приложение для Windows**, чтобы установить клиент iPass SmartConnect.

![Ссылка iPass SmartConnect в списке "Приложения"](./media/ipasssmartconnect-tutorial/testing4.png)

d. При первом запуске клиента активация будет запущена автоматически после принятия условий использования.

д. Если активация не запускается, нажмите кнопку "Активировать" на странице SSA, чтобы начать активацию.

Е. Клиент будет активирован.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)