---
title: Руководство по интеграции единого входа Azure Active Directory с Google Cloud (G Suite) Connector | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Google Cloud (G Suite) Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: 5ff34b967e2ba42c01c904b8854ecfa04c394e17
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551529"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Руководство по интеграции единого входа Azure Active Directory с Google Cloud (G Suite) Connector

В этом руководстве описано, как интегрировать Google Cloud (G Suite) Connector с Azure Active Directory (Azure AD). Интеграция Google Cloud (G Suite) Connector с Azure AD обеспечивает следующие возможности:

* Контроль доступа к Google Cloud (G Suite) Connector с помощью Azure AD.
* Включение автоматического входа пользователей в Google Cloud (G Suite) Connector с использованием учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

- Подписка Azure AD.
- Подписка Google Cloud (G Suite) Connector с поддержкой единого входа.
- подписка Google Apps или Google Cloud Platform.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. Этот документ был создан с использованием нового пользовательского интерфейса единого входа. Если вы все еще используете старую версию, установка будет выглядеть иначе. Вы можете включить новый интерфейс в настройках единого входа приложения G-Suite. Перейдите в Azure AD в раздел **Корпоративные приложения**, выберите элементы**Google Cloud (G Suite) Connector**, **Единый вход**, а затем щелкните **Попробуйте новый интерфейс**.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

1. **Вопрос. Поддерживается ли интеграция единого входа Google Cloud Platform с Azure AD?**

    A. Да. Google Cloud Platform и Google Apps используют одну и ту же платформу проверки подлинности. Таким образом, чтобы обеспечить интеграцию с Google Cloud Platform, необходимо настроить единый вход в Google Apps.

2. **Вопрос. Поддерживают ли Chromebooks и другие устройства Chrome единый вход Azure AD?**
  
    A. Да, пользователи могут войти на свои устройства Chromebook, используя свои учетные данные Azure AD. Сведения о том, почему учетные данные могут быть запрошены у пользователей дважды, см. в этой статье о [поддержке Google Cloud (G Suite) Connector](https://support.google.com/chrome/a/answer/6060880).

3. **Вопрос. Если включить единый вход, смогут ли пользователи использовать свои учетные данные Azure AD для входа в любой продукт Google, такой как Google Класс, GMail, Google Drive, YouTube и т. д.?**

    A. Да, в зависимости от того, [какие приложения Google Cloud (G Suite) Connector](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) вы решили включить или отключить для своей организации.

4. **Вопрос. Можно ли включить единый вход только для подмножества пользователей Google Cloud (G Suite) Connector?**

    A. Нет, при включении единого входа всем пользователям Google Cloud (G Suite) Connector в вашей организации сразу же потребуется использовать свои учетные данные Azure AD для аутентификации. Google Cloud (G Suite) Connector не поддерживает наличие нескольких поставщиков удостоверений, поэтому в качестве поставщика удостоверений для среды Google Cloud (G Suite) Connector можно использовать либо Azure AD, либо Google — но не оба одновременно.

5. **Вопрос. Если пользователь выполнил вход в Windows, пройдет ли он автоматическую аутентификацию в Google Cloud (G Suite) Connector без запроса пароля?**

    A. Существует два варианта включения этого сценария. Во-первых, пользователи могут входить в устройства Windows 10 посредством [присоединения к Azure Active Directory](../device-management-introduction.md). Кроме того, пользователи могут входить на устройства с Windows, присоединенные к домену локального каталога Active Directory, для которого разрешен единый вход Azure AD посредством развертывания [служб федерации Active Directory (AD FS)](../hybrid/plan-connect-user-signin.md). В обоих случаях необходимо выполнить действия следующего учебника, чтобы включить единый вход между Azure AD и Google Cloud (G Suite) Connector.

6. **Вопрос. Что делать при получении сообщения об ошибке "Недопустимый адрес электронной почты"?**

    A. Для этой установки атрибут электронной почты необходим для того, чтобы пользователи могли войти в систему. Этот атрибут не может быть задан вручную.

    Атрибут электронной почты заполняется автоматически для любого пользователя с действующей лицензией Exchange. Эта ошибка может возникнуть, если пользователь не включил поддержку электронной почты, поскольку приложению необходимо получить этот атрибут для предоставления доступа.

    Вы можете перейти на portal.office.com с помощью учетной записи администратора, щелкнуть "Центр администрирования" > выставление счетов > подписки, выбрать подписку на Office 365, а затем щелкнуть "Назначить для пользователей", выбрать пользователей, подписки которых нужно проверить, и на панели справа щелкнуть "Изменить лицензии".

    После назначения лицензии O365 ее применение может занять несколько минут. После этого атрибут user.mail заполнится автоматически, и проблема будет решена.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Google Cloud (G Suite) Connector поддерживает единый вход, инициируемый **поставщиком служб**.

* Google Cloud (G Suite) Connector поддерживает [**автоматическую** подготовку пользователей](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).
* После настройки Google Cloud (G Suite) Connector вы можете применить функцию управления сеансом, которая защищает от хищения конфиденциальных данных вашей организации и несанкционированного доступа к ним в реальном времени. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad).

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Добавление Google Cloud (G Suite) Connector из коллекции

Чтобы настроить интеграцию Google Cloud (G Suite) Connector с Azure AD, нужно добавить Google Cloud (G Suite) Connector из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Google Cloud (G Suite) Connector**.
1. Выберите **Google Cloud (G Suite) Connector** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Настройка и проверка единого входа Azure AD для Google Cloud (G Suite) Connector

Настройте и проверьте единый вход Azure AD в Google Cloud (G Suite) Connector, используя данные тестового пользователя **B.Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Google Cloud (G Suite) Connector.

Чтобы настроить и проверить единый вход Azure AD в Google Cloud (G Suite) Connector, выполните действия из следующих стандартных блоков:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Google Cloud (G Suite) Connector](#configure-google-cloud-g-suite-connector-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Google Cloud (G Suite) Connector](#create-google-cloud-g-suite-connector-test-user)** требуется для того, чтобы в Google Cloud (G Suite) Connector существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Google Cloud (G Suite) Connector** найдите раздел **Управление** и щелкните **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. Чтобы настроить **Gmail**, в разделе **Базовая конфигурация SAML** выполните следующие действия:

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: 

    ```http
    https://www.google.com
    https://www.google.com/a/<yourdomain.com>
    ```

1. Чтобы настроить **Google Cloud Platform**, в разделе **Базовая конфигурация SAML** выполните следующие действия:

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    
    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```
    
    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: 
    
    ```http
    https://www.google.com
    https://www.google.com/a/<yourdomain.com>
    ```

    > [!NOTE]
    > Эти значения приведены для примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Google Cloud (G Suite) Connector не предоставляет значение идентификатора сущности для конфигурации единого входа, поэтому при снятии флажка **Domain specific issuer** (Издатель для определенного домена) значение идентификатора будет `google.com`. Если установить флажок **Издатель для определенного домена**, он будет равен "`google.com/a/<yourdomainname.com>`". Чтобы установить или снять флажок **Domain specific issuer** (Издатель для определенного домена), необходимо перейти в раздел **Настройка единого входа в Google Cloud (G Suite) Connector**, который описан далее в этом учебнике. Дополнительные сведения можно получить, обратившись в [группу поддержки клиентов Google Cloud (G Suite) Connector](https://www.google.com/contact/).

1. Приложение Google Cloud (G Suite) Connector ожидает проверочные утверждения SAML в определенном формате, поэтому следует добавить сопоставления настраиваемых атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример. По умолчанию для **уникального идентификатора пользователя** установлено значение **user.userprincipalname**, но для Google Cloud (G Suite) Connector требуется сопоставить это значение с адресом электронной почты пользователя. Для этого можно использовать атрибут **user.mail** из списка или соответствующее значение атрибута, основанное на конфигурации организации.

    ![Изображение](common/default-attributes.png)


1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Требуемые URL-адреса вы можете скопировать из раздела **Настройка Google Cloud (G Suite) Connector**.

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

В этом разделе описано, как разрешить пользователю B.Simon использовать единый вход Azure, предоставив этому пользователю доступ к Google Cloud (G Suite) Connector.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. Из списка приложений выберите **Google Cloud (G Suite) Connector**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Настройка единого входа в Google Cloud (G Suite) Connector

1. Откройте в браузере новую вкладку и войдите в [консоль администратора Google Cloud (G Suite) Connector](https://admin.google.com/) с использованием учетной записи администратора.

2. Перейдите на вкладку **Безопасность**. Если эта ссылка не отображается, она может быть скрыта в меню **More Controls** (Другие элементы управления) в нижней части экрана.

    ![Выбор элемента "Безопасность"][10]

3. На странице **Security** (Безопасность) выберите **Set up single sign-on (SSO)** (Настроить единый вход).

    ![Выбор элемента "Единый вход"][11]

4. Выполните следующие настройки.

    ![Настройка единого входа][12]

    а. Выберите **Setup SSO with third party identity provider**(Настройка единого входа с помощью стороннего поставщика удостоверений).

    b. В поле **Sign-in page URL** (URL-адрес страницы входа) в Google Cloud (G Suite) Connector вставьте значение **URL-адрес входа**, которое вы скопировали на портале Azure.

    c. В поле **Sign-out page URL** (URL-адрес страницы выхода) в Google Cloud (G Suite) Connector вставьте значение **URL-адрес выхода**, которое вы скопировали на портале Azure.

    d. В поле **Change password URL** (Изменить URL-адрес пароля) в Google Cloud (G Suite) Connector вставьте значение **Изменить URL-адрес пароля**, которое вы скопировали на портале Azure.

    д) В Google Cloud (G Suite) Connector в поле **Verification certificate** (Сертификат проверки) передайте сертификат, скачанный с портала Azure.

    е) Установите или снимите флажок **Использовать издателя для определенного домена**, как было сказано выше в разделе **Начальная настройка SAML** в Azure AD.

    ж. Щелкните **Сохранить изменения**.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Создание тестового пользователя Google Cloud (G Suite) Connector

Цель этого раздела — [создать пользователя в Google Cloud (G Suite) Connector](https://support.google.com/a/answer/33310?hl=en) с именем B.Simon. После того как пользователь будет создан в Google Cloud (G Suite) Connector вручную, он сможет войти в систему, используя учетные данные для входа в Office 365.

Google Cloud (G Suite) Connector также поддерживает автоматическую подготовку пользователей. Чтобы настроить автоматическую подготовку пользователей, сначала необходимо [настроить ее в Google Cloud (G Suite) Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Если выполнять проверку единого входа, когда подготовка в Azure AD еще не включена, необходимо убедиться, что пользователь уже существует в Google Cloud (G Suite) Connector.

> [!NOTE]
> Чтобы создать пользователя вручную, обратитесь к [группе поддержки Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Google Cloud (G Suite) Connector на панели доступа, вы автоматически войдете в Google Cloud (G Suite) Connector, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Руководство по настройке Google Apps для автоматической подготовки пользователей](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [Попробуйте использовать Google Cloud (G Suite) Connector с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Как защитить Google Cloud (G Suite) Connector с помощью функции управления настройками условного доступа](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png