---
title: Руководство по интеграции единого входа Azure Active Directory с RStudio Server Pro SAML Authentication | Документация Майкрософт
description: Из этой статьи вы узнаете, как настроить единый вход Azure Active Directory в приложение RStudio Server Pro SAML Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: abd65fc0cfd1f1c563ac9bd68b6ba58b741e152b
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94881138"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rstudio-server-pro-saml-authentication"></a>Руководство по интеграции единого входа Azure Active Directory с RStudio Server Pro SAML Authentication

В этом руководстве объясняется, как интегрировать приложение RStudio Server Pro SAML Authentication с Azure Active Directory (Azure AD). Интеграция RStudio Server Pro SAML Authentication с Azure AD обеспечивает следующие возможности:

* Вы можете управлять доступом к RStudio Server Pro SAML Authentication с помощью Azure AD.
* Вы можете включить для пользователей автоматический вход в RStudio Server Pro SAML Authentication с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка RStudio Server Pro SAML Authentication с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Приложение RStudio Server Pro SAML Authentication поддерживает единый вход, инициируемый **поставщиком услуг и поставщиком удостоверений**.

## <a name="adding-rstudio-server-pro-saml-authentication-from-the-gallery"></a>Добавление приложения RStudio Server Pro SAML Authentication из коллекции

Чтобы настроить интеграцию RStudio Server Pro SAML Authentication с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

1. Войдите на портал Azure с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **RStudio Server Pro SAML Authentication**.
1. Выберите **RStudio Server Pro SAML Authentication** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-server-pro-saml-authentication"></a>Настройка и проверка единого входа Azure AD для приложения RStudio Server Pro SAML Authentication

Настройте и проверьте единый вход Azure AD в приложение RStudio Server Pro SAML Authentication с помощью тестового пользователя **B.Simon**. Для обеспечения единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в приложении RStudio Server Pro SAML Authentication.

Чтобы настроить и проверить единый вход Azure AD в RStudio Server Pro SAML Authentication, выполните действия из следующих стандартных блоков:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в RStudio Server Pro SAML Authentication](#configure-rstudio-server-pro-saml-authentication-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя приложения RStudio Server Pro SAML Authentication](#create-rstudio-server-pro-saml-authentication-test-user)** требуется для того, чтобы в приложении RStudio Server Pro SAML Authentication существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На портале Azure на странице интеграции с приложением **RStudio Server Pro SAML Authentication** найдите раздел **Управление** и выберите элемент **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    а. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<SUBDOMAIN>.rstudioservices.com/<PATH>/saml/metadata`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<SUBDOMAIN>.rstudioservices.com/<PATH>/saml/acs`.

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<SUBDOMAIN>.rstudioservices.com`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу технической поддержки клиентов RStudio Server Pro SAML Authentication](mailto:support@rstudio.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

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

В этом разделе объясняется, как включить единый вход Azure для пользователя B.Simon, предоставив этому пользователю доступ к приложению RStudio Server Pro SAML Authentication.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **RStudio Server Pro SAML Authentication**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.
1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.
1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-rstudio-server-pro-saml-authentication-sso"></a>Настройка единого входа в RStudio Server Pro SAML Authentication

Чтобы настроить единый вход на стороне **RStudio Server Pro SAML Authentication**, необходимо отправить созданный **URL-адрес метаданных федерации приложений** в [службу технической поддержки RStudio Server Pro SAML Authentication](mailto:support@rstudio.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-rstudio-server-pro-saml-authentication-test-user"></a>Создание тестового пользователя в RStudio Server Pro SAML Authentication

Из этого раздела вы узнаете, как создать пользователя B.Simon в приложении RStudio Server Pro SAML Authentication. Чтобы добавить пользователей на платформу RStudio Server Pro SAML Authentication, обратитесь в [службу технической поддержки RStudio Server Pro SAML Authentication](mailto:support@rstudio.com). Перед использованием единого входа необходимо создать и активировать пользователей.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью указанных ниже способов. 

#### <a name="sp-initiated"></a>Инициация поставщиком услуг:

* Выберите **Тестировать приложение** на портале Azure. После этого действия откроется URL-адрес для входа в RStudio Server Pro SAML Authentication, где можно инициировать поток входа.  

* Вручную введите URL-адрес для входа в RStudio Server Pro SAML Authentication и инициируйте поток входа.

#### <a name="idp-initiated"></a>Вход, инициированный поставщиком удостоверений

* На портале Azure выберите элемент **Тестировать приложение**. Вы автоматически войдете в приложение RStudio Server Pro SAML Authentication, для которого настроен единый вход. 

Вы можете также использовать Панель доступа корпорации Майкрософт для тестирования приложения в любом режиме. Щелкните плитку приложения RStudio Server Pro SAML Authentication на Панели доступа. Вы будете перенаправлены на страницу входа приложения, если задан режим поставщика услуг, или автоматически войдете в приложение RStudio Server Pro SAML Authentication, для которого настроен единый вход, если задан режим поставщика удостоверений. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Дальнейшие действия

После настройки RStudio Server Pro SAML Authentication вы можете применить функцию управления сеансом, которая в реальном времени защищает от хищения конфиденциальных данных вашей организации и несанкционированного доступа к ним. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).