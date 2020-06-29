---
title: Руководство по Интеграция единого входа Azure Active Directory со SmartKargo | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и SmartKargo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4112e1f0-1a71-4e60-9052-3a1bae1d84b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 310023893d25a484cf05dac00628c632a4421edb
ms.sourcegitcommit: a8928136b49362448e992a297db1072ee322b7fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84719954"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartkargo"></a>Руководство по интеграции единого входа Azure Active Directory со SmartKargo

В этом учебнике описано, как интегрировать SmartKargo с Azure Active Directory (Azure AD). Интеграция SmartKargo с Azure AD обеспечивает следующие возможности.

* Контроль доступа к SmartKargo с помощью Azure AD.
* Включение автоматического входа пользователей в SmartKargo с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка SmartKargo с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* SmartKargo поддерживает единый вход, инициированный **поставщиком услуг**.
* После настройки SmartKargo вы можете применить функцию управления сеансом, которая в реальном времени защищает конфиденциальные данные вашей организации от кражи и несанкционированного доступа. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-smartkargo-from-the-gallery"></a>Добавление SmartKargo из коллекции

Чтобы настроить интеграцию SmartKargo с Azure AD, нужно добавить SmartKargo из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **SmartKargo**.
1. Выберите **SmartKargo** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-smartkargo"></a>Настройка и проверка единого входа Azure AD в SmartKargo

Настройте и проверьте единый вход Azure AD в SmartKargo с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем AAD и соответствующим пользователем в SmartKargo.

Чтобы настроить и проверить единый вход AAD в SmartKargo, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в SmartKargo](#configure-smartkargo-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя приложения SmartKargo](#create-smartkargo-test-user)** требуется для того, чтобы в SmartKargo существовал пользователь B.Simon, связанный с представлением этого пользователя в AAD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **SmartKargo** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<CUSTOMER_NAME>.smartkargo.com/`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<CUSTOMER_NAME>.smartkargo.com/`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<CUSTOMER_NAME>.smartkargo.com/SamlResponse.aspx`.

    > [!NOTE]
    > Эти значения приведены для примера. Вместо них необходимо указать фактические значения URL-адреса входа, URL-адреса ответа и идентификатора. Чтобы получить их, обратитесь к [группе поддержки клиентов SmartKargo](https://www.smartkargo.com/company/contact-2/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. В разделе **Сертификат подписи SAML** щелкните кнопку **Правка**, чтобы открыть диалоговое окно **Сертификат подписи SAML**.

    ![Изменение сертификата подписи SAML](common/edit-certificate.png)

1. В разделе **Сертификат подписи SAML** скопируйте **значение отпечатка** и сохраните его на компьютере.

    ![Копирование значения "Отпечаток"](common/copy-thumbprint.png)

1. Требуемые URL-адреса можно скопировать из раздела **Настройка SmartKargo**.

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

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к SmartKargo.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. Из списка приложений выберите **SmartKargo**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-smartkargo-sso"></a>Настройка единого входа в SmartKargo

Чтобы настроить единый вход на стороне **SmartKargo**, нужно отправить **значение отпечатка** и соответствующие URL-адреса, скопированные на портале Azure, [группе поддержки SmartKargo](https://www.smartkargo.com/company/contact-2/). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-smartkargo-test-user"></a>Создание тестового пользователя SmartKargo

На этом шаге вы создадите пользователя с именем B.Simon в приложении SmartKargo. Чтобы добавить пользователей на платформу SmartKargo, обратитесь в  [службу поддержки платформы SmartKargo](https://www.smartkargo.com/company/contact-2/). Перед использованием единого входа необходимо создать и активировать пользователей.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SmartKargo на Панели доступа, вы автоматически войдете в приложение SmartKargo, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте работу в SmartKargo с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Защита приложений с помощью Управления условным доступом к приложениям в Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)