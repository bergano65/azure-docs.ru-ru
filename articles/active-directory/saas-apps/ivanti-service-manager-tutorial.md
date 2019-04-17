---
title: Руководство по Интеграция Azure Active Directory с Ivanti Service Manager (ISM) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Ivanti Service Manager (ISM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 14297c74-0d57-4146-97fa-7a055fb73057
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9130717f7846c81bee2bf4892a13fcf6042257c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261729"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>Руководство по Интеграция Azure Active Directory с Ivanti Service Manager (ISM)

В этом руководстве описано, как интегрировать Ivanti Service Manager (ISM) с Azure Active Directory (AAD).
Интеграция AAD с Ivanti Service Manager (ISM) обеспечивает следующие преимущества:

* С помощью AAD вы можете контролировать доступ к Ivanti Service Manager (ISM).
* Вы можете включить автоматический вход пользователей в Ivanti Service Manager (ISM) (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию AAD с приложением Ivanti Service Manager (ISM), вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Ivanti Service Manager (ISM) с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Ivanti Service Manager (ISM) поддерживает единый вход, инициируемый **поставщиком услуг и поставщиком удостоверений**.
* Ivanti Service Manager (ISM) поддерживает **JIT**-подготовку пользователей.

## <a name="adding-ivanti-service-manager-ism-from-the-gallery"></a>Добавление Ivanti Service Manager (ISM) из коллекции

Чтобы настроить интеграцию Ivanti Service Manager (ISM) в AAD, необходимо добавить Ivanti Service Manager (ISM) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Ivanti Service Manager (ISM) из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Ivanti Service Manager (ISM)** и выберите **Ivanti Service Manager (ISM)** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Ivanti Service Manager (ISM) в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа AAD в Ivanti Service Manager (ISM) с использованием тестового пользователя **Britta Simon**.
Чтобы единый вход функционировал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Ivanti Service Manager (ISM).

Чтобы настроить и проверить единый вход AAD в Ivanti Service Manager (ISM), потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Ivanti Service Manager (ISM)](#configure-ivanti-service-manager-ism-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Ivanti Service Manager (ISM)](#create-ivanti-service-manager-ism-test-user)** требуется для того, чтобы в Ivanti Service Manager (ISM) существовал пользователь Britta Simon, связанный с одноименным пользователем в AAD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Ivanti Service Manager (ISM), выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Ivanti Service Manager (ISM)** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Ivanti Service Manager (ISM)](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в таком формате:
    
    | |
    |--|
    | `https://<customer>.saasit.com/` |
    | `https://<customer>.saasiteu.com/` |
    | `https://<customer>.saasitau.com/` |

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в таком формате: `https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Ivanti Service Manager (ISM)](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<customer>.saasit.com/`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (необработанный)** из предложенных вариантов и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificateraw.png)

7. Требуемые URL-адреса вы можете скопировать из раздела **Настройка Ivanti Service Manager (ISM)**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-ivanti-service-manager-ism-single-sign-on"></a>Настройка единого входа в Ivanti Service Manager (ISM)

Чтобы настроить единый вход на стороне **Ivanti Service Manager (ISM)**, нужно отправить скачанный **сертификат (необработанный)** и соответствующие URL-адреса, скопированные на портале Azure, [группе поддержки Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@домен_вашей_компании.доменная_зона**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Ivanti Service Manager (ISM).

1. На портале Azure последовательно выберите **Корпоративные приложения**, **Все приложения**, а затем — **Ivanti Service Manager (ISM)**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Ivanti Service Manager (ISM)**.

    ![Ссылка на Ivanti Service Manager (ISM) в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-ivanti-service-manager-ism-test-user"></a>Создание тестового пользователя Ivanti Service Manager (ISM)

В этом разделе вы создадите в Ivanti Service Manager (ISM) пользователя с именем Britta Simon. Приложение Ivanti Service Manager (ISM) поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Ivanti Service Manager (ISM), он создается после выполнения аутентификации.

> [!Note]
> Если вам нужно создать пользователя вручную, обратитесь в  [службу поддержки Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Ivanti Service Manager (ISM)" на панели доступа, вы автоматически войдете в приложение Ivanti Service Manager (ISM). См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

