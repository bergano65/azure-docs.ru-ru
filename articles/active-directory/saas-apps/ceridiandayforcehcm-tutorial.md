---
title: Руководство. Интеграция Azure Active Directory с Ceridian Dayforce HCM | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Ceridian Dayforce HCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3442497561af4fb9a5cfa6bdcb0dfe31fa0b8e05
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881028"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Руководство по Интеграция Azure Active Directory с Ceridian Dayforce HCM

В этом учебнике описано, как интегрировать Ceridian Dayforce HCM с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Ceridian Dayforce HCM обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Ceridian Dayforce HCM.
* Вы можете включить автоматический вход пользователей в Ceridian Dayforce HCM (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Ceridian Dayforce HCM, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка Ceridian Dayforce HCM с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Ceridian Dayforce HCM поддерживает единый вход инициированного **пакета обновления**.

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Добавление Ceridian Dayforce HCM из коллекции

Чтобы настроить интеграцию Ceridian Dayforce HCM с Azure AD, необходимо добавить Ceridian Dayforce HCM из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Ceridian Dayforce HCM из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Ceridian Dayforce HCM**, на панели результатов выберите **Ceridian Dayforce HCM**, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Ceridian Dayforce HCM в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD с помощью Ceridian Dayforce HCM с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Ceridian Dayforce HCM.

Чтобы настроить и проверить единый вход Azure AD в Ceridian Dayforce HCM, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Ceridian Dayforce HCM](#configure-ceridian-dayforce-hcm-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Ceridian Dayforce HCM](#create-ceridian-dayforce-hcm-test-user)** требуется для создания пользователя Britta Simon в Ceridian Dayforce HCM, связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Ceridian Dayforce HCM, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции приложения **Ceridian Dayforce HCM** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Ceridian Dayforce HCM](common/sp-identifier-reply.png)

    a. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Ceridian Dayforce HCM.

    | Среда | URL-адрес |
    | :-- | :-- |
    | Рабочая среда | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Тестирование | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    | Среда | URL-адрес |
    | :-- | :-- |
    | Рабочая среда | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Тестирование | `https://fs-test.dayforcehcm.com/sp` |

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес, используемый Azure AD для размещения ответа.

    | Среда | URL-адрес |
    | :-- | :-- |
    | Рабочая среда | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Тестирование | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Эти значения приведены для примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки Ceridian Dayforce HCM](https://www.ceridian.com/support). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. Приложение Ceridian Dayforce HCM ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![изображение](common/edit-attribute.png)

6. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.
    
    | ИМЯ | Исходный атрибут|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](common/new-save-attribute.png)

    ![изображение](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    4.3. В качестве источника выберите **Атрибут**.

    д. Из списка **Атрибут источника** выберите атрибут пользователя, который хотите использовать в своей реализации. Например, если в качестве уникального идентификатора пользователя вы хотите использовать EmployeeID и сохранили значение атрибута в ExtensionAttribute2, выберите user.extensionattribute2.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

8. Требуемый URL-адрес можно скопировать из раздела **Настройка Ceridian Dayforce HCM**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-ceridian-dayforce-hcm-single-sign-on"></a>Настройка единого входа Ceridian Dayforce HCM

Чтобы настроить единый вход на стороне **Ceridian Dayforce HCM**, нужно отправить скачанный файл **XML метаданных** и соответствующие URL-адреса, скопированные на портале Azure, в [группу поддержки Ceridian Dayforce HCM](https://www.ceridian.com/support). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Ceridian Dayforce HCM, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Ceridian Dayforce HCM**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Ceridian Dayforce HCM**.

    ![Ссылка "Ceridian Dayforce HCM" в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Создание тестового пользователя Ceridian Dayforce HCM

В этом разделе описано, как создать пользователя Britta Simon в приложении Ceridian Dayforce HCM. Обратитесь в  [группу поддержки Ceridian Dayforce HCM](https://www.ceridian.com/support), чтобы добавить пользователей в платформу Ceridian Dayforce HCM. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Ceridian Dayforce HCM" на панели доступа, вы автоматически войдете в приложение Ceridian Dayforce HCM, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

