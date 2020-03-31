---
title: Файл конфигурации Android MSAL Azure
titleSuffix: Microsoft identity platform
description: Обзор файла конфигурации Библиотеки подлинности Android Microsoft (MSAL), который представляет конфигурацию приложения в Active Directory Azure.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050378"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Файл конфигурации библиотеки аутентификации Android Microsoft

Библиотека подлинности Android Microsoft (MSAL) поставляется с [конфигурацией JSON по умолчанию,](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) которую вы настраиваете для определения поведения приложения для публичного клиента для таких вещей, как власть по умолчанию, которую власти вы будете использовать, и так далее.

Эта статья поможет вам понять различные параметры в файле конфигурации и как указать файл конфигурации для использования в вашем приложении на основе MSAL.

## <a name="configuration-settings"></a>Параметры конфигурации

### <a name="general-settings"></a>Общие параметры

| Свойство | Тип данных | Обязательно | Примечания |
|-----------|------------|-------------|-------|
| `client_id` | Строка | Да | Идентификатор клиента приложения со [страницы регистрации приложения](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Строка | Да | Перенаправление URI вашего приложения со [страницы регистрации приложения](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `authorities` | Список\<органа> | нет | Список органов, необходимых вашему приложению |
| `authorization_user_agent` | АвторизацияAgent (enum) | нет | Возможные `DEFAULT`значения: `BROWSER`,`WEBVIEW` |
| `http` | HttpConfiguration | нет | Настройка `HttpUrlConnection` `connect_timeout` и`read_timeout` |
| `logging` | РегистрацияКонфигурация | нет | Определяет уровень детализации регистрации. Дополнительные конфигурации `pii_enabled`включают в себя: , `log_level`который `ERROR`принимает `WARNING` `INFO`boolean `VERBOSE`значение, и , который принимает, , или . |

### <a name="client_id"></a>client_id

Идентификатор клиента или идентификатор приложения, созданный при регистрации приложения.

### <a name="redirect_uri"></a>redirect_uri

Перенаправление URI, которое вы зарегистрировали при регистрации заявления. Если перенаправление URI на брокерское приложение, обратитесь к [Redirect URI для приложений для публичных клиентов,](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) чтобы убедиться, что вы используете правильный формат перенаправления URI для вашего приложения брокера.

### <a name="authorities"></a>Власти

Список органов власти, которые известны и доверены вам. В дополнение к властям, перечисленным здесь, MSAL также запрашивает Microsoft, чтобы получить список облаков и органов, известных Microsoft. В этом списке органов укажите тип полномочий и любые дополнительные дополнительные параметры, такие как `"audience"`, которые должны согласовываться с аудиторией вашего приложения на основе регистрации вашего приложения. Ниже приводится пример списка органов власти:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Карта AAD власти & аудитории в конечных точках платформы идентификации Майкрософт

| Тип | Аудитория | Tenant ID | Authority_Url | В результате конечная точка | Примечания |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common`является псевдонимом арендатора, где находится учетная запись. Например, конкретный арендатор Active Directory Azure или система учетных записей Майкрософт. |
| AAD | Лазурадмюрг | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Приобрести токен могут только счета, присутствующие в contoso.com. Любой проверенный домен или графический интерфейс клиента может использоваться в качестве идентификатора арендатора. |
| AAD | ЛазурадМноведМногены | | | `https://login.microsoftonline.com/organizations` | С этой конечной точкой можно использовать только учетные записи Active Directory Azure. Учетные записи Майкрософт могут быть членами организаций. Чтобы приобрести токен с помощью учетной записи Майкрософт для ресурса в организации, укажите организатора арендатора, от которого вы хотите токен. |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Только учетные записи Майкрософт могут использовать эту конечную точку. |
| B2C | | | Посмотреть конечную точку | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Приобрести токен могут только счета, присутствующие в contoso.onmicrosoft.com арендатор. В этом примере политика B2C является частью пути URL-адреса органа. |

> [!NOTE]
> Проверка органа не может быть включена и отключена в MSAL.
> Органы власти либо известны вам как разработчик, как указано через конфигурацию или известны Microsoft через метаданные.
> Если MSAL получает запрос на токен неизвестному `MsalClientException` органу, результат типа. `UnknownAuthority`

#### <a name="authority-properties"></a>Свойства органа

| Свойство | Тип данных  | Обязательно | Примечания |
|-----------|-------------|-----------|--------|
| `type` | Строка | Да | Зеркала аудитории или учетной записи типа приложения цели. Возможные значения: `AAD`,`B2C` |
| `audience` | Объект | нет | Применяется только при`AAD`типе. Укажите личность, на которые нацелено ваше приложение. Используйте значение регистрации приложения |
| `authority_url` | Строка | Да | Требуется только`B2C`при типе . Укажите URL-адрес или политику, которые должно использоваться приложением  |
| `default` | Логическое | Да | Один `"default":true` требуется, когда указан один или несколько органов власти. |

#### <a name="audience-properties"></a>Свойства аудитории

| Свойство | Тип данных  | Обязательно | Примечания |
|-----------|-------------|------------|-------|
| `type` | Строка | Да | Определяет аудиторию, на которая хочет ориентироваться приложение. Возможные `AzureADandPersonalMicrosoftAccount`значения: `PersonalMicrosoftAccount` `AzureADMultipleOrgs`, ,`AzureADMyOrg` |
| `tenant_id` | Строка | Да | Требуется `"type":"AzureADMyOrg"`только тогда, когда . Необязательно `type` для других значений. Это может быть домен `contoso.com`арендатора, такой `72f988bf-86f1-41af-91ab-2d7cd011db46`как, или идентификатор арендатора, например) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Указывает, использовать ли встроенный веб-визу или браузер по умолчанию на устройстве при подписании учетной записи или авторизации доступа к ресурсу.

Возможные значения:
- `DEFAULT`: Предпочитает системный браузер. Использует встроенный веб-вид, если браузер недоступен на устройстве.
- `WEBVIEW`: Используйте встроенный веб-вид.
- `BROWSER`: Использует браузер по умолчанию на устройстве.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Для клиентов, поддерживающих `true`несколько национальных облаков, укажите. Платформа идентификации Майкрософт автоматически перенаправит на правильное национальное облако во время авторизации и выкупа токенов. Вы можете определить национальное облако вписанных счетов, `AuthenticationResult`изучив орган, связанный с . Обратите внимание, что `AuthenticationResult` не укажите национальный адрес конечной точки облака ресурса, для которого вы запрашиваете токен.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Булеан, указывающий, используете ли вы совместимый брокер Microsoft Identity в брокере, перенаправляющий URI. Установите, `false` если вы не хотите использовать брокера в вашем приложении.

Если вы используете AAD органа с `"MicrosoftPersonalAccount"`аудиторией установить, брокер не будет использоваться.

### <a name="http"></a>http

Настроите глобальные настройки для тайм-аутов HTTP, таких как:

| Свойство | Тип данных | Обязательно | Примечания |
| ---------|-----------|------------|--------|
| `connect_timeout` | INT | нет | Время в миллисекундах |
| `read_timeout` | INT | нет | Время в миллисекундах |

### <a name="logging"></a>Ведение журналов

Следующие глобальные настройки для ведения журнала:

| Свойство | Тип данных  | Обязательно | Примечания |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | Логическое | нет | Следует ли излучать персональные данные |
| `log_level`   | Логическое | нет | Какие сообщения журнала на выходе |
| `logcat_enabled` | Логическое | нет | Ли выход для регистрации кошки в дополнение к интерфейсу регистрации |

### <a name="account_mode"></a>account_mode

Укажите, сколько учетных записей может быть использовано в приложении одновременно. Вы можете выбрать

- `MULTIPLE` (по умолчанию)
- `SINGLE`

Построение `PublicClientApplication` режима использования учетной записи, не совпадающего с этим параметром, приведет к исключению.

Для получения дополнительной информации о различиях [Single and multiple account apps](single-multi-account.md)между одной и несколькими учетными записями см.

### <a name="browser_safelist"></a>browser_safelist

Разрешительное список браузеров, совместимых с MSAL. Эти браузеры правильно обрабатывать перенаправляет на пользовательские намерения. Вы можете добавить к этому списку. Значение по умолчанию предусмотрено в конфигурации по умолчанию, показанной ниже.
``
## <a name="the-default-msal-configuration-file"></a>Файл конфигурации MSAL по умолчанию

Ниже отображается конфигурация MSAL по умолчанию, которая поставляется с MSAL. Вы можете увидеть последнюю версию на [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Эта конфигурация дополняется значениями, которые вы предоставляете. Значения, которые вы предоставляете, переопределяют значения по умолчанию.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Пример базовой конфигурации

Следующий пример иллюстрирует базовую конфигурацию, которая определяет идентификатор клиента, перенаправляет URI, зарегистрирован ли брокер, и список органов власти.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Как использовать файл конфигурации

1. Создайте файл конфигурации. Мы рекомендуем создать пользовательский файл `res/raw/auth_config.json`конфигурации в . Но вы можете положить его в любом месте, что вы хотите.
2. Сообщите MSAL, где искать конфигурацию `PublicClientApplication`при построении . Пример:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
