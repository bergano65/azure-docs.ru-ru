---
title: Руководство по Интеграция Azure Active Directory с приложением SAP Cloud Platform Identity Authentication | Документация Майкрософт
description: Сведения о настройке единого входа в Azure Active Directory и SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8e14d54ca30c0e46d3bef0202784a35493663ef
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198190"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Руководство по Интеграция Azure Active Directory с приложением SAP Cloud Platform Identity Authentication

В этом руководстве описано, как интегрировать приложение SAP Cloud Platform Identity Authentication с Azure Active Directory (AAD).
Интеграция приложения SAP Cloud Platform Identity Authentication с AAD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к SAP Cloud Platform Identity Authentication.
* Вы можете включить автоматический вход пользователей в SAP Cloud Platform Identity Authentication (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию AAD с приложением SAP Cloud Platform Identity Authentication, вам потребуются:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка на SAP Cloud Platform Identity Authentication с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* SAP Cloud Platform Identity Authentication поддерживает единый вход, инициируемый **поставщиком услуг** и **поставщиком удостоверений**.

Прежде чем углубиться в технические подробности, очень важно разобраться в основных понятиях этого процесса. Службы федерации SAP Cloud Platform Identity Authentication и AD позволяют реализовать единый вход в приложения и службы, защищенные с помощью Azure AD (выступающей в качестве поставщика удостоверений), и приложения и службы SAP, защищенные с помощью SAP Cloud Platform Identity Authentication.

Приложение SAP Cloud Platform Identity Authentication выполняет роль "промежуточного" поставщика удостоверений для приложений SAP. В свою очередь, Azure Active Directory выступает ведущим поставщиком удостоверений. 

Этот пример представлен на схеме ниже.

![Создание тестового пользователя Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

В этой конфигурации клиент SAP Cloud Platform Identity Authentication настроен как доверенное приложение в AAD.

Все приложения и службы SAP, которые вы намерены защитить, настраиваются в консоли управления SAP Cloud Platform Identity Authentication.

Это значит, что авторизация для доступа к этим приложениям и службам SAP будет выполняться в приложении SAP Cloud Platform Identity Authentication, а не в Azure Active Directory.

Настройка SAP Cloud Platform Identity Authentication в роли приложения в Azure Active Directory Marketplace избавляет от необходимости настраивать отдельные утверждения или утверждения SAML.

> [!NOTE]
> В настоящее время обе стороны проверили только единый вход через веб-интерфейс. Потоки взаимодействия "приложение — API" и "API — API" готовы и работают, но пока не тестировались. Выполнение таких тестов входит в программу дальнейших действий.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Добавление приложения SAP Cloud Platform Identity Authentication из коллекции

Чтобы настроить интеграцию SAP Cloud Platform Identity Authentication с AAD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Cloud Platform Identity Authentication из коллекции:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **SAP Cloud Platform Identity Authentication**, выберите **SAP Cloud Platform Identity Authentication** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Приложения SAP Cloud Platform Identity Authentication в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в [название приложения] с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в [название приложения].

Чтобы настроить и проверить единый вход Azure AD в [название приложения], вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя SAP Cloud Platform Identity Authentication ](#create-sap-cloud-platform-identity-authentication-test-user)** требуется, чтобы создать в SAP Cloud Platform Identity Authentication пользователя Britta Simon, связанного представлением этого пользователя в AAD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в [название приложения], выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **SAP Cloud Platform Identity Authentication** выберите пункт **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить в режиме, инициируемом **IDP**, в разделе **Базовая конфигурация SAML** выполните следующие действия:

    ![Сведения для единого входа в разделе "Домен и URL-адреса приложения SAP Cloud Platform Identity Authentication"](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `<IAS-tenant-id>.accounts.ondemand.com`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`.

    > [!NOTE]
    > Эти значения приведены для примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Обратитесь в [службу поддержки SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html), чтобы узнать эти значения. Если вы не знаете, что такое значение идентификатора, изучите документацию по [настройке клиента SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) для SAP Cloud Platform Identity Authentication.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения для единого входа в разделе "Домен и URL-адреса приложения SAP Cloud Platform Identity Authentication"](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Используйте свой URL-адрес входа для бизнес-приложения. Если у вас возникнут сомнения, обратитесь в [службу поддержки SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html).

6. Приложение SAP Cloud Platform Identity Authentication поддерживает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![изображение](common/edit-attribute.png)

7. Если ваше приложение SAP ожидает определенный атрибут, например **firstName**, добавьте атрибут **firstName** в раздел **Утверждения пользователя** диалогового окна **Атрибуты пользователя**, настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](common/new-save-attribute.png)

    ![изображение](common/new-attribute-details.png)

    б) В текстовом поле **Имя** введите firstName.

    c. Оставьте пустым поле **Пространство имен**.

    4.3. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** выберите user.givenname.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

9. Скопируйте требуемый URL-адрес из раздела **Настройка SAP Cloud Platform Identity Authentication**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Настройка SAP Cloud Platform Identity Authentication с поддержкой единого входа

1. Чтобы настроить единый вход в приложении, откройте консоль администрирования SAP Cloud Platform Identity Authentication. URL-адрес имеет следующий формат: `https://<tenant-id>.accounts.ondemand.com/admin`. Ознакомьтесь с документацией по SAP Cloud Platform Identity Authentication на странице [интеграции с Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. На портале Azure нажмите кнопку **Сохранить**.

3. Следующие действия нужно выполнять только в том случае, если вы хотите добавить и включить единый вход для другого приложения SAP. Повторите весь процесс, описанный в разделе **Добавление приложения SAP Cloud Platform Identity Authentication из коллекции**.

4. На портале Azure на странице интеграции приложения **SAP Cloud Platform Identity Authentication** выберите пункт **Вход по ссылке**.

    ![Настройка связанного единого входа](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Сохраните конфигурацию.

> [!NOTE]
> Новое приложение применит ту же конфигурацию единого входа, которая настроена для первого приложения SAP. Убедитесь, что в консоли администрирования SAP Cloud Platform Identity Authentication указаны те же поставщики корпоративных удостоверений.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а) В поле **Имя** введите **BrittaSimon**.
  
    б) В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    в) Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    г) Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к SAP Cloud Platform Identity Authentication.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **SAP Cloud Platform Identity Authentication**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **SAP Cloud Platform Identity Authentication**.

    ![Ссылка на SAP Cloud Platform Identity Authentication в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Создание тестового пользователя SAP Cloud Platform Identity Authentication

В приложении SAP Cloud Platform Identity Authentication не нужно создавать пользователя. Пользователи, находящиеся в хранилище пользователей в Azure AD, могут использовать функцию единого входа.

Приложение SAP Cloud Platform Identity Authentication поддерживает федерацию удостоверений. Это позволяет приложению проверять, существуют ли пользователи, прошедшие аутентификацию через корпоративного поставщика удостоверений, в хранилище пользователей SAP Cloud Platform Identity Authentication.

Параметр федерации удостоверений по умолчанию отключен. Если вы включите этот параметр, доступ к приложению смогут получить только те пользователи, которые заранее импортированы в SAP Cloud Platform Identity Authentication.

Дополнительные сведения о том, как включить или отключить параметр федерации удостоверений для SAP Cloud Platform Identity Authentication см. в соответствующем разделе статьи о [настройке федерации удостоверений с использованием хранилища пользователей SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SAP Cloud Platform Identity Authentication на панели доступа, вы автоматически войдете в это приложение, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
