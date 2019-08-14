---
title: Руководство по Интеграция Azure Active Directory с Secret Server (On-Premises) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Secret Server (On-Premises).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4926fc1833cc14b2ad81a01e230a5c3c37ba6ab3
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880165"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Руководство по Интеграция Secret Server (On-Premises) с Azure Active Directory

В этом руководстве описано, как интегрировать Secret Server (On-Premises) с Azure Active Directory (Azure AD). Интеграция Secret Server (On-Premises) с Azure Active Directory обеспечивает следующие возможности:

* контроль доступа к Secret Server (On-Premises) с помощью Azure AD;
* включение автоматического входа пользователей в Secret Server (On-Premises) с использованием учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Secret Server (On-Premises) с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Secret Server (On-Premises) поддерживает единый вход, инициированный **поставщиком служб и поставщиком удостоверений**.

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Добавление Secret Server (On-Premises) из коллекции приложений

Чтобы настроить интеграцию Secret Server (On-Premises) с Azure AD, необходимо добавить Secret Server (On-Premises) из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Secret Server (On-Premises)** .
1. Выберите **Secret Server (On-Premises)** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Настройте и проверьте единый вход Azure AD в Secret Server (On-Premises) с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Secret Server (On-Premises).

Чтобы настроить и проверить единый вход Azure AD в Secret Server (On-Premises), выполните действия из следующих стандартных блоков:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Secret Server (On-Premises)](#configure-secret-server-on-premises-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Secret Server (On-Premises)](#create-secret-server-on-premises-test-user)** требуется, чтобы в Secret Server (On-Premises) существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Secret Server (On-Premises)** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    a. В текстовом поле **Идентификатор** укажите пользовательское значение, например: `https://secretserveronpremises.azure`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`.

    > [!NOTE]
    > Представленный выше идентификатор сущности используется только для примера. Вы можете выбрать любое уникальное значение для представления экземпляра Secret Server в AAD. Этот идентификатор сущности следует отправить в [группу поддержки приложения Secret Server (On-Premises)](https://thycotic.force.com/support/s/), чтобы они выполнили настройки со своей стороны. Дополнительные сведения см. в [этой статье](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<SecretServerURL>/login.aspx`.

    > [!NOTE]
    > Эти значения приведены для примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Сертификат для подписи SAML**.

    ![Вариант подписывания](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Для параметра **Вариант подписывания** выберите значение **Ответ знака SAML и утверждение**.

    ![Вариант подписывания](./media/secretserver-on-premises-tutorial/signing-option.png)

1. Требуемые URL-адреса вы можете скопировать из раздела **Настройка Secret Server (On-Premises)** .

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="configure-secret-server-on-premises-sso"></a>Настройка единого входа в Secret Server (On-Premises)

Чтобы настроить единый вход на стороне **Secret Server (On-Premises)** , нужно отправить скачанный **сертификат (Base64)** и соответствующие URL-адреса, скопированные на портале Azure, [службе поддержки Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

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

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к Secret Server (On-Premises).

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **Secret Server (On-Premises)** .
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-secret-server-on-premises-test-user"></a>Создание тестового пользователя в Secret Server (On-Premises)

В этом разделе описано, как создать пользователя Britta Simon в приложении Secret Server (On-Premises). Обратитесь в  [службу поддержки Secret Server (On-Premises)](https://thycotic.force.com/support/s/) для добавления пользователей на платформу Secret Server (On-Premises). Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Secret Server (On-Premises) на панели доступа, вы автоматически войдете в приложение Secret Server (On-Premises), для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)