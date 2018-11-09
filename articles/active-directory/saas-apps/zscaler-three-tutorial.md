---
title: Руководство по интеграции Azure Active Directory с Zscaler Three | Документация Майкрософт
description: Вы можете узнать, как настроить единый вход Azure Active Directory в Zscaler Three.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: b148967af0882993d8ab113bdf0fd3ad3835296f
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092616"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>Руководство по интеграции Azure Active Directory с Zscaler Three

Это руководство описывает, как интегрировать Zscaler Three с Azure Active Directory (Azure AD).

Интеграция Azure AD с Zscaler Three обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Zscaler Three.
- Вы можете включить автоматический вход пользователей в Zscaler Three (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Zscaler Three, вам потребуется:

- подписка Azure AD;
- подписка Zscaler Three с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.
Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Zscaler Three из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-zscaler-three-from-the-gallery"></a>Добавление Zscaler Three из коллекции

Чтобы настроить интеграцию Zscaler Three с Azure AD, нужно добавить Zscaler Three из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Zscaler Three из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Active Directory][1]

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]

3. В верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. На панели результатов выберите **Zscaler Three** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.

В этом разделе описана настройка и проверка единого входа Azure AD в Zscaler Three с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Zscaler Three соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Zscaler Three.

Чтобы настроить и проверить единый вход Azure AD в Zscaler Three, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка параметров прокси-сервера](#configuring-proxy-settings)** нужна для настройки параметров прокси-сервера в Internet Explorer.
3. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Zscaler Three](#creating-a-zscaler-three-test-user)** требуется для того, чтобы в Zscaler Three существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
6. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Zscaler Three.

**Чтобы настроить единый вход Azure AD в Zscaler Three, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Zscaler Three** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Настройка единого входа](./media/zscaler-three-tutorial/tutorial_general_301.png)

3. Если вам нужно переключиться на режим **SAML** из любого другого режима, щелкните **Изменить режим единого входа** в верхней части экрана.

    ![Настройка единого входа](./media/zscaler-three-tutorial/tutorial_general_300.png)

4. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](./media/zscaler-three-tutorial/tutorial_general_302.png)

5. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Настройка единого входа](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    В текстовом поле "URL-адрес для входа" введите URL-адрес: `https://login.zscalerthree.net/sfc_sso`.

6. В разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать **сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png)

8. В разделе **Настройка Zscaler Three** скопируйте **URL-адрес входа**.

    ![Настройка единого входа](./media/zscaler-three-tutorial/tutorial_zscalerthree_configure.png)

9. В другом окне веб-браузера войдите на свой корпоративный сайт Zscaler Three в качестве администратора.

10. В верхнем меню щелкните **Администрирование**.

    ![Администрирование](./media/zscaler-three-tutorial/ic800206.png "Администрирование")

9. В разделе **Manage Administrators & Roles** (Управление администраторами и ролями) щелкните **Manage Users & Authentication** (Управление пользователями и проверкой подлинности).

    ![Управление пользователями и проверкой подлинности](./media/zscaler-three-tutorial/ic800207.png "Управление пользователями и проверкой подлинности")

10. В разделе **Выбор параметров проверки подлинности для организации** выполните следующие действия.

    ![Аутентификация](./media/zscaler-three-tutorial/ic800208.png "Аутентификация")

    a. Выберите параметр **Проверка подлинности с помощью единого входа SAML**.

    b. Щелкните **Настроить параметры единого входа SAML**.

11. На странице диалогового окна **Configure SAML Single Sign-On Parameters** (Настройка параметров единого входа в SAML) выполните указанные ниже действия и нажмите кнопку **Готово**.

    ![Единый вход](./media/zscaler-three-tutorial/ic800209.png "Единый вход")

    a. Вставьте значение **URL-адрес входа**, скопированное на портале Azure, в текстовое поле **URL of the SAML Portal to which users are sent for authentication** (URL-адрес портала SAML, куда пользователи направляются для аутентификации).

    b. В текстовом поле **Attribute containing Login Name** (Атрибут, содержащий имя входа) введите **NameID**.

    c. Чтобы передать скачанный сертификат, щелкните **Zscaler pem**.

    d. Выберите параметр **Включить автоматическую подготовку SAML**.

12. На странице **Настройка проверки подлинности пользователей** выполните следующие действия.

    ![Администрирование](./media/zscaler-three-tutorial/ic800210.png "Администрирование")

    a. Выберите команду **Сохранить**.

    b. Щелкните **Активировать сейчас**.

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

    b. В текстовом поле "Адрес" введите **gateway.zscalerthree.net**.

    c. В текстовом поле "Порт" введите **80**.

    d. Установите флаг **Не использовать прокси-сервер для локальных адресов**.

    д. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Настройка параметров локальной сети**.

6. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства браузера**.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Создание пользователя Azure AD][100]

2. В верхней части экрана выберите **Новый пользователь**.

    ![Создание тестового пользователя Azure AD](./media/zscaler-three-tutorial/create_aaduser_01.png) 

3. В разделе свойств пользователя сделайте следующее.

    ![Создание тестового пользователя Azure AD](./media/zscaler-three-tutorial/create_aaduser_02.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="creating-a-zscaler-three-test-user"></a>Создание тестового пользователя Zscaler Three

Чтобы пользователи Azure AD могли выполнять вход в Zscaler Three, их необходимо подготовить в Zscaler Three. В случае с Zscaler Three подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.

1. Войдите в клиент **Zscaler Three**.

2. Щелкните **Администрирование**.

    ![Администрирование](./media/zscaler-three-tutorial/ic781035.png "Администрирование")

3. Щелкните **Управление пользователями**.

     ![Добавление](./media/zscaler-three-tutorial/ic781036.png "Добавление")

4. На вкладке **Users** (Пользователи) нажмите кнопку **Add** (Добавить).

    ![Добавление](./media/zscaler-three-tutorial/ic781037.png "Добавление")

5. В разделе "Добавить пользователя" выполните следующие действия.

    ![Добавление пользователя](./media/zscaler-three-tutorial/ic781038.png "Добавление пользователя")

    a. Заполните текстовые поля **UserID** (Идентификатор пользователя), **User Display Name** (Отображаемое имя пользователя), **Password** (Пароль), **Confirm Password** (Подтверждение пароля) и выберите **Groups** (Группы) и **Department** (Отдел) для действительной учетной записи Azure AD, которую необходимо подготовить.

    b. Выберите команду **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя Zscaler Three или API, предоставляемые Zscaler Three для подготовки учетных записей пользователя Azure Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Zscaler Three.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. Из списка приложений выберите **Zscaler Three**.

    ![Настройка единого входа](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить пользователя**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Zscaler Three" на панели доступа, вы автоматически войдете в приложение Zscaler Three.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-three-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-three-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-three-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-three-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-three-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-three-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-three-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-three-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-three-tutorial/tutorial_general_203.png