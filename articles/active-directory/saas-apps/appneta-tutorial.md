---
title: Руководство по Интеграция единого входа Azure Active Directory с AppNeta Performance Monitor | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в AppNeta Performance Monitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.openlocfilehash: 5100974c071d6a63fd2c00e496a5422fef2dffb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88517666"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-monitor"></a>Руководство по Интеграция единого входа Azure Active Directory с AppNeta Performance Monitor

В этом учебнике описано, как интегрировать Azure Active Directory (Azure AD) с AppNeta Performance Monitor. Интеграция AppNeta Performance Monitor с Azure AD обеспечивает следующие возможности:

* Контроль доступа к AppNeta Performance Monitor с помощью Azure AD.
* Автоматический вход пользователей в AppNeta Performance Monitor с использованием учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка AppNeta Performance Monitor с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Приложение AppNeta Performance Monitor поддерживает единый вход, инициируемый **поставщиком услуг**.


* Приложение AppNeta Performance Monitor поддерживает **JIT**-подготовку пользователей.

> [!NOTE]
> Идентификатор этого приложения — фиксированное строковое значение, поэтому в одном клиенте можно настроить только один экземпляр.


## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Добавление AppNeta Performance Monitor из коллекции.

Чтобы настроить интеграцию AppNeta Performance Monitor с Azure AD, необходимо добавить AppNeta Performance Monitor из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **AppNeta Performance Monitor**.
1. Выберите **AppNeta Performance Monitor** на панели результатов, а затем добавьте приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-appneta-performance-monitor"></a>Настройка и проверка единого входа Azure AD для AppNeta Performance Monitor

Настройте и проверьте единый вход Azure AD в AppNeta Performance Monitor с помощью тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в AppNeta Performance Monitor.

Чтобы настроить и проверить единый вход Azure AD в AppNeta Performance Monitor, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в AppNeta Performance Monitor](#configure-appneta-performance-monitor-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя AppNeta Performance Monitor](#create-appneta-performance-monitor-test-user)** нужно для того, чтобы в AppNeta Performance Monitor существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **AppNeta Performance Monitor** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.pm.appneta.com`.

    b. В текстовом поле **Идентификатор (сущности)** введите значение следующим образом: `PingConnect`.

    > [!NOTE]
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, свяжитесь со [службой поддержки клиентов AppNeta Performance Monitor](mailto:support@appneta.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. Приложение AppNeta Performance Monitor ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![Изображение](common/edit-attribute.png)

1. В дополнение к описанному выше, приложение AppNeta Performance Monitor ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.

    | Имя | Исходный атрибут|
    | --------| ----------------|
    | firstName| user.givenname|
    | lastName| user.surname|
    | email| user.userprincipalname|
    | name| user.userprincipalname|
    | groups  | user.assignedroles |
    | phone| user.telephonenumber |
    | title| user.jobtitle|
    | | |

    > [!NOTE]
    > **groups** — это группа безопасности в Appneta, которая сопоставлена с **ролью** в Azure AD. Подробные сведения о создании пользовательских ролей в Azure AD см. в [этом](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) документе.

    1. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    1. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    1. Оставьте пустым поле **Пространство имен**.

    1. В качестве источника выберите **Атрибут**.

    1. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    1. Нажмите кнопку **ОК**.

    1. Выберите команду **Сохранить**.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и выберите **Скачать**, чтобы скачать сертификат и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Требуемый URL-адрес можно скопировать из раздела **Настройка AppNeta Performance Monitor**.

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

В этом разделе описано, как разрешить пользователю B. Simon использовать единый вход Azure, предоставив этому пользователю доступ к AppNeta Performance Monitor.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **AppNeta Performance Monitor**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-appneta-performance-monitor-sso"></a>Настройка единого входа в AppNeta Performance Monitor

Чтобы настроить единый вход на стороне **AppNeta Performance Monitor**, нужно отправить загруженный файл **XML метаданных федерации** и соответствующие URL-адреса, скопированные на портале Azure, в [службу технической поддержки AppNeta Performance Monitor](mailto:support@appneta.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-appneta-performance-monitor-test-user"></a>Создание тестового пользователя в приложении AppNeta Performance Monitor

В рамках этого раздела в AppNeta Performance Monitor создается пользователь с именем Britta Simon. Приложение AppNeta Performance Monitor поддерживает JIT-подготовку пользователей. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в AppNeta Performance Monitor, он создается после проверки подлинности.

> [!Note]
> Если необходимо вручную создать пользователя, обратитесь в [службу поддержки клиентов AppNeta Performance Monitor](mailto:support@appneta.com).

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент AppNeta Performance Monitor на панели доступа, вы автоматически войдете в приложение AppNeta Performance Monitor, для которого настроен единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать AppNeta Performance Monitor с Azure AD](https://aad.portal.azure.com/)

