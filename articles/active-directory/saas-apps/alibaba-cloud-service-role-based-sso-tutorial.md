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
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8de2f7cb90e004673c59282a8023d55df364220a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65140858"
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

5. На странице **Alibaba Cloud Service (единый вход на основе ролей)** щелкните **Свойства** в области навигации слева, скопируйте **идентификатор объекта** и сохраните его на своем компьютере для последующего использования.

    ![Конфигурация свойств](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Alibaba Cloud Service (единый вход на основе ролей) с использованием тестового пользователя **Britta Simon**.
Для работы единого входа необходимо установить связь между пользователем Azure Active Directory и соответствующим пользователем Alibaba Cloud Service (единый вход на основе ролей).

Чтобы настроить и проверить единый вход Azure AD в Alibaba Cloud Service (единый вход на основе ролей), потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа на основе ролей в Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** необходима, чтобы пользователи могли использовать эту функцию.
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

4. Если у вас есть **файл метаданных поставщика служб**, выполните следующие действия в разделе **Базовая конфигурация SAML**:

    >[!NOTE]
    >Метаданные поставщика службы можно получить по этому [URL-адресу](https://signin.alibabacloud.com/saml-role/sp-metadata.xml).

    a. Щелкните **Отправить файл метаданных**.

    ![изображение](common/upload-metadata.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![изображение](common/browse-upload-metadata.png)

    c. После успешной отправки файла метаданных значения **Идентификатор** и **URL-адрес ответа** будут заполнены автоматически в разделе "Alibaba Cloud Service (единый вход на основе ролей)":

    ![image](common/idp-intiated.png)

    > [!Note]
    > Если поля **Идентификатор** и **URL-адрес ответа** автоматически не заполняются, введите эти значения вручную в соответствии с поставленной задачей.

5. Приложение Alibaba Cloud Service (единый вход на основе ролей) ожидает утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию. Нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![image](common/edit-attribute.png)

6. В дополнение к описанному выше приложение Alibaba Cloud Service (единый вход на основе ролей) ожидает передачи в ответе SAML нескольких дополнительных атрибутов. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** выполните следующие действия, чтобы добавить атрибут токена SAML, как показано в приведенной ниже таблице.

    | ИМЯ | Пространство имен | Исходный атрибут|
    | ---------------| ------------| --------------- |
    | Роль | https://www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https://www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

    > [!NOTE]
    > Перейдите по [этой ссылке](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management), чтобы прочитать о настройке **роли** в Azure AD.

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

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

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Настройка единого входа на основе ролей для Alibaba Cloud Service

1. Войдите в [консоль управления доступом к ресурсам](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) Alibaba Cloud с помощью учетной записи Account1.

2. В области навигации слева выберите **SSO** (Единый вход).

3. На вкладке **Role-based SSO** (Единый вход на основе ролей), нажмите **Create IdP** (Создать поставщика удостоверений).

4. На отображаемой странице введите `AAD` в поле IdP Name (Имя поставщика удостоверений), введите описание в поле **Note** (Примечание), нажмите **Upload**, чтобы отправить файл метаданных федерации, который вы скачали ранее, и щелкните **OК**.

5. После успешного создания поставщика удостоверений нажмите **Create RAM Role** (Создать роль для управления доступом к ресурсам).

6. В поле **RAM Role Name** (Имя роли для управления доступом к ресурсам) введите `AADrole`, выберите `AAD` из раскрывающегося списка **Select IdP** (Выбор поставщика удостоверений) и щелкните "OК".

    >[!NOTE]
    >При необходимости вы можете назначить роли разрешения. Мы рекомендуем, чтобы после создания поставщика удостоверений и соответствующей роли вы сохранили имена ARN поставщика удостоверений и роли для последующего использования. Вы можете получить имена ARN на страницах со сведениями о поставщике удостоверений и роли.

7. Свяжите роль для управления доступом к ресурсам Alibaba Cloud (AADrole) с пользователем Azure AD (u2): Чтобы связать роль для управления доступом к ресурсам с пользователем Azure AD, создайте роль в Azure AD, выполнив такие шаги:

    a. Войдите в [обозреватель Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9).

    b. Нажмите кнопку **Изменить разрешения**, чтобы получить необходимые разрешения для создания роли.

    ![Конфигурация Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Выберите указанные ниже разрешения в списке и нажмите кнопку **Изменить разрешения**, как показано на следующем рисунке.

    ![Конфигурация Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >После предоставления разрешений повторно войдите в обозреватель Graph.

    d. На странице обозревателя Graph выберите **GET** в первом раскрывающемся списке и **beta** во втором. Затем введите `https://graph.microsoft.com/beta/servicePrincipals` в поле рядом с раскрывающимися списками и щелкните **Выполнить запрос**.

    ![Конфигурация Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Если вы используете несколько каталогов, в поле запроса можно ввести `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.

    д. В разделе **Предварительный просмотр ответа** извлеките свойство appRoles из субъекта-службы для последующего использования.

    ![Конфигурация Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Свойство appRoles можно найти, введя `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` в поле запроса. Обратите внимание, что `objectID` является идентификатором объекта, который вы скопировали на странице **Свойства** Azure AD.

    Е. Вернитесь в обозреватель Graph, измените метод с **GET** на **PATCH**, вставьте указанное ниже содержимое в раздел **Текст запроса** и щелкните **Выполнить запрос**:
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value` — это ARN поставщика удостоверений и роли, созданных вами в консоли управления доступом к ресурсам. При необходимости здесь можно добавить несколько ролей. Azure AD будет отправлять значение этих ролей в качестве значения утверждения в ответе SAML. Но вы сможете добавить новые роли только после части `msiam_access` для операции исправления. Чтобы упростить процесс создания, мы рекомендуем вам использовать генератор идентификаторов (например, GUID Generator) для создания идентификаторов в реальном времени.

    ж. Когда к субъекту-службе будет применена необходимая роль, свяжите роль с пользователем Azure AD (u2), выполнив шаги в разделе **Назначение тестового пользователя Azure AD** этого руководства.

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

    a. В поле **Имя** введите **BrittaSimon**.
  
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

5. На вкладке **Пользователи и группы** выберите u2 из списка пользователей и щелкните **Выбрать**. Затем щелкните **Назначить**.

    ![Конфигурация тестирования](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. Просмотрите назначенную роль и протестируйте приложение Alibaba Cloud Service (единый вход на основе ролей).

    ![Конфигурация тестирования](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >После назначения пользователя (u2) созданная роль будет автоматически связана с пользователем. Если вы создали несколько ролей, при необходимости свяжите соответствующую роль с пользователем. Если вы хотите реализовать единый вход Azure AD на основе ролей для нескольких учетных записей Alibaba Cloud, повторите предыдущие шаги.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Создание тестового пользователя Alibaba Cloud Service (единый вход на основе ролей)

В этом разделе описано, как создать пользователя Britta Simon в приложении Alibaba Cloud Service (единый вход на основе ролей). Обратитесь в [службу поддержки Alibaba Cloud Service (единый вход на основе ролей)](https://www.aliyun.com/service/), чтобы добавить пользователей на эту платформу. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа 

После завершения работы с предыдущими конфигурациями протестируйте приложение Alibaba Cloud Service (единый вход на основе ролей), выполнив следующие шаги:

1. На портале Azure перейдите на страницу **Alibaba Cloud Service (единый вход на основе ролей)**, выберите **Единый вход** и нажмите **Тест**.

    ![Конфигурация тестирования](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Щелкните **Войдите от имени текущего пользователя**.

    ![Конфигурация тестирования](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. На странице выбора учетной записи выберите u2.

    ![Конфигурация тестирования](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Отобразится следующая страница с указанием того, что единый вход на основе ролей успешно выполнен.

    ![Конфигурация тестирования](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

