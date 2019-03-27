---
title: Руководство. Интеграция Azure Active Directory с Palo Alto Networks (Captive Portal) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Palo Alto Networks (Captive Portal).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a62aa573d49ccdd237e692b63a6fda0dd83d52a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57888303"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Руководство. Интеграция Azure Active Directory с Palo Alto Networks (Captive Portal)

В этом руководстве описано, как интегрировать Palo Alto Networks (Captive Portal) с Azure Active Directory (Azure AD).

Интеграция Azure AD с Palo Alto Networks (Captive Portal) обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Palo Alto Networks (Captive Portal).
* Вы можете включить автоматический вход пользователей в Palo Alto Networks (Captive Portal) (единый вход) с использованием их учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Предварительные требования

Чтобы интегрировать Azure AD с Palo Alto Networks (Captive Portal), вам потребуется:

* Подписка Azure Active Directory. Если у вас нет Azure AD, вы можете получить [пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).
* Подписка Palo Alto Networks (Captive Portal) с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

Palo Alto Networks (Captive Portal) поддерживает следующие сценарии:

* **единый вход, инициированный поставщиком удостоверений**;
* **JIT-подготовку пользователей**.

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Добавление Palo Alto Networks (Captive Portal) из коллекции

Для начала работы добавьте Palo Alto Networks (Captive Portal) из коллекции в свой список управляемых приложений SaaS:

1. На [портале Azure](https://portal.azure.com) в меню слева щелкните **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Щелкните **Корпоративные приложения** > **Все приложения**.

    ![Элемент "Корпоративные приложения" в меню](common/enterprise-applications.png)

3. Выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Palo Alto Networks (Captive Portal)**. В результатах поиска выберите **Palo Alto Networks (Captive Portal)**, а затем выберите **Добавить**.

     ![Palo Alto Networks (Captive Portal) в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Настройте и проверьте единый вход Azure AD в Palo Alto Networks (Captive Portal) с использованием тестового пользователя *Britta Simon*. Для правильной работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем Palo Alto Networks (Captive Portal). 

Чтобы настроить и проверить единый вход Azure AD в Palo Alto Networks (Captive Portal), вам потребуется выполнить приведенные ниже задачи.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)**: позволяет пользователю использовать эту функцию.
2. **[Настройка единого входа для Palo Alto Networks (Captive Portal)](#configure-palo-alto-networks-captive-portal-single-sign-on)**: настройте параметры единого входа в приложении.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)**: проверьте работу единого входа Azure AD от имени пользователя *Britta Simon*.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)**: настройте пользователя Britta Simon для использования единого входа Azure AD.
5. **Создание тестового пользователя в Palo Alto Networks (Captive Portal)**: создайте пользователя *Britta Simon* в Palo Alto Networks (Captive Portal), который будет связан с пользователем Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)**: убедитесь, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

Сначала включите единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Palo Alto Networks (Captive Portal)** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В области **Выбрать метод единого входа** выберите **SAML**.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. В области **Настройка единого входа с помощью SAML** щелкните значок карандаша **Изменить**.

    ![Значок карандаша "Изменить"](common/edit-urls.png)

4. В области **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Область "Базовая конфигурация SAML" в Palo Alto Networks (Captive Portal)](common/idp-intiated.png)

   1. В поле **Идентификатор** введите URL-адрес по шаблону `https://<customer_firewall_host_name>/SAML20/SP`.

   2. В поле **URL-адрес ответа** введите URL-адрес по шаблону `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > На этом шаге необходимо заменить значения заполнителей фактическим идентификатором и URL-адресами ответа. Чтобы получить эти значения, обратитесь в [группу поддержки клиентов Palo Alto Networks (Captive Portal)](https://support.paloaltonetworks.com/support).

5. В разделе **Сертификат подписи SAML** рядом с **XML метаданных федерации** выберите **Загрузить**. Сохраните скачанный файл на компьютере.

    ![Ссылка для скачивания XML-файла метаданных федерации](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Настройка единого входа для Palo Alto Networks (Captive Portal)

Настройте единый вход для Palo Alto Networks (Captive Portal), выполнив приведенные ниже действия.

1. В другом окне браузера войдите на веб-сайт Palo Alto Networks в качестве администратора.

2. Выберите вкладку **Device** (Устройство).

    ![Вкладка "Устройство" на веб-сайте Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. В меню выберите **SAML Identity Provider** (Поставщик удостоверений SAML), а затем выберите **Импортировать**.

    ![Кнопка "Импортировать"](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. В диалоговом окне **SAML Identity Provider Server Profile Import** (Импорт профиля сервера поставщика удостоверений SAML) выполните указанные ниже действия.

    ![Настройка единого входа Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. В поле **Имя профиля** введите имя, например **AzureAD-CaptivePortal**.
    
    2. Рядом с параметром **Identity Provider Metadata** (Метаданные поставщика удостоверений) выберите **Обзор**. Выберите файл Metadata.xml, скачанный с портала Azure.
    
    3. Нажмите кнопку **ОК**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Затем создайте на портале Azure тестового пользователя с именем *Britta Simon*.

1. На портале Azure выберите **Azure Active Directory** > **Пользователи** > **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. Выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В области **Пользователь** выполните приведенные ниже действия.

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    1. В поле **Имя** введите **BrittaSimon**.
  
    2. В поле **Имя пользователя** введите **BrittaSimon@\@\<домен_вашей_компании\>**. Например **BrittaSimon\@contoso.com**.

    3. В поле **Пароль** введите пароль. Мы рекомендуем сохранить введенный пароль. Вы можете выбрать флажок **Показать пароль** для отображения пароля.

    4. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Затем предоставьте доступ к Palo Alto Networks (Captive Portal), чтобы пользователь Britta Simon мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения** > **Все приложения**.

    ![Область "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите **Palo Alto Networks — Captive Portal**, а затем выберите приложение.

    ![Ссылка на Palo Alto Networks (Captive Portal) в списке приложений](common/all-applications.png)

3. В меню выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Выберите **Добавить пользователя**. Затем в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В области **Пользователи и группы** в списке **Пользователи** выберите **Britta Simon**. Щелкните **Выбрать**.

6. Чтобы добавить значение роли в утверждение SAML, выберите соответствующую роль для пользователя в области **Выбор ролей**. Щелкните **Выбрать**.

7. В области **Добавление назначения** выберите **Назначить**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Создание тестового пользователя в Palo Alto Networks (Captive Portal)

Затем создайте пользователя с именем *Britta Simon* в Palo Alto Networks (Captive Portal). Palo Alto Networks (Captive Portal) поддерживает JIT-подготовку пользователей. Эта функция включена по умолчанию. В этом разделе не нужно выполнять никакие задачи. Если пользователь еще не существует в Palo Alto Networks (Captive Portal), он создается после проверки подлинности.

> [!NOTE]
> Чтобы создать пользователя вручную, обратитесь в [группу поддержки клиентов Palo Alto Networks (Captive Portal)](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Проверка единого входа 

Palo Alto Networks (Captive Portal) устанавливается за брандмауэром на виртуальной машине Windows. Чтобы протестировать единый вход в Palo Alto Networks (Captive Portal), войдите на виртуальную машину Windows с помощью протокола удаленного рабочего стола (RDP). В сеансе RDP откройте браузер и перейдите на любой веб-сайт. Откроется URL-адрес единого входа, и вы получите запрос на проверку подлинности. После завершения проверки подлинности вы можете получить доступ к веб-сайтам.

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения см. в статьях ниже:

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Условный доступ в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

