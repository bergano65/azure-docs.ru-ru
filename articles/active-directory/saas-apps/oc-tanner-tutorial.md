---
title: Руководство по Интеграция Azure Active Directory с O.C. Tanner — AppreciateHub | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и O.C. Tanner — AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8814dc9ba94ca7fa56a2225c71895520467bf05f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095821"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Руководство по Интеграция Azure Active Directory с O.C. Tanner — AppreciateHub

В этом руководстве вы узнаете, как интегрировать O.C. Tanner — AppreciateHub с Azure Active Directory (Azure AD).
Интеграция O.C. Tanner — AppreciateHub с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к O.C. Tanner — AppreciateHub.
* Вы можете включить автоматический вход пользователей (единый вход) в O.C. Tanner — AppreciateHub под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с O.C. Tanner — AppreciateHub, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* O.C. подписка Tanner — AppreciateHub с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* O.C. Tanner — AppreciateHub поддерживает единый вход, инициированный **поставщиком удостоверений**.

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Добавление O.C. Tanner — AppreciateHub из коллекции

Чтобы настроить интеграцию O.C. Tanner — AppreciateHub с Azure AD, необходимо добавить O.C. Tanner — AppreciateHub из коллекции в список управляемых приложений SaaS.

**Чтобы добавить O.C. Tanner — AppreciateHub из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **O.C. Tanner — AppreciateHub**, выберите **O.C. Tanner — AppreciateHub** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![O.C. Tanner — AppreciateHub в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение O.C. Tanner — AppreciateHub с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в O.C. Необходимо установить связь Tanner — AppreciateHub.

Чтобы настроить и проверить единый вход Azure AD в O.C. Tanner — AppreciateHub, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в O.C. Tanner — AppreciateHub](#configure-oc-tanner---appreciatehub-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя в O.C. Tanner — AppreciateHub](#create-oc-tanner---appreciatehub-test-user)** требуется для создания пользователя Britta Simon в O.C. Tanner — AppreciateHub, связанного с соответствующим представлением в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в O.C. Tanner — AppreciateHub, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **O.C. Tanner — AppreciateHub**выберите**Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если у вас есть **файл метаданных поставщика служб**, выполните следующие действия в разделе **Базовая конфигурация SAML**:

    >[!NOTE]
    >Вы можете скачать **файл метаданных поставщика услуг** на [этой странице](https://fed.appreciatehub.com/fed/sp/metadata).

    a. Щелкните **Отправить файл метаданных**.

    ![Передача файла метаданных](common/upload-metadata.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Выбор файла метаданных](common/browse-upload-metadata.png)

    c. После успешной передачи файла метаданных значения **Идентификатор** и **URL-адрес ответа** в разделе "Базовая конфигурация SAML" заполнятся автоматически.

     ![O.C. Tanner — AppreciateHub. Сведения о домене и URL-адресах единого входа](common/idp-intiated.png)

    > [!Note]
    > Если поля **Идентификатор** и **URL-адрес ответа** автоматически не заполняются, введите эти значения вручную в соответствии с поставленной задачей. Обратитесь в [службу поддержки клиентов O.C. Tanner — AppreciateHub](mailto:sso@octanner.com), чтобы получить эти значения. Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Требуемый URL-адрес можно скопировать из раздела **Настройка O.C. Tanner — AppreciateHub**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-oc-tanner---appreciatehub-single-sign-on"></a>Настройка единого входа в O.C. Tanner — AppreciateHub

Чтобы настроить единый вход на стороне приложения **O.C. Tanner — AppreciateHub**, нужно отправить скачанный **XML-файл метаданных федерации** и соответствующие URL-адреса, скопированные на портале Azure, [группе поддержки O.C. службе поддержки О.С. Tanner — AppreciateHub](mailto:sso@octanner.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к O.C. Tanner — AppreciateHub.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **O.C. Tanner — AppreciateHub**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **O.C. Tanner — AppreciateHub**.

    ![Ссылка на O.C. Tanner — AppreciateHub в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-oc-tanner---appreciatehub-test-user"></a>Создание тестового пользователя в O.C. Tanner — AppreciateHub

Цель этого раздела — создать пользователя с именем Britta Simon в O.C. Tanner — AppreciateHub.

**Чтобы создать пользователя с именем Britta Simon в O.C. Tanner — AppreciateHub, выполните следующие действия.**

Попросите [службу поддержки O.C. Tanner — AppreciateHub](mailto:sso@octanner.com) создать пользователя, у которого значение атрибута nameID совпадает с именем пользователя Britta Simon в Azure AD.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку O.C. Tanner — AppreciateHub на Панели доступа, вы автоматически войдете в приложение O.C. Tanner — AppreciateHub, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
