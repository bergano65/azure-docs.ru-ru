---
title: Руководство по интеграции единого входа Azure Active Directory с Workplace by Facebook | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc55130bd840de3960a44ddc1bd0617af185148
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969740"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Руководство по интеграции единого входа Azure Active Directory с Workplace by Facebook

В этом руководстве описано, как интегрировать Workplace by Facebook с Azure Active Directory (Azure AD). Интеграция Workplace by Facebook с Azure AD позволяет:

* Контролировать доступ к Workplace by Facebook с помощью Azure AD.
* Включить автоматический вход пользователей в Workplace by Facebook через учетную запись Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* подписка Workplace by Facebook с поддержкой единого входа.

> [!NOTE]
> В Facebook имеются два продукта, Workplace Standard (бесплатный) и Workplace Premium (платный). Любой клиент Workplace Premium позволяет настроить SCIM и интеграцию единого входа без какой-либо дополнительной оплаты или лицензий. Единый вход и SCIM недоступны в экземплярах Workplace Standard.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Workplace by Facebook поддерживает единый вход, инициированный **поставщиком услуг**.
* Workplace by Facebook поддерживает **[автоматическую подготовку пользователей и ее отмену (рекомендуется)](workplacebyfacebook-provisioning-tutorial.md)** .
* Workplace by Facebook поддерживает **JIT-подготовку**.
* Теперь для приложения Workplace by Facebook можно настроить Azure AD для обеспечения единого входа. В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Добавление Workplace by Facebook из коллекции

Чтобы настроить интеграцию Workplace by Facebook с Azure AD, необходимо добавить Workplace by Facebook из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Workplace by Facebook**.
1. Выберите **Workplace by Facebook** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Настройка и проверка единого входа Azure AD для Workplace by Facebook

Настройте и проверьте единый вход Azure AD в Workplace by Facebook с помощью тестового пользователя **B. Simon**. Чтобы единый вход работал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workplace by Facebook.

Чтобы настроить и проверить единый вход Azure AD в Workplace by Facebook, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
2. **[Настройка единого входа в Workplace by Facebook](#configure-workplace-by-facebook-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя Workplace by Facebook](#create-workplace-by-facebook-test-user)** требуется для того, чтобы в Workplace by Facebook существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
3. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Workplace by Facebook** найдите раздел **Управление** и щелкните **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<instancename>.facebook.com`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://www.facebook.com/company/<instanceID>`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Правильные значения для своего сообщества Workplace см. на странице аутентификации панели мониторинга компании Workplace.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Скопируйте требуемый URL-адрес в разделе **Настройка Workplace by Facebook**.

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

В этом разделе описано, как включить единый вход Azure для пользователя B. Simon, предоставив этому пользователю доступ к Workplace by Facebook.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. Из списка приложений выберите **Workplace by Facebook**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-workplace-by-facebook-sso"></a>Настройка единого входа в Workplace by Facebook

1. Для автоматизации настройки в Workplace by Facebook необходимо установить **расширение браузера My Apps Secure Sign-in**, щелкнув **Установить расширение**.

    ![Расширение "Мои приложения"](common/install-myappssecure-extension.png)

1. Чтобы перейти к приложению Workplace by Facebook после добавления расширения в браузер, щелкните **Настройка Workplace by Facebook**. После этого укажите учетные данные администратора для входа в Workplace by Facebook. Расширение браузера автоматически настроит приложение и автоматизирует шаги 3–5.

    ![Настройка конфигурации](common/setup-sso.png)

1. Если вы хотите настроить Workplace by Facebook вручную, откройте новое окно веб-браузера, войдите на свой корпоративный сайт Workplace by Facebook в качестве администратора и выполните следующие действия:

    > [!NOTE]
    > В рамках процесса проверки подлинности SAML приложение Workplace может использовать строки запросов размером до 2,5 КБ для передачи параметров в Azure AD.

1. На панели навигации слева перейдите на вкладку **Безопасность** > **Проверка подлинности**.

    ![Панель администрирования](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Установите флажок **Единый вход (SSO)** .
    
    b. Щелкните **+Add new SSO Provider** (+Добавить нового поставщика единого входа).

1. На вкладке **Authentication** (Аутентификация) выберите **Single-Sign On (SSO)** (Единый вход (SSO)) и выполните следующие действия:

    ![Вкладка "Authentication" (Аутентификация)](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. В поле **Name of the SSO Provider** (Имя поставщика единого входа) введите имя экземпляра единого входа, например Azureadsso.

    b. В текстовое поле **SAML URL** (URL-адрес SAML) вставьте **URL-адрес SAML**, скопированный на портале Azure.

    c. В текстовое поле **SAML Issuer URL** (URL-адрес издателя SAML) вставьте **идентификатор Azure AD**, скопированный на портале Azure.

    d. Откройте в Блокноте **сертификат в кодировке Base64**, скачанный с портала Azure, скопируйте его содержимое в буфер обмена и вставьте в текстовое поле **SAML Certificate** (Сертификат SAML).

    д. Скопируйте **URL-адрес аудитории** и вставьте его в текстовое поле **Идентификатор (сущности)** в разделе**Базовая конфигурации SAML** на портале Azure.

    Е. Скопируйте **URL-адрес получателя** и вставьте его в текстовое поле **URL-адрес для входа** в разделе**Базовая конфигурации SAML** на портале Azure.

    ж. Прокрутите страницу до конца раздела и нажмите кнопку **Test SSO** (Проверить единый вход). Появится всплывающее окно со страницей входа в Azure AD. Введите учетные данные, как при обычной аутентификации.

    **Устранение неполадок**. Убедитесь в том, что адрес электронной почты, возвращаемый из Azure AD, совпадает с учетной записью Workplace, которая использовалась для входа.

    h. После успешного прохождения проверки прокрутите страницу до конца и нажмите кнопку **Save** (Сохранить).

    i. Теперь для аутентификации всех пользователей Workplace будет отображаться страница входа в Azure AD.

1. **Перенаправление для выхода SAML (необязательно)**  -

    При необходимости можно настроить URL-адрес выхода SAML, с помощью которого можно указать страницу выхода из Azure AD. После активации и настройки этого параметра пользователи больше не будут направляться на страницу выхода из Workplace. Вместо этого они будут переходить по URL-адресу, указанному в параметре перенаправления для выхода SAML.

### <a name="configuring-reauthentication-frequency"></a>Настройка частоты повторной проверки подлинности

Вы можете настроить в Workplace запрос на проверку SAML каждый день, каждые 3 дня, каждую неделю, каждые 2 недели, каждый месяц или никогда.

> [!NOTE]
> Минимальная частота проверки SAML в мобильных приложениях равна одной неделе.

Вы также можете принудительно применить сброс SAML для всех пользователей, используя кнопку Require SAML authentication for all users now (Применить аутентификацию SAML для всех пользователей).

### <a name="create-workplace-by-facebook-test-user"></a>Создание тестового пользователя Workplace by Facebook

В этом разделе вы создадите в Workplace by Facebook пользователя с именем B. Simon. Workplace by Facebook поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Если пользователь не существует в Workplace by Facebook, он создается при попытке доступа к Workplace by Facebook.

>[!Note]
>Если вам нужно создать пользователя вручную, обратитесь к [группе поддержки клиентов Workplace by Facebook](https://workplace.fb.com/faq/).

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Workplace by Facebook на панели доступа, вы автоматически войдете в приложение Workplace by Facebook, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Проверка единого входа для Workplace by Facebook (мобильное приложение)

1. Откройте мобильное приложение Workplace by Facebook. На странице входа щелкните **LOG IN** (ВХОД).

    ![Вход](./media/workplacebyfacebook-tutorial/test05.png)

2. Введите служебный адрес электронной почты и нажмите кнопку **CONTINUE** (ПРОДОЛЖИТЬ).

    ![Сообщение электронной почты](./media/workplacebyfacebook-tutorial/test02.png)

3. Щелкните **JUST ONCE** (Один раз).

    ![Один раз](./media/workplacebyfacebook-tutorial/test04.png)

4. Нажмите кнопку **Разрешить**.

    ![Разрешение](./media/workplacebyfacebook-tutorial/test03.png)

5. Наконец, после успешного входа на сайт отобразится домашняя страница приложения.    

    ![Домашняя страница](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Руководство по настройке Google Apps для автоматической подготовки пользователей](workplacebyfacebook-provisioning-tutorial.md)

- [Проверьте работу Workplace by Facebook с Azure Active Directory](https://aad.portal.azure.com)
