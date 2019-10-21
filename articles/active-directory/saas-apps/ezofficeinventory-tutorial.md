---
title: Руководство по интеграции единого входа Azure Active Directory с EZOfficeInventory | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и EZOfficeInventory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e8594f7c-dc2f-446f-9c25-676fe49ff3af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ebfe5f75d5d8546e0f5e8ad6f8c5d0063e5bda2
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377286"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>Руководство по интеграции единого входа Azure Active Directory с EZOfficeInventory

В этом учебнике описано, как интегрировать EZOfficeInventory с Azure Active Directory (Azure AD). Интеграция EZOfficeInventory с Azure AD обеспечивает следующие возможности:

* Вы можете контролировать доступ к EZOfficeInventory с помощью Azure AD.
* Вы можете включить автоматический вход пользователей в EZOfficeInventory с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка EZOfficeInventory с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* EZOfficeInventory поддерживает единый вход, инициированный **поставщиком услуг**.

* EZOfficeInventory поддерживает **JIT**-подготовку пользователей.

> [!NOTE]
> Идентификатор этого приложения — фиксированное строковое значение, поэтому в одном клиенте можно настроить только один экземпляр.

## <a name="adding-ezofficeinventory-from-the-gallery"></a>Добавление EZOfficeInventory из коллекции

Чтобы настроить интеграцию EZOfficeInventory с Azure AD, необходимо добавить EZOfficeInventory из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **EZOfficeInventory**.
1. Выберите **EZOfficeInventory** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ezofficeinventory"></a>Настройка и проверка единого входа Azure AD для EZOfficeInventory

Настройте и проверьте единый вход Azure AD в EZOfficeInventory с использованием тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в EZOfficeInventory.

Чтобы настроить и проверить единый вход Azure AD в EZOfficeInventory, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в EZOfficeInventory](#configure-ezofficeinventory-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя](#create-ezofficeinventory-test-user)** EZOfficeInventory требуется для того, чтобы в EZOfficeInventory существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **EZOfficeInventory** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in`.

    > [!NOTE]
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов EZOfficeInventory](mailto:support@ezofficeinventory.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. Приложение EZOfficeInventory предусматривает использование проверочных утверждений SAML в определенном формате, что предполагает добавление настраиваемых сопоставлений атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![image](common/edit-attribute.png)

1. В дополнение к описанному выше приложение EZOfficeInventory ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.

    | ИМЯ | Исходный атрибут|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | Email | user.mail |

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Требуемые URL-адреса можно скопировать из раздела **Настройка EZOfficeInventory**.

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

В этом разделе описано, как включить единый вход Azure для пользователя B. Simon, предоставив этому пользователю доступ к EZOfficeInventory.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **EZOfficeInventory**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-ezofficeinventory-sso"></a>Настройка единого входа для EZOfficeInventory

1. Откройте новое окно веб-браузера и войдите на корпоративный сайт EZOfficeInventory с правами администратора.

2. В правом верхнем углу страницы щелкните  **Profile**  (Профиль) и выберите  **Settings** > **Add Ons** (Параметры > Надстройки).

    ![Настройка EZOfficeInventory](./media/ezofficeinventory-tutorial/configure01.png)

3. Прокрутите вниз до раздела **SAML Integration** (Интеграция SAML) и выполните следующие действия:

    ![Настройка EZOfficeInventory](./media/ezofficeinventory-tutorial/configure02.png)

    a. Установите флажок **Enabled** (Включено).

    b. В текстовое поле **Identity provider URL** (URL-адрес входа поставщика удостоверений) вставьте значение **URL-адрес входа**, скопированное на портале Azure.

    c. Откройте сертификат в кодировке Base64 в Блокноте, скопируйте его содержимое и вставьте его в текстовое поле **Identity Provider Certificate** (Сертификат поставщика удостоверений).

    d. В текстовом поле **Login Button Text** (Текст кнопки входа) введите текст кнопки входа.

    д. В текстовом поле **First Name** (Имя) введите **first_name**.

    Е. В текстовом поле **Last Name** (Фамилия) введите **last_name**.

    ж. В текстовом поле **Email** (Электронная почта) введите **адрес электронной почты**.

    h. В поле **EZOfficeInventory Role By default** (Роль EZOfficeInventory по умолчанию) выберите роль в соответствии с требованиями.

    i. Нажмите кнопку **Обновить**.

### <a name="create-ezofficeinventory-test-user"></a>Создание тестового пользователя в EZOfficeInventory

В этом разделе вы создадите в EZOfficeInventory пользователя Britta Simon. Приложение EZOfficeInventory поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в EZOfficeInventory, он создается после проверки подлинности.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку EZOfficeInventory на Панели доступа, вы автоматически войдете в приложение EZOfficeInventory, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать EZOfficeInventory с Azure AD](https://aad.portal.azure.com/)

