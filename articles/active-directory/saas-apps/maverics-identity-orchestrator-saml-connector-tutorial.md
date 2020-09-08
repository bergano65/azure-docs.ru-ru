---
title: Руководство по интеграции единого входа Azure Active Directory с Maverics Identity Orchestrator SAML Connector | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: 116ee7c8db3070a667c21a052bec739fd397a2dd
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88825568"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Руководство по интеграции единого входа Azure AD с Maverics Identity Orchestrator SAML Connector

Strata предоставляет простой способ интеграции локальных приложений с Azure Active Directory (Azure AD) для проверки подлинности и управления доступом.

В этой статье содержатся сведения о настройке Maverics Identity Orchestrator для выполнения следующих задач:
* Постепенный перенос пользователей из локальной системы идентификации в Azure AD во время входа в систему устаревшего локального приложения.
* Направление запросов на вход в систему из устаревшего продукта управления веб-доступом, такого как CA SiteMinder или Oracle Access Manager, в Azure AD.
* Выполнение проверки подлинности пользователей в локальных приложениях, которые защищены с помощью заголовков HTTP или частных файлов cookie сеанса, после проверки подлинности пользователя в Azure AD.

Strata предоставляет программное обеспечение, которое развертывается локально или в облаке. Оно позволяет выполнить обнаружение, подключение и оркестрацию между поставщиками удостоверений для создания распределенного управления удостоверениями для гибридных и многооблачных предприятий.

В этом учебнике показано, как перенести локальное веб-приложение, защищенное устаревшим продуктом управления веб-доступом (CA SiteMinder), чтобы использовать Azure AD для проверки подлинности и контроля доступа. Ниже перечислены основные шаги.
1. Установите Maverics Identity Orchestrator.
2. Зарегистрируйте свое корпоративное приложение в Azure AD и настройте его для использования Maverics Azure AD SAML Zero Code Connector для единого входа на основе SAML.
3. Интегрируйте Maverics с SiteMinder и пользовательским хранилищем протокола LDAP.
4. Настройте Azure Key Vault и Maverics для использования его в качестве своего поставщика управления секретами.
5. Продемонстрируйте миграцию пользователей и абстракцию сеанса с помощью Maverics для предоставления доступа к локальному веб-приложению Java.

Дополнительные инструкции по установке и настройке см. на [веб-сайте Strata](https://strata.io/docs).

## <a name="prerequisites"></a>Обязательные условия

- Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
- Подписка Mavericks Identity Orchestrator SAML Connector с поддержкой единого входа. Чтобы получить программное обеспечение Mavericks, свяжитесь с [торговым представителем Strata](mailto:sales@strata.io).

## <a name="install-maverics-identity-orchestrator"></a>Установка Maverics Identity Orchestrator

Чтобы начать установку Maverics Identity Orchestrator, ознакомьтесь с [инструкциями по установке](https://strata.io/docs).

### <a name="system-requirements"></a>Требования к системе
* Поддерживаемые операционные системы
  * RHEL 7 или более поздней версии;
  * CentOS 7 или более поздней версии;

* Зависимости
  * systemd

### <a name="installation"></a>Установка

1. Получите последнюю версию пакета RPM Maverics (RedHat Package Manager). Скопируйте пакет в систему, в которой вы хотите установить программное обеспечение Maverics.

2. Установите пакет Maverics, указав свое имя файла вместо `maverics.rpm`.

    `sudo rpm -Uvf maverics.rpm`

3. После установки Maverics будет работать как служба под `systemd`. Чтобы убедиться, что служба запущена, выполните указанную ниже команду.

    `sudo systemctl status maverics`

По умолчанию Maverics устанавливается в каталог */usr/local/bin*.

После установки Maverics в каталоге */etc/maverics* создается файл по умолчанию *maverics.yaml*. Прежде чем вы отредактируете конфигурацию, включив в нее `workflows` и `connectors`, ваш файл конфигурации будет выглядеть следующим образом:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>Варианты настройки
### <a name="version"></a>Версия
В поле `version` указывается используемая версия файла конфигурации. Если она не указана, будет использоваться самая последняя версия конфигурации.

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
`listenAddress` объявляет, какой адрес будет прослушивать Orchestrator. Если раздел узла адреса пуст, Orchestrator будет прослушивать все доступные одноадресные системы и произвольные IP-адреса локальной системы. Если раздел порта адреса пуст, номер порта выбирается автоматически.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

Поле `tls` объявляет карту объектов протокола TLS. Объекты TLS могут использоваться соединителями и сервером Orchestrator. Все доступные варианты TLS см. в документации по пакету `transport`.

При использовании системы единого входа на основе SAML для Microsoft Azure требуется взаимодействие, выполняемое по протоколу TLS. Дополнительные сведения о создании сертификатов Let's Encrypt см. на [этом веб-сайте](https://letsencrypt.org/getting-started/).

Ключ `maverics` зарезервирован для сервера Orchestrator. Все остальные ключи доступны и могут использоваться для внедрения объекта TLS в данный соединитель.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Включаемые файлы

`connectors` и `workflows` могут быть определены в их собственных отдельных файлах конфигурации. На них можно ссылаться в файле *maverics.yaml* с помощью `includeFiles`, как показано в следующем примере:

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

В этом учебнике используется один файл конфигурации *maverics.yaml*.

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>Использование Azure Key Vault в качестве поставщика секретов

### <a name="manage-secrets"></a>Управление секретами

Для загрузки секретов Maverics может интегрироваться с различными решениями по управлению секретами. Текущие интеграции включают файл, Hashicorp Vault и Azure Key Vault. Если решение для управления секретами не указано, Maverics по умолчанию будет их загружать в виде обычного текста из файла *maverics.yaml*.

Чтобы объявить значение как секрет в файле конфигурации *maverics.yaml*, заключите секрет в угловые скобки:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>Загрузка секретов из файла

1. Чтобы загрузить секреты из файла, добавьте переменную среды `MAVERICS_SECRET_PROVIDER` в файл */etc/maverics/maverics.env* с помощью:

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. Затем перезапустите службу Maverics, выполнив следующую команду:

   `sudo systemctl restart maverics`

Содержимое файла *secrets.yaml* можно заполнить любым количеством `secrets`.

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Настройка хранилища ключей Azure

Вы можете настроить хранилище ключей Azure с помощью портала Azure или Azure CLI.

**Использование портала Azure**
1. Войдите на [портал Azure](https://portal.azure.com).
1. [Создайте хранилище ключей](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault).
1. [Добавьте секреты в хранилище ключей](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault).
1. [Зарегистрируйте приложение в Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).
1. [Разрешите приложению использовать секрет](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault).

**Использование Azure CLI**

1. Откройте [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и введите следующую команду:

    ```shell
    az login
    ```

1. Создайте хранилище ключей, выполнив следующую команду:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. Добавьте секреты в хранилище ключей, выполнив следующую команду:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. Зарегистрируйте приложение в Azure AD, выполнив следующую команду:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. Разрешите приложению использовать секрет, выполнив следующую команду:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Чтобы загрузить секреты из хранилища ключей Azure, задайте переменную среды `MAVERICS_SECRET_PROVIDER`в файле */etc/maverics/maverics.env* с учетными данными, найденными в файле *azure-credentials.json*, используя следующий формат:
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Перезапустите службу Maverics: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Настройка приложения в Azure AD для единого входа на основе SAML

1. В клиенте Azure AD перейдите в раздел **Корпоративные приложения**, найдите приложение **Maverics Identity Orchestrator SAML Connector** и выберите его.

1. На панели **Properties** (Свойства) страницы Maverics Identity Orchestrator SAML Connector установите для параметра **User assignment required?** (Требуется ли назн. польз.?) значение **No** (Нет), чтобы недавно перенесенные пользователи могли использовать приложение.

1. На панели **Overview** (Обзор) страницы Maverics Identity Orchestrator SAML Connector Overview выберите **Set up single sign-on** (Настройка единого входа), а затем — **SAML**.

1. На панели **SAML-based sign on** (Вход на основе SAML) страницы Maverics Identity Orchestrator SAML Connector измените **базовую конфигурацию SAML**, нажав кнопку **редактирования** (значок карандаша).

   ![Снимок экрана: кнопка редактирования базовой конфигурации SAML.](common/edit-urls.png)

1. Введите **идентификатор сущности**, указав URL-адрес в следующем формате: `https://<SUBDOMAIN>.maverics.org`. Идентификатор сущности должен быть уникальным для всех приложений в клиенте. Сохраните введенное значение, чтобы включить его в конфигурацию Maverics.

1. Введите **URL-адрес ответа** в следующем формате: `https://<AZURECOMPANY.COM>/<MY_APP>/`. 

1. Введите **URL-адрес для входа** в следующем формате: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>`. 

1. Щелкните **Сохранить**.

1. В разделе **Сертификат подписи SAML** нажмите кнопку **Копировать**, чтобы скопировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Снимок экрана: "Сертификат подписи SAML" с кнопкой "Копировать".](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Настройка Maverics Identity Orchestrator Azure AD SAML Connector

Maverics Identity Orchestrator Azure AD Connector поддерживает OpenID Connect и SAML Connect. Чтобы настроить соединитель, выполните указанные ниже действия. 

1. Чтобы включить единый вход на основе SAML, установите `authType: saml`.

1. Создайте значение для `samlMetadataURL` в следующем формате: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`.

1. Определите URL-адрес, на который Azure будет перенаправлять пользователей обратно в приложении после входа в систему с учетными данными Azure. Используйте следующий формат: `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`.

1. Скопируйте значение из указанного выше настроенного идентификатора сущности: `samlEntityID: https://<SUBDOMAIN>.maverics.org`.

1. Скопируйте значение из URL-адреса ответа, которое Azure AD будет использовать для размещения ответа SAML: `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`.

1. Создайте ключ подписи JSON Web Token (JWT), используемый для защиты информации сеанса Maverics Identity Orchestrator, с помощью [инструмента OpenSSL](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Скопируйте ответ в свойство конфигурации `jwtSigningKey`: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`.

## <a name="attributes-and-attribute-mapping"></a>Атрибуты и сопоставление атрибутов
Сопоставление атрибутов используется для определения сопоставления атрибутов пользователя из исходного локального каталога пользователей в клиенте Azure AD после настройки пользователей.

Атрибуты определяют данные пользователя, которые могут быть возвращены приложению в заявке, переданы в файлы cookie сеанса или приложению в переменных заголовка HTTP.

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>Настройка YAML-файла Maverics Identity Orchestrator Azure AD SAML Connector

Конфигурация Maverics Identity Orchestrator Azure AD Connector будет выглядеть следующим образом:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-an-azure-ad-tenant"></a>Миграция пользователей в клиент Azure AD

Выполните эту конфигурацию для постепенной миграции пользователей из продукта управления доступом в Интернет, такого как CA SiteMinder, Oracle Access Manager или IBM Tivoli. Их также можно перенести из службы каталогов протокола LDAP или базы данных SQL.

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Настройка разрешения приложения в Azure AD для создания пользователей.

1. В своем клиенте Azure AD перейдите в раздел `App registrations` и выберите приложение **Mavericks Identity Orchestrator SAML Connector**.

1. На странице **Maverics Identity Orchestrator SAML Connector на панели Certificates & secrets (Сертификаты и секреты)** выберите `New client secret`, а затем — вариант истечения срока. Нажмите кнопку **Копировать**, чтобы скопировать секрет и сохранить его на своем компьютере.

1. На странице **Maverics Identity Orchestrator SAML Connector на панели API permissions (Разрешения API)** выберите **Add permission** (Добавить разрешение), а затем в разделе **Request API permissions** (Запрос разрешений API) выберите **Microsoft Graph** и **Application permissions** (Разрешения приложения). 

1. На следующем экране выберите **User.ReadWrite.All**, а затем — **Add permissions** (Добавить разрешения). 

1. Вернувшись на панель**API permissions** (Разрешения API) выберите **Grant admin consent** (Предоставление согласия администратора).

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>Настройка YAML-файла Maverics Identity Orchestrator SAML Connector для миграции пользователей

Чтобы включить рабочий процесс миграции пользователей, добавьте эти дополнительные свойства в файл конфигурации:
1. Введите **URL-адрес Azure Graph** в следующем формате: `graphURL: https://graph.microsoft.com`.
1. Введите **URL-адрес маркера OAuth** в следующем формате: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`.
1. Введите созданный ранее секрет клиента в следующем формате: `oauthClientSecret: <CLIENT SECRET>`.


Окончательный файл конфигурации Maverics Identity Orchestrator Azure AD Connector будет выглядеть следующим образом:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>Настройка Maverics Zero Code Connector для SiteMinder

Соединитель SiteMinder используется для миграции пользователей в клиент Azure AD. Он позволяет выполнить пользователям вход в устаревшие локальные приложения, защищенные SiteMinder, с использованием только что созданных удостоверений и учетных данных Azure AD.

В рамках этого учебника SiteMinder был настроен для защиты устаревшего приложения с помощью проверки подлинности на основе форм и использования файла cookie `SMSESSION`. Для интеграции с приложением, которое использует информацию о проверке подлинности и сеансе через HTTP-заголовки, вам необходимо добавить конфигурацию эмуляции заголовка в соединитель.

В этом примере атрибут `username` сопоставляется с HTTP-заголовком `SM_USER`:

```yaml
  headers:
    SM_USER: username
```

Задайте `proxyPass` в расположение, в которое направляются запросы. Обычно это узел защищенного приложения.

`loginPage` должен соответствовать URL-адресу формы входа, которая в настоящее время используется SiteMinder при перенаправлении пользователей для проверки подлинности.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>Настройка Configure the Maverics Zero Code Connector для LDAP

Если приложения защищены продуктом управления веб-доступом (WAM), таким как SiteMinder, идентификаторы и атрибуты пользователей обычно хранятся в каталоге LDAP.

Эта конфигурация соединителя демонстрирует подключение к каталогу LDAP. Соединитель настроен в качестве пользовательского хранилища для SiteMinder, чтобы во время рабочего процесса миграции можно было собрать правильную информацию о профиле пользователя и создать соответствующего пользователя в Azure AD.

* `baseDN` указывает место в каталоге, в котором выполняется поиск LDAP.

* `url` — это адрес и порт сервера LDAP для подключения.

* `serviceAccountUsername` — это имя пользователя, используемое для подключения к серверу LDAP, обычно выражаемое как Bind DN (например, `CN=Directory Manager`).

* `serviceAccountPassword` — пароль, используемый для подключения к серверу LDAP. Это значение хранится в ранее настроенном экземпляре хранилища ключей Azure.  

* `userAttributes` определяет список связанных с пользователем атрибутов для запроса. Эти атрибуты позже сопоставляются с соответствующими атрибутами Azure AD.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

### <a name="configure-the-migration-workflow"></a>Настройка рабочего процесса миграции

Конфигурация рабочего процесса миграции определяет, как Maverics будет переносить пользователей из SiteMinder или LDAP в Azure AD.

Этот рабочий процесс:
- Использует соединитель SiteMinder для входа в SiteMinder через прокси-сервер. Учетные данные пользователя проверяются посредством проверки подлинности SiteMinder и затем передаются на последующие этапы рабочего процесса.
- Извлекает атрибуты профиля пользователя из хранилища пользователей SiteMinder.
- Создает запрос к API Microsoft Graph для создания пользователя в клиенте Azure AD.

Для настройки рабочего процесса миграции выполните указанные ниже действия.

1. Присвойте рабочему процессу имя (например, **Миграция с SiteMinder на Azure AD**).
1. Укажите `endpoint`, представляющий собой HTTP-путь, по которому отображается рабочий процесс, запускающий `actions` этого рабочего процесса в ответ на запросы. `endpoint` обычно соответствует приложению с прокси-сервером (например, `/my_app`). Значение должно включать как начальную, так и конечную косую черту.
1. Добавьте соответствующий объект `actions` в рабочий процесс.

   а. Определите метод `login` для соединителя SiteMinder. Значение соединителя должно соответствовать значению имени в конфигурации соединителя.

   b. Определите метод `getprofile` для соединителя LDAP.

   c.  Определите метод `createuser` для соединителя AzureAD.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>Проверка рабочего процесса миграции

1. Запустите службу Maverics, если она еще не запущена, выполнив команду:  

   `sudo systemctl start maverics`

1. Перейдите к URL-адресу входа в систему через прокси-сервер: `http://host.company.com/my_app`.
1. Предоставьте учетные данные пользователя, используемые для входа в приложение, защищенное SiteMinder.
4. Выберите **Домашняя страница** > **Users | All Users** (Пользователи | Все пользователи), чтобы проверить, создан ли в клиенте Azure AD пользователь.  

### <a name="configure-the-session-abstraction-workflow"></a>Настройка рабочего процесса абстракции сеанса

Рабочий процесс абстракции сеанса перемещает проверку подлинности и контроль доступа для устаревшего локального веб-приложения в клиенте Azure AD.

Соединитель Azure использует метод `login` для перенаправления пользователя на URL-адрес для входа при условии, что сеанс не существует.

После проверки подлинности созданный в результате маркер сеанса передается в Maverics. Метод `emulate` соединителя SiteMinder используется для имитации сеанса на основе файлов cookie или сеанса на основе заголовка, а затем для дополнения запроса любыми атрибутами, необходимыми для применения.

1. Присвойте рабочему процессу имя (например, **SiteMinder Session Abstraction**).
1. Укажите объект `endpoint`, который соответствует приложению с прокси-сервером. Значение должно включать как начальную, так и конечную косую черту (например, `/my_app/`).
1. Добавьте соответствующий объект `actions` в рабочий процесс.

   а. Определите метод `login` для соединителя Azure. Значение `connector` должно соответствовать значению `name` в конфигурации соединителя.

   b. Определите метод `emulate` для соединителя SiteMinder.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>Проверка рабочего процесса абстракции сеанса

1. Перейдите к URL-адресу приложения с прокси-сервером: `https://<AZURECOMPANY.COM>/<MY_APP>`. 
    
    Вы будете перенаправлены на страницу входа в систему через прокси-сервер.

1. Введите учетные данные пользователя Azure AD.

   Вы должны быть перенаправлены в приложение, как будто проверка подлинности выполнялась непосредственно SiteMinder.
