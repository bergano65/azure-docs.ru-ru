---
title: Руководство по интеграции Azure Active Directory с Ivanti Service Manager (ISM) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Ivanti Service Manager (ISM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 14297c74-0d57-4146-97fa-7a055fb73057
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: jeedes
ms.openlocfilehash: 3b394ff8e3638a9663e756fd6db866b0c3e5d2ef
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447665"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>Руководство по интеграции Azure Active Directory с Ivanti Service Manager (ISM)

В этом руководстве описано, как интегрировать Ivanti Service Manager (ISM) с Azure Active Directory (AAD).

Интеграция AAD с Ivanti Service Manager (ISM) обеспечивает следующие преимущества:

- С помощью AAD вы можете контролировать доступ к Ivanti Service Manager (ISM).
- Вы можете включить автоматический вход пользователей в Ivanti Service Manager (ISM) (единый вход) с использованием учетной записи AAD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию AAD с приложением Ivanti Service Manager (ISM), вам потребуется следующее:

- подписка Azure AD;
- подписка Ivanti Service Manager (ISM) с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Ivanti Service Manager (ISM) из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-ivanti-service-manager-ism-from-the-gallery"></a>Добавление Ivanti Service Manager (ISM) из коллекции

Чтобы настроить интеграцию Ivanti Service Manager (ISM) в AAD, необходимо добавить Ivanti Service Manager (ISM) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Ivanti Service Manager (ISM) из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Ivanti Service Manager (ISM)** и выберите **Ivanti Service Manager (ISM)** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Ivanti Service Manager (ISM) в списке результатов](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа AAD в Ivanti Service Manager (ISM) с использованием тестового пользователя Britta Simon.

Чтобы активировать единый вход, AAD необходима информация о том, какой пользователь в Ivanti Service Manager (ISM) соответствует пользователю в AAD. Иными словами, необходимо установить связь между пользователем AAD и соответствующим пользователем в Ivanti Service Manager (ISM).

Чтобы настроить и проверить единый вход AAD в Ivanti Service Manager (ISM), потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Ivanti Service Manager (ISM)](#creating-an-ivanti-service-manager-ism-test-user)** требуется для того, чтобы в Ivanti Service Manager (ISM) существовал пользователь Britta Simon, связанный с одноименным пользователем в AAD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход AAD на портале Azure и настроить его в приложении Ivanti Service Manager (ISM).

**Чтобы настроить единый вход AAD в Ivanti Service Manager (ISM), выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Ivanti Service Manager (ISM)** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Настройка единого входа](common/tutorial-general-301.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](common/editconfigure.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Ivanti Service Manager (ISM)](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<customer>.saasit.com/` |
    | `https://<customer>.saasiteu.com/` |
    | `https://<customer>.saasitau.com/` |

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующее действие:

    ![Сведения о домене и URL-адресах единого входа для приложения Ivanti Service Manager (ISM)](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<customer>.saasit.com/`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact).

6. На странице **Сертификат подписи SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать **Сертификат (необработанный)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-certificate.png) 

7. Требуемый URL-адрес можно скопировать из раздела **Настройка Ivanti Service Manager (ISM)**.

    a. URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода

    ![Настройка Ivanti Service Manager (ISM)](common/configuresection.png)

8. Чтобы настроить единый вход на стороне **Ivanti Service Manager (ISM)**, отправьте в [службу поддержки клиентов Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact) скачанный **сертификат (необработанный)** и скопированные **URL-адрес входа**, **идентификатор AAD** и **URL-адрес выхода**. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Создание пользователя Azure AD][100]

2. В верхней части экрана выберите **Новый пользователь**.

    ![Создание тестового пользователя Azure AD](common/create-aaduser-01.png) 

3. В разделе свойств пользователя сделайте следующее.

    ![Создание тестового пользователя Azure AD](common/create-aaduser-02.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="creating-an-ivanti-service-manager-ism-test-user"></a>Создание тестового пользователя Ivanti Service Manager (ISM)

Цель этого раздела — создать пользователя с именем Britta Simon в приложении Ivanti Service Manager (ISM). Приложение Ivanti Service Manager (ISM) поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к приложению Ivanti Service Manager (ISM) создается учетная запись пользователя (если она еще не создана).
>[!Note]
>Если вам нужно создать пользователя вручную, обратитесь в  [службу поддержки Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Ivanti Service Manager (ISM).

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **Ivanti Service Manager (ISM)**.

    ![Настройка единого входа](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Ivanti Service Manager (ISM) на панели доступа, вы автоматически войдете в приложение Ivanti Service Manager (ISM).
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
