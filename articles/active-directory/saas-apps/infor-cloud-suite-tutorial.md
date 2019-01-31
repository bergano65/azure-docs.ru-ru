---
title: Руководство. Интеграция Azure Active Directory с Infor CloudSuite | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и Infor CloudSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a2f4f843-00d2-4522-a29d-6496cc5a781a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 09d0c92703c13baba2555245f7c71a3d48b4ee9d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174981"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-cloudsuite"></a>Руководство. Интеграция Azure Active Directory с Infor CloudSuite

В этом руководстве описано, как интегрировать Infor CloudSuite с Azure Active Directory (Azure AD).

Интеграция Infor CloudSuite с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Infor CloudSuite.
- Вы можете включить автоматический вход пользователей в Infor CloudSuite (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Infor CloudSuite, вам потребуется:

- подписка Azure AD;
- подписка Infor CloudSuite с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. добавление Infor CloudSuite из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-infor-cloudsuite-from-the-gallery"></a>Добавление Infor CloudSuite из коллекции

Чтобы настроить интеграцию Infor CloudSuite в Azure AD, необходимо добавить Infor CloudSuite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Infor CloudSuite из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Infor CloudSuite**, выберите **Infor CloudSuite** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Infor CloudSuite в списке результатов](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Infor CloudSuite с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Infor CloudSuite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Infor CloudSuite.

Чтобы настроить и проверить единый вход Azure AD в Infor CloudSuite, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Infor CloudSuite](#creating-infor-cloudsuite-test-user)** требуется для того, чтобы в Infor CloudSuite существовал пользователь Britta Simon, связанный с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Infor CloudSuite.

**Чтобы настроить единый вход Azure AD в Infor CloudSuite, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Infor CloudSuite** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Настройка единого входа](common/tutorial-general-301.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](common/editconfigure.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Infor CloudSuite](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-url1.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес:
    
    | | |
    |-|-|
    | ` http://mingle-sso.inforcloudsuite.com`|
    | `http://mingle-sso.se1.inforcloudsuite.com`|
    | `http://mingle-sso.eu1.inforcloudsuite.com`|
    | `http://mingle-sso.se2.inforcloudsuite.com`|
    | |

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес:

    | | |
    |-|-|
    | ` https://mingle-sso.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.se1.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.se2.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.eu1.inforcloudsuite.com:443/sp/ACS.saml2`|
    | |

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующее действие:

    ![Сведения о домене и URL-адресах единого входа приложения Infor CloudSuite](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-url2.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    
    | | |
    |-|-|
    | `https://mingle-portal.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.eu1.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.se1.inforcloudsuite.com/Tenant-Name/ `|
    | `https://mingle-portal.se2.inforcloudsuite.com/Tenant-Name/`| 

    > [!NOTE]
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Для получения данного значения обратитесь к [группе поддержки клиентов Infor CloudSuite](mailto:support@infor.com).

6. На странице **сертификата подписи SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать **XML метаданных федерации**, а затем сохраните метаданные на компьютере.

    ![Ссылка для скачивания сертификата](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-certificate.png)

7. Чтобы настроить единый вход на стороне **Infor CloudSuite**, нужно отправить скачанный **XML метаданных федерации** [группе поддержки клиентов Infor CloudSuite](mailto:support@infor.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

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

### <a name="creating-infor-cloudsuite-test-user"></a>Создание тестового пользователя Infor CloudSuite

Цель этого раздела — создать пользователя с именем Britta Simon в Infor CloudSuite. В Infor CloudSuite поддерживается JIT-подготовка, включаемая администратором клиента. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Infor CloudSuite (если он еще не создан).

> [!Note]
> Чтобы создать пользователя вручную, обратитесь к  [группе поддержки Infor CloudSuite](mailto:support@infor.com).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Infor CloudSuite.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **Infor CloudSuite**.

    ![Настройка единого входа](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Infor CloudSuite на панели доступа, вы автоматически войдете в приложение Infor CloudSuite.
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
