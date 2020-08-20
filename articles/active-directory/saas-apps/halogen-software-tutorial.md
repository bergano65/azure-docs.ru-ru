---
title: Руководство по интеграции Azure Active Directory с Saba TalentSpace | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Saba TalentSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.openlocfilehash: 1743908652a70667a368dced18b2e808ce590a1b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549999"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Руководство по интеграции единого входа Azure Active Directory с Saba TalentSpace

В этом руководстве описано, как интегрировать Saba TalentSpace с Azure Active Directory (Azure AD). Интеграция Saba TalentSpace с Azure AD обеспечивает следующие возможности:

* контролировать доступа к Saba TalentSpace с помощью Azure AD;
* включение автоматического входа пользователей в Saba TalentSpace с использованием учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Saba TalentSpace с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Saba TalentSpace поддерживает единый вход, инициированный **поставщиком услуг**.
* После настройки Saba TalentSpace можно применять элемент управления сеансами, который защищает от хищения и несанкционированного доступа к конфиденциальным данным вашей организации в режиме реального времени. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-saba-talentspace-from-the-gallery"></a>Добавление Saba TalentSpace из коллекции

Чтобы настроить интеграцию Saba TalentSpace с Azure AD, необходимо добавить Saba TalentSpace из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Saba TalentSpace**.
1. Выберите **Saba TalentSpace** в области результатов, а затем добавьте приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Настройка и проверка единого входа в Azure AD для Saba TalentSpace

Настройте и проверьте единый вход Azure AD в Saba TalentSpace с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем AAD и соответствующим пользователем в Saba TalentSpace.

Чтобы настроить и проверить единый вход Azure AD в Saba TalentSpace выполните действия описанные в стандартных блоках ниже.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Saba TalentSpace](#configure-saba-talentspace-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя Saba TalentSpace](#create-saba-talentspace-test-user)** требуется для создания в Saba TalentSpace пользователя B.Simon, связанного с представлением этого пользователя в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Saba TalentSpace** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://global.hgncloud.com/[companyname]/saml/login`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://global.hgncloud.com/[companyname]/saml/metadata`.

    c. В текстовое поле **URL-адреса ответа или URL-адрес службы обработчика утверждений** введите URL-адрес в следующем формате: `https://global.hgncloud.com/[companyname]/saml/SSO`.

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Saba TalentSpace](https://support.saba.com/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Скопируйте требуемый URL-адрес из раздела **Настройка Saba TalentSpace**.

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

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к Saba TalentSpace.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. Из списка приложений выберите **Saba TalentSpace**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-saba-talentspace-sso"></a>Настройка единого входа в Saba TalentSpace

1. В отдельном окне браузера войдите в приложение **Saba TalentSpace** под учетной записью администратора.

2. Откройте вкладку **Параметры** .
  
    ![Что такое Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. На панели навигации слева щелкните **Настройка SAML**.
  
    ![Что такое Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. На странице **Настройка SAML** сделайте следующее:

    ![Что такое Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    а. В качестве значения поля **Уникальный идентификатор** выберите **NameID**.

    b. В качестве значения поля **Unique Identifier Maps To** (Уникальный идентификатор сопоставляется с) выберите **Имя пользователя**.
  
    c. Для отправки скачанного файла метаданных нажмите кнопку **Обзор**, чтобы выбрать файл, а затем щелкните **Отправить файл**.

    d. Чтобы проверить конфигурацию, нажмите кнопку **Запустить тест**.

    > [!NOTE]
    > Подождите, пока не появится сообщение "*Проверка SAML завершена. Закройте это окно*". Закройте окно браузера. Флажок **Включить SAML** установлен, только если проверка завершена.

    д) Выберите **Включить SAML**.

    е) Щелкните **Сохранить изменения**.

### <a name="create-saba-talentspace-test-user"></a>Создание тестового пользователя в Saba TalentSpace

Цель этого раздела — создать пользователя с именем Britta Simon в Saba TalentSpace.

**Чтобы создать в приложении Saba TalentSpace пользователя с именем Britta Simon, выполните следующие действия:**

1. Войдите в приложение **Saba TalentSpace** в качестве администратора.

2. Щелкните вкладку **Центр пользователей**, затем щелкните **Создать пользователя**.

    ![Что такое Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. На странице диалогового окна **Новый пользователь** выполните следующие действия.

    ![Что такое Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    а. В текстовое поле **Имя** введите имя пользователя, например **B**.

    b. В текстовом поле **Фамилия** введите фамилию, например **Simon**.

    c. В текстовом поле **Имя пользователя** введите **B.Simon**, имя пользователя на портале Azure.

    d. В текстовом поле **Пароль** введите пароль для пользователя B.Simon.

    д) Выберите команду **Сохранить**.

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Saba TalentSpace на панели доступа, вы автоматически войдете в приложение Saba TalentSpace, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Пробное использование Saba TalentSpace с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)