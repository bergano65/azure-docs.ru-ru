---
title: Руководство по интеграции Azure Active Directory с RingCentral | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 35033e52fb54177428f8869ebcc462bd9465ad4c
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48872502"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Руководство по интеграции Azure Active Directory с RingCentral

В этом руководстве описано, как интегрировать RingCentral с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением RingCentral обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к RingCentral.
- Вы можете включить автоматический вход пользователей в RingCentral (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением RingCentral, вам потребуется:

- подписка Azure AD;
- подписка RingCentral с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление RingCentral из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-ringcentral-from-the-gallery"></a>Добавление RingCentral из коллекции
Чтобы настроить интеграцию RingCentral с Azure AD, необходимо добавить RingCentral из коллекции в список управляемых приложений SaaS.

**Чтобы добавить RingCentral из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![изображение](./media/ringcentral-tutorial/selectazuread.png)

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![изображение](./media/ringcentral-tutorial/a_select_app.png)
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![изображение](./media/ringcentral-tutorial/a_new_app.png)

4. В поле поиска введите **RingCentral**, выберите **RingCentral** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![изображение](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в RingCentral с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в RingCentral соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в RingCentral.

Чтобы настроить и проверить единый вход Azure AD в RingCentral, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя RingCentral](#create-a-ringcentral-test-user)** требуется для того, чтобы в RingCentral существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении RingCentral.

**Чтобы настроить единый вход Azure AD в RingCentral, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **RingCentral** щелкните **Единый вход**.

    ![изображение](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. В верхней части экрана щелкните **Изменить режим единого входа**, чтобы выбрать режим **SAML**.

      ![изображение](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML**, чтобы включить единый вход.

    ![изображение](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![изображение](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. Если у вас есть **файл метаданных поставщика служб**, выполните следующие действия в разделе **Базовая конфигурация SAML**:

    a. Щелкните **Отправить файл метаданных**.

    ![изображение](./media/ringcentral-tutorial/b9_saml.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![изображение](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. После успешной передачи файла метаданных поля **Идентификатор** и **URL-адрес ответа** в разделе **Базовая конфигурация SAML** автоматически заполняются значениями, как показано ниже:

    ![изображение](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. В текстовом поле **URL-адрес для входа** введите следующий URL-адрес:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > Вы получите **файл метаданных поставщика служб** на странице настройки единого входа RingCentral, что описано далее в этом руководстве.

6. Если у вас нет **файла метаданных поставщика службы**, выполните следующие действия:

    a. В текстовом поле **URL-адрес для входа** введите следующий URL-адрес:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. В текстовом поле **Идентификатор** введите URL-адрес:
    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес:
    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![изображение](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить нужный вам файл сертификата, и сохраните его на компьютере.

    ![изображение](./media/ringcentral-tutorial/certificatebase64.png)
    
8. В другом окне браузера войдите в приложение RingCentral с правами администратора безопасности.

9. Щелкните **Tools** (Средства) вверху.

    ![изображение](./media/ringcentral-tutorial/ringcentral1.png)

10. Перейдите к параметру **Single Sign-on** (Единый вход).

    ![изображение](./media/ringcentral-tutorial/ringcentral2.png)

11. На странице **Single Sign-on** (Единый вход) в разделе **SSO Configuration** (Настройка единого входа) в **шаге 1** щелкните **Edit** (Изменить) и выполните следующие шаги:

    ![изображение](./media/ringcentral-tutorial/ringcentral3.png)

12. На странице **Set up Single Sign-on** (Настройка единого входа) выполните следующие действия:

    ![изображение](./media/ringcentral-tutorial/ringcentral4.png)

    a. Затем щелкните **Browse** (Обзор), чтобы передать файл метаданных, скачанный с портала Azure.

    b. После отправки метаданных значения автоматически заполняются в разделе **SSO General Information** (Общие сведения о едином входе).

    c. В разделе **Attribute Mapping** (Сопоставление атрибутов) укажите для параметра **Map Email Attribute to** (Сопоставить атрибут почты с) значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    d. Выберите команду **Сохранить**.

    д. В **шаге 2** нажмите кнопку **Download** (Загрузить), чтобы скачать **файл метаданных поставщика служб** и отправить его в раздел **Базовая конфигурация SAML** для автоматического заполнения значений **идентификатора** и **URL-адреса ответа** на портале Azure.

    ![изображение](./media/ringcentral-tutorial/ringcentral6.png) 

    Е. На этой же странице перейдите к разделу **Enable SSO** (Включение единого входа) и выполните следующие действия:

    ![изображение](./media/ringcentral-tutorial/ringcentral5.png)

    a. Выберите **Enable SSO Service** (Включить службу единого входа).
    
    b. Выберите **Allow users to log in with SSO or RingCentral credential** (Разрешить пользователям входить в систему с помощью единого входа или учетных данных RingCentral).

    c. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![изображение](./media/ringcentral-tutorial/d_users_and_groups.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![изображение](./media/ringcentral-tutorial/d_adduser.png)

3. В разделе свойств пользователя сделайте следующее.

    ![изображение](./media/ringcentral-tutorial/d_userproperties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-ringcentral-test-user"></a>Создание тестового пользователя RingCentral

В этом разделе описано, как создать пользователя Britta Simon в приложении RingCentral. Обратитесь к [группе поддержки клиентов RingCentral](https://success.ringcentral.com/RCContactSupp), чтобы добавить пользователей на платформу RingCentral. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к RingCentral.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![изображение](./media/ringcentral-tutorial/d_all_applications.png)

2. В списке приложений выберите **RingCentral**.

    ![изображение](./media/ringcentral-tutorial/d_all_proapplications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![изображение](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. Нажмите кнопку **Добавить**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![изображение](./media/ringcentral-tutorial/d_assign_user.png)

4. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

5. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент RingCentral на панели доступа, вы автоматически войдете в приложение RingCentral.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png

