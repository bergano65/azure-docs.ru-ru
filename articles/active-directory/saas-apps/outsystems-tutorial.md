---
title: Руководство по Интеграция Azure Active Directory с OutSystems Azure AD | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в OutSystems Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: cf6f99b7-0604-4db2-a72e-0d1a1d643a08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ff4f8f45bee86013b3f3603ba12fc121de9d7ce
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475689"
---
# <a name="tutorial-integrate-outsystems-azure-ad-with-azure-active-directory"></a>Руководство по Интеграция OutSystems Azure AD с Azure Active Directory

В этом руководстве описано, как интегрировать OutSystems Azure AD с Azure Active Directory (Azure AD). Интеграция OutSystems Azure AD с Azure AD обеспечивает следующие возможности:

* С помощью Azure AD вы можете контролировать доступ к OutSystems Azure AD.
* Автоматический вход пользователей в Azure AD OutSystems с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка OutSystems Azure AD с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде. OutSystems Azure AD поддерживает единый вход, инициированный **поставщиком службы и поставщиком удостоверений**, а также **JIT**-подготовку пользователей.

## <a name="adding-outsystems-azure-ad-from-the-gallery"></a>Добавление OutSystems Azure AD из коллекции

Чтобы настроить интеграцию OutSystems Azure AD с Azure AD, необходимо добавить OutSystems Azure AD из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **OutSystems Azure AD**.
1. Выберите **OutSystems Azure AD** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Настройте и проверьте единый вход Azure AD в OutSystems Azure AD с помощью тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OutSystems Azure AD.

Чтобы настроить и проверить единый вход Azure AD в OutSystems Azure AD, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка OutSystems Azure AD](#configure-outsystems-azure-ad)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя B. Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить B. Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя OutSystems Azure AD](#create-outsystems-azure-ad-test-user)** требуется для того, чтобы в OutSystems Azure AD существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **OutSystems Azure AD** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `http://<YOURBASEURL>/IdP`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<YOURBASEURL>/IdP/SSO.aspx`.

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<YOURBASEURL>`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов OutSystems](mailto:support@outsystems.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и нажмите кнопку **Скачать**, чтобы скачать сертификат и сохранить его на компьютере.

   ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Скопируйте требуемые URL-адреса из раздела **Настройка OutSystems Azure AD**.

   ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="configure-outsystems-azure-ad"></a>Настройка единого входа в OutSystems Azure AD

Чтобы настроить единый вход на стороне **OutSystems**, нужно отправить скачанный **XML-файл метаданных федерации** и соответствующие URL-адреса, скопированные на портале Azure, [группе поддержки OutSystems](mailto:support@outsystems.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B. Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B. Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `BrittaSimon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя B. Simon и как предоставить такому пользователю доступ к OutSystems Azure AD.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. Из списка приложений выберите **OutSystems Azure AD**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B. Simon** из списка пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-outsystems-azure-ad-test-user"></a>Создание тестового пользователя OutSystems Azure AD

В этом разделе вы создадите в OutSystems пользователя с именем Britta Simon. OutSystems поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в OutSystems, он создается после выполнения аутентификации.

### <a name="test-sso"></a>Проверка единого входа

Щелкнув плитку OutSystems Azure AD на Панели доступа, вы автоматически войдете в приложение OutSystems Azure AD, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
