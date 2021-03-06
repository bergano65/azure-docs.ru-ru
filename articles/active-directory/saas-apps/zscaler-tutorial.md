---
title: Руководство по Интеграции единого входа Azure Active Directory с Zscaler | Документация Майкрософт
description: Вы можете узнать, как настроить единый вход Azure Active Directory в Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcf0341e03a5d95abbe8b1a8ce69379fef8251b7
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "68989052"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler"></a>Руководство по Интеграции единого входа Azure Active Directory с Zscaler

Это руководство описывает, как интегрировать Zscaler с Azure Active Directory (Azure AD). Интеграция Zscaler с Azure AD дает следующие возможности:

* С помощью Azure AD вы можете контролировать доступ к Zscaler.
* Автоматический вход пользователей в Zscaler с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Zscaler с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Zscaler поддерживает единый вход, инициируемый **поставщиком услуг**.
* Zscaler поддерживает **JIT**-подготовку пользователей.

## <a name="adding-zscaler-from-the-gallery"></a>Добавление Zscaler из коллекции

Чтобы настроить интеграцию Zscaler с Azure AD, нужно добавить Zscaler из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в окно поиска введите **Zscaler**.
1. Выберите **Zscaler** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler"></a>Настройка и проверка единого входа Azure AD для Zscaler

Настройте и проверьте единый вход Azure AD в Zscaler с помощью тестового пользователя **B.Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Zscaler.

Чтобы настроить и проверить единый вход Azure AD в Zscaler, выполните следующие стандартные блоки действий:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Zscaler](#configure-zscaler-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Zscaler](#create-zscaler-test-user)** требуется для того, чтобы в Zscaler существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Zscaler** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<companyname>.zscaler.net`.

    > [!NOTE]
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Для получения значения обратитесь к [группе поддержки клиентов Zscaler](https://www.zscaler.com/company/contact). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. Приложение Zscaler ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию. Нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![image](common/edit-attribute.png)

1. В дополнение к описанному выше приложение Zscaler ожидает несколько дополнительных атрибутов в ответе SAML. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** выполните следующие действия, чтобы добавить атрибут токена SAML, как показано в приведенной ниже таблице.

    | ИМЯ | Исходный атрибут |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Выберите команду **Сохранить**.

    > [!NOTE]
    > Перейдите по [этой ссылке](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management), чтобы прочитать о настройке роли в Azure Active Directory.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Требуемые URL-адреса можно скопировать из раздела **Настройка Zscaler**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Zscaler.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Zscaler**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Zscaler**.

    ![Ссылка на Zscaler в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в нижней части экрана нажмите кнопку **Выбрать**.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_users.png)

6. В диалоговом окне **Выбор роли** выберите соответствующие роли пользователей из списка, а затем нажмите кнопку **Выбрать** в нижней части экрана.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_roles.png)

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_assign.png)

## <a name="configure-zscaler-sso"></a>Настройка единого входа Zscaler

1. Для автоматизации настройки в Zscaler необходимо установить **Расширение браузера "Безопасный вход в мои приложения"** , щелкнув **Установить расширение**.

    ![Расширение "Мои приложения"](common/install-myappssecure-extension.png)

1. Чтобы перейти к приложению Zscaler после добавления расширения в браузере, щелкните **Настройка Zscaler**. После этого укажите учетные данные администратора для входа в Zscaler. Расширение браузера автоматически настроит приложение и автоматизирует шаги 3–6.

    ![Установка единого входа](common/setup-sso.png)

1. Если необходимо вручную настроить Zscaler, откройте новое окно веб-браузера, зайдите на сайт компании Zscaler в роли администратора и выполните следующие шаги.

1. Выберите **Administration (Администрирование) > Authentication (Проверка подлинности) > Authentication Settings (Параметры проверки подлинности)** и выполните следующие действия:

    ![Администрирование](./media/zscaler-tutorial/ic800206.png "Администрирование")

    a. В разделе Authentication Type (Тип проверки подлинности) выберите **SAML**.

    b. Нажмите кнопку **Configure SAML** (Настроить SAML).

1. В окне **Изменить параметры SAML** выполните следующие действия и нажмите кнопку "Сохранить".  

    ![Управление пользователями и проверкой подлинности](./media/zscaler-tutorial/ic800208.png "Управление пользователями и проверкой подлинности")
    
    a. В текстовое поле **SAML Portal URL** (URL-адрес портала SAML) вставьте **URL-адрес входа**, скопированный на портале Azure.

    b. В текстовое поле **Login Name Attribute** (Атрибут имени входа) введите **NameID**.

    c. Нажмите **Upload** (Отправить), чтобы загрузить сертификат для подписи SAML, который вы скачали на портале Azure в разделе **Public SSL Certificate** (Публичный SSL-сертификат).

    d. Включите параметр **Enable SAML Auto-Provisioning** (Включить автоматическую подготовку SAML).

    д. В текстовое поле **User Display Name Attribute** (Атрибут отображаемого имени пользователя) введите **displayName**, если вы хотите включить автоматическую подготовку SAML для атрибутов displayName.

    Е. В текстовое поле **Group Name Attribute** (Атрибут имени группы) введите **memberOf**, если вы хотите включить автоматическую подготовку SAML для атрибутов memberOf.

    ж. В поле **Department Name Attribute** (Атрибут имени отдела) введите **department**, если вы хотите включить автоматическую подготовку SAML для атрибутов department.

    h. Выберите команду **Сохранить**.

1. На странице **Настройка проверки подлинности пользователей** выполните следующие действия.

    ![Администрирование](./media/zscaler-tutorial/ic800207.png)

    a. Наведите указатель мыши на меню **Activation** (Активация) в нижнем левом углу.

    b. Щелкните **Активировать**.

## <a name="configuring-proxy-settings"></a>Настройка параметров прокси-сервера

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Настройка параметров прокси-сервера в Internet Explorer

1. Запустите **Internet Explorer**.

1. В меню **Сервис** выберите **Свойства браузера**, чтобы открыть диалоговое окно **Свойства браузера**.

    ![Свойства браузера](./media/zscaler-tutorial/ic769492.png "Свойства браузера")

1. Щелкните вкладку **Подключения** .
  
    ![Подключения](./media/zscaler-tutorial/ic769493.png "Подключения")

1. Нажмите кнопку **Настройка сети**, чтобы открыть диалоговое окно **Настройка сети**.

1. В разделе "Прокси-сервер" выполните следующие действия.   

    ![Прокси-сервер](./media/zscaler-tutorial/ic769494.png "Прокси-сервер")

    a. Установите флажок **Использовать прокси-сервер для локальной сети**.

    b. В текстовом поле "Адрес" введите **gateway.zscaler.net**.

    c. В текстовом поле "Порт" введите **80**.

    d. Установите флаг **Не использовать прокси-сервер для локальных адресов**.

    д. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Настройка параметров локальной сети**.

1. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства браузера**.

### <a name="create-zscaler-test-user"></a>Создание тестового пользователя Zscaler

В этом разделе вы создадите в Zscaler пользователя Britta Simon. В Zscaler поддерживается JIT-подготовка пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Zscaler, он создается после проверки подлинности.

> [!Note]
> Чтобы создать пользователя вручную, обратитесь к [группе поддержки Zscaler](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Zscaler на Панели доступа, вы автоматически войдете в приложение Zscaler, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Пробное использование Zscaler с помощью Azure AD](https://aad.portal.azure.com/)
