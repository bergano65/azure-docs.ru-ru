---
title: Руководство. Интеграция Azure Active Directory с Amplitude | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Amplitude.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 496c9ffa-c833-41fa-8d17-2dc3044954d1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba92047d282ab86b6cd1f72017996f7de92088c2
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874925"
---
# <a name="tutorial-azure-active-directory-integration-with-amplitude"></a>Руководство. Интеграция Azure Active Directory с Amplitude

В этом руководстве описано, как интегрировать Amplitude с Azure Active Directory (Azure AD).
Интеграция Amplitude с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Amplitude.
* Вы можете включить автоматический вход пользователей в Amplitude (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Amplitude, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Amplitude с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Amplitude поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.
* Amplitude поддерживает **JIT**-подготовку пользователей.

## <a name="adding-amplitude-from-the-gallery"></a>Добавление Amplitude из коллекции.

Чтобы настроить интеграцию Amplitude с Azure AD, необходимо добавить Amplitude из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Amplitude из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Amplitude**, выберите **Amplitude** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Amplitude в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Amplitude с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Amplitude.

Чтобы настроить и проверить единый вход Azure AD в Amplitude, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Amplitude](#configure-amplitude-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Amplitude](#create-amplitude-test-user)** нужно для того, чтобы в Amplitude существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Amplitude, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Amplitude** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Amplitude](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес `https://amplitude.com/saml/sso/metadata`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://analytics.amplitude.com/saml/sso/<uniqueid>`.

    > [!NOTE]
    > Значение URL-адреса ответа приведено для примера. Далее в этом руководстве объясняется, как получить значение URL-адреса ответа.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Amplitude](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес: `https://analytics.amplitude.com/sso`.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

7. Требуемый URL-адрес можно скопировать из раздела **Настройка Amplitude**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-amplitude-single-sign-on"></a>Настройка единого входа для Amplitude

1. Войдите на корпоративный сайт Amplitude с правами администратора.

2. Щелкните **Plan Admin** (Администратор плана) на панели навигации слева.

    ![Настройка единого входа](./media/amplitude-tutorial/configure1.png)

3. Выберите **Microsoft Azure Active Directory Metadata** (Метаданные Microsoft Azure Active Directory) в разделе **SSO Integration** (Интеграция единого входа).

    ![Настройка единого входа](./media/amplitude-tutorial/configure2.png)

4. В разделе **Set Up Single Sign-On** (Настройка единого входа) сделайте следующее:

    ![Настройка единого входа](./media/amplitude-tutorial/configure3.png)

    a. Откройте в Блокноте **XML-файл метаданных**, скачанный с портала Azure, и вставьте его содержимое в текстовое поле **Microsoft Azure Active Directory Metadata** (Метаданные Microsoft Azure Active Directory).

    b. Скопируйте значение **Reply URL (ACS)** (URL-адрес ответа (ACS) и вставьте его в текстовое поле **URL-адрес ответа** раздела **Базовая конфигурация SAML** на портале Azure.

    c. Нажмите кнопку **Сохранить**

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Amplitude.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Amplitude**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Amplitude**.

    ![Ссылка на Amplitude в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-amplitude-test-user"></a>Создание тестового пользователя в Amplitude

В этом разделе вы создадите пользователя Britta Simon в Amplitude. Приложение Amplitude поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Amplitude, он создается после проверки подлинности.

> [!Note]
> Чтобы создать пользователя вручную, обратитесь в  [службу поддержки Amplitude](https://amplitude.zendesk.com).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Amplitude на панели доступа, вы автоматически войдете в приложение Amplitude, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
