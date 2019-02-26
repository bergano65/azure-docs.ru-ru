---
title: Руководство по Интеграция Azure Active Directory с Workplace by Facebook | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e233de6f0909477e5033d2a0104f9165e86a9077
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311041"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Руководство. Интеграция Azure Active Directory с Workplace by Facebook

В этом руководстве описано, как интегрировать Workplace by Facebook с Azure Active Directory (Azure AD).
Интеграция Workplace by Facebook с Azure AD дает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Workplace by Facebook.
* Вы можете включить автоматический вход пользователей в Workplace by Facebook (единый вход) через учетную запись Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Workplace by Facebook, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка Workplace by Facebook с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> В Facebook имеются два продукта, Workplace Standard (бесплатный) и Workplace Premium (платный). Любой клиент Workplace Premium позволяет настроить SCIM и интеграцию единого входа без какой-либо дополнительной оплаты или лицензий. Единый вход и SCIM недоступны в экземплярах Workplace Standard.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Workplace by Facebook поддерживает единый вход, инициированный **поставщиком услуг**.
* Workplace by Facebook поддерживает **JIT-подготовку**.
* Workplace by Facebook поддерживает **[автоматическую подготовку пользователей](workplacebyfacebook-provisioning-tutorial.md)**.

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Добавление Workplace by Facebook из коллекции

Чтобы настроить интеграцию Workplace by Facebook с Azure AD, необходимо добавить Workplace by Facebook из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Workplace by Facebook из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Workplace by Facebook**, на панели результатов выберите **Workplace by Facebook** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Workplace by Facebook в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Workplace by Facebook с использованием тестового пользователя **Britta Simon**.
Для работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workplace by Facebook.

Чтобы настроить и проверить единый вход Azure AD в Workplace by Facebook, вам потребуется выполнить действия в указанных ниже стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Workplace by Facebook](#configure-workplace-by-facebook-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Workplace by Facebook](#create-workplace-by-facebook-test-user)** требуется для того, чтобы в Workplace by Facebook существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Workplace by Facebook, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Workplace by Facebook** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Workplace by Facebook](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<instancename>.facebook.com`.

    б) В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://www.facebook.com/company/<instanceID>`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Правильные значения для своего сообщества Workplace см. на странице аутентификации панели мониторинга компании Workplace.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Скопируйте требуемый URL-адрес из раздела **Настройка Workplace by Facebook**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-workplace-by-facebook-single-sign-on"></a>Настройка единого входа в Workplace by Facebook

1. В другом окне веб-браузера войдите на свой корпоративный сайт Workplace by Facebook в качестве администратора.
  
    > [!NOTE]
    > В рамках процесса проверки подлинности SAML приложение Workplace может использовать строки запросов размером до 2,5 КБ для передачи параметров в Azure AD.

2. На **панели администрирования** перейдите на вкладку **Security** (Безопасность).

    ![Панель администрирования](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. На вкладке **Authentication** (Аутентификация) выберите **Single-Sign On (SSO)** (Единый вход (SSO)) и выполните следующие действия:

    ![Вкладка "Authentication" (Аутентификация)](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. В текстовое поле **SAML URL** (URL-адрес SAML) вставьте **URL-адрес SAML**, скопированный на портале Azure.

    б) В текстовое поле **SAML Issuer URL** (URI издателя SAML) вставьте **идентификатор Azure AD**, скопированный на портале Azure.

    c. В текстовое поле **SAML Logout Redirect** (Перенаправление для выхода SAML) вставьте значение **URL-адреса выхода**, скопированное на портале Azure (необязательно).

    4.3. Откройте в Блокноте **сертификат в кодировке Base64**, скачанный с портала Azure, скопируйте его содержимое в буфер обмена и вставьте в текстовое поле **SAML Certificate** (Сертификат SAML).

    д. Скопируйте **URL-адрес аудитории** и вставьте его в текстовое поле  **Идентификатор (сущности)** в разделе **Базовая конфигурации SAML** на портале Azure.

    Е. Скопируйте **URL-адрес получателя** и вставьте его в текстовое поле  **URL-адрес для входа** в разделе **Базовая конфигурации SAML** на портале Azure.

    ж. Прокрутите страницу до конца раздела и нажмите кнопку **Test SSO** (Проверить единый вход). Появится всплывающее окно со страницей входа в Azure AD. Введите учетные данные, как при обычной аутентификации.

    **Устранение неполадок**. Убедитесь в том, что адрес электронной почты, возвращаемый из Azure AD, совпадает с учетной записью Workplace, которая использовалась для входа.

    h. После успешного прохождения проверки прокрутите страницу до конца и нажмите кнопку **Save** (Сохранить).

    i. Теперь для аутентификации всех пользователей Workplace будет отображаться страница входа в Azure AD.

4. **Перенаправление для выхода SAML (необязательно)** -

    При необходимости можно настроить URL-адрес выхода SAML, с помощью которого можно указать страницу выхода из Azure AD. После активации и настройки этого параметра пользователи больше не будут направляться на страницу выхода из Workplace. Вместо этого они будут переходить по URL-адресу, указанному в параметре перенаправления для выхода SAML.

### <a name="configuring-reauthentication-frequency"></a>Настройка частоты повторной проверки подлинности

Вы можете настроить в Workplace запрос на проверку SAML каждый день, каждые 3 дня, каждую неделю, каждые 2 недели, каждый месяц или никогда.

> [!NOTE]
> Минимальная частота проверки SAML в мобильных приложениях равна одной неделе.

Вы также можете принудительно применить сброс SAML для всех пользователей, используя кнопку Require SAML authentication for all users now (Применить аутентификацию SAML для всех пользователей).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а) В поле **Имя** введите **BrittaSimon**.
  
    б) В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    в) Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    г) Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Workplace by Facebook.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Workplace by Facebook**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **Workplace by Facebook**.

    ![Ссылка на Workplace by Facebook в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-workplace-by-facebook-test-user"></a>Создание тестового пользователя Workplace by Facebook

В этом разделе вы создадите в Workplace by Facebook пользователя с именем Britta Simon. Workplace by Facebook поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Если пользователь не существует в Workplace by Facebook, он создается при попытке доступа к Workplace by Facebook.

>[!Note]
>Если вам нужно создать пользователя вручную, обратитесь к [группе поддержки клиентов Workplace by Facebook](https://workplace.fb.com/faq/).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Workplace by Facebook на панели доступа, вы автоматически войдете в приложение Workplace by Facebook, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Руководство по настройке Google Apps для автоматической подготовки пользователей](workplacebyfacebook-provisioning-tutorial.md)
