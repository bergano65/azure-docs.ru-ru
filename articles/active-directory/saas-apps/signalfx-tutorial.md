---
title: Руководство по Интеграция Azure Active Directory с SignalFx | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 2ce766da0521b787edec020d7dfc3de2a2d83b19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090709"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Руководство по Интеграция Azure Active Directory с SignalFx

В этом руководстве описано, как интегрировать SignalFx с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением SignalFx обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к SignalFx.
* Вы можете включить автоматический вход пользователей в SignalFx (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с SignalFx, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка SignalFx с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* SignalFx поддерживает единый вход, инициируемый **поставщиком удостоверений**.
* SignalFx поддерживает **JIT**-подготовку пользователей.

## <a name="adding-signalfx-from-the-gallery"></a>Добавление SignalFx из коллекции.

Чтобы настроить интеграцию SignalFx с Azure AD, необходимо добавить SignalFx из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SignalFx из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **SignalFx**, выберите **SignalFx** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![SignalFx в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SignalFx с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SignalFx.

Чтобы настроить и проверить единый вход Azure AD в SignalFx, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в SignalFx](#configure-signalfx-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя приложения SignalFx](#create-signalfx-test-user)** требуется для того, чтобы в SignalFx существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в SignalFx, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **SignalFx** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. На странице **Настройка единого входа с помощью SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения SignalFx](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `https://api.signalfx.com/v1/saml/metadata`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://api.signalfx.com/v1/saml/acs/<integration ID>`.

    > [!NOTE]
    > Приведенное выше значение используется только для примера. Это значение следует заменить фактическим URL-адресом ответа, который описывается далее в этом руководстве.

5. Приложение SignalFx ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![image](common/edit-attribute.png)

6. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** измените утверждения, щелкнув значок **Изменить**, или добавьте утверждение, нажав кнопку **Добавить новое утверждение**, чтобы настроить атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия. 

    | ИМЯ |  Исходный атрибут|
    | ------------------- | -------------------- |
    | User.FirstName     | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

8. Требуемый URL-адрес можно скопировать из раздела **Настройка SignalFx**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-signalfx-single-sign-on"></a>Настройка единого входа в SignalFx

1. Войдите на свой корпоративный сайт SignalFx как администратор.

1. В верхней части сайта щелкните **Integrations** (Интеграция), чтобы открыть соответствующую страницу.

    ![Интеграция SignalFx](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. Щелкните плитку **Azure Active Directory** в разделе **Login Services** (Службы входа).

    ![SAML SignalFx](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. Щелкните **NEW INTEGRATION** (Новая интеграция) и на вкладке **INSTALL** (Установка) выполните следующие действия:

    ![Страница samlintgpage SignalFx](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. В текстовом поле **Name** (Имя) введите имя новой интеграции, например **OurOrgName SAML SSO**.

    b. Скопируйте значение **Integration ID** (Идентификатор интеграции) и, добавив к нему значение **Reply URL** (URL-адрес ответа), замените `<integration ID>` в текстовом поле **URL-адрес ответа** в разделе **Домены и URL-адреса приложения SignalFx** на портале Azure.

    c. Щелкните **Upload File** (Отправить файл), чтобы отправить **сертификат в кодировке Base64**, скачанный с портала Azure, в поле **Certificate** (Сертификат).

    d. В текстовое поле **Issuer URL** (URL-адрес издателя) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    д. В текстовое поле **Metadata URL** (URL-адрес метаданных) вставьте значение **URL-адрес метаданных**, скопированное на портале Azure.

    Е. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon@yourcompanydomain.extension`.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к SignalFx.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **SignalFx**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **SignalFx**.

    ![Ссылка на SignalFx в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-signalfx-test-user"></a>Создание тестового пользователя SignalFx

Цель этого раздела — создать пользователя с именем Britta Simon в SignalFx. Приложение SignalFx поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению SignalFx (если он еще не создан).

При первом входе пользователя в SignalFx с использованием единого входа SAML [служба поддержки SignalFx](mailto:kmazzola@signalfx.com) отправит ему по электронной почте ссылку, которую необходимо открыть для проверки подлинности. Это происходит только при первом входе пользователя. При последующих попытках входа проверка по электронной почте не требуется.

> [!Note]
> Чтобы создать пользователя вручную, обратитесь к  [группе поддержки SignalFx](mailto:kmazzola@signalfx.com).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "SignalFx" на Панели доступа, вы автоматически войдете в приложение SignalFx, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

