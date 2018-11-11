---
title: Руководство по интеграции Azure Active Directory с 8x8 Virtual Office | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и 8x8 Virtual Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 53db637bf7ad47896747b491fcbe31123fdb104e
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741816"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Руководство. Интеграция Azure Active Directory с 8x8 Virtual Office

В этом руководстве описано, как интегрировать 8x8 Virtual Office с Azure Active Directory (Azure AD).

Интеграция 8x8 Virtual Office с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к 8x8 Virtual Office.
- Вы можете включить автоматический вход пользователей в 8x8 Virtual Office (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с 8x8 Virtual Office, вам потребуется:

- подписка Azure AD;
- подписка 8x8 Virtual Office с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление 8x8 Virtual Office из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Добавление 8x8 Virtual Office из коллекции.

Чтобы настроить интеграцию приложения 8x8 Virtual Office с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить 8x8 Virtual Office из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **8x8 Virtual Office**, выберите **8x8 Virtual Office** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![8x8 Virtual Office в списке результатов](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение 8x8 Virtual Office с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в 8x8 Virtual Office соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в 8x8 Virtual Office.

Чтобы настроить и проверить единый вход Azure AD в 8x8 Virtual Office, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя 8x8 Virtual Office](#creating-a-8x8-virtual-office-test-user)** нужно для того, чтобы в 8x8 Virtual Office также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении 8x8 Virtual Office.

**Чтобы настроить единый вход Azure AD в 8x8 Virtual Office, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **8x8 Virtual Office** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Настройка единого входа](common/tutorial_general_301.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](common/editconfigure.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения 8x8 Virtual Office](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `https://sso.8x8.com/saml2`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес: `https://sso.8x8.com/saml2`

5. На странице **Сертификат подписи SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать **Сертификат (необработанный)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

6. Требуемый URL-адрес можно скопировать из раздела **Настройка 8x8 Virtual Office**.

    a. URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода

    ![Конфигурация 8x8 Virtual Office](common/configuresection.png)

7. Войдите в клиент 8x8 Virtual Office от имени администратора.

8. На панели приложения выберите **Virtual Office Account Mgr** (Диспетчер учетных записей Virtual Office).

    ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Выберите в качестве учетной записи, которой нужно управлять, **Business** (Рабочая) и нажмите кнопку **Sign In** (Вход).

    ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. В списке меню щелкните вкладку **ACCOUNTS** (Учетные записи).

    ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. В списке Accounts (Учетные записи) щелкните **Single Sign On** (Единый вход).
  
    ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. В разделе "Authentication methods" (Методы аутентификации) установите флажок **Signle Sign On** (Единый вход), а затем щелкните **SAML**.

    ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. В разделе **SAML Single Sign on** (Единый вход SAML) выполните следующие действия.

    ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. В текстовое поле **Sign In URL** (URL-адрес входа) вставьте значение **URL-адрес входа**, скопированное на портале Azure.

    b. В текстовое поле **Sign Out URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    c. В текстовое поле **Issuer URL** (URL-адрес издателя) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    d. Нажмите кнопку **Browse** (Обзор), чтобы передать сертификат, скачанный с портала Azure.

    д. Нажмите кнопку **Сохранить** .

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Создание пользователя Azure AD][100]

2. В верхней части экрана выберите **Новый пользователь**.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_01.png) 

3. В разделе свойств пользователя сделайте следующее.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_02.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
  
### <a name="creating-a-8x8-virtual-office-test-user"></a>Создание тестового пользователя 8x8 Virtual Office

Цель этого раздела — создать пользователя с именем Britta Simon в приложении 8x8 Virtual Office. Приложение 8x8 Virtual Office поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению 8x8 Virtual Office (если он еще не создан).

> [!NOTE]
> Чтобы создать пользователя вручную, необходимо обратиться к [группе поддержки 8x8 Virtual Office](https://www.8x8.com/about-us/contact-us).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к 8x8 Virtual Office.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **8x8 Virtual Office**.

    ![Настройка единого входа](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент 8x8 Virtual Office на панели доступа, вы автоматически войдете в приложение 8x8 Virtual Office.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
