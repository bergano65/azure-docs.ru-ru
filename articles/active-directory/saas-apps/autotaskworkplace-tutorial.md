---
title: Руководство. Интеграция Azure Active Directory с Autotask Workplace | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Autotask Workplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7be7d8b9fc9aa8b6c9c1d888744c25d710ca79a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106503"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Руководство. Интеграция Azure Active Directory с Autotask Workplace

В этом руководстве описано, как интегрировать Autotask Workplace с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Autotask Workplace обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Autotask Workplace.
* Вы можете включить автоматический вход пользователей в Autotask Workplace (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Autotask Workplace, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Autotask Workplace с поддержкой единого входа;
* подписка Autotask Workplace с поддержкой единого входа;
* права администратора или суперадминистратора в рабочей области;
* учетная запись администратора в Azure AD;
* учетные записи в Workplace и Azure AD (адреса электронной почты для обоих должны совпадать) для пользователей, которые будут применять этот компонент.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Autotask Workplace поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.

## <a name="adding-autotask-workplace-from-the-gallery"></a>Добавление Autotask Workplace из коллекции

Чтобы настроить интеграцию Autotask Workplace с Azure AD, необходимо добавить Autotask Workplace из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Autotask Workplace из коллекции, выполните следующие действия**.

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Autotask Workplace**, на панели результатов выберите **Autotask Workplace** и нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Autotask Workplace в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Autotask Workplace с использованием тестового пользователя **Britta Simon**.
Для работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Autotask Workplace.

Чтобы настроить и проверить единый вход Azure AD в Autotask Workplace, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Autotask Workplace](#configure-autotask-workplace-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Autotask Workplace](#create-autotask-workplace-test-user)** нужно для того, чтобы в Autotask Workplace также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Autotask Workplace, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Autotask Workplace** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах приложения Autotask Workplace](common/idp-intiated.png)

    а) В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах приложения Autotask Workplace](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<subdomain>.awp.autotask.net/loginsso`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

7. Скопируйте требуемый URL-адрес из раздела **Настройка Autotask Workplace**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-autotask-workplace-single-sign-on"></a>Настройка единого входа для Autotask Workplace

1. В другом окне браузера войдите в Workplace Online, используя учетные данные администратора.

    > [!Note]
    > При настройке поставщика удостоверений нужно указать поддомен. Чтобы убедиться, что указан правильный поддомен, войдите в Workplace Online. После входа обратите внимание на поддомен в URL-адресе. Поддомен (us, eu, ca или au) должен быть указан между https:// и .awp.autotask.net/.

2. Выберите **Конфигурация** > **Единый вход** и выполните следующие действия.

    ![Конфигурация единого входа в Autotask](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)

    а) Выберите **XML Metadata File** (XML-файл метаданных), а затем отправьте **XML-файл метаданных федерации**, скачанный на портале Azure.

    б) Нажмите кнопку **ENABLE SSO** (Включить единый вход).

    ![Подтверждение конфигурации единого входа в Autotask](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    в) Установите флажок **I confirm this information is correct and I trust this IdP** (Я подтверждаю эти сведения и доверяю этому поставщику удостоверений).

    г) Нажмите кнопку **Утвердить**.

> [!Note]
> Если вам требуется помощь по настройке Autotask Workplace и учетной записи Workplace, см. [эту страницу](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@домен_вашей_компании.доменная_зона**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Autotask Workplace.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Autotask Workplace**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Autotask Workplace**.

    ![Ссылка Autotask Workplace в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-autotask-workplace-test-user"></a>Создание тестового пользователя Autotask Workplace

В этом разделе описано, как создать пользователя Britta Simon в приложении Autotask Workplace. Обратитесь в [службу поддержки Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm), чтобы добавить пользователей на платформу Autotask Workplace.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Autotask Workplace на панели доступа, вы автоматически войдете в приложение Autotask Workplace, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
