---
title: Руководство по Интеграция Azure Active Directory с RingCentral | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: 7cd20397c122834a23586bf51af6467f58531094
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997246"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Руководство по Интеграция Azure Active Directory с RingCentral

В этом руководстве описано, как интегрировать RingCentral с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением RingCentral обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к RingCentral.
* Можно включить автоматический вход пользователей в RingCentral (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением RingCentral, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка RingCentral с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* RingCentral поддерживает единый вход, инициированный **поставщиком служб**.

## <a name="adding-ringcentral-from-the-gallery"></a>Добавление RingCentral из коллекции.

Чтобы настроить интеграцию RingCentral с Azure AD, необходимо добавить RingCentral из коллекции в список управляемых приложений SaaS.

**Чтобы добавить RingCentral из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **RingCentral**, выберите **RingCentral** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![RingCentral в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в RingCentral с использованием тестового пользователя **Britta Simon**.
Для обеспечения единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в RingCentral.

Чтобы настроить и проверить единый вход Azure AD в RingCentral, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в RingCentral](#configure-ringcentral-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя RingCentral](#create-ringcentral-test-user)** требуется для того, чтобы в RingCentral существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в RingCentral, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **RingCentral** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если у вас есть **файл метаданных поставщика служб**, выполните следующие действия в разделе **Базовая конфигурация SAML**:

    a. Щелкните **Отправить файл метаданных**.

    ![Передача файла метаданных](common/upload-metadata.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Выбор файла метаданных](common/browse-upload-metadata.png)

    c. После успешной передачи файла метаданных значения параметров **Идентификатор** и **URL-адрес ответа** в разделе **Базовая конфигурация SAML** будут указаны автоматически.

    ![Сведения о домене и URL-адресах единого входа приложения RingCentral](common/sp-identifier-reply.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес:
    
    | |
    |--|
    | `https://service.ringcentral.com`|
    | `https://service.ringcentral.com.au`|
    | `https://service.ringcentral.co.uk`|
    | `https://service.ringcentral.eu`|

    > [!Note]
    > Вы получите **файл метаданных поставщика служб** на странице настройки единого входа RingCentral, что описано далее в этом руководстве.

5. Если у вас нет **файла метаданных поставщика службы**, выполните следующие действия:

    a. В текстовом поле **URL-адрес для входа** введите следующий URL-адрес:

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. В текстовом поле **Идентификатор** введите URL-адрес:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![Сведения о домене и URL-адресах единого входа приложения RingCentral](common/sp-identifier-reply.png)

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы копировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

### <a name="configure-ringcentral-single-sign-on"></a>Настройка единого входа в RingCentral

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к RingCentral.

1. На портале Azure последовательно выберите **Корпоративные приложения**, **Все приложения** и **RingCentral**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **RingCentral**.

    ![Ссылка на RingCentral в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-ringcentral-test-user"></a>Создание тестового пользователя RingCentral

В этом разделе описано, как создать пользователя Britta Simon в приложении RingCentral. Чтобы добавить пользователей на платформу RingCentral, обратитесь в  [службу поддержки RingCentral](https://success.ringcentral.com/RCContactSupp) . Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку RingCentral на Панели доступа, вы автоматически войдете в приложение RingCentral, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
