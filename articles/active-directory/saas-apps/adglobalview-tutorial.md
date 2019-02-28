---
title: Руководство по Интеграция Azure Active Directory с ADP Globalview | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в ADP Globalview.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c520e85979a3cee1cffb4cab5761012b765c383d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880977"
---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>Руководство по Интеграция Azure Active Directory с ADP Globalview

В этом руководстве описано, как интегрировать ADP Globalview с Azure Active Directory (Azure AD).
Интеграция Azure AD с ADP Globalview обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к ADP GlobalView.
* Вы можете включить автоматический вход для пользователей в ADP GlobalView (единый вход) с помощью их учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с ADP Globalview, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка ADP GlobalView с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* ADP GlobalView поддерживает инициированный единый вход **выдающей точки распространения**.

## <a name="adding-adp-globalview-from-the-gallery"></a>Добавление ADP Globalview из коллекции

Чтобы настроить интеграцию ADP Globalview с Azure AD, необходимо добавить ADP Globalview из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ADP Globalview из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **ADP GlobalView**, выберите **ADP GlobalView** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![ADP GlobalView в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в ADP GlobalView с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ADP GlobalView.

Чтобы настроить и проверить единый вход Azure AD в ADP Globalview, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в ADP GlobalView](#configure-adp-globalview-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя ADP GlobalView](#create-adp-globalview-test-user)** нужно для того, чтобы в ADP GlobalView существовала копия пользователя Britta Simon, связанная с представлением пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в ADP GlobalView, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **ADP GlobalView** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения ADP GlobalView](common/idp-identifier.png)

    В текстовом поле **Идентификатор** введите URL-адрес в таком формате:

    | |
    |--|
    | `https://<subdomain>.globalview.adp.com/federate`|
    | `https://<subdomain>.globalview.adp.com/federate2`|
    | |

    > [!NOTE]
    > Это значение приведено для справки. Вместо него нужно указать фактический идентификатор. Чтобы получить это значение, обратитесь к [группе поддержки клиентов ADP GlobalView](https://www.adp.com/contact-us/overview.aspx). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. Приложение ADP GlobalView ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![изображение](common/edit-attribute.png)

6. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** измените утверждения, щелкнув значок **Изменить**, или добавьте утверждение, нажав кнопку **Добавить новое утверждение**, чтобы настроить атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия. 

    | ИМЯ  |  Исходный атрибут|
    | ---------------| --------- |
    | personalimmutableid | user.extensionattribute2 |
    | email               | user.mail |
    | userid              | user.userprincipalname|
    | | |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](common/new-save-attribute.png)

    ![изображение](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    4.3. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

    > [!NOTE] 
    > Чтобы настроить утверждение SAML, обратитесь в [службу поддержки ADP Globalview](https://www.adp.com/contact-us/overview.aspx) и запросите значение атрибута уникального идентификатора для своего клиента. Это значение необходимо для настройки пользовательского утверждения для вашего приложения.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

8. Скопируйте требуемый URL-адрес из раздела **Настройка ADP GlobalView**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-adp-globalview-single-sign-on"></a>Настройка единого входа ADP GlobalView

Чтобы настроить единый вход на стороне **ADP GlobalView**, нужно отправить скачанный **сертификат (Base64)** и соответствующие URL-адреса, скопированные на портале Azure в [группу поддержки ADP GlobalView](https://www.adp.com/contact-us/overview.aspx). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к ADP Globalview.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **ADP GlobalView**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **ADP Globalview**.

    ![Ссылка на ADP GlobalView в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-adp-globalview-test-user"></a>Создание тестового пользователя ADP GlobalView

В этом разделе описано, как создать пользователя Britta Simon в приложении ADP GlobalView. Обратитесь в  [группу поддержки ADP GlobalView](https://www.adp.com/contact-us/overview.aspx), чтобы добавить пользователей на платформу ADP GlobalView. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "ADP Globalview" на панели доступа, вы автоматически войдете в ADP Globalview, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
