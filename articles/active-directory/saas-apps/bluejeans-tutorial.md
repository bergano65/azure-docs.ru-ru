---
title: Руководство по интеграции Azure Active Directory с BlueJeans | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeedes
ms.openlocfilehash: cff1512c56dba9907adbf1bb4452f11d47d0787d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095234"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Руководство по интеграции Azure Active Directory с BlueJeans

В этом руководстве описано, как интегрировать BlueJeans с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением BlueJeans обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к BlueJeans.
- Вы можете включить автоматический вход пользователей в BlueJeans (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с BlueJeans, вам потребуется:

- подписка Azure AD;
- Подписка с поддержкой единого входа BlueJeans.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление BlueJeans из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-bluejeans-from-the-gallery"></a>Добавление BlueJeans из коллекции

Чтобы настроить интеграцию BlueJeans с Azure AD, необходимо добавить BlueJeans из коллекции в список управляемых приложений SaaS.

**Чтобы добавить BlueJeans из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **BlueJeans**, выберите **BlueJeans** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![BlueJeans в списке результатов](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в BlueJeans с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в BlueJeans соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в BlueJeans.

Чтобы настроить и проверить единый вход Azure AD в BlueJeans, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя BlueJeans](#creating-a-bluejeans-test-user)** нужно для того, чтобы в BlueJeans также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении BlueJeans.

**Чтобы настроить единый вход Azure AD в BlueJeans, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **BlueJeans** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Настройка единого входа](./media/bluejeans-tutorial/tutorial_general_301.png)

4. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](./media/bluejeans-tutorial/tutorial_bluejeans_editurl.png)

5. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения BlueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [группу поддержки BlueJeans](https://support.bluejeans.com/contact).

6. На странице **сертификата подписи SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/bluejeans-tutorial/tutorial_bluejeans_certficate.png) 

7. Требуемый URL-адрес можно скопировать из раздела **Set up BlueJeans** (Настройка BlueJeans).

    a. URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода

    ![Настройка BlueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

8. В другом окне веб-браузера войдите на свой корпоративный веб-сайт **BlueJeans** в качестве администратора.

9. Последовательно щелкните **ADMIN \> GROUP SETTINGS \> SECURITY** (Администратор > Параметры группы > Безопасность).

    ![Администратор](./media/bluejeans-tutorial/IC785868.png "Администратор")

10. В разделе **SECURITY** (Безопасность) выполните следующие действия:

    ![Единый вход SAML](./media/bluejeans-tutorial/IC785869.png "Единый вход SAML")

    a. Выберите параметр **SAML Single Sign On**(Единый вход SAML).

    b. Установите флажок **Enable automatic provisioning**(Включить автоматическую подготовку).

11. После этого выполните следующие действия.

    ![Путь к сертификату](./media/bluejeans-tutorial/IC785870.png "Путь к сертификату")

    a. Щелкните **Choose a File** (Выбрать файл), чтобы отправить сертификат в кодировке Base64, скачанный с портала Azure.

    b. В текстовое поле **Login URL** (URL-адрес входа) вставьте **URL-адрес входа**, скопированный на портале Azure.

    c. В текстовое поле **Password Change URL** (URL-адрес для изменения пароля) вставьте **URL-адрес изменения пароля**, скопированный на портале Azure.

    d. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте **URL-адрес выхода**, скопированный на портале Azure.

12. После этого выполните следующие действия.

    ![Сохранение изменений](./media/bluejeans-tutorial/IC785874.png "Сохранение изменений")

    a. В текстовое поле **User id** (Идентификатор пользователя) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. В текстовое поле **Email** (Электронная почта) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Нажмите кнопку **Сохранить изменения**.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Создание пользователя Azure AD][100]

2. В верхней части экрана выберите **Новый пользователь**.

    ![Создание тестового пользователя Azure AD](./media/bluejeans-tutorial/create_aaduser_01.png) 

3. В разделе свойств пользователя сделайте следующее.

    ![Создание тестового пользователя Azure AD](./media/bluejeans-tutorial/create_aaduser_02.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="creating-a-bluejeans-test-user"></a>Создание тестового пользователя BlueJeans

Цель этого раздела — создать пользователя с именем Britta Simon в BlueJeans. BlueJeans поддерживает автоматическую подготовку пользователей, которая по умолчанию включена. Дополнительные сведения о настройке автоматической подготовки пользователей можно найти [здесь](bluejeans-provisioning-tutorial.md).

**Если необходимо создать пользователя вручную, выполните следующие действия:**

1. Выполните вход на веб-сайт компании **BlueJeans** в качестве администратора.

2. Последовательно выберите пункты **ADMIN \> MANAGE USERS \> ADD USER** (Администратор > Управление пользователями > Добавить пользователя).

    ![Администратор](./media/bluejeans-tutorial/IC785877.png "Администратор")

    >[!IMPORTANT]
    >Вкладка **ADD USER** (Добавить пользователя) доступна, только если на вкладке **SECURITY** (Безопасность) снят флажок **Enable automatic provisioning** (Включить автоматическую подготовку). 

3. В разделе **ADD USER** (Добавление пользователя) выполните следующие действия.

    ![Добавление пользователя](./media/bluejeans-tutorial/IC785886.png "Добавление пользователя")

    a. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    b. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

    c. В текстовом поле **Pick a BlueJeans Username** (Выбрать имя пользователя BlueJeans) введите имя пользователя, например **Brittasimon**

    d. Введите пароль в поле **Create a Password** (Создать пароль).

    д. В текстовое поле **Company** (Компания) введите название компании.

    Е. В текстовое поле **Email Address** (Адрес электронной почты) введите адрес электронной почты пользователя (например, **brittasimon@contoso.com**).

    ж. В текстовое поле **Create a BlueJeans Meeting I.D** (Создать идентификатор собрания BlueJeans) введите идентификатор собрания.

    h. В текстовое поле **Pick a Moderator Passcode** (Выбрать секретный код модератора) введите секретный код.

    i. Щелкните **CONTINUE** (Продолжить).

    ![Добавление пользователя](./media/bluejeans-tutorial/IC785887.png "Add User")

    Дж. Нажмите кнопку **Add user** (Добавить пользователя).

>[!NOTE]
>Вы можете использовать для создания учетной записи пользователя BlueJeans любые другие средства или API, предоставленные BlueJeans для подготовки учетных записей пользователей AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к BlueJeans.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. Из списка приложений выберите **BlueJeans**.

    ![Настройка единого входа](./media/bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент BlueJeans на панели доступа, вы автоматически войдете в приложение BlueJeans.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
