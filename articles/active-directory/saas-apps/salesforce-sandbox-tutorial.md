---
title: Руководство по Интеграция Azure Active Directory с песочницей Salesforce | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2940f3eee3112fe1c6d57cc92157c573ecad109
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65904281"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Руководство по Интеграция Azure Active Directory с песочницей Salesforce

В этом руководстве описано, как интегрировать Salesforce Sandbox с Azure Active Directory (Azure AD).

Песочницы позволяет создать несколько копий организации в отдельных средах для различных целей, например для разработки, тестирования и обучения, не подвергая риску данные и приложения в рабочей организации Salesforce.
Дополнительные сведения см. в статье[Sandbox Types and Templates](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5) (Типы и шаблоны песочниц).

Интеграция Azure AD с Salesforce Sandbox обеспечивает следующие преимущества:

* С помощью Azure AD можно управлять доступом к Salesforce Sandbox.
* Можно включить автоматический вход пользователей в Salesforce Sandbox (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Salesforce Sandbox, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка Salesforce Sandbox с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Salesforce Sandbox поддерживает единый вход, инициируемый **поставщиком услуг и поставщиком удостоверений**.
* Salesforce Sandbox поддерживает **JIT**-подготовку пользователей.
* Salesforce Sandbox поддерживает [**автоматизированную** подготовку пользователей](salesforce-sandbox-provisioning-tutorial.md).

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Добавление Salesforce Sandbox из коллекции

Чтобы настроить интеграцию Salesforce Sandbox с Azure AD, нужно добавить Salesforce Sandbox из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Salesforce Sandbox из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Salesforce Sandbox**, выберите **Salesforce Sandbox** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Salesforce Sandbox в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе объясняется, как настроить и проверить единый вход Azure AD в Salesforce Sandbox с использованием тестового пользователя **Britta Simon**.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Salesforce Sandbox соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Salesforce Sandbox.

Чтобы настроить и проверить единый вход Azure AD в Salesforce Sandbox, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Salesforce Sandbox](#configure-salesforce-sandbox-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Salesforce Sandbox](#create-salesforce-sandbox-test-user)** требуется для создания в Salesforce Sandbox пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Salesforce Sandbox, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Salesforce Sandbox** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вам нужно настроить единый вход, инициируемый **поставщиком удостоверений**, и у вас есть **файл метаданных поставщика услуг**, выполните следующие действия в разделе **Базовая конфигурация SAML**:

    a. Щелкните **Отправить файл метаданных**.

    ![Передача файла метаданных](common/upload-metadata.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Выбор файла метаданных](common/browse-upload-metadata.png)

    > [!NOTE]
    > Вы получите доступ к файлу метаданных поставщика служб на портале администрирования Salesforce Sandbox, как описано далее в этом руководстве.

    c. После успешной передачи файла метаданных значение **URL-адрес ответа** будет автоматически добавлено в поле **URL-адрес ответа**.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Если поле **URL-адрес ответа** автоматически не заполнилось, введите это значение вручную в соответствии со своими требованиями.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Требуемые URL-адреса можно скопировать из раздела **Настройка Salesforce Sandbox**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-salesforce-sandbox-single-sign-on"></a>Настройка единого входа в Salesforce Sandbox

1. Откройте новую вкладку в браузере и войдите в учетную запись администратора Salesforce Sandbox.

2. Щелкните **Setup** (Настройка) под **значком параметров** в правом верхнем углу страницы.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure1.png)

3. В области навигации слева прокрутите вниз до элемента **Параметры** и щелкните **Удостоверение**, чтобы развернуть соответствующий раздел. Затем щелкните **Параметры единого входа**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. На странице **Single Sign-On Settings** (Параметры единого входа) нажмите кнопку **Edit** (Изменить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure3.png)

5. Выберите **SAML Enabled** (SAML включен), а затем щелкните **Save** (Сохранить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Чтобы настроить параметры единого входа SAML, щелкните **New from Metadata File** (Создать из файла метаданных).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Щелкните **Выбрать файл** для отправки XML-файла метаданных, загруженного с портала Azure, и щелкните **Создать**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. На странице **SAML Single Sign-On Settings** (Параметры единого входа SAML) поля заполняются автоматически. Щелкните "Сохранить".

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. На странице **Параметры единого входа** нажмите кнопку **Download Metadata** (Скачать метаданные), чтобы скачать файл метаданных поставщика служб. Используйте этот файл в разделе **Базовая конфигурация SAML** на портале Azure для настройки необходимых URL-адресов, как описано выше.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure4.png)

10. Если вы хотите настроить приложение в режиме, инициированном **SP**, для этого есть следующие требования:

    a. Вам необходимо получить проверенный домен.

    b. Необходимо настроить и включить домен в Salesforce Sandbox. Дальнейшие действия объясняются далее в этом руководстве.

    c. На портале Azure в разделе **Базовая конфигурация SAML** щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.
  
    ![Сведения о домене и URL-адресах единого входа для приложения Salesforce Sandbox](common/both-signonurl.png)

    В текстовом поле **URL-адрес для входа** введите значение в следующем формате: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`.

    > [!NOTE]
    > Это значение следует скопировать с портала Salesforce Sandbox сразу после включения домена.

11. В разделе **Сертификат подписи SAML** щелкните **XML метаданных федерации** и сохраните XML-файл на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

12. Откройте новую вкладку в браузере и войдите в систему с учетной записью администратора Salesforce Sandbox.

13. Щелкните **Setup** (Настройка) под **значком параметров** в правом верхнем углу страницы.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure1.png)

14. В области навигации слева прокрутите вниз до элемента **Параметры** и щелкните **Удостоверение**, чтобы развернуть соответствующий раздел. Затем щелкните **Параметры единого входа**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. На странице **Single Sign-On Settings** (Параметры единого входа) нажмите кнопку **Edit** (Изменить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure3.png)

16. Выберите **SAML Enabled** (SAML включен), а затем щелкните **Save** (Сохранить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Чтобы настроить параметры единого входа SAML, щелкните **New from Metadata File** (Создать из файла метаданных).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Щелкните **Выбрать файл**, чтобы передать XML-файл метаданных, затем нажмите кнопку **Создать**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. На странице **SAML Single Sign-On Settings** (Параметры единого входа SAML) поля заполняются автоматически. Введите имя конфигурации (например, *SPSSOWAAD_Test*) в текстовом поле **Имя** и нажмите кнопку "Сохранить".

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Чтобы включить домен в приложении Salesforce Sandbox, выполните следующие действия:

    > [!NOTE]
    > Перед включением домена необходимо его создать в приложении Salesforce Sandbox. Дополнительные сведения см. в разделе [Define Your Domain Name](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US) (Определение доменного имени). Создав домен, убедитесь, что он настроен правильно.

21. В области навигации слева в Salesforce Sandbox щелкните **Company Settings** (Параметры компании), чтобы развернуть соответствующий раздел, и выберите пункт **My Domain** (Мой домен).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. В разделе **Authentication Configuration** (Конфигурация проверки подлинности) щелкните **Edit** (Редактировать).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. В разделе **Authentication Configuration** (Конфигурация проверки подлинности) в качестве **службы проверки подлинности** выберите имя параметра единого входа SAML, установленного при конфигурации единого входа в Salesforce Sandbox, после чего щелкните **Сохранить**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon@yourcompanydomain.extension`. Например, BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Salesforce Sandbox.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Salesforce Sandbox**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Salesforce Sandbox**.

    ![Ссылка на Salesforce Sandbox в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-salesforce-sandbox-test-user"></a>Создание тестового пользователя Salesforce Sandbox

В этом разделе вы создадите в Salesforce Sandbox пользователя с именем Britta Simon. Salesforce Sandbox поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в Salesforce Sandbox еще не существует, он создается при попытке доступа к Salesforce Sandbox. Salesforce также поддерживает автоматическую подготовку пользователей. Дополнительные сведения о настройке автоматической подготовки пользователей можно найти [здесь](salesforce-sandbox-provisioning-tutorial.md).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Salesforce Sandbox" на Панели доступа, вы автоматически войдете в приложение Salesforce Sandbox, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Руководство по настройке Google Apps для автоматической подготовки пользователей](salesforce-sandbox-provisioning-tutorial.md)
