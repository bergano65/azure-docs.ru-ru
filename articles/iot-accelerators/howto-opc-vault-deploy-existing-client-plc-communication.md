---
title: Безопасный обмен данными клиента OPC и PLC OPC хранилище OPC - Azure | Документация Майкрософт
description: Безопасный обмен данными клиента OPC и OPC PLC входя свои сертификаты, используя хранилище OPC ЦС.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 30eedd982fa0536ce45506c159de6d04132e9a14
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494019"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>Безопасный обмен данными клиента OPC и OPC PLC

Хранилище OPC — микрослужбу, можно настроить, регистрации и управления жизненным циклом сертификатов для сервера OPC UA и клиентских приложений в облаке. В этой статье показано, как защитить обмен данными клиента OPC и OPC PLC входя свои сертификаты, используя хранилище OPC ЦС.

В следующей программе установки клиент OPC проверяет возможность подключения к OPC PLC. По умолчанию возможность подключения не поддерживается, так как оба компонента не подготавливаются с сертификатами, правого. Если компонент OPC UA была еще не подготовлена с помощью сертификата, он будет генерировать самозаверяющий сертификат при запуске. Тем не менее сертификат можно подписан центром сертификации (ЦС) и установить в компоненте OPC UA. После этого для клиента OPC и OPC PLC, подключение будет активирована. Процесс ниже рабочем процессе. Некоторые общие сведения о безопасности на основе унифицированной Архитектуры OPC можно найти в [в этом документе](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf) Технический документ. Полные сведения можно найти в спецификации OPC UA.

Характеристики: Следующая среда настроена для тестирования.

Сценарии хранилище OPC.
- Безопасный обмен данными, клиент OPC и OPC PLC входя свои сертификаты, используя хранилище OPC ЦС.

> [!NOTE]
> Дополнительные сведения см. в разделе со сведениями о GitHub [репозитория](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-self-signed-certificate-on-startup"></a>Создать самозаверяющий сертификат при запуске

**Подготовка**

- Убедитесь, что переменные среды `$env:_PLC_OPT` и `$env:_CLIENT_OPT` не определены, например, `$env:_PLC_OPT=""` в PowerShell.

- Задайте переменную среды `$env:_OPCVAULTID` на строку, которая позволяет попытку поиска данных в хранилище OPC. Разрешены только буквенно-цифровые символы. В нашем примере «123456» использовался в качестве значения для этой переменной.

- Убедитесь, нет томов docker `opcclient` или `opcplc`. Уточните `docker volume ls` и удалить их с `docker volume rm <volumename>`. Необходимо также удалить контейнеры с `docker rm <containerid>` если тома по-прежнему используются в контейнере.

**Быстрый запуск**

В корне репозитория, выполните следующую команду PowerShell:

```
docker-compose -f connecttest.yml up
```

**Проверка**

В журнале убедиться в отсутствии сертификаты, не установлен при первой загрузке. Здесь выходные данные журнала PLC OPC (как показано для использования клиента OPC):...
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
Если вы видите сообщил сертификаты, выполните указанные выше действия подготовки и удалите тома docker.

Убедитесь, что сбой подключения к OPC PLC. Вы должны увидеть следующие выходные данные в выходные данные журнала клиента OPC:

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
Причину сбоя является то, что сертификат не является доверенным. Это означает, что `opc-client` пытался выполнить подключение к `opc-plc` и получил ответ обратно, означает, что `opc-plc` не доверяет `opc-client`, в результате `opc-plc` не удалось проверить сертификат, `opc-client` предоставил. Это самозаверяющий сертификат без дальнейшей настройки сертификата на `opc-plc`, и поэтому не удалось выполнить подключение.

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>Войдите и установить сертификаты в компонентах OPC UA

**Подготовка**
1. Просмотрите выходные данные журнала из шага 1, выборка «Сведения о CreateSigningRequest» OPC PLC и клиента OPC. Здесь только выходные данные для OPC PLC:

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. Перейдите к [хранилище OPC веб-сайт](https://opcvault.azurewebsites.net/).

1. Выберите пункт `Register New`

1. Введите сведения OPC PLC из выходных данных журнала `CreateSigningRequest information` область в поля ввода на `Register New OPC UA Application` выберите `Server` как тип приложения.

1. Выберите `Register`.

1. На следующей странице `Request New Certificate for OPC UA Application` выберите `Request new Certificate with Signing Request`.

1. На следующей странице `Generate a new Certificate with a Signing Request` вставлю `CSR (base64 encoded)` строка из выходных данных журнала в `CreateRequest` поля ввода. Убедитесь, что вы скопировать полную строку.

1. Выберите `Generate New Certificate`.

1. Теперь перемещается вперед `View Certificate Request Details`. На этой странице можно загрузить все необходимые сведения, чтобы подготовить хранилище сертификатов `opc-plc`.

1. На этой странице:  
    - Выберите `Certificate` в `Download as Base64` и Скопируйте представленный текстовую строку в `EncodedBase64` поле и сохранить его для последующего использования. Мы называем его как `<applicationcertbase64-string>` позже. Выберите `Back`.

    - Выберите `Issuer` в `Download as Base64` и Скопируйте представленный текстовую строку в `EncodedBase64` поле и сохранить его для последующего использования. Мы называем его как `<addissuercertbase64-string>` позже. Выберите `Back`.

    - Выберите `Crl` в `Download as Base64` и Скопируйте представленный текстовую строку в `EncodedBase64` поле и сохранить его для последующего использования. Мы называем его как `<updatecrlbase64-string>` позже. Выберите `Back`.

1. Теперь задайте в PowerShell переменную с именем `$env:_PLC_OPT`:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > Замените строки, переданный в качестве параметра значения Base64 строк, которые получены из веб-сайта.

Повторите процесс завершения, начиная с `Register New` (шаг 3 выше) для клиента OPC. Существуют следующие различия, которые необходимо учитывать.

- Используйте выходные данные журнала `opcclient`.
- Выберите `Client` как тип приложения во время регистрации.
- Используйте `$env:_CLIENT_OPT` как имя переменной PowerShell.

> [!NOTE]
> При работе с этим сценарием, вы может распознать, `<addissuercertbase64-string>` и `<updatecrlbase64-string>` значения идентичны для `opcplc` и `opcclient`. Это справедливо для наших вариантов использования и может сэкономить время при выполнении действия.

**Быстрый запуск**

В корне репозитория, выполните следующую команду PowerShell:

```
docker-compose -f connecttest.yml up
```

**Проверка** убедитесь, что эти два компонента теперь выполнили сертификаты приложения. Проверьте выходные данные журнала на следующие выходные данные:

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
Сертификат приложения существует и подписанный центром сертификации.

Убедитесь, что теперь есть сертификатов, установленных в журнале. Ниже приведен выходные данные журнала OPC PLC, а клиент OPC имеет такой же результат.
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
Издатель сертификата приложения — это ЦС `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.` и OPC PLC доверия также все сертификаты, подписанные этим ЦС.


Убедитесь, что подключение к OPC PLC будет создан, и клиент OPC может считывать данные из OPC PLC. Вы должны увидеть следующие выходные данные в выходные данные журнала клиента OPC:
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
Если вы видите эти выходные данные, OPC PLC, теперь доверяющих OPC клиента и наоборот, так как оба имеют теперь сертификатов, подписанных центром сертификации и сертификатов какие where доверие подписанные этим ЦС.

> [!NOTE] 
> Несмотря на то, что мы показали первые две проверки шаги только для OPC PLC, их необходимо также проверить для клиента OPC.


## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как развернуть хранилище OPC в существующий проект, вот мы предлагаем:

> [!div class="nextstepaction"]
> [Развертывание OPC Двойника в существующий проект](howto-opc-twin-deploy-existing.md)