---
title: 'Руководство: интеграция Azure Active Directory с Infinite Campus | Документация Майкрософт'
description: Узнайте, как настроить единый вход между Azure Active Directory и Infinite Campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 7ce577901530856690754f3db18ba9f40bfb8a51
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019994"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Руководство: интеграция Azure Active Directory с Infinite Campus

В этом руководстве вы узнаете, как интегрировать Infinite Campus c Azure Active Directory (Azure AD).

Интеграция Infinite Campus с Azure AD обеспечивает следующие преимущества:

- Вы можете контролировать в Azure AD, кто имеет доступ к Infinite Campus.
- Вы можете позволить пользователям автоматически входить в Infinite Campus (единый вход) с помощью учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Infinite Campus, вам потребуется следующее:

- подписка Azure AD;
- Подписка с поддержкой единого входа Infinite Campus.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Infinite Campus из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-infinite-campus-from-the-gallery"></a>Добавление Infinite Campus из коллекции

Чтобы настроить интеграцию Infinite Campus с Azure AD, необходимо добавить Infinite Campus из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Infinite Campus из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Infinite Campus**, выберите **Infinite Campus** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Infinite Campus в списке результатов](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе вы настроите и проверите единый вход Azure AD с помощью Infinite Campus с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в Infinite Campus соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Infinite Campus.

Чтобы настроить и проверить единый вход Azure AD с помощью Infinite Campus, вам потребуется выполнить следующие стандартные блоки:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Infinite Campus](#creating-a-infinite-campus-test-user)** требуется для того, чтобы в Infinite Campus существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Infinite Campus.

**Чтобы настроить единый вход Azure AD в Infinite Campus, выполните следующие действия:**

1. На портале Azure на странице интеграции приложения **Infinite Campus** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Настройка единого входа](common/tutorial_general_301.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](common/editconfigure.png)

5. Если у вас есть **файл метаданных поставщика служб**, выполните следующие действия в разделе **Базовая конфигурация SAML**:

    a. Щелкните **Отправить файл метаданных**.

    ![изображение](common/b9_saml.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![изображение](common/b9(1)_saml.png)

    c. После успешной передачи файла метаданных поля **Идентификатор** и **URL-адрес ответа** в разделе **Базовая конфигурация SAML** автоматически заполняются значениями, как показано ниже:

    ![изображение](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    > [!NOTE]
    > Вы получите **файл метаданных поставщика служб** на странице настройки поставщика услуг единого входа Infinite Campus, что описано далее в этом руководстве.

6. Если у вас нет **файла метаданных поставщика службы**, выполните следующие действия:

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`.

    ![Сведения о домене и URL-адресах единого входа Infinite Campus](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. На странице **Сертификат подписи SAML** в разделе **Сертификат подписи SAML** щелкните **значок** "Копировать", чтобы скопировать ** URL-адрес метаданных федерации приложений** , и вставьте его в Блокнот.

    ![Ссылка для скачивания сертификата](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. Требуемый URL-адрес можно скопировать из раздела **Настройка Infinite Campus**.

    a. URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода

    ![Настройка Infinite Campus](common/configuresection.png)

8. В другом окне браузера войдите в Infinite Campus как администратор безопасности.

9. В левой части меню щелкните **System Administration** (Системное администрирование).

    ![Администратор](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. Выберите **Безопасность пользователя** > **SAML Management** (Управление SAML) > **SSO Service Provider Configuration** (Настройки поставщика службы единого входа).

    ![SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. На странице **SSO Service Provider Configuration** (Настройки поставщика службы единого входа) выполните следующие действия:

    ![Единый вход](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Установите флажок **Enable SAML Single Sign On** (Разрешить единый вход SAML).

    b. Щелкните ссылку **Service Provider Metadata** (Метаданные поставщика услуг), чтобы сохранить **файл метаданных поставщика услуг** на своем компьютере, и отправьте его в раздел **Базовая конфигурация SAML**, чтобы автоматически заполнить значения **идентификатора** и **URL-адреса ответа** на портале Azure.

    c. В разделе **Select an option to retrieve Identity Provider (IDP) server data** (Выберите параметр, чтобы получить данные сервера поставщика удостоверений (IDP)) выберите **URL-адрес метаданных**, вставьте **URL-адрес метаданных федерации приложения** в текстовом поле и щелкните **Синхронизация**.

    d. После нажатия элемента **Синхронизация** значения автоматически заполнятся на странице **SSO Service Provider Configuration** (Настройка поставщика услуг единого входа).

    д. Выберите команду **Сохранить**.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Создание пользователя Azure AD][100]

2. В верхней части экрана выберите **Новый пользователь**.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_01.png) 

3. В разделе свойств пользователя сделайте следующее.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_02.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="creating-a-infinite-campus-test-user"></a>Создание тестового пользователя Infinite Campus

Infinite Campus имеет архитектуру, ориентированную на демографические данные. Обратитесь в [службу технической поддержки Infinite Campus](mailto:sales@infinitecampus.com), чтобы добавить пользователей на платформу Infinite Campus.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Infinite Campus, чтобы он мог использовать единый вход Azure.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **Infinite Campus**.

    ![Настройка единого входа](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Infinite Campus на панели доступа, вы должны автоматически войти в свое приложение Infinite Campus.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
