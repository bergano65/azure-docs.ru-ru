---
title: Руководство по Интеграция Azure Active Directory с Dropbox for Business | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2109c3f67a666d0c379cc188c07968c8b31d6a1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989675"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Руководство по Интеграция Dropbox for Business с Azure Active Directory

В этом руководстве описано, как интегрировать Dropbox for Business с Azure Active Directory (Azure AD). Интеграция Dropbox for Business с Azure AD обеспечивает следующие возможности.

* С помощью Azure AD вы можете контролировать доступ к приложению Dropbox for Business.
* Вы можете включить автоматический вход пользователей в Dropbox for Business с использованием учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, [здесь](https://azure.microsoft.com/pricing/free-trial/) вы можете получить бесплатную пробную версию сроком на один месяц.
* подписка Dropbox for Business с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

* В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде. Dropbox for Business поддерживает единый вход, инициированный **поставщиком услуг**.

* Dropbox for Business поддерживает **JIT**-подготовку пользователей.

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Добавление Dropbox for Business из коллекции

Чтобы настроить интеграцию Dropbox for Business с Azure AD, необходимо добавить Dropbox for Business из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Dropbox for Business**.
1. Выберите **Dropbox for Business** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Настройте и проверьте единый вход Azure AD в Dropbox for Business с использованием тестового пользователя **Britta Simon**. Чтобы единый вход работал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Dropbox for Business.

Чтобы настроить и проверить единый вход Azure AD в Dropbox for Business, потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Dropbox for Business](#configure-dropbox-for-business-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Dropbox for Business](#create-dropbox-for-business-test-user)** требуется для того, чтобы в Dropbox for Business существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Dropbox for Business** найдите раздел **Управление** и щелкните **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения для следующих полей.

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.dropbox.com/sso/<id>`.

    b. В текстовом поле **Идентификатор (сущности)** введите значение следующим образом: `Dropbox`.

    > [!NOTE]
    > Приведенное выше значение "URL-адрес для входа" используется только для примера. Вы замените это значение на фактический URL-адрес для входа, который описывается далее в этом руководстве.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Скопируйте соответствующий вашим требованиям URL-адрес из раздела **Настройка Dropbox for Business**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-dropbox-for-business-sso"></a>Настройка единого входа в Dropbox for Business

1. Чтобы автоматизировать настройку в Dropbox for Business, необходимо установить **расширение браузера My Apps Secure Sign-in Extension**, щелкнув **Установить расширение**.

    ![Расширение "Мои приложения"](common/install-myappssecure-extension.png)

2. После добавления расширения в браузер щелкните **Настройка Dropbox for Business**, чтобы перейти к приложению Dropbox for Business. После этого укажите учетные данные администратора для входа в Dropbox for Business. Расширение браузера автоматически настроит приложение и автоматизирует шаги 3–8.

    ![Настройка конфигурации](common/setup-sso.png)

3. Если вы хотите вручную настроить Dropbox for Business, откройте новое окно веб-браузера, перейдите к своему клиенту Dropbox for Business и войдите в него. Затем выполните следующие действия.

    ![Настройка единого входа](./media/dropboxforbusiness-tutorial/ic769509.png "Настройка единого входа")

4. Щелкните значок **Пользователь** и выберите вкладку **Параметры**.

    ![Настройка единого входа](./media/dropboxforbusiness-tutorial/configure1.png "Настройка единого входа")

5. На панели навигации слева щелкните **консоль администрирования**.

    ![Настройка единого входа](./media/dropboxforbusiness-tutorial/configure2.png "Настройка единого входа")

6. В **консоли администрирования** выберите пункт **Параметры** в левой области навигации.

    ![Настройка единого входа](./media/dropboxforbusiness-tutorial/configure3.png "Настройка единого входа")

7. Выберите параметр **единый вход** в разделе **Проверка подлинности**.

    ![Настройка единого входа](./media/dropboxforbusiness-tutorial/configure4.png "Настройка единого входа")

8. В разделе **Единый вход** выполните следующие действия.  

    ![Настройка единого входа](./media/dropboxforbusiness-tutorial/configure5.png "Настройка единого входа")

    a. Выберите параметр **Обязательная** из раскрывающегося списка для **единого входа**.

    b. Щелкните **Add sign-in URL** (Добавить URL-адреса единого входа) и в текстовое поле **Identity provider sign-in URL** (URL-адрес входа для поставщика удостоверений) вставьте значение **URL-адреса входа**, скопированное на портале Azure, а затем выберите **Готово**.

    ![Настройка единого входа](./media/dropboxforbusiness-tutorial/configure6.png "Настройка единого входа")

    c. Щелкните **Отправка сертификата**, а затем перейдите в свой **файл сертификата с кодировкой Base64**, загруженный с портала Azure.

    d. Щелкните **Копировать ссылку** и вставьте это значение **URL-адрес входа** в текстовое поле раздела **Домен и URL-адреса Dropbox for Business** на портале Azure.

    д. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `Britta Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `BrittaSimon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Dropbox for Business.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **Dropbox for Business**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** из списка пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-dropbox-for-business-test-user"></a>Создание тестового пользователя Dropbox for Business

В этом разделе вы создадите в Dropbox for Business пользователя с именем Britta Simon. Приложение Dropbox for Business поддерживает JIT-подготовку пользователей. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Dropbox for Business, он создается после проверки подлинности.

>[!Note]
>Если вам нужно вручную создать пользователя, обратитесь в [службу поддержки клиентов Dropbox for Business](https://www.dropbox.com/business/contact).

### <a name="test-sso"></a>Проверка единого входа

Щелкнув элемент "Dropbox for Business" на Панели доступа, вы автоматически войдете в приложение Dropbox for Business, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)