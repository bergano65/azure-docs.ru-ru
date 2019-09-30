---
title: Руководство по Интеграция единого входа Azure Active Directory с LinkedIn Learning | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cf5f8d79c2f416ea0c00064fecc8fd6008ae047
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71119848"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Руководство по Интеграция единого входа Azure Active Directory с LinkedIn Learning

В этом руководстве описано, как интегрировать приложение LinkedIn Learning с Azure Active Directory (Azure AD). Интеграция LinkedIn Learning с Azure AD обеспечивает следующие возможности:

* Управление доступом к LinkedIn Learning с помощью Azure AD.
* Включение автоматического входа пользователей в LinkedIn Learning с использованием учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка LinkedIn Learning с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* LinkedIn Learning поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.
* LinkedIn Learning поддерживает **JIT**-подготовку пользователей.

## <a name="adding-linkedin-learning-from-the-gallery"></a>Добавление LinkedIn Learning из коллекции

Чтобы настроить интеграцию LinkedIn Learning с Azure AD, необходимо добавить LinkedIn Learning из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **LinkedIn Learning**.
1. Выберите **LinkedIn Learning** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-learning"></a>Настройка и проверка единого входа Azure AD для LinkedIn Learning

Настройте и проверьте единый вход Azure AD в LinkedIn Learning с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в LinkedIn Learning.

Чтобы настроить и проверить единый вход Azure AD в LinkedIn Learning, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в LinkedIn Learning](#configure-linkedin-learning-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя LinkedIn Learning](#create-linkedin-learning-test-user)** требуется для того, чтобы в LinkedIn Learning существовал пользователь B.Simon, связанный с представлением одноименного пользователя в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **LinkedIn Learning** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

     a. В текстовом поле **Идентификатор** введите **идентификатор сущности**, скопированный с портала LinkedIn. 

    b. В текстовом поле **URL-адрес ответа** введите **URL-адрес службы обработчика утверждений (ACS)** , скопированный с портала LinkedIn.

    c. Если вы хотите настроить для приложения режим, **инициированный поставщиком услуг**, тогда выберите вариант **Задать дополнительные URL-адреса** в разделе **Базовая конфигурация SAML**, где вы будете указывать URL-адрес для входа. Чтобы создать URL-адреса входа, скопируйте **URL-адрес службы обработчика утверждений (ACS)** и замените /saml/ на /login/. После этого URL-адрес входа должен иметь следующий вид.

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Сведения о домене и URL-адресах единого входа для приложения LinkedIn Learning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора и URL-адреса ответа, которые описываются далее в разделе **Настройка единого входа в LinkedIn Learning**.

1. Приложение LinkedIn Learning ожидает утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию, когда **nameidentifier** сопоставляется с **user.userprincipalname**. Приложение LinkedIn Learning ожидает сопоставления **nameidentifier** сопоставлены с **user.mail**, поэтому необходимо изменить сопоставление атрибутов, щелкнув значок **Изменить** и изменив сопоставление атрибутов.

    ![image](common/edit-attribute.png)

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и выберите **Скачать**, чтобы скачать сертификат и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Скопируйте требуемые URL-адреса из раздела **Настройка LinkedIn Learning**.

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

В этом разделе описано, как включить единый вход Azure для пользователя B.Simon, предоставив этому пользователю доступ к LinkedIn Learning.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **LinkedIn Learning**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-linkedin-learning-sso"></a>Настройка единого входа в LinkedIn Learning

1. В другом окне веб-браузера войдите в свой клиент LinkedIn Learning с правами администратора.

2. В **Account Center** (Центр учетных записей) в разделе **Settings** (Параметры) щелкните **Global Settings** (Глобальные параметры). Также выберите **Learning - Default** в раскрывающемся списке.

    ![Настройка единого входа](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Щелкните **OR Click Here to load and copy individual fields from the form** (Или щелкните здесь, чтобы загрузить и скопировать отдельные поля из формы) и скопируйте значения **идентификатора сущности** и **URL-адреса службы обработчика утверждений (ACS)** , и вставьте их в раздел **Базовая конфигурация SAML** на портале Azure.

    ![Настройка единого входа](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Перейдите в раздел **LinkedIn Admin Settings** (Параметры администратора LinkedIn). Передайте XML-файл, скачанный с портала Azure. Для этого нажмите кнопку **Upload XML file** (Передать XML-файл).

    ![Настройка единого входа](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Нажмите кнопку **On** (Включить), чтобы включить единый вход. Состояние единого входа изменяется с **Not Connected** (Не подключено) на **Connected** (Подключено.)

    ![Настройка единого входа](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Создание тестового пользователя LinkedIn Learning

Приложение LinkedIn Learning поддерживает JIT-подготовку пользователей, поэтому после аутентификации пользователи будут созданы в приложении автоматически. На странице параметров администратора на портале LinkedIn Learning включите параметр **Automatically assign licenses** (Автоматически назначать лицензии), чтобы активировать JIT-подготовку. При этом пользователю будет также назначена лицензия.

   ![Создание тестового пользователя Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку LinkedIn Learning на панели доступа, вы автоматически войдете в приложение LinkedIn Learning, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте LinkedIn Learning в работе с Azure AD](https://aad.portal.azure.com/)

