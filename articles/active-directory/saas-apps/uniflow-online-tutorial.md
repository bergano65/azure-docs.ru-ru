---
title: Руководство по интеграции единого входа Azure Active Directory с uniFLOW Online | Документация Майкрософт
description: Сведения о том, как настроить единый вход между Azure Active Directory и uniFLOW Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f26af813fcd4032aabce2305ac8845307d1fca65
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76262135"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Руководство по интеграции единого входа Azure Active Directory с uniFLOW Online

В этом учебнике описано, как интегрировать uniFLOW Online с Azure Active Directory (Azure AD). Интеграция uniFLOW Online с Azure AD обеспечивает следующие возможности:

* Контроль того, кто имеет доступ к uniFLOW Online, с помощью Azure AD.
* Включение входа пользователей в uniFLOW Online с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Клиент uniFLOW Online.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* uniFLOW Online поддерживает единый вход, инициируемый **поставщиком услуг**

## <a name="adding-uniflow-online-from-the-gallery"></a>Добавление uniFLOW Online из коллекции

Чтобы настроить интеграцию uniFLOW Online с Azure AD, нужно добавить uniFLOW Online из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **uniFLOW Online**.
1. Выберите **uniFLOW Online** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Настройка и проверка единого входа Azure AD для uniFLOW Online

Настройте и проверьте единый вход Azure AD в uniFLOW Online с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в uniFLOW Online.

Чтобы настроить и проверить единый вход Azure AD в uniFLOW Online выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
   1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
   1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в uniFLOW Online](#configure-uniflow-online-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Войдите в uniFLOW Online с помощью созданного тестового пользователя](#sign-in-to-uniflow-online-using-the-created-test-user)** , чтобы проверить вход пользователя на стороне приложения.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **uniFLOW Online** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Для получения этих значений обратитесь в [группу поддержки uniFLOW Online](mailto:support@nt-ware.com). Вы можете также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure или обратиться к URL-адресу ответа, который отображается в клиенте uniFLOW Online.

1. Приложение uniFLOW Online ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![Изображение](common/default-attributes.png)

1. В дополнение к описанному выше приложение uniFLOW Online ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.

    | Имя |  Исходный атрибут|
    | -----------| --------------- |
    | displayname | user.displayname |
    | псевдоним | user.onpremisessamaccountname |

   > [!NOTE]
   > Атрибут `user.onpremisessamaccountname` будет содержать значение только в том случае, если пользователи Azure AD синхронизированы с локальной Active Directory Windows.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

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

В этом разделе описано, как включить единый вход Azure для пользователя B. Simon, предоставив этому пользователю доступ к uniFLOW Online.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **uniFLOW Online**.
1. На странице "Обзор" приложения перейдите в раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

   ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

> [!NOTE]
> Чтобы разрешить всем пользователям доступ к приложению без назначения вручную, перейдите в раздел **Управление** и выберите **Свойства**. Затем установите для параметра **Требуется назначение пользователей** значение **Нет**.

## <a name="configure-uniflow-online-sso"></a>Настройка единого входа uniFLOW Online

1. В другом окне веб-браузера войдите на веб-сайт uniFLOW Online с правами администратора.

1. В левой панели навигации выберите вкладку **Пользователь**.

    ![Конфигурация uniFLOW Online](./media/uniflow-online-tutorial/configure1.png)

1. Щелкните **Поставщики удостоверений**.

    ![Конфигурация uniFLOW Online](./media/uniflow-online-tutorial/configure2.png)

1. Щелкните **Добавление поставщика удостоверений**.

    ![Конфигурация uniFLOW Online](./media/uniflow-online-tutorial/configure3.png)

1. В разделе **Добавление поставщика удостоверений** выполните следующие действия:


    ![Конфигурация uniFLOW Online](./media/uniflow-online-tutorial/configure4.png)

    а. Введите отображаемое имя, например: *Единый вход в AzureAD*.

    b. В качестве **типа поставщика** выберите пункт **WS-Fed** из раскрывающегося списка.

    c. В качестве типа **WS-Fed** выберите пункт **Azure Active Directory** из раскрывающегося списка.

    d. Выберите команду **Сохранить**.

1. На вкладке **Общие** сделайте следующее:

    ![Конфигурация uniFLOW Online](./media/uniflow-online-tutorial/configure5.png)

    а. Введите отображаемое имя, например: *Единый вход в AzureAD*.

    b. Выберите параметр **Из URL-адреса** для **метаданных федерации ADFS**.

    c. В текстовое поле **Federation Metadata URL** (URL-адрес метаданных федерации) вставьте значение **URL-адреса метаданных федерации приложения**, скопированное на портале Azure.

    d. Выберите для параметра **Поставщик удостоверений** значение **Включено**.

    д) Выберите для параметра **Automatic user registration** (Автоматическая регистрация пользователей) значение **Активировано**.

    е) Выберите команду **Сохранить**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Вход в uniFLOW Online с помощью созданного тестового пользователя

1. В другом окне веб-браузера перейдите к URL-адресу uniFLOW Online для своего клиента.

1. Выберите ранее созданный поставщик удостоверений для входа через экземпляр Azure AD.

1. Войдите с помощью тестового пользователя.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать uniFLOW Online с Azure AD](https://aad.portal.azure.com/)
