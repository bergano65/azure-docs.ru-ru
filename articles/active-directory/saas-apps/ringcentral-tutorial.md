---
title: Руководство по Интеграция Azure Active Directory с RingCentral | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: jeedes
ms.openlocfilehash: c374c9e8fd91d50b7e6589f22f9bed09fbe0de39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092872"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Руководство по Интеграция RingCentral с Azure Active Directory

В этом руководстве описано, как интегрировать RingCentral с Azure Active Directory (Azure AD). Интеграция RingCentral с Azure AD обеспечивает следующие возможности.

* С помощью Azure AD вы можете контролировать доступ к RingCentral.
* Вы можете включить автоматический вход пользователей в RingCentral с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, [здесь](https://azure.microsoft.com/pricing/free-trial/) вы можете получить бесплатную пробную версию сроком на один месяц.
* подписка RingCentral с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде. RingCentral поддерживает единый вход, инициируемый **поставщиком служб**.

## <a name="adding-ringcentral-from-the-gallery"></a>Добавление RingCentral из коллекции.

Чтобы настроить интеграцию RingCentral с Azure AD, необходимо добавить RingCentral из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **RingCentral**.
1. Выберите **RingCentral** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Настройте и проверьте единый вход Azure AD в RingCentral с использованием тестового пользователя **Britta Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в RingCentral.

Чтобы настроить и проверить единый вход Azure AD в RingCentral, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в RingCentral](#configure-ringcentral-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя RingCentral](#create-ringcentral-test-user)** требуется для того, чтобы в RingCentral существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **RingCentral** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если у вас есть **файл метаданных поставщика служб**, выполните следующие действия в разделе **Базовая конфигурация SAML**:

    1. Щелкните **Отправить файл метаданных**.
    1. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.
    1. После успешной передачи файла метаданных значения параметров **Идентификатор** и **URL-адрес ответа** в разделе **Базовая конфигурация SAML** будут указаны автоматически.

    > [!Note]
    > Вы получите **файл метаданных поставщика служб** на странице настройки единого входа RingCentral, что описано далее в этом руководстве.

1. Если у вас нет **файла метаданных поставщика услуг**, введите значения в следующие поля.

    a. В текстовом поле **Идентификатор** введите URL-адрес:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы копировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

### <a name="configure-ringcentral-sso"></a>Настройка единого входа в RingCentral

1. В другом окне браузера войдите в приложение RingCentral с правами администратора безопасности.

1. Щелкните **Tools** (Средства) вверху.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Перейдите к параметру **Single Sign-on** (Единый вход).

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. На странице **Single Sign-on** (Единый вход) в разделе **SSO Configuration** (Настройка единого входа) в **шаге 1** щелкните **Edit** (Изменить) и выполните следующие шаги:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. На странице **Set up Single Sign-on** (Настройка единого входа) выполните следующие действия:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Затем щелкните **Browse** (Обзор), чтобы передать файл метаданных, скачанный с портала Azure.

    b. После отправки метаданных значения автоматически заполняются в разделе **SSO General Information** (Общие сведения о едином входе).

    c. В разделе **Attribute Mapping** (Сопоставление атрибутов) укажите для параметра **Map Email Attribute to** (Сопоставить атрибут почты с) значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    d. Выберите команду **Сохранить**.

    д. Как описано в **шаге 2**, нажмите кнопку **Download** (Скачать), чтобы скачать **файл метаданных поставщика службы** и отправить его в раздел **Базовая конфигурация SAML** для указания значений **идентификатора** и **URL-адреса ответа** на портале Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    Е. На этой же странице перейдите к разделу **Enable SSO** (Включение единого входа) и выполните следующие действия:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Выберите **Enable SSO Service** (Включить службу единого входа).

    * Выберите **Allow users to log in with SSO or RingCentral credential** (Разрешить пользователям входить в систему с помощью единого входа или учетных данных RingCentral).

    * Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `Britta Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `BrittaSimon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к RingCentral.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **RingCentral**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** из списка пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-ringcentral-test-user"></a>Создание тестового пользователя RingCentral

В этом разделе описано, как создать пользователя Britta Simon в приложении RingCentral. Чтобы добавить пользователей на платформу RingCentral, обратитесь в  [службу поддержки RingCentral](https://success.ringcentral.com/RCContactSupp) . Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-sso"></a>Проверка единого входа

Щелкнув элемент "RingCentral" на Панели доступа, вы автоматически войдете в приложение RingCentral, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
