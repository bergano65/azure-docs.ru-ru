---
title: Руководство по Интеграция единого входа Azure Active Directory со Splashtop | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Splashtop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c05f63c2-4170-49ce-a967-be1cb1dbcd06
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6ecb03130e26d432f0bd10980c7c3553ce9f8b0
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539701"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Руководство по Интеграция единого входа Azure Active Directory с Splashtop

В этом руководстве описано, как интегрировать Splashtop с Azure Active Directory (Azure AD). Интеграция Azure AD с Splashtop обеспечивает следующие возможности.

* С помощью AAD вы можете контролировать доступ к Splashtop.
* Вы можете включить автоматический вход пользователей в Splashtop с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Splashtop с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Splashtop поддерживает единый вход, инициируемый **поставщиком услуг**.

* После настройки Splashtop вы можете применить элементы управления сеансом для защиты от хищения и несанкционированного доступа к конфиденциальным данным вашей организации в реальном времени. Элементы управления сеансом являются расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Добавление Splashtop из коллекции

Чтобы настроить интеграцию Splashtop с Azure AD, необходимо добавить Splashtop из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Splashtop**.
1. Выберите **Splashtop** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Настройка и проверка единого входа Azure AD в Splashtop

Настройте и проверьте единый вход Azure AD в Splashtop с использованием данных тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Splashtop.

Чтобы настроить и проверить единый вход Azure AD в Splashtop, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Splashtop](#configure-splashtop-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя Splashtop](#create-splashtop-test-user)** требуется для того, чтобы в Splashtop существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Splashtop** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    В текстовом поле **URL-адрес входа** введите URL-адрес: `https://my.splashtop.com/login/sso`.

1. Приложение Splashtop ожидает проверочные утверждения SAML в определенном формате, поэтому вам нужно добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию, в котором **nameidentifier** сопоставляется с **user.userprincipalname**. Приложение TicketManager ожидает, что **nameidentifier** будет сопоставляться с **user.mail**, поэтому необходимо изменить сопоставление атрибутов, щелкнув значок **Изменить**.

    ![Изображение](common/edit-attribute.png)

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Требуемые URL-адреса можно скопировать в разделе **Настройка Splashtop**.

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

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к Splashtop.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **Splashtop**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-splashtop-sso"></a>Настройка единого входа в Splashtop

В этом разделе описано, как оформить запрос на использование нового метода единого входа на [веб-портале Splashtop](https://my.splashtop.com/login).
1. Откройте веб-портал Splashtop, перейдите к вкладке **Account info** / **Team** (Сведения об учетной записи > Команда) и прокрутите экран вниз до раздела **Single Sign On** (Единый вход). Щелкните действие **Apply for new SSO method** (Запросить новый метод единого входа).

    ![Изображение](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. На странице запроса предоставьте **имя для единого входа**. Например, введите строку New Azure, затем выберите тип поставщика удостоверений **Azure** и вставьте в поля **URL-адреса входа** и **идентификатора Azure AD** соответствующие значения с портала Azure.

    ![Изображение](media/splashtop-tutorial/azure-sso-1.png)

1. Чтобы получить сведения о сертификате, щелкните правой кнопкой мыши файл сертификата, скачанный для приложения Splashtop с портала Azure, откройте его в Блокноте, скопируйте все содержимое и вставьте его в поле **Download Certificate (Base64)** (Скачать сертификат Base64).

    ![Изображение](media/splashtop-tutorial/cert-1.png) ![Изображение](media/splashtop-tutorial/cert-2.png) ![Изображение](media/splashtop-tutorial/azure-sso-2.png)

1. Вот и все! Щелкните **Save** (Сохранить) и через некоторое время сотрудник Splashtop из отдела проверки единого входа свяжется с вами для уточнения сведений, а затем активирует новый метод единого входа.

### <a name="create-splashtop-test-user"></a>Создание тестового пользователя Splashtop

1. После активации метода единого входа проверьте наличие соответствующих элементов управления в разделе **Single Sign On** (Единый вход) и включите его.

    ![Изображение](media/splashtop-tutorial/enable.png)

1. Пригласите тестового пользователя (например, `B.Simon@contoso.com`) в команду Splashtop с использованием нового метода единого входа.

    ![Изображение](media/splashtop-tutorial/invite.png)

1. Также вы можете заменить существующую учетную запись Splashtop учетной записью единого входа, выполнив [эти инструкции](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. Вот и все! Теперь вы можете использовать учетную запись единого входа для входа на веб-портал Splashtop или приложение Splashtop Business.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Splashtop на панели доступа, вы автоматически войдете в Splashtop, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать Splashtop с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Protect apps with Microsoft Cloud App Security Conditional Access App Control](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) (Защита приложений с помощью функции управления настройками условного доступа для приложений в Microsoft Cloud App Security)