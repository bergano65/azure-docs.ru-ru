---
title: Руководство по Интеграция Azure Active Directory с Alibaba Cloud Service (единый вход на основе ролей) | Документация Майкрософт
description: Из этой статьи вы узнаете, как настроить единый вход Azure Active Directory в Alibaba Cloud Service (единый вход на основе ролей).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0ab129d7b215f0f10841b13fc2835af59227198
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565247"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Руководство по Интеграция Azure Active Directory с Alibaba Cloud Service (единый вход на основе ролей)

В этом руководстве описано, как интегрировать Alibaba Cloud Service (единый вход на основе ролей) с Azure Active Directory (Azure AD).
Интеграция Azure AD с Alibaba Cloud Service (единый вход на основе ролей) обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Alibaba Cloud Service (единый вход на основе ролей).
* Вы можете включить автоматический вход пользователей в Alibaba Cloud Service (единый вход на основе ролей) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Alibaba Cloud Service (единый вход на основе ролей), вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка на Alibaba Cloud Service (единый вход на основе ролей) с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Alibaba Cloud Service (единый вход на основе ролей) поддерживает единый вход, инициированный **поставщиком удостоверений**.

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Добавление Alibaba Cloud Service (единый вход на основе ролей) из коллекции

Чтобы настроить интеграцию Alibaba Cloud Service (единый вход на основе ролей) с Azure AD, необходимо добавить Alibaba Cloud Service (единый вход на основе ролей) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Alibaba Cloud Service (единый вход на основе ролей) из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Alibaba Cloud Service (единый вход на основе ролей)**, выберите **Alibaba Cloud Service (единый вход на основе ролей)** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Alibaba Cloud Service (единый вход на основе ролей) в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Alibaba Cloud Service (единый вход на основе ролей) с использованием тестового пользователя **Britta Simon**.
Для работы единого входа необходимо установить связь между пользователем Azure Active Directory и соответствующим пользователем Alibaba Cloud Service (единый вход на основе ролей).

Чтобы настроить и проверить единый вход Azure AD в Alibaba Cloud Service (единый вход на основе ролей), потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Alibaba Cloud Service (единый вход на основе ролей)](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Alibaba Cloud Service (единый вход на основе ролей)](#create-alibaba-cloud-service-role-based-sso-test-user)** нужно для того, чтобы в Alibaba Cloud Service (единый вход на основе ролей) существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Alibaba Cloud Service (единый вход на основе ролей), выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Alibaba Cloud Service (единый вход на основе ролей)** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. На странице **Настройка единого входа с помощью SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для Alibaba Cloud Service (единый вход на основе ролей)](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите любой из URL-адресов:
    
    | |
    |--|
    | `urn:alibaba:cloudcomputing` |
    | `urn:alibaba:cloudcomputing:international` |

    b. В текстовом поле **URL-адрес ответа** введите любой из URL-адресов:

    | |
    |--|
    | `https://signin.aliyun.com/saml-role/SSO` |
    | `https://signin.alibabacloud.com/saml-role/SSO` |

5. Приложение Alibaba Cloud Service (единый вход на основе ролей) ожидает утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию. Щелкните значок **Изменить**, чтобы открыть диалоговое окно  **Атрибуты пользователя** .

    ![изображение](common/edit-attribute.png)

6. В дополнение к описанному выше приложение Alibaba Cloud Service (единый вход на основе ролей) ожидает передачи в ответе SAML нескольких дополнительных атрибутов. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** выполните следующие действия, чтобы добавить атрибут токена SAML, как показано в приведенной ниже таблице.

    | ИМЯ | Исходный атрибут|
    | ---------------| --------------- |
    | Роль | user.assignedroles |
    | RoleSessionName | user.mail |

    > [!NOTE]
    > Перейдите по [этой ссылке](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management), чтобы прочитать о настройке **роли** в Azure AD.

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](common/new-save-attribute.png)

    ![изображение](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Выберите команду **Сохранить**.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

8. В разделе **Настройка Alibaba Cloud Service (единый вход на основе ролей)** скопируйте требуемый URL-адрес.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Настройка единого входа для Alibaba Cloud Service (единый вход на основе ролей)

Чтобы настроить единый вход на стороне **Alibaba Cloud Service (единый вход на основе ролей)**, нужно отправить скачанный **XML-файл метаданных федерации** и соответствующие URL-адреса, скопированные на портале Azure, в [службу поддержки Alibaba Cloud Service (единый вход на основе ролей)](https://www.aliyun.com/service/). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon@yourcompanydomain.extension`. Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Alibaba Cloud Service (единый вход на основе ролей).

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Alibaba Cloud Service (единый вход на основе ролей)**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Выберите **Alibaba Cloud Service (единый вход на основе ролей)** из списка приложений.

    ![Ссылка на Alibaba Cloud Service (единый вход на основе ролей) в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Создание тестового пользователя Alibaba Cloud Service (единый вход на основе ролей)

В этом разделе описано, как создать пользователя Britta Simon в приложении Alibaba Cloud Service (единый вход на основе ролей). Обратитесь в  [службу поддержки Alibaba Cloud Service (единый вход на основе ролей)](https://www.aliyun.com/service/), чтобы добавить пользователей на эту платформу. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Alibaba Cloud Service (единый вход на основе ролей)" на Панели доступа, вы автоматически войдете в приложение Alibaba Cloud Service (единый вход на основе ролей), для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

