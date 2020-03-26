---
title: Руководство по Интеграция Azure Active Directory с приложением SAP Cloud Platform Identity Authentication | Документация Майкрософт
description: Сведения о настройке единого входа в Azure Active Directory и SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95be73bd125c124409585a478fa9707e7b6a2ac2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76289073"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Руководство по Интеграция единого входа Azure Active Directory с приложением SAP Cloud Platform Identity Authentication

В этом учебнике описано, как интегрировать приложение SAP Cloud Platform Identity Authentication с Azure Active Directory (Azure AD). Интеграция приложения SAP Cloud Platform Identity Authentication с Azure AD обеспечивает следующие возможности:

* Контроль доступа к SAP Cloud Platform Identity Authentication с помощью Azure AD.
* Автоматический вход пользователей в SAP Cloud Platform Identity Authentication с использованием учетной записи Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка SAP Cloud Platform Identity Authentication с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* SAP Cloud Platform Identity Authentication поддерживает единый вход, инициируемый **поставщиком услуг** и **поставщиком удостоверений**.
* После настройки SAP Cloud Platform Identity Authentication можно применять элементы управления сеансами, которые защищают от хищения конфиденциальных данных вашей организации и несанкционированного доступа к ним в режиме реального времени. Элементы управления сеансом являются расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad).

Прежде чем углубиться в технические подробности, очень важно разобраться в основных понятиях этого процесса. Службы федерации SAP Cloud Platform Identity Authentication и AD позволяют реализовать единый вход в приложения и службы, защищенные с помощью Azure AD (выступающей в качестве поставщика удостоверений), и приложения и службы SAP, защищенные с помощью SAP Cloud Platform Identity Authentication.

Приложение SAP Cloud Platform Identity Authentication выполняет роль "промежуточного" поставщика удостоверений для приложений SAP. В свою очередь, Azure Active Directory выступает ведущим поставщиком удостоверений. 

Этот пример представлен на схеме ниже.

![Создание тестового пользователя Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

В этой конфигурации клиент SAP Cloud Platform Identity Authentication настроен как доверенное приложение в AAD.

Все приложения и службы SAP, которые вы намерены защитить, настраиваются в консоли управления SAP Cloud Platform Identity Authentication.

Это значит, что авторизация для доступа к этим приложениям и службам SAP будет выполняться в приложении SAP Cloud Platform Identity Authentication, а не в Azure Active Directory.

Настройка SAP Cloud Platform Identity Authentication в роли приложения в Azure Active Directory Marketplace избавляет от необходимости настраивать отдельные утверждения или утверждения SAML.

> [!NOTE]
> В настоящее время обе стороны проверили только единый вход через веб-интерфейс. Потоки взаимодействия "приложение — API" и "API — API" готовы и работают, но пока не тестировались. Выполнение таких тестов входит в программу дальнейших действий.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Добавление приложения SAP Cloud Platform Identity Authentication из коллекции

Чтобы настроить интеграцию SAP Cloud Platform Identity Authentication с AAD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **SAP Cloud Platform Identity Authentication**.
1. Выберите **SAP Cloud Platform Identity Authentication** на панели результатов поиска, а затем добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Настройка и проверка единого входа Azure AD в SAP Cloud Platform Identity Authentication

Настройте и проверьте единый вход Azure AD в SAP Cloud Platform Identity Authentication с помощью тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Cloud Platform Identity Authentication.

Чтобы настроить и проверить единый вход Azure AD в SAP Cloud Platform Identity Authentication, выполните действия в следующих блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя SAP Cloud Platform Identity Authentication](#create-sap-cloud-platform-identity-authentication-test-user)** требуется, чтобы создать в SAP Cloud Platform Identity Authentication пользователя B. Simon, связанного представлением этого пользователя в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **SAP Cloud Platform Identity Authentication** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить в **режиме, инициируемом поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия:

    ![Сведения для единого входа в разделе "Домен и URL-адреса приложения SAP Cloud Platform Identity Authentication"](common/idp-intiated.png)

    а. В текстовом поле **Идентификатор** введите URL-адрес в формате `<IAS-tenant-id>.accounts.ondemand.com`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`.

    > [!NOTE]
    > Эти значения приведены для примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Обратитесь в [службу поддержки SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html), чтобы узнать эти значения. Если вы не знаете, что такое значение идентификатора, изучите документацию по [настройке клиента SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) для SAP Cloud Platform Identity Authentication.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия:

    ![Сведения для единого входа в разделе "Домен и URL-адреса приложения SAP Cloud Platform Identity Authentication"](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Используйте свой URL-адрес входа для бизнес-приложения. Если у вас возникнут сомнения, обратитесь в [службу поддержки SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html).

1. Приложение SAP Cloud Platform Identity Authentication ожидает проверочные утверждения SAML в определенном формате, который требует добавить сопоставления настраиваемых атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![Изображение](common/default-attributes.png)

1. В дополнение к описанному выше приложение SAP Cloud Platform Identity Authentication ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.

    | Имя | Исходный атрибут|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

9. Скопируйте требуемый URL-адрес из раздела **Настройка SAP Cloud Platform Identity Authentication**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю B. Simon использовать единый вход Azure, предоставив ему доступ к SAP Cloud Platform Identity Authentication.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **SAP Cloud Platform Identity Authentication**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Настройка единого входа в SAP Cloud Platform Identity Authentication

1. Чтобы настроить единый вход в приложении, откройте консоль администрирования SAP Cloud Platform Identity Authentication. URL-адрес имеет следующий формат: `https://<tenant-id>.accounts.ondemand.com/admin`. Ознакомьтесь с документацией по SAP Cloud Platform Identity Authentication на странице [интеграции с Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. На портале Azure нажмите кнопку **Сохранить**.

3. Следующие действия нужно выполнять только в том случае, если вы хотите добавить и включить единый вход для другого приложения SAP. Повторите весь процесс, описанный в разделе **Добавление приложения SAP Cloud Platform Identity Authentication из коллекции**.

4. На портале Azure на странице интеграции приложения **SAP Cloud Platform Identity Authentication** выберите пункт **Вход по ссылке**.

    ![Настройка связанного единого входа](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Сохраните конфигурацию.

> [!NOTE]
> Новое приложение применит ту же конфигурацию единого входа, которая настроена для первого приложения SAP. Убедитесь, что в консоли администрирования SAP Cloud Platform Identity Authentication указаны те же поставщики корпоративных удостоверений.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Создание тестового пользователя SAP Cloud Platform Identity Authentication

В приложении SAP Cloud Platform Identity Authentication не нужно создавать пользователя. Пользователи, находящиеся в хранилище пользователей в Azure AD, могут использовать функцию единого входа.

Приложение SAP Cloud Platform Identity Authentication поддерживает федерацию удостоверений. Это позволяет приложению проверять, существуют ли пользователи, прошедшие аутентификацию через корпоративного поставщика удостоверений, в хранилище пользователей SAP Cloud Platform Identity Authentication.

Параметр федерации удостоверений по умолчанию отключен. Если вы включите этот параметр, доступ к приложению смогут получить только те пользователи, которые заранее импортированы в SAP Cloud Platform Identity Authentication.

Дополнительные сведения о том, как включить или отключить параметр федерации удостоверений для SAP Cloud Platform Identity Authentication см. в соответствующем разделе статьи о [настройке федерации удостоверений с использованием хранилища пользователей SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SAP Cloud Platform Identity Authentication на панели доступа, вы автоматически войдете в это приложение, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать SAP Cloud Platform Identity Authentication с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Защита приложений с помощью функции управления настройками условного доступа для приложений в Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
