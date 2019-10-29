---
title: Руководство по интеграции единого входа Azure Active Directory с Check Point CloudGuard Dome9 Arc | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Check Point CloudGuard Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75f0669a474c24647e71eae8b5e0e0830b7c0bef
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533097"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Руководство по интеграции единого входа Azure Active Directory с Check Point CloudGuard Dome9 Arc

В этом руководстве описано, как интегрировать Check Point CloudGuard Dome9 Arc с Azure Active Directory (Azure AD). Интеграция Check Point CloudGuard Dome9 Arc с Azure AD обеспечивает следующие возможности.

* С помощью Azure AD вы можете контролировать доступ к Check Point CloudGuard Dome9 Arc.
* Вы можете включить автоматический вход пользователей в Check Point CloudGuard Dome9 Arc (единый вход) с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* подписка Check Point CloudGuard Dome9 Arc с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Check Point CloudGuard Dome9 Arc поддерживает единый вход, инициируемый **поставщиком услуг и поставщиком удостоверений**.

> [!NOTE]
> Идентификатор этого приложения — фиксированное строковое значение, поэтому в одном клиенте можно настроить только один экземпляр.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Добавление Check Point CloudGuard Dome9 Arc из коллекции

Чтобы настроить интеграцию Check Point CloudGuard Dome9 Arc с Azure AD, необходимо добавить Check Point CloudGuard Dome9 Arc из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Check Point CloudGuard Dome9 Arc**.
1. Выберите **Check Point CloudGuard Dome9 Arc** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Настройка и проверка единого входа Azure AD для Check Point CloudGuard Dome9 Arc

Настройте и проверьте единый вход Azure AD в Check Point CloudGuard Dome9 Arc с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Check Point CloudGuard Dome9 Arc.

Чтобы настроить и проверить единый вход Azure AD в Check Point CloudGuard Dome9 Arc, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа Check Point CloudGuard Dome9 Arc](#configure-check-point-cloudguard-dome9-arc-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Check Point CloudGuard Dome9 Arc](#create-check-point-cloudguard-dome9-arc-test-user)** требуется для того, чтобы в Check Point CloudGuard Dome9 Arc существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Check Point CloudGuard Dome9 Arc** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    a. В текстовом поле **Идентификатор** введите URL-адрес: `https://secure.dome9.com/`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://secure.dome9.com/sso/saml/<yourcompanyname>`.

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://secure.dome9.com/sso/saml/<yourcompanyname>`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями URL-адреса ответа и URL-адреса входа. Вы получите значение `<company name>` из раздела **Настройка единого входа в Check Point CloudGuard Dome9 Arc**, как описано далее в учебнике. Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. Приложение Check Point CloudGuard Dome9 Arc ожидает проверочные утверждения SAML в определенном формате, что требует добавить сопоставления настраиваемых атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![image](common/edit-attribute.png)

1. В дополнение к описанному выше приложение Check Point CloudGuard Dome9 Arc ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.
    
    | ИМЯ |  Исходный атрибут|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >Перейдите по [этой ссылке](https://docs.microsoft.com/en-us/azure/active-directory/saas-apps/apptio-tutorial), чтобы понять, как создать роли в Azure AD.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Требуемые URL-адреса можно скопировать из раздела **Настройка Check Point CloudGuard Dome9 Arc**.

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

В этом разделе описано, как включить единый вход Azure для пользователя B.Simon, предоставив этому пользователю доступ к Check Point CloudGuard Dome9 Arc.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. Из списка приложений выберите **Check Point CloudGuard Dome9 Arc**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Настройка единого входа для Check Point CloudGuard Dome9 Arc

1. Чтобы автоматизировать настройку в Check Point CloudGuard Dome9 Arc, необходимо установить **расширение браузера "Безопасный вход в мои приложения"** , щелкнув **Установить расширение**.

    ![Расширение "Мои приложения"](common/install-myappssecure-extension.png)

2. После добавления расширения в браузер щелкните **Setup Check Point CloudGuard Dome9 Arc** (Настроить Check Point CloudGuard Dome9 Arc), чтобы перейти к приложению Check Point CloudGuard Dome9 Arc. После этого укажите учетные данные администратора для входа в Check Point CloudGuard Dome9 Arc. Расширение браузера автоматически настроит приложение и автоматизирует шаги 3–6.

    ![Настройка конфигурации](common/setup-sso.png)

3. Если вы хотите настроить Check Point CloudGuard Dome9 Arc вручную, откройте новое окно веб-браузера, войдите на свой корпоративный сайт Check Point CloudGuard Dome9 Arc в качестве администратора и выполните следующие действия:

2. Щелкните **Profile Settings** (Параметры профиля) в правом верхнем углу и выберите пункт **Account Settings** (Параметры учетной записи). 

    ![Конфигурация Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure1.png)

3. Перейдите на вкладку **SSO** (Единый вход) и щелкните **Enable** (Включить).

    ![Конфигурация Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure2.png)

4. В разделе настройки единого входа выполните следующие действия:

    ![Конфигурация Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure3.png)

    a. Введите название компании в текстовом поле **Account ID** (Идентификатор учетной записи). Это значение будет использоваться в URL-адресе **ответа** и **входа**, как упоминалось в разделе **Базовая конфигурация SAML** на портале Azure.

    b. В текстовое поле **Issuer** (Издатель) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    c. В текстовое поле **Idp endpoint url** (URL-адрес конечной точки поставщика удостоверений) вставьте **URL-адрес входа**, скопированный на портале Azure.

    d. Откройте загруженный сертификат в кодировке Base64 с помощью блокнота, скопируйте содержимое файла в буфер обмена, а затем вставьте его в текстовое поле **X.509 certificate** (Сертификат X.509).

    д. Выберите команду **Сохранить**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Создание тестового пользователя Check Point CloudGuard Dome9 Arc

Чтобы пользователи Azure AD могли выполнять вход в Check Point CloudGuard Dome9 Arc, они должны быть подготовлены в этом приложении. Check Point CloudGuard Dome9 Arc поддерживает JIT-подготовку, но чтобы она работала правильно, необходимо назначить определенную **роль** для пользователя.

   >[!Note]
   >Чтобы получить инструкции по созданию **роли** и другие сведения, обратитесь к [группе поддержки клиентов Check Point CloudGuard Dome9 Arc](mailto:Dome9@checkpoint.com).

**Чтобы самостоятельно подготовить учетную запись пользователя, выполните следующие действия:**

1. Войдите на свой корпоративный сайт Check Point CloudGuard Dome9 Arc с правами администратора.

2. Щелкните **Users & Roles** (Пользователи и роли), а затем выберите **Users** (Пользователи).

    ![Добавление сотрудника](./media/dome9arc-tutorial/user1.png)

3. Нажмите кнопку **Add user** (Добавить пользователя).

    ![Добавление сотрудника](./media/dome9arc-tutorial/user2.png)

4. В разделе **Создание пользователя** выполните следующие действия.

    ![Добавление сотрудника](./media/dome9arc-tutorial/user3.png)

    a. В текстовое поле **Email** (Электронная почта) введите электронную почту пользователя, например B.Simon@contoso.com.

    b. В текстовое поле **First Name** (Имя) введите имя пользователя, например B.

    c. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например Simon.

    d. Установите для параметра **SSO User** (Пользователь единого входа) значение **On** (Вкл.).

    д. Нажмите кнопку **Create** (Создать).

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Check Point CloudGuard Dome9 Arc на Панели доступа, вы автоматически войдете в приложение Check Point CloudGuard Dome9 Arc, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать Check Point CloudGuard Dome9 Arc с Azure AD](https://aad.portal.azure.com/)