---
title: Руководство по Интеграция Azure Active Directory с Zscaler Three | Документация Майкрософт
description: Вы можете узнать, как настроить единый вход Azure Active Directory в Zscaler Three.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 5fba7498f724c13297d05fc66fc57e331f096188
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825655"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>Руководство по Интеграция Azure Active Directory с Zscaler Three

Это руководство описывает, как интегрировать Zscaler Three с Azure Active Directory (Azure AD).
Интеграция Azure AD с Zscaler Three обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Zscaler Three.
* Вы можете включить автоматический вход пользователей в Zscaler Three (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Zscaler Three, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка Zscaler Three с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Zscaler Three поддерживает единый вход, инициируемый **поставщиком услуг**.

* Zscaler Three поддерживает **JIT**-подготовку пользователей.

## <a name="adding-zscaler-three-from-the-gallery"></a>Добавление Zscaler Three из коллекции

Чтобы настроить интеграцию Zscaler Three с Azure AD, нужно добавить Zscaler Three из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Zscaler Three из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Zscaler Three**, выберите **Zscaler Three** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Zscaler Three в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Zscaler Three с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Zscaler Three.

Чтобы настроить и проверить единый вход Azure AD в Zscaler Three, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Zscaler Three](#configure-zscaler-three-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Zscaler Three](#create-zscaler-three-test-user)** требуется для того, чтобы в Zscaler Three существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Zscaler Three, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Zscaler Three** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Zscaler Three](common/sp-intiated.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес: `https://login.zscalerthree.net/sfc_sso`.

5. Приложение Zscaler Three ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию. Нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![image](common/edit-attribute.png)

6. В дополнение к описанному выше приложение Zscaler Three ожидает несколько дополнительных атрибутов в ответе SAML. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** выполните следующие действия, чтобы добавить атрибут токена SAML, как показано в приведенной ниже таблице.
    
    | ИМЯ | Исходный атрибут |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.
    
    Е. Выберите команду **Сохранить**.

    > [!NOTE]
    > Перейдите по [этой ссылке](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management), чтобы прочитать о настройке роли в Azure Active Directory.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

8. Требуемые URL-адреса можно скопировать из раздела **Настройка Zscaler Three**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-zscaler-three-single-sign-on"></a>Настройка единого входа для Zscaler Three

1. Для автоматизации настройки в Zscaler Three необходимо установить **Расширение браузера "Безопасный вход в мои приложения"** , щелкнув **Установить расширение**.

    ![Расширение "Мои приложения"](common/install-myappssecure-extension.png)

2. Чтобы перейти к приложению Zscaler Three после добавления расширения в браузере, щелкните **Настройка Zscaler Three**. После этого укажите учетные данные администратора для входа в Zscaler Three. Расширение браузера автоматически настроит приложение и автоматизирует шаги 3–6.

    ![Настройка единого входа](common/setup-sso.png)

3. Если необходимо вручную настроить Zscaler Three, откройте новое окно веб-браузера, зайдите на корпоративный сайт Zscaler Three в роли администратора и выполните следующие шаги:

4. Выберите **Administration (Администрирование) > Authentication (Проверка подлинности) > Authentication Settings (Параметры проверки подлинности)** и выполните следующие действия:
   
    ![Администрирование](./media/zscaler-three-tutorial/ic800206.png "Администрирование")

    a. В разделе Authentication Type (Тип проверки подлинности) выберите **SAML**.

    b. Нажмите кнопку **Configure SAML** (Настроить SAML).

5. В окне **Изменить параметры SAML** выполните следующие действия и нажмите кнопку "Сохранить".  
            
    ![Управление пользователями и проверкой подлинности](./media/zscaler-three-tutorial/ic800208.png "Управление пользователями и проверкой подлинности")
    
    a. В текстовое поле **SAML Portal URL** (URL-адрес портала SAML) вставьте **URL-адрес входа**, скопированный на портале Azure.

    b. В текстовое поле **Login Name Attribute** (Атрибут имени входа) введите **NameID**.

    c. Нажмите **Upload** (Отправить), чтобы загрузить сертификат для подписи SAML, который вы скачали на портале Azure в разделе **Public SSL Certificate** (Публичный SSL-сертификат).

    d. Включите параметр **Enable SAML Auto-Provisioning** (Включить автоматическую подготовку SAML).

    д. В текстовое поле **User Display Name Attribute** (Атрибут отображаемого имени пользователя) введите **displayName**, если вы хотите включить автоматическую подготовку SAML для атрибутов displayName.

    Е. В текстовое поле **Group Name Attribute** (Атрибут имени группы) введите **memberOf**, если вы хотите включить автоматическую подготовку SAML для атрибутов memberOf.

    ж. В поле **Department Name Attribute** (Атрибут имени отдела) введите **department**, если вы хотите включить автоматическую подготовку SAML для атрибутов department.

    h. Выберите команду **Сохранить**.

6. На странице **Настройка проверки подлинности пользователей** выполните следующие действия.

    ![Администрирование](./media/zscaler-three-tutorial/ic800207.png)

    a. Наведите указатель мыши на меню **Activation** (Активация) в нижнем левом углу.

    b. Щелкните **Активировать**.

## <a name="configuring-proxy-settings"></a>Настройка параметров прокси-сервера
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Настройка параметров прокси-сервера в Internet Explorer

1. Запустите **Internet Explorer**.

2. В меню **Сервис** выберите **Свойства браузера**, чтобы открыть диалоговое окно **Свойства браузера**.   
    
     ![Свойства браузера](./media/zscaler-three-tutorial/ic769492.png "Свойства браузера")

3. Щелкните вкладку **Подключения** .   
  
     ![Подключения](./media/zscaler-three-tutorial/ic769493.png "Подключения")

4. Нажмите кнопку **Настройка сети**, чтобы открыть диалоговое окно **Настройка сети**.

5. В разделе "Прокси-сервер" выполните следующие действия.   
   
    ![Прокси-сервер](./media/zscaler-three-tutorial/ic769494.png "Прокси-сервер")

    a. Установите флажок **Использовать прокси-сервер для локальной сети**.

    b. В текстовом поле "Адрес" введите **gateway.Zscaler Three.net**.

    c. В текстовом поле "Порт" введите **80**.

    d. Установите флаг **Не использовать прокси-сервер для локальных адресов**.

    д. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Настройка параметров локальной сети**.

6. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства браузера**.

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Zscaler Three.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Zscaler Three**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Zscaler Three**.

    ![Ссылка на Zscaler Three в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в нижней части экрана нажмите кнопку **Выбрать**.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

6. В диалоговом окне **Выбор роли** выберите соответствующие роли пользователей из списка, а затем нажмите кнопку **Выбрать** в нижней части экрана.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

### <a name="create-zscaler-three-test-user"></a>Создание тестового пользователя Zscaler Three

В этом разделе вы создадите пользователя Britta Simon в Zscaler Three. Приложение Zscaler Three поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Zscaler Three, он создается при попытке доступа к Zscaler Three.

>[!Note]
>Чтобы создать пользователя вручную, обратитесь к [группе поддержки Zscaler Three](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Zscaler Three" на Панели доступа, вы автоматически войдете в приложение Zscaler Three, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

