---
title: Обзор Служб мультимедиа Azure с шаблоном лицензии Widevine | Документация Майкрософт
description: В этом разделе описан шаблон лицензии Widevine, который используется для настройки лицензий Widevine.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: dcfe9c1c3e12aa726f57db29db59732cceb87a69
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967462"
---
# <a name="widevine-license-template-overview"></a>Обзор шаблона лицензии Widevine 

Службы мультимедиа Azure позволяют шифровать содержимое с помощью **Google Widevine**. Службы мультимедиа также обеспечивают доставку лицензий Widevine. С помощью интерфейсов API Служб мультимедиа Azure можно настраивать лицензии Widevine. Когда проигрыватель пытается воспроизвести содержимое, защищенное с помощью Widevine, в службу доставки лицензий отправляется запрос на получение лицензии. Если служба лицензий утвердит запрос, служба выдаст лицензию. Лицензия отправляется клиенту и используется для расшифровки и воспроизведения указанного содержимого.

Запрос на лицензию Widevine форматируется как сообщение JSON.  

>[!NOTE]
> Можно создать пустое сообщение без значений — просто {}. Затем будет создан шаблон лицензии со значениями по умолчанию. Значения по умолчанию подходят для большинства случаев. В сценариях доставки лицензий на основе технологий Майкрософт следует всегда использовать значения по умолчанию. Если необходимо задать значения параметров provider (поставщик) и content_id (идентификатор содержимого), то поставщик должен совпадать с учетными данными Widevine.

    {  
       "payload":"<license challenge>",
       "content_id": "<content id>"
       "provider": "<provider>"
       "allowed_track_types":"<types>",
       "content_key_specs":[  
          {  
             "track_type":"<track type 1>"
          },
          {  
             "track_type":"<track type 2>"
          },
          …
       ],
       "policy_overrides":{  
          "can_play":<can play>,
          "can persist":<can persist>,
          "can_renew":<can renew>,
          "rental_duration_seconds":<rental duration>,
          "playback_duration_seconds":<playback duration>,
          "license_duration_seconds":<license duration>,
          "renewal_recovery_duration_seconds":<renewal recovery duration>,
          "renewal_server_url":"<renewal server url>",
          "renewal_delay_seconds":<renewal delay>,
          "renewal_retry_interval_seconds":<renewal retry interval>,
          "renew_with_usage":<renew with usage>
       }
    }

## <a name="json-message"></a>Сообщение JSON

| Name | Value | Описание |
| --- | --- | --- |
| payload |Строка в кодировке base64 |Запрос на лицензию, отправленный клиентом. |
| content_id |Строка в кодировке base64 |Идентификатор, используемый для получения идентификатора ключа и ключа содержимого для каждого content_key_specs.track_type. |
| provider |string |Используется для поиска ключей и политик содержимого. Если для доставки лицензий Widevine используется доставка ключей Майкрософт, этот параметр пропускается. |
| policy_name |string |Имя ранее зарегистрированной политики. Необязательный элемент. |
| allowed_track_types |enum |SD_ONLY или SD_HD. Контролирует, какие ключи содержимого включаются в лицензию. |
| content_key_specs |Массив структур JSON, см. раздел "Спецификации ключей содержимого".  |Более точный контроль возвращаемых ключей содержимого. Дополнительные сведения см. в разделе "Спецификации ключей содержимого". Указать можно только одно из значений allowed_track_types и content_key_specs. |
| use_policy_overrides_exclusively |Логическое значение: true или false |Используйте атрибуты политики, указанные в параметре policy_overrides, и пропустите все сохраненные ранее политики. |
| policy_overrides |Структура JSON, см. раздел "Переопределения политики". |Параметры политики для этой лицензии.  Если у этого ресурса есть предопределенная политика, будут использоваться указанные значения. |
| session_init |Структура JSON, см. раздел "Инициализация сеанса". |Необязательные данные передаются в лицензию. |
| parse_only |Логическое значение: true или false |Запрос на лицензию проанализирован, но лицензия не выдана. Но в ответе возвращаются значения из запроса на лицензию. |

## <a name="content-key-specs"></a>Спецификации ключей содержимого
Если существующая политика существует, нет необходимости указывать какие-либо значения в спецификации ключа содержимого. Существовавшая ранее политика, связанная с этим содержимым, используется для определения защиты выходных данных, например цифровой Защита содержимого с высокой пропускной способностью (HDCP) и службой копирования общей системы управления (CGMS-Management System). Если существующая политика не зарегистрирована на сервере лицензирования Widevine, поставщик содержимого может внедрить значения в запрос на лицензию.   

Каждое значение content_key_specs должно быть указано для всех записей независимо от параметра use_policy_overrides_exclusively. 

| Name | Value | Описание |
| --- | --- | --- |
| content_key_specs. track_type |string |Имя типа записи. Если content_key_specs указан в запросе лицензии, убедитесь, что все типы записей указаны явным образом. Невыполнение этого требования приведет к сбою воспроизведения последних 10 секунд. |
| content_key_specs  <br/> security_level |uint32 |Определяет требования к надежности клиента для воспроизведения. <br/> — Требуется программное шифрование методом белого ящика. <br/> — Требуются шифрование ПО и скрытый декодер. <br/> — Материал ключа и операции шифрования должны быть выполнены в резервной доверенной аппаратной среде выполнения. <br/> — Операции шифрования и расшифровки содержимого должны быть выполнены в резервной доверенной аппаратной среде выполнения.  <br/> — Шифрование, расшифровка и обработка всех носителей (сжатых и несжатых) должны быть выполнены в резервной доверенной аппаратной среде выполнения. |
| content_key_specs <br/> required_output_protection.hdc |Строка, одна из: HDCP_NONE, HDCP_V1, HDCP_V2 |Указывает, требуется ли HDCP. |
| content_key_specs <br/>key |Base64-<br/>Base64 |Ключ содержимого, используемый для этой записи. Если указано, требуется track_type или key_id. Поставщик содержимого с помощью этого параметра может вставить ключ содержимого для этой дорожки вместо того, чтобы сервер лицензирования Widevine создал или нашел ключ. |
| content_key_specs.key_ID |Двоичные данные строки в кодировке base64, 16 байт |Уникальный идентификатор ключа. |

## <a name="policy-overrides"></a>Переопределения политики
| Name | Value | Описание |
| --- | --- | --- |
| policy_overrides&#46;can_play |Логическое значение: true или false |Указывает, допускается ли воспроизведение содержимого. Значение по умолчанию — false. |
| policy_overrides&#46;can_persist |Логическое значение: true или false |Указывает, что лицензия может быть сохранена в энергонезависимом хранилище для автономного использования. Значение по умолчанию — false. |
| policy_overrides&#46;can_renew |Логическое значение: true или false |Указывает, что разрешено продление лицензии. Если указано значение "true", лицензию можно расширить с помощью периодического сигнала. Значение по умолчанию — false. |
| policy_overrides&#46;license_duration_seconds |int64 |Указывает интервал времени для данной конкретной лицензии. Значение 0 указывает на неограниченное время. Значение по умолчанию — 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Указывает интервал времени, в течение которого разрешено воспроизведение. Значение 0 указывает на неограниченное время. Значение по умолчанию — 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |Окно просмотра времени после начала воспроизведения в течение срока действия лицензии. Значение 0 указывает на неограниченное время. Значение по умолчанию — 0. |
| policy_overrides&#46;renewal_server_url |string |Все запросы на продление для этой лицензии направляются на указанный URL-адрес. Это поле используется, только если can_renew имеет значение true. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Число секунд после license_start_time перед первой попыткой продления. Это поле используется, только если can_renew имеет значение true. Значение по умолчанию — 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Определяет задержку в секундах между последующими запросами на продление лицензии (в случае сбоя). Это поле используется, только если can_renew имеет значение true. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Интервал времени, в течение которого разрешено продолжать воспроизведение при неудачной попытке продления лицензии из-за внутренних проблем с сервером лицензирования. Значение 0 указывает на неограниченное время. Это поле используется, только если can_renew имеет значение true. |
| policy_overrides&#46;renew_with_usage |Логическое значение: true или false |Указывает, что лицензия отправлена на продление в начале использования. Это поле используется, только если can_renew имеет значение true. |

## <a name="session-initialization"></a>Инициализация сеанса
| Name | Value | Описание |
| --- | --- | --- |
| provider_session_token |Строка в кодировке base64 |Этот маркер сеанса передается обратно в лицензию и существует в рамках последующих операций продления. Маркер сеанса не сохраняется вне сеансов. |
| provider_client_token |Строка в кодировке base64 |Маркер клиента для отправки обратно в ответе лицензии. Если запрос лицензии содержит маркер клиента, это значение игнорируется. Маркер клиента сохраняется вне сеансов лицензии. |
| override_provider_client_token |Логическое значение: true или false |Если задано значение "false" и запрос лицензии содержит маркер клиента, используйте маркер из запроса, даже если в этой структуре был указан маркер клиента. Если задано значение "true", всегда используйте маркер, заданный в этой структуре. |

## <a name="configure-your-widevine-license-with-net"></a>Настройка лицензий Widevine с помощью .NET 

Службы мультимедиа предоставляют класс, который позволяет настроить лицензию Widevine. Чтобы создать лицензию, передайте JSON в [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Чтобы настроить шаблон, вы можете:

### <a name="directly-construct-a-json-string"></a>Непосредственно создать строку JSON.

Этот метод может быть подвержен ошибкам. Рекомендуется использовать другой метод, описанный в разделе [Обзор шаблона лицензии Widevine](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a id="classes"></a> Определение необходимых классов и выполнение сериализации в JSON

#### <a name="define-classes"></a>Определение классов

В следующем примере показаны определения классов, которые сопоставляются со схемой JSON Widevine. Экземпляры классов можно создать перед их сериализацией в строку JSON.  

```csharp
public class PolicyOverrides
{
    public bool CanPlay { get; set; }
    public bool CanPersist { get; set; }
    public bool CanRenew { get; set; }
    public int RentalDurationSeconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int PlaybackDurationSeconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int LicenseDurationSeconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
}

public class ContentKeySpec
{
    public string TrackType { get; set; }
    public int SecurityLevel { get; set; }
    public OutputProtection RequiredOutputProtection { get; set; }
}

public class OutputProtection
{
    public string HDCP { get; set; }
}

public class WidevineTemplate
{
    public string AllowedTrackTypes { get; set; }
    public ContentKeySpec[] ContentKeySpecs { get; set; }
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Настройка лицензии

Используйте классы, определенные в предыдущем разделе, чтобы создать JSON для настройки [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="additional-notes"></a>Дополнительные замечания

* Widevine — это служба, предоставляемая Google Inc. и подпадает под условия обслуживания и политики конфиденциальности Google, Inc.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о возможностях [защиты с помощью технологии DRM](protect-with-drm.md).
