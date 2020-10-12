---
title: Протокол безопасности на основе виртуализации (VBS) для аттестации Azure
description: Протокол аттестации VBS
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5cc3b5fb7ca38df196119de12d346f5d0346b58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91346083"
---
# <a name="virtualization-based-security-vbs-attestation-protocol"></a>Протокол аттестации на основе виртуализации (VBS) 

Для Microsoft Azure аттестации, обеспечивающей надежную защиту данных, которые передаются в отчеты, необходимо создать цепочку доверия от встроенного по к запуску низкоуровневой оболочки и защищенному ядру. Чтобы добиться этой аттестации Azure, необходимо подтвердить состояние загрузки компьютера, прежде чем можно будет установить отношения доверия в Secure анклава. Двоичные файлы операционной системы, гипервизора и защищенного ядра должны быть подписаны правильными официальными центрами Майкрософт и настроены безопасным образом. После установления отношения доверия между доверенный платформенный модуль (TPM) (TPM) и работоспособностью гипервизора можно доверять VBS ИДКС, указанному в журнале загрузки. Благодаря этому можно проверить, что пара ключей была создана анклава и Mint отчет аттестации, который привязывает доверие к этому ключу и содержит другие утверждения, такие как свойства уровня безопасности и аттестации загрузки.

## <a name="protocol-messages"></a>Сообщения протокола

### <a name="init-message"></a>Сообщение о инициализации

#### <a name="direction"></a>Direction

Клиент-> аттестация Azure

#### <a name="payload"></a>Payload

```
{
  "type": "aikcert"
}
```

"Type" (строка ASCII): представляет тип запрошенной аттестации. В настоящее время поддерживается только "аикцерт".

### <a name="challenge-message"></a>Сообщение запроса

#### <a name="direction"></a>Direction

Аттестация Azure — > клиент

#### <a name="payload"></a>Payload

```
{

  "challenge": "<BASE64URL(CHALLENGE)>",
  
  "service_context": "<BASE64URL(SERVICECONTEXT)>"
  
}
```

**запрос** (BASE64URL (октет)): случайное значение, выданное службой.

**service_context** (BASE64URL (октеты)): непрозрачный, зашифрованный контекст, созданный службой, который включает, в частности, и время истечения срока действия этой задачи.


### <a name="request-message"></a>Сообщение запроса

#### <a name="direction"></a>Direction

Клиент-> аттестация Azure 

#### <a name="payload"></a>Payload

```
{

  "request": "<JWS>"
  
}
```

**запрос** (ЖВС): запрос состоит из структуры JSON Web Signature (ЖВС). Ниже показаны полезные данные защищенного заголовка ЖВС и ЖВС. Как и в любой структуре ЖВС, окончательное значение состоит из следующих компонентов:

BASE64URL (UTF8 (защищенный заголовок ЖВС)) | | '.' ||

BASE64URL (полезные данные ЖВС) | | '.' ||

BASE64URL (сигнатура ЖВС)

##### <a name="jws-protected-header"></a>ЖВС, защищенный заголовок

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>Полезные данные ЖВС

Полезные данные ЖВС могут иметь тип "базовый" или "VBS". Базовый используется, когда свидетельство аттестации не включает данные VBS.

Простой пример

``` 
{
  "att_type": "basic",
  "att_data": {
    "rp_id": "<URL>",
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
    "challenge": "<BASE64URL(CHALLENGE)>",
    "tpm_att_data": {
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
      "aik_pub": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "current_claim": "<BASE64URL(CURRENTCLAIM)>",
      "boot_claim": "<BASE64URL(BOOTCLAIM)>"
    },
    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
    "attest_key": {
      "kty": "RSA",
      "n": "<Base64urlUInt(MODULUS)>",
      "e": "<Base64urlUInt(EXPONENT)>"
    },
    "custom_claims": [
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      },
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      }
    ],
    "service_context": "<BASE64URL(SERVICECONTEXT)>"
  }
}
```

Пример VBS

``` 
{
  "att_type": "vbs",
  "att_data": {
    "report_signed": {
      "rp_id": "<URL>",
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
      "challenge": "<BASE64URL(CHALLENGE)>",
      "tpm_att_data": {
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
        "aik_pub": {
          "kty": "RSA",
          "n": "<Base64urlUInt(MODULUS)>",
          "e": "<Base64urlUInt(EXPONENT)>"
        },
        "current_claim": "<BASE64URL(CURRENTCLAIM)>",
        "boot_claim": "<BASE64URL(BOOTCLAIM)>"
      },
      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
      "attest_key": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "custom_claims": [
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        },
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        }
      ],
      "service_context": "<BASE64URL(SERVICECONTEXT)>"
    },
    "vbs_report": "<BASE64URL(REPORT)>"
  }
}
``` 

**rp_id** (стрингорури): идентификатор проверяющей стороны. Используется службой в вычислении утверждения идентификатора компьютера

**rp_data** (BASE64URL (октеты)): непрозрачные данные, переданные проверяющей стороной. Это обычно используется проверяющей стороной как nonce, чтобы гарантировать актуальность отчета.

**запрос** (BASE64URL (октет)): случайное значение, выданное службой

**tpm_att_data**: данные аттестации, связанные с TPM

- **srtm_boot_log (BASE64URL (октет))**: журнал загрузки СРТМ, полученный функцией Tbsi_Get_TCG_Log_Ex с типом журнала = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL (октет))**: СРТМ Resume log, полученный функцией Tbsi_Get_TCG_Log_Ex с типом журнала = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL (октет))**: журнал загрузки ДРТМ, полученный функцией Tbsi_Get_TCG_Log_Ex с типом журнала = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL (октет))**: ДРТМ Resume log, полученный функцией Tbsi_Get_TCG_Log_Ex с типом журнала = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL (октет))**: сертификат X. 509 для AIK, возвращенный функцией нкриптжетпроперти со свойством = NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: открытая часть пакета AIK представлена как объект JSON Web Key (ЖВК) (RFC 7517).

- **current_claim (BASE64URL (октет))**: утверждение аттестации для текущего состояния PCR, возвращенное функцией Нкрипткреатеклаим с двклаимтипе = NCRYPT_CLAIM_PLATFORM и параметром, NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK установить для включения всех PCRS. Запрос, отправленный службой, должен также использоваться при вычислении этого утверждения.

- **boot_claim (BASE64URL (октет))**: утверждение аттестации для состояния PCR при загрузке, возвращенное функцией Нкрипткреатеклаим с двклаимтипе = NCRYPT_CLAIM_PLATFORM и параметром, NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK задать включение всех PCRS

**отчет vbs** (BASE64URL (октеты)): отчет о аттестации vbs анклава, возвращенный функцией енклавежетаттестатионрепорт. Параметр Енклаведата должен быть хэшем SHA-512 в значении report_signed (включая открывающую и закрывающую фигурные скобки). Входная хэш-функция имеет кодировку UTF8 (report_signed)

**attest_key**: открытая часть ключа анклава, представленная в виде объекта JSON Web Key (ЖВК) (RFC 7517).

**custom_claims**: массив настраиваемых утверждений анклава, отправленных в службу, которая может быть оценена политикой. Утверждение

- **Name (строка)**: имя утверждения. Это имя будет добавлено к URL-адресу, определенному службой аттестации (во избежание конфликтов), а Сцепленная строка становится типом утверждения, которое можно использовать в политике.

- **value (строка)**: значение утверждения.

- **value_type (строка)**: тип данных для значения утверждения

**service_context** (BASE64URL (октеты)): непрозрачный, зашифрованный контекст, созданный службой, который включает, в частности, и время истечения срока действия этой задачи.

### <a name="report-message"></a>Сообщение отчета

#### <a name="direction"></a>Direction

Аттестация Azure — > клиент

#### <a name="payload"></a>Payload

```
{
  "report": "<JWT>"
}
```

**Report** (JWT): отчет аттестации в формате JSON Web Token (JWT) (RFC 7519).
