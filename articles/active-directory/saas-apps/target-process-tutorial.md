---
title: Руководство. Интеграция Azure Active Directory с TargetProcess | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/7/2018
ms.author: jeedes
ms.openlocfilehash: 7fb3c1c3e8dbff54802915a69033001cc9378670
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262169"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Руководство. Интеграция Azure Active Directory с TargetProcess

В этом учебнике описано, как интегрировать приложение TargetProcess с Azure Active Directory (Azure AD).
Интеграция TargetProcess с Azure AD дает приведенные далее преимущества:

* С помощью Azure AD вы можете контролировать доступ к TargetProcess.
* Можно включить автоматический вход пользователей в TargetProcess (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением TargetProcess, вам потребуется следующее:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка TargetProcess с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* TargetProcess поддерживает единый вход, инициированный **поставщиком услуг**.
* TargetProcess поддерживает **JIT**-подготовку пользователей.

## <a name="adding-targetprocess-from-the-gallery"></a>Добавление приложения TargetProcess из коллекции

Чтобы настроить интеграцию приложения TargetProcess с Azure AD, необходимо добавить TargetProcess из коллекции в список управляемых приложений SaaS.

**Чтобы добавить TargetProcess из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **TargetProcess**, выберите **TargetProcess** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![TargetProcess в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в TargetProcess с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TargetProcess.

Чтобы настроить и проверить единый вход Azure AD в TargetProcess, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя TargetProcess](#create-targetprocess-test-user)** требуется для того, чтобы в TargetProcess существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в TargetProcess, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **TargetProcess** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения TargetProcess](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.tpondemand.com/`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.tpondemand.com/`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором.  Чтобы получить эти значения, обратитесь к [группе поддержки клиентов TargetProcess](mailto:support@targetprocess.com).

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Скопируйте соответствующий вашим требованиям URL-адрес из раздела **Настройка TargetProcess**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    a. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода

7. Для автоматизации настройки в **TargetProcess** необходимо установить **Расширение браузера "Безопасный вход в мои приложения"**, щелкнув **Установить расширение**.

    ![изображение](./media/target-process-tutorial/install_extension.png)

8. После добавления расширения в браузере щелкните **Установка TargetProcess**, чтобы перейти к приложению TargetProcess. После этого укажите учетные данные администратора для входа в TargetProcess. Расширение браузера автоматически настроит приложение и автоматизирует шаги 9–13.

    **Если вы хотите настроить приложение вручную, выполните следующие действия:**

9. Войдите в приложение TargetProcess с правами администратора.

10. В верхнем меню щелкните **Настройка**.

    ![Настройка](./media/target-process-tutorial/tutorial_target_process_05.png)

11. Щелкните **Параметры**.

    ![Параметры](./media/target-process-tutorial/tutorial_target_process_06.png)

12. Щелкните **Single Sign-on**(Единый вход).

    ![Щелчок "Single Sign-on" (Единый вход)](./media/target-process-tutorial/tutorial_target_process_07.png)

13. В диалоговом окне "Параметры единого входа" выполните следующие действия.

    ![Настройка единого входа](./media/target-process-tutorial/tutorial_target_process_08.png)

    a. Щелкните **Enable Single Sign-on**(Включить единый вход).

    b. В текстовое поле **Sign-on URL** (URL-адрес входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    c. Откройте скачанный сертификат в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте в текстовое поле **Certificate** (Сертификат).

    d. Установите флажок **Enable JIT Provisioning**(Включить JIT-подготовку).

    д. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в статье: [Руководство. Настройка единого входа на основе SAML для приложения в Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее.

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к TargetProcess.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **TargetProcess**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **TargetProcess**.

    ![Ссылка на TargetProcess в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-targetprocess-test-user"></a>Создание тестового пользователя TargetProcess

Цель этого раздела — создать в приложении TargetProcess пользователя с именем Britta Simon. Приложение TargetProcess поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к приложению TargetProcess создается учетная запись пользователя (если она еще не создана).

> [!Note]
> Чтобы создать пользователя вручную, обратитесь к  [группе поддержки TargetProcess](mailto:support@targetprocess.com).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку TargetProcess на панели доступа, вы автоматически войдете в приложение TargetProcess, для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)