---
title: Руководство. Интеграция Azure Active Directory с Qlik Sense Enterprise | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd6b66d010702ca2c3c4892aa47beea76dc18c8d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198853"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Руководство. Интеграция Azure Active Directory с Qlik Sense Enterprise

В этом руководстве описано, как интегрировать Qlik Sense Enterprise с Azure Active Directory (Azure AD).
Интеграция Qlik Sense Enterprise с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD можно контролировать, кто будет иметь доступ к Qlik Sense Enterprise.
* Вы можете включить автоматический вход для пользователей в Qlik Sense Enterprise (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Qlik Sense Enterprise, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Qlik Sense Enterprise с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Qlik Sense Enterprise поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Добавление Qlik Sense Enterprise из коллекции

Чтобы настроить интеграцию Qlik Sense Enterprise в Azure AD, необходимо добавить Qlik Sense Enterprise из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Qlik Sense Enterprise из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Qlik Sense Enterprise**, выберите **Qlik Sense Enterprise** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Qlik Sense Enterprise в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Qlik Sense Enterprise с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Qlik Sense Enterprise.

Чтобы настроить и проверить единый вход Azure AD в Qlik Sense Enterprise, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Qlik Sense Enterprise](#configure-qlik-sense-enterprise-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Qlik Sense Enterprise](#create-qlik-sense-enterprise-test-user)** требуется, чтобы в Qlik Sense Enterprise существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Qlik Sense Enterprise, сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Qlik Sense Enterprise** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах в приложении Qlik Sense Enterprise](common/sp-identifier-reply.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<Qlik Sense Fully Qualifed Hostname>:4443/azure/hub`

    б) В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|
    | |

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:

    `https://<Qlik Sense Fully Qualifed Hostname>:4443/samlauthn/`

    > [!NOTE]
    > Эти значения приведены для примера. Вместо этих значений укажите фактические URL-адрес для входа, идентификатор и URL-адрес ответа, к которым мы вернемся позже в этом руководстве, или обратитесь к [группе поддержки Qlik Sense Enterprise](https://www.qlik.com/us/services/support), чтобы получить эти значения.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

### <a name="configure-qlik-sense-enterprise-single-sign-on"></a>Настройка Qlik Sense Enterprise с поддержкой единого входа

1. Подготовьте XML-файл метаданных федерации для отправки на сервер Qlik Sense.

    > [!NOTE]
    > Перед отправкой метаданных IdP на сервер Qlik Sense файл необходимо изменить. В нем нужно удалить определенные сведения, чтобы обеспечить корректное взаимодействие Azure AD и сервера Qlik Sense.

    ![QlikSense][qs24]

    a. Откройте в текстовом редакторе файл FederationMetaData.xml, скачанный с портала Azure.

    б) Найдите значение **RoleDescriptor**.  Вы найдете четыре записи (две пары открывающих и закрывающих тегов для элементов).

    c. Удалите теги RoleDescriptor и все данные между ними из файла.

    4.3. Сохраните файл и держите его под рукой для дальнейшего использования в этом документе.

2. Перейдите к консоли управления Qlik Management Console (QMC) Qlik Sense как пользователь с возможностью создания конфигураций виртуального прокси-сервера.

3. В QMC щелкните пункт меню **Virtual Proxies** (Виртуальные прокси-серверы).

    ![QlikSense][qs6]

4. В нижней части страницы нажмите кнопку **Create new** (Создать).

    ![QlikSense][qs7]

5. Появится экран Virtual proxy edit (Редактирование виртуального прокси-сервера).  В правой части экрана расположено меню для отображения параметров конфигурации.

    ![QlikSense][qs9]

6. Установите галочку возле пункта меню Identification (Идентификация) и введите идентифицирующие сведения для конфигурации виртуального прокси-сервера Azure.

    ![QlikSense][qs8]  

    a. В поле **Description** (Описание) укажите понятное имя для конфигурации виртуального прокси-сервера.  Введите значение в поле Description (Описание).

    б) Значение в поле **Prefix** (Префикс) определяет конечную точку виртуального прокси-сервера для подключения к Qlik Sense с использованием единого входа Azure AD.  Введите уникальное имя префикса для этого виртуального прокси-сервера.

    c. **Session inactivity timeout (minutes)** (Время ожидания при бездействии сеанса (в минутах)) — это время для ожидания подключений через этот виртуальный прокси-сервер.

    4.3. **Session cookie header name** (Имя заголовка сеанса в файле cookie) — имя cookie, которое содержит идентификатор для сеанса Qlik Sense, полученный пользователем после успешной проверки подлинности.  Это имя должно быть уникальным.

7. Щелкните пункт меню Authentication (Проверка подлинности), чтобы сделать его видимым.  Появится экран Authentication (Проверка подлинности).

    ![QlikSense][qs10]

    a. Значение в раскрывающемся списке **Anonymous access mode** (Режим анонимного доступа) определяет, могут ли анонимные пользователи получить доступ к Qlik Sense через виртуальный прокси-сервер.  Параметр по умолчанию — No anonymous user (Без анонимных пользователей).

    б) В раскрывающемся меню **Authentication method** (Метод аутентификации) следует выбрать схему аутентификации, которую будет использовать виртуальный прокси-сервер.  Выберите в этом списке элемент SAML.  После этого появятся дополнительные параметры.

    c. В поле **SAML host URI** (URI узла SAML) введите имя узла, которое пользователи будут вводить для доступа к Qlik Sense через этот виртуальный прокси-сервер SAML.  Имя узла представляет собой URI сервера Qlik Sense.

    4.3. В поле **SAML entity ID** (Идентификатор сущности SAML) введите то же значение, что и в поле SAML host URI (URI узла SAML).

    д. **SAML IdP metadata** (Метаданные IdP SAML) — это файл, измененный ранее в разделе **Изменение метаданных федерации из конфигурации Azure AD**.  **Перед отправкой метаданных IdP файл необходимо изменить.** В нем нужно удалить определенные сведения, чтобы обеспечить корректное взаимодействие между Azure AD и сервером Qlik Sense.  **Если файл нужно изменить, см. инструкции выше.**  Если файл изменен, щелкните кнопку Browse (Обзор) и выберите измененный файл метаданных для передачи в конфигурацию виртуального прокси-сервера.

    Е. Введите имя атрибута или ссылку на схему для атрибута SAML, представляющего **идентификатор пользователя**, который Azure AD отправит на сервер Qlik Sense.  Сведения о ссылке на схему доступны в постконфигурации экранов приложения Azure.  Чтобы использовать атрибут имени, введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    ж. Введите значение для **каталога пользователя** , который будет присоединен к пользователям при проверке подлинности на сервере Qlik Sense с использованием Azure AD.  Жестко заданные значения нужно заключить в **квадратные скобки []**.  Чтобы использовать атрибут, отправленный в утверждении SAML Azure AD, введите имя атрибута в этом текстовом поле **без** квадратных скобок.

    h. В раскрывающемся списке **SAML signing algorithm** (Алгоритм подписывания SAML) можно задать подписывание сертификата поставщика услуг (в этом случае сервер Qlik Sense) для конфигурации виртуального прокси-сервера.  Если сервер Qlik Sense использует доверенный сертификат, созданный с помощью Microsoft Enhanced RSA и AES Cryptographic Provider, измените алгоритм подписывания SAML на **SHA-256**.

    i. В разделе SAML attribute mapping (Сопоставление атрибутов SAML) можно отправлять дополнительные атрибуты, например группы, в Qlik Sense для использования в правилах безопасности.

8. Щелкните пункт меню **Load balancing** (Балансировка нагрузки), чтобы сделать его видимым.  Появится экран Load balancing (Балансировка нагрузки).

    ![QlikSense][qs11]

9. Нажмите кнопку **Add new server node** (Добавить новый узел сервера), выберите узел или узлы обработчика, на которые Qlik Sense будет отправлять сеансы для балансировки нагрузки, а затем нажмите кнопку **Add** (Добавить).

    ![QlikSense][qs12]

10. Щелкните пункт меню Advanced (Дополнительно), чтобы сделать его видимым. Откроется экран Advanced (Дополнительно).

    ![QlikSense][qs13]

    В разделе Host white list (Список разрешенных узлов) определяются имена принимаемых узлов при подключении к серверу Qlik Sense.  **Введите имя узла, которое пользователи будут указывать при подключении к серверу Qlik Sense.**  Для имени узла указывается то же значение, что и для URI узла SAML, только без https://.

11. Щелкните кнопку **Apply** (Применить).

    ![QlikSense][qs14]

12. Нажмите кнопку OK (ОК), чтобы принять предупреждение о перезапуске прокси-серверов, связанных с виртуальным прокси-сервером.

    ![QlikSense][qs15]

13. В правой части экрана отображается меню Associated items (Связанные элементы).  Щелкните пункт меню **Proxies** (Прокси-серверы).

    ![QlikSense][qs16]

14. Появится экран прокси-сервера.  Щелкните кнопку **Link** (Связать) внизу, чтобы связать прокси-сервер с виртуальным прокси-сервером.

    ![QlikSense][qs17]

15. Выберите узел прокси-сервера, который будет поддерживать подключение к этому виртуальному прокси-серверу, и щелкните кнопку **Link** (Связать).  После установки связи прокси-сервер отобразится в списке связанных прокси-серверов.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Через 5–10 секунд появится сообщение об обновлении QMC.  Нажмите кнопку **Refresh QMC** (Обновить QMC).

    ![QlikSense][qs20]

17. После обновления QMC щелкните пункт меню **Virtual proxies** (Виртуальные прокси-серверы). Новая запись виртуального прокси-сервера SAML указана в таблице на экране.  Выделите запись виртуального прокси-сервера.

    ![QlikSense][qs51]

18. В нижней части экрана станет активной кнопка Download SP metadata (Скачать метаданные SP).  Щелкните **Download SP metadata** (Скачать метаданные SP), чтобы сохранить метаданные в файл.

    ![QlikSense][qs52]

19. Откройте файл метаданных SP.  Обратите внимание на записи **entityID** и **AssertionConsumerService**.  Значения этих записей должны быть эквивалентны **идентификатору**, **URL-адресу для входа** и **URL-адресу ответа** в конфигурации приложения Azure AD. Вставьте эти значения в разделе **Домены и URL-адреса приложения Qlik Sense Enterprise** в параметрах конфигурации приложения в Azure AD. Если они не совпадают, измените их через мастер настройки приложения Azure AD.

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а) В поле **Имя** введите **BrittaSimon**.
  
    б) В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    в) Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    г) Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Qlik Sense Enterprise.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем выберите **Qlik Sense Enterprise**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **Qlik Sense Enterprise**.

    ![Ссылка на Qlik Sense Enterprise в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-qlik-sense-enterprise-test-user"></a>Создание тестового пользователя Qlik Sense Enterprise

В этом разделе описано, как создать пользователя Britta Simon в приложении Sense Enterprise. Чтобы добавить пользователя на платформе Qlik Sense Enterprise, обратитесь в  [службу поддержки Qlik Sense Enterprise](https://www.qlik.com/us/services/support). Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Qlik Sense Enterprise на панели доступа, вы автоматически войдете в приложение Qlik Sense Enterprise, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

