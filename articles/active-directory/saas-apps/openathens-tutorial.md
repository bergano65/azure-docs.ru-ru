---
title: Руководство по интеграции единого входа Azure Active Directory с OpenAthens | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fc2272a81672ea613bdcbe17c5381e99cafbff
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053216"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Руководство по интеграции единого входа Azure Active Directory с OpenAthens

В этом руководстве описано, как интегрировать OpenAthens с Azure Active Directory (Azure AD). Интеграция OpenAthens с Azure AD обеспечивает следующие возможности.

* Контроль доступа к OpenAthens с помощью Azure AD.
* Автоматический вход пользователей в OpenAthens с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка OpenAthens с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* OpenAthens поддерживает единый вход инициированной **выдающей точки распространения**.
* OpenAthens поддерживает **JIT**-подготовку пользователей.

## <a name="adding-openathens-from-the-gallery"></a>Добавление OpenAthens из коллекции

Чтобы настроить интеграцию OpenAthens с Azure AD, необходимо добавить OpenAthens из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **OpenAthens**.
1. Выберите **OpenAthens** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Настройка и проверка единого входа Azure AD для OpenAthens

Настройте и проверьте единый вход Azure AD в OpenAthens с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OpenAthens.

Чтобы настроить и проверить единый вход Azure AD в OpenAthens, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в OpenAthens](#configure-openathens-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя OpenAthens](#create-openathens-test-user)** требуется для того, чтобы в OpenAthens существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **OpenAthens** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. В разделе **Базовая конфигурация SAML** отправьте **файл метаданных Поставщика услуг**, следуя инструкциям, упомянутым далее в этом руководстве.

    a. Щелкните **Отправить файл метаданных**.

    ![Отправка метаданных OpenAthens](common/upload-metadata.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Обзор отправки метаданных OpenAthens](common/browse-upload-metadata.png)

    c. После успешной передачи файла метаданных значение **Идентификатор** автоматически заполняется в разделе текстового поля **Базовая конфигурация SAML**.

    ![Сведения о домене и URL-адресах единого входа для приложения OpenAthens](common/idp-identifier.png)

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и выберите **Скачать**, чтобы скачать сертификат и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Требуемые URL-адреса можно скопировать из раздела **Настройка OpenAthens**.

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

В этом разделе описано, как включить единый вход Azure для пользователя B.Simon, предоставив этому пользователю доступ к OpenAthens.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. Из списка приложений выберите **OpenAthens**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-openathens-sso"></a>Настройка единого входа для OpenAthens

1. В другом окне веб-браузера войдите на свой корпоративный сайт OpenAthens в качестве администратора.

1. Выберите **Connections** (Подключения) в списке на вкладке **Management** (Управление).

    ![Настройка единого входа](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Выберите **SAML 1.1/2.0**, а затем нажмите кнопку **Configure** (Настроить).

    ![Настройка единого входа](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. Чтобы добавить конфигурацию, нажмите кнопку **​​Browse** (Обзор), чтобы передать XML-файл метаданных, скачанный на портале Azure, а затем нажмите кнопку **Add** (Добавить).

    ![Настройка единого входа](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Выполните следующие действия на вкладке **Details** (Сведения).

    ![Настройка единого входа](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. Из списка **Display name mapping** (Сопоставление отображаемого имени) выберите **Use Attribute** (Использовать атрибут).

    b. В текстовом поле **Display name attribute** (Атрибут отображаемого имени) введите значение `http://schema.microsoft.com/identity/claims/displayname`.

    c. Из списка **Unique user mapping** (Сопоставление уникального пользователя) выберите **Use Attribute** (Использовать атрибут).

    d. В текстовом поле **Unique user attribute** (Атрибут уникального пользователя имени) введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    д. В разделе **Status** (Состояние) установите все три флажка.

    Е. Из списка **Create local accounts** (Создание локальных учетных записей) выберите **automatically** (Автоматически).

    ж. Щелкните **Save changes** (Сохранить изменения).

    h. Из вкладки **</> Проверяющая сторона** скопируйте **URL-адрес метаданных** и откройте его в браузере, чтобы скачать файл **XML метаданных пакета обновления**. Отправьте этот файл метаданных пакета обновления в раздел **Базовая конфигурация SAML** Azure AD.

    ![Настройка единого входа](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Создание тестового пользователя OpenAthens

В этом разделе вы создадите в OpenAthens пользователя с именем Britta Simon. Приложение OpenAthens поддерживает **JIT-подготовку пользователей**, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в OpenAthens, он создается после проверки подлинности.

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "OpenAthens" на панели доступа, вы автоматически войдете в приложение OpenAthens, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Пробное использование OpenAthens с помощью Azure AD](https://aad.portal.azure.com/)