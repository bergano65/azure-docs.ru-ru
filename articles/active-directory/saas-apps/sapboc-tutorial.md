---
title: Руководство. Интеграции Azure Active Directory с SAP Business Object Cloud | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP Business Object Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c397cd3b535c7bf918eac2ac1e9ae1f967d7b95a
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877509"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Руководство по Интеграции Azure Active Directory с SAP Business Object Cloud

В этом руководстве описано, как интегрировать SAP Business Object Cloud с Azure Active Directory (Azure AD).
Интеграция SAP Business Object Cloud с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к приложению SAP Business Object Cloud.
* Вы можете включить автоматический вход пользователей в SAP Business Object Cloud (единый вход) с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с SAP Business Object Cloud, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка SAP Business Object Cloud с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* SAP Business Object Cloud поддерживает единый вход инициированного **пакета обновления**.

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Добавление SAP Business Object Cloud из коллекции

Чтобы настроить интеграцию SAP Business Object Cloud с Azure AD, необходимо добавить SAP Business Object Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Business Object Cloud из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **SAP Business Object Cloud**, выберите **SAP Business Object Cloud** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![SAP Business Object Cloud в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SAP Business Object Cloud с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Business Object Cloud.

Чтобы настроить и проверить единый вход Azure AD в SAP Business Object Cloud, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в SAP Business Object Cloud](#configure-sap-business-object-cloud-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя SAP Business Object Cloud](#create-sap-business-object-cloud-test-user)** требуется для создания пользователя Britta Simon в SAP Business Object Cloud, связанного с соответствующим пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в SAP Business Object Cloud, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **SAP Business Object Cloud** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения SAP Business Object Cloud](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Значения этих URL-адресов приведены только в качестве примера. Замените эти значения фактическим URL-адресом для входа и URL-адресом идентификатора. Чтобы получить URL-адрес для входа, обратитесь в [службу поддержки клиентов SAP Business Object Cloud](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Вы можете получить URL-адрес идентификатора при загрузке метаданных SAP Business Object Cloud из консоли администрирования. Это объясняется далее в руководстве.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>Настройка единого входа для SAP Business Object Cloud

1. В другом окне браузера войдите на сайт SAP Business Object Cloud своей компании в качестве администратора.

2. Выберите **Меню** > **Система** > **Администрирование**.
    
    ![Выбор параметров "Меню", "Система" и "Администрирование"](./media/sapboc-tutorial/config1.png)

3. На вкладке **Безопасность** щелкните значок пера **Изменить**.
    
    ![Выбор значка пера "Изменить" на вкладке "Безопасность"](./media/sapboc-tutorial/config2.png)  

4. В качестве **метода проверки подлинности** выберите **SAML Single Sign-On (SSO)** (Единый вход SAML (SSO)).

    ![Выбор параметра SAML Single Sign-On (SSO) (Единый вход SAML (SSO)) в качестве метода проверки подлинности](./media/sapboc-tutorial/config3.png)  

5. Нажмите кнопку **Загрузить** для загрузки метаданных поставщика услуг (шаг 1). В файле метаданных найдите и скопируйте значение **EntityID** На портале Azure в диалоговом окне **Базовая конфигурация SAML** вставьте значение в поле **Идентификатор**.

    ![Копирование и вставка значения EntityID](./media/sapboc-tutorial/config4.png)  

6. Чтобы отправить метаданные поставщика услуг (шаг 2) в файл, загруженный с портала Azure, в разделе **Upload Identity Provider metadata** (Отправить метаданные поставщика удостоверений) нажмите кнопку **Отправить**.  

    ![Нажатие кнопки "Отправить" в разделе Upload Identity Provider metadata (Отправить метаданные поставщика удостоверений)](./media/sapboc-tutorial/config5.png)

7. В списке **Атрибут пользователя** выберите атрибут пользователя (шаг 3), который хотите использовать в своей реализации. Этот атрибут пользователя сопоставляется с поставщиком удостоверений. Чтобы ввести пользовательский атрибут на странице пользователя, используйте параметр **Custom SAML Mapping** (Пользовательское сопоставление SAML). Или выберите **Email** или **USER ID** в качестве пользовательского атрибута. В нашем примере выбран **Адрес электронной почты**, так как мы сопоставили утверждение идентификатора пользователя с атрибутом **userprincipalname** в разделе **User Attributes & Claims** (Утверждения и атрибуты пользователя) на портале Azure. Так вы получаете уникальный адрес электронной почты пользователя, который отправляется в приложение SAP Business Object Cloud в каждом успешном ответе SAML.

    ![Выбор атрибута пользователя](./media/sapboc-tutorial/config6.png)

8. Чтобы проверить учетную запись с помощью поставщика удостоверений (шаг 4), в поле **Login Credential (Email)** (Учетные данные входа (электронная почта)) введите адрес электронной почты пользователя. Выберите **Проверить учетную запись**. Система добавит учетные данные входа в учетную запись пользователя.

    ![Ввод электронного адреса и нажатие кнопки "Проверить учетную запись"](./media/sapboc-tutorial/config7.png)

9. Щелкните значок **Сохранить**.

    ![Значок "Сохранить"](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к SAP Business Object Cloud.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **SAP Business Object Cloud**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **SAP Business Object Cloud**.

    ![В списке приложений выберите SAP Business Object Cloud](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-sap-business-object-cloud-test-user"></a>Создание тестового пользователя SAP Business Object Cloud

Пользователей Azure AD нужно подготовить в SAP Business Object Cloud, прежде чем они смогут входить в SAP Business Object Cloud. В SAP Business Object Cloud подготовка выполняется вручную.

Чтобы подготовить учетную запись пользователя, сделайте следующее:

1. Выполните вход на корпоративный сайт SAP Business Object Cloud с правами администратора.

2. Выберите **Меню** > **Безопасность** > **Пользователи**.

    ![Добавление сотрудника](./media/sapboc-tutorial/user1.png)

3. На странице **Пользователи** щелкните **+**, чтобы добавить сведения о новом пользователе. 

    ![Страница добавления пользователей](./media/sapboc-tutorial/user4.png)

    Затем сделайте следующее:

    a. В поле **ИД пользователя** введите идентификатор пользователя, например **Britta**.

    b. В текстовом поле **Имя** введите имя пользователя, например **Britta**.

    c. В текстовом поле **Фамилия** введите фамилию пользователя, например **Simon**.

    4.3. В текстовом поле **Отображаемое имя** введите полное имя пользователя, например **Britta Simon**.

    д. В текстовом поле **Электронная почта** введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    Е. На странице **выбора ролей** выберите соответствующую роль для пользователя и нажмите кнопку **ОК**.

      ![Выбрать роль](./media/sapboc-tutorial/user3.png)

    ж. Щелкните значок **Сохранить**.    

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "SAP Business Object Cloud" на панели доступа, вы автоматически войдете в приложение SAP Business Object Cloud, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

