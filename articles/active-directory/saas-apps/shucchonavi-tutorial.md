---
title: Руководство. Интеграция Azure Active Directory со Shuccho Navi | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Shuccho Navi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32b6676c-d1ec-48c2-91b1-41990ed0560c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: e4fcc261a2952224870d5f1a0db4b9ddb666034e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850349"
---
# <a name="tutorial-azure-active-directory-integration-with-shuccho-navi"></a>Руководство. Интеграция Azure Active Directory со Shuccho Navi

В этом руководстве описано, как интегрировать Shuccho Navi с Azure Active Directory (Azure AD).
Интеграция Shuccho Navi с Azure AD обеспечивает следующие преимущества.

* C помощью Azure AD вы можете контролировать доступ к Shuccho Navi.
* Можно включить пользователей требуется автоматически войти в систему для Shuccho Navi (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD со Shuccho Navi, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Shuccho Navi единого входа — подписка с поддержкой

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Поддерживает Shuccho Navi **SP** единый вход

## <a name="adding-shuccho-navi-from-the-gallery"></a>Добавление Shuccho Navi из коллекции

Чтобы настроить интеграцию Shuccho Navi с Azure AD, необходимо добавить приложение Shuccho Navi из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Shuccho Navi из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Shuccho Navi**, выберите **Shuccho Navi** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Shuccho Navi в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка Azure AD единого входа с Shuccho Navi использованием тестового пользователя **пользователя Britta Simon**.
Для единого входа для работы необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Shuccho Navi.

Чтобы настроить и проверить единый вход Azure AD в Shuccho Navi, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа Navi Shuccho](#configure-shuccho-navi-single-sign-on)**  — Настройка параметров единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Shuccho Navi](#create-shuccho-navi-test-user)**  — требуется для создания пользователя Britta Simon в Shuccho Navi, связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить Azure AD единого входа в Shuccho Navi, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Shuccho Navi** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Shuccho Navi домене и URL-адреса приложения единого входа сведения](common/sp-signonurl.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://naviauth.nta.co.jp/saml/login?ENTP_CD=<Your company code>`.

    > [!NOTE]
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Обратитесь к [службу поддержки клиентов Navi Shuccho](mailto:sys_ntabtm@nta.co.jp) для получения значения. Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. На **Настройка Shuccho Navi** скопируйте соответствующий URL-адреса в соответствии с вашим требованиям.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-shuccho-navi-single-sign-on"></a>Настройка Shuccho Navi единого входа

Чтобы настроить единый вход на **Shuccho Navi** стороны, нужно отправить Скачанный **XML-ФАЙЛ метаданных** и соответствующие URL-адреса приложения, скопированный с портала Azure, чтобы [Shuccho Navi поддержки](mailto:sys_ntabtm@nta.co.jp). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    2. В **имя пользователя** тип поля **brittasimon\@yourcompanydomain.extension**  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Shuccho Navi.

1. На портале Azure выберите **корпоративные приложения**выберите **все приложения**, а затем выберите **Shuccho Navi**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Shuccho Navi**.

    ![Ссылка на Shuccho Navi в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-shuccho-navi-test-user"></a>Создание тестового пользователя Shuccho Navi

В этом разделе описано, как создать пользователя Britta Simon в Shuccho Navi. Обратитесь в  [службу поддержки Shuccho Navi](mailto:sys_ntabtm@nta.co.jp), чтобы добавить пользователей на платформу Shuccho Navi. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Когда вы нажмете плитку Shuccho Navi на панели доступа, вы автоматически войдете Shuccho Navi, для которого настраивается единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
