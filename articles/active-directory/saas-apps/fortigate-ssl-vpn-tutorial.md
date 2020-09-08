---
title: Руководство по интеграции единого входа Azure Active Directory с VPN SSL FortiGate | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в VPN SSL FortiGate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299717"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Руководство по интеграции единого входа Azure Active Directory с VPN SSL FortiGate

В этом руководстве описано, как интегрировать VPN SSL FortiGate с Azure Active Directory (Azure AD). Интеграция VPN SSL FortiGate с Azure AD обеспечивает следующие возможности.

* Контроль доступа к VPN SSL FortiGate с помощью Azure AD.
* Автоматический вход пользователей в VPN SSL FortiGate с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка VPN SSL FortiGate с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* VPN SSL FortiGate поддерживает единый вход, инициированный **поставщиком услуг**.
* После настройки VPN SSL FortiGate вы можете применить функцию управления сеансом, которая защищает от хищения конфиденциальных данных вашей организации и несанкционированного доступа к ним в реальном времени. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>Добавление VPN SSL FortiGate из коллекции

Чтобы настроить интеграцию VPN SSL FortiGate с Azure AD, необходимо добавить VPN SSL FortiGate из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **VPN SSL FortiGate**.
1. Выберите **VPN SSL FortiGate** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Настройка и проверка единого входа Azure AD для VPN SSL FortiGate

Настройте и проверьте единый вход с Azure AD в VPN SSL FortiGate, используя тестового пользователя **B. Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в VPN SSL FortiGate.

Чтобы настроить и проверить единый вход Azure AD в VPN SSL FortiGate, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в VPN SSL FortiGate](#configure-fortigate-ssl-vpn-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **Создание тестового пользователя приложения VPN SSL FortiGate** требуется для того, чтобы в VPN SSL FortiGate существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **VPN SSL FortiGate** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Настройка единого входа с помощью SAML** введите значения для следующих полей:

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<FQDN>/remote/login`.

    b. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<FQDN>/remote/saml/metadata`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://><FQDN/remote/saml/login`.

    d. В текстовом поле **URL-адрес выхода** введите URL-адрес в следующем формате: `https://<FQDN>/remote/saml/logout`

    > [!NOTE]
    > Эти значения приведены для примера. Замените эти значения фактическим идентификатором, URL-адресом ответа, URL-адресом для входа и URL-адресом для выхода. Чтобы получить их, обратитесь к [группе поддержки клиентов VPN SSL FortiGate](mailto:tac_amer@fortinet.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. Приложение VPN SSL FortiGate ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![Изображение](common/default-attributes.png)

1. В дополнение к описанному выше приложение VPN SSL FortiGate ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.
    
    | Имя |  Исходный атрибут|
    | ------------ | --------- |
    | username | user.userprincipalname |
    | group | user.groups |

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Требуемые URL-адреса вы можете скопировать из раздела **Настройка VPN SSL FortiGate**.

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

В этом разделе описано, как включить единый вход Azure для пользователя B. Simon, предоставив ему доступ к VPN SSL FortiGate.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **VPN SSL FortiGate**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-a-security-group-for-the-test-user"></a>Создание группы безопасности для тестового пользователя

В этом разделе вы создадите группу безопасности в Azure Active Directory для тестового пользователя. Она будет использоваться в FortiGate для предоставления пользователю сетевого доступа через VPN.

1. На портале Azure в области слева выберите **Azure Active Directory**, а затем — **Группы**.
1. В верхней части экрана щелкните **Новый пользователь**.
1. В разделе свойств **Новая группа** сделайте следующее.
   1. В поле **Тип группы** выберите **Безопасность**.
   1. В поле **Имя** введите `FortiGateAccess`.
   1. В поле **Описание группы** введите `Group for granting FortiGate VPN access`.
   1. Установите для параметра **Azure AD roles can be assigned to the group (Preview)** (Роли Azure AD можно назначить группе (предварительная версия)) значение **Нет**.
   1. В поле **Тип членства** выберите **Назначено**.
   1. В разделе **Участники** выберите **Нет выбранных участников**.
   1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
   1. Нажмите кнопку **создания**.
1. Вернувшись в колонку **Группы** в Azure Active Directory найдите группу **доступа к FortiGate** и запишите **идентификатор объекта** для последующего использования.

## <a name="configure-fortigate-ssl-vpn-sso"></a>Настройка единого входа в VPN SSL FortiGate

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Загрузка сертификата SAML в кодировке Base64 в приложение FortiGate

После завершения в клиенте настройки SAML приложения FortiGate будет скачан сертификат SAML в кодировке Base64. Загрузите его в приложение FortiGate, выполните следующие действия:

1. Откройте портал управления приложения FortiGate.
1. В меню слева выберите **System** (Система).
1. В разделе **System** (Система) выберите **Certificates** (Сертификаты).
1. Выберите **Import** -> **Remote Certificate** (Импорт > Удаленный сертификат).
1. Перейдите к сертификату, скачанному из развертывания приложения FortiGate в клиенте Azure, выберите его и нажмите кнопку **OK**.

После отправки сертификата запишите его имя в разделе **System** > **Certificates** > **Remote Certificate** (Система > Сертификаты > Удаленный сертификат). Имя по умолчанию — REMOTE_Cert_**N**, где **N** — это целое значение.

### <a name="perform-fortigate-command-line-configuration"></a>Выполнение настройки из командной строки FortiGate

Для выполнения следующих действий необходимо настроить URL-адрес выхода Azure. Этот URL-адрес содержит вопросительный знак (?). Для успешной отправки этого символа необходимо выполнить специальные действия. Их невозможно выполнить из консоли FortiGate CLI. Вместо этого установите сеанс SSH для приложения FortiGate с помощью средства PuTTY. Если приложение FortiGate является виртуальной машиной Azure, вы можете выполнить указанные ниже действия в последовательной консоли виртуальной машины Azure.

Для выполнения этих действий потребуются записанные ранее значения.

- Идентификатор сущности
- URL-адрес ответа
- URL-адрес выхода.
- URL-адрес входа Azure
- Идентификатор Azure AD
- URL-адрес выхода Azure
- Имя сертификата SAML в кодировке Base64 (REMOTE_Cert_N).

1. Установите сеанс SSH для приложения FortiGate и выполните вход с помощью учетной записи администратора FortiGate.
1. Выполните следующие команды:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > **URL-адрес выхода Azure** содержит знак `?`. Чтобы правильно указать URL-адрес в последовательной консоли FortiGate, необходимо нажать правильную последовательность клавиш. `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` — это как правило стандартный URL-адрес.
   >
   > Чтобы ввести URL-адрес выхода Azure в последовательной консоли, введите `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`.
   > 
   > Затем нажмите клавиши CTRL+V и вставьте остальную часть URL-адреса, чтобы завершить строку: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.

### <a name="configure-fortigate-for-group-matching"></a>Настройка FortiGate для сопоставления групп

В этом разделе вы настроите FortiGate для распознавания идентификатора объекта группы безопасности, в которой находится тестовый пользователь. Это позволит FortiGate принимать решения о доступе на основе членства в группе.

Для выполнения этих действий потребуется идентификатор объекта группы безопасности **FortiGateAccess**, созданной ранее.

1. Установите сеанс SSH для приложения FortiGate и выполните вход с помощью учетной записи администратора FortiGate.
1. Выполните следующие команды:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>Создание VPN-порталов и политики брандмауэра FortiGate

В этом разделе вы настроите VPN-порталы и политики брандмауэра FortiGate, предоставляющие доступ к группе безопасности **FortiGateAccess**, созданной ранее.

Обратитесь в  [группу поддержки FortiGate](mailto:tac_amer@fortinet.com), чтобы добавить VPN-порталы и политику брандмауэра на VPN-платформу FortiGate. Эти действия необходимо выполнить перед использованием единого входа.

## <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку VPN SSL FortiGate на Панели доступа, вы автоматически войдете в приложение VPN SSL FortiGate, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Чтобы обеспечить лучшее взаимодействие с пользователем, Майкрософт и FortiGate рекомендуют использовать VPN-клиент Fortinet и FortiClient.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать VPN SSL FortiGate с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
