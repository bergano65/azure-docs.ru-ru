---
title: Руководство. Интеграция Azure Active Directory c Palo Alto Networks (GlobalProtect) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Palo Alto Networks (GlobalProtect).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/11/2018
ms.author: jeedes
ms.openlocfilehash: 16bcd61d226fe97e9f3e4eb5c40f2fdf6c304a12
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808287"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---globalprotect"></a>Руководство. Интеграция Azure Active Directory c Palo Alto Networks (GlobalProtect)

В этом руководстве описано, как интегрировать Palo Alto Networks (GlobalProtect) с Azure Active Directory (Azure AD).
Интеграция Azure AD с Palo Alto Networks (GlobalProtect) обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать предоставление доступа к Palo Alto Networks (GlobalProtect).
* Вы можете включить автоматический вход пользователей в Palo Alto Networks (GlobalProtect) (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Palo Alto Networks (GlobalProtect), вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* подписка Palo Alto Networks (GlobalProtect) с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* Palo Alto Networks (GlobalProtect) поддерживает единый вход, инициированный **поставщиком услуг**.
* Palo Alto Networks (GlobalProtect) поддерживает **JIT**-подготовку пользователей.

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Добавление Palo Alto Networks (GlobalProtect) из коллекции

Чтобы настроить интеграцию Palo Alto Networks (GlobalProtect) с Azure AD, необходимо добавить Palo Alto Networks (GlobalProtect) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Palo Alto Networks (GlobalProtect) из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Palo Alto Networks (GlobalProtect)** и выберите **Palo Alto Networks (GlobalProtect)** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Palo Alto Networks (GlobalProtect) в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Palo Alto Networks (GlobalProtect) с использованием тестового пользователя **Britta Simon**.
Для правильной работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем Palo Alto Networks (GlobalProtect).

Чтобы настроить и проверить единый вход Azure AD в Palo Alto Networks (GlobalProtect), вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Palo Alto Networks (GlobalProtect)](#configure-palo-alto-networks---globalprotect-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Palo Alto Networks (GlobalProtect)](#create-palo-alto-networks---globalprotect-test-user)** требуется, чтобы в Palo Alto Networks (GlobalProtect) существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD с Palo Alto Networks (GlobalProtect), сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Palo Alto Networks (GlobalProtect)** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для Palo Alto Networks (GlobalProtect)](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<Customer Firewall URL>`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<Customer Firewall URL>/SAML20/SP`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Для получения этих значений обратитесь в [службу поддержки клиентов Palo Alto Networks (GlobalProtect)](https://support.paloaltonetworks.com/support). Можно также обратиться к шаблонам, указанным в разделе **Базовая конфигурация SAML** на портале Azure.

5. Приложение Palo Alto Networks (GlobalProtect) ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![изображение](common/edit-attribute.png)

6. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.

    | ИМЯ | Исходный атрибут|
    | ------|--------- |
    | Имя пользователя  | user.userprincipalname  |
    | | |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](common/new-save-attribute.png)

    ![изображение](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

8. Скопируйте требуемый URL-адрес из раздела **Настройка Palo Alto Networks (GlobalProtect)**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    a. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода

### <a name="configure-palo-alto-networks---globalprotect-single-sign-on"></a>Настройка Palo Alto Networks (GlobalProtect) с поддержкой единого входа

1. В другом окне браузера откройте пользовательский интерфейс администратора брандмауэров Palo Alto с правами администратора.

2. Щелкните **Device** (Устройство).

    ![Настройка единого входа в Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Чтобы импортировать файл метаданных, в левой области навигации выберите **SAML Identity Provider** (Поставщик удостоверений SAML) и нажмите кнопку Import (Импорт).

    ![Настройка единого входа в Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Сделайте следующее в окне Import (Импорт).

    ![Настройка единого входа в Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Укажите имя в текстовом поле **Profile Name** (Имя профиля), например Azure AD GlobalProtect.

    b. В разделе **Identity Provider Metadata** (Метаданные поставщика удостоверений) щелкните **Browse** (Обзор) и выберите файл metadata.xml, загруженный ранее с портала Azure.

    c. Щелкните **ОК**

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее.

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Palo Alto Networks (GlobalProtect), чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Palo Alto Networks (GlobalProtect)**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите **Palo Alto Networks (GlobalProtect)** и выберите его.

    ![Ссылка на Palo Alto Networks (GlobalProtect) в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Создание тестового пользователя в Palo Alto Networks (GlobalProtect)

В этом разделе создается пользователь Britta Simon в приложении Palo Alto Networks (GlobalProtect). Приложение Palo Alto Networks (GlobalProtect) поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Palo Alto Networks (GlobalProtect), он создается после проверки подлинности.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Palo Alto Networks (GlobalProtect) на панели доступа, вы автоматически войдете в приложение Palo Alto Networks (GlobalProtect), для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)