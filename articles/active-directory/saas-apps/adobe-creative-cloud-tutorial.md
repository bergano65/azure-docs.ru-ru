---
title: Руководство по интеграции единого входа Azure Active Directory с Adobe Creative Cloud | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 815cffab118f6900c1c9d42a7e44821f8af62532
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081987"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Руководство по интеграции единого входа Azure Active Directory с Adobe Creative Cloud

В этом учебнике вы узнаете, как интегрировать приложение Adobe Creative Cloud с Azure Active Directory (Azure AD). Интеграция Adobe Creative Cloud с Azure AD обеспечивает следующие возможности.

* С помощью Azure AD вы можете контролировать доступ к Adobe Creative Cloud.
* Вы можете включить автоматический вход пользователей в Adobe Creative Cloud с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Adobe Creative Cloud с поддержкой единого входа (SSO).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Adobe Creative Cloud поддерживает единый вход, инициированный **поставщиком услуг**.





## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Добавление Adobe Creative Cloud из коллекции

Чтобы настроить интеграцию Adobe Creative Cloud с Azure AD, необходимо добавить Adobe Creative Cloud из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Adobe Creative Cloud**.
1. Выберите **Adobe Creative Cloud** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-adobe-creative-cloud"></a>Настройка и проверка единого входа Azure AD для Adobe Creative Cloud

Настройте и проверьте единый вход Azure AD в Adobe Creative Cloud с помощью тестового пользователя **B.Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Adobe Creative Cloud.

Чтобы настроить и проверить единый вход Azure AD в Adobe Creative Cloud, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Adobe Creative Cloud](#configure-adobe-creative-cloud-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** требуется для того, чтобы в Adobe Creative Cloud существовал пользователь B.Simon, связанный с представлением этого же пользователя в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Adobe Creative Cloud** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    a. В текстовом поле **URL-адрес входа** введите URL-адрес: `https://adobe.com`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://www.okta.com/saml2/service-provider/<token>`.

    > [!NOTE]
    > Значение идентификатора приведено для примера и не является реальным. Вместо него нужно указать фактический идентификатор. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. Приложение Adobe Creative Cloud ожидает проверочные утверждения SAML в определенном формате, который требует добавить сопоставления настраиваемых атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![image](common/edit-attribute.png)

1. В дополнение к описанному выше приложение Adobe Creative Cloud ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.

    | ИМЯ | Исходный атрибут|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |

    > [!NOTE]
    > У пользователей должна быть действительная лицензия Office 365 ExO для заполнения значения утверждения из электронного письма в ответе SAML.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Скопируйте требуемый URL-адрес из раздела **Настройка Adobe Creative Cloud**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

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

В этом разделе описано, как разрешить пользователю B.Simon использовать единый вход Azure путем предоставления доступа к Adobe Creative Cloud.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **Adobe Creative Cloud**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-adobe-creative-cloud-sso"></a>Подписка Adobe Creative Cloud с поддержкой единого входа

1. В другом окне веб-браузера войдите в [консоль администрирования Adobe](https://adminconsole.adobe.com) от имени администратора.

2. На панели навигации вверху выберите **Параметры**, а затем щелкните **Удостоверение**. Откроется список доменов. Щелкните ссылку **Настройка** для вашего домена. В разделе **Single Sign On Configuration Required** (Обязательная конфигурация единого входа) выполните следующие действия. Дополнительные сведения см. в статье о [настройке домена](https://helpx.adobe.com/enterprise/using/set-up-domain.html).

    ![Параметры](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Параметры")

    a. Щелкните **Browse** (Просмотр), чтобы найти и передать сертификат, загруженный из Azure AD, в качестве **сертификата поставщика удостоверений**.

    b. В текстовое поле **IDP Issuer** (Издатель IDP) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    c. В текстовое поле **IDP Login URL** (URL-адрес для входа с помощью IDP) вставьте значение **URL-адреса входа**, скопированное на портале Azure.

    d. Для параметра **HTTP — Redirect** (Перенаправление HTTP) выберите вариант **IDP Binding** (Привязка к поставщику удостоверений).

    д. Для параметра **Email Address** (Адрес электронной почты) выберите вариант **User Login Setting** (Настройки входа пользователя).

    Е. Нажмите кнопку **Сохранить** .

3. Теперь на панели мониторинга отобразится XML-файл **Download Metadata** (Загрузить метаданные). Он содержит URL-адреса компании Adobe для описания сущности (EntityDescriptor) и URL-адреса службы утверждений (AssertionConsumerService). Откройте этот файл и перенесите настройки в приложение Azure AD.

    ![Настройка единого входа на стороне приложения](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Используйте значение EntityDescriptor, предоставленное компанией Adobe, в качестве значения для параметра **Identifier** (Идентификатор) в диалоговом окне **Configure App Settings** (Настройка параметров приложения).

    b. Используйте значение AssertionConsumerService, предоставленное компанией Adobe, в качестве значения для параметра **Reply URL** (URL-адрес ответа) в диалоговом окне **Configure App Settings** (Настройка параметров приложения).

### <a name="create-adobe-creative-cloud-test-user"></a>Создание тестового пользователя Adobe Creative Cloud

Чтобы пользователи Azure AD могли входить в Adobe Creative Cloud, они должны быть подготовлены для него. В случае Adobe Creative Cloud подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.

1. Войдите на сайт [консоли администрирования Adobe](https://adminconsole.adobe.com) от имени администратора.

2. Добавьте пользователя в консоль Adobe с помощью федеративного идентификатора и назначьте его профилю продукта. Подробные сведения о добавлении пользователей см. в статье, посвященной [добавлению пользователей в консоль администрирования Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. На этом этапе введите свой адрес электронной почты или имя участника-пользователя в форму регистрации Adobe, нажмите клавишу TAB, и вы должны попасть обратно в Azure AD через федеративные отношения:
   * Веб-доступ: www\.adobe.com > "Вход"
   * В программе классического приложения > "Вход"
   * В приложении > "Справка" > "Вход"

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Adobe Creative Cloud на панели доступа, вы автоматически войдете в приложение Adobe Creative Cloud, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Пробное использование Adobe Creative Cloud с Azure AD](https://aad.portal.azure.com/)

- [Настройка домена (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Настройка Azure для использования с единым входом Adobe (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

