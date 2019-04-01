---
title: Безопасность клиента OPC UA и OPC UA серверное приложение с помощью управления сертификатами OPC UA Интернета вещей Azure | Документация Майкрософт
description: Безопасный клиент OPC UA и OPC UA серверному приложению, в новую пару ключей и сертификатов, используя хранилище OPC.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: bfa6bdf6a54cb5e54087055988e9682565667105
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759624"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>Обеспечение безопасности клиента OPC UA и OPC UA сервера приложения 
Azure IoT OPC UA Управление сертификатами, а также знать, что хранилище OPC является микрослужба, можно настроить, регистр и управление жизненным циклом сертификатов для OPC UA серверных и клиентских приложений в облаке. В этой статье показано, как защитить клиента OPC UA и OPC UA серверное приложение с помощью новую пару ключей и сертификатов с помощью хранилища OPC.

В следующей программе установки клиент OPC тестирует подключение к OPC PLC. По умолчанию возможность подключения не поддерживается, так как оба компонента еще не подготовлено с сертификатами, правого. В этом рабочем процессе мы не использовать самозаверяющие сертификаты компоненты OPC UA и подпишите их через хранилище OPC. См. в предыдущем [характеристики](howto-opc-vault-deploy-existing-client-plc-communication.md). Вместо этого этой характеристики подготавливает компоненты с помощью нового сертификата, а также с нового закрытым ключом, созданных функцией хранилище OPC. Некоторые общие сведения о безопасности на основе унифицированной Архитектуры OPC можно найти в этом [Технический документ](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). Полные сведения можно найти в спецификации OPC UA.

Характеристики: Следующая среда настроена для тестирования.

Сценарии хранилище OPC.
- Безопасный клиент OPC UA и OPC UA серверному приложению, в новую пару ключей и сертификатов, используя хранилище OPC.

> [!NOTE]
> Дополнительные сведения см. в разделе со сведениями о GitHub [репозитория](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-new-certificate-and-private-key"></a>Создайте новый сертификат и закрытый ключ 
**Подготовка**
- Убедитесь, что переменные среды `$env:_PLC_OPT` и `$env:_CLIENT_OPT` не определены. Например `$env:_PLC_OPT=""` в PowerShell
- Задайте переменную среды `$env:_OPCVAULTID` на строку, которая позволяет попытку поиска данных в хранилище OPC. Рекомендуется установить его из 6 цифр. Например «123456» использовался как значение для переменной.
- Убедитесь в том не docker `opcclient` или `opcplc`. Уточните `docker volume ls` и удалить их с `docker volume rm <volumename>`. Необходимо также удалить контейнеры с `docker rm <containerid>` если тома по-прежнему используются в контейнере.

**Краткое руководство**
1. Перейдите к [хранилище OPC веб-сайта](https://opcvault.azurewebsites.net/)

1. Выберите `Register New`

1. Введите информацию OPC PLC, как было показано в предыдущем характеристики выходные данные журнала `CreateSigningRequest information` область в поля ввода на `Register New OPC UA Application` выберите `Server` как тип приложения.

1. Выберите `Register`

1. На следующей странице `Request New Certificate for OPC UA Application` выберите `Request new KeyPair and Certificate`

1. На следующей странице `Generate a new Certificate with a Signing Request` вставлю `CSR (base64 encoded)` строка из выходных данных журнала в `CreateRequest` поля ввода. Убедитесь, что вы скопировать полную строку.

1. На следующей странице `Request New Certificate for OPC UA Application` выберите `Request new Certificate with Signing Request`

1. На следующей странице `Generate a new KeyPair and for an OPC UA Application` введите `CN=OpcPlc` как имя субъекта, `opcplc-<_OPCVAULTID>` (Замените `<_OPCVAULTID>` с вашими) как имя домена, выберите `PEM` как PrivateKeyFormat и введите пароль (мы называем позднее на него в качестве `<certpassword-string>`)

1. Выберите `Generate New KeyPair`

1. Теперь перемещается вперед `View Certificate Request Details`. На этой странице можно загрузить все необходимые сведения, чтобы подготовить хранилище сертификатов `opc-plc`.

1. На этой странице:  
    - Выберите `Certificate` в `Download as Base64` и Скопируйте представленный текстовую строку в `EncodedBase64` поле и сохранить его для последующего использования. Мы называем его как `<applicationcertbase64-string>` позже. Выберите `Back`.
    - Выберите `PrivateKey` в `Download as Base64` и Скопируйте представленный текстовую строку в `EncodedBase64` поле и сохранить его для последующего использования. Мы называем его как `<privatekeybase64-string>` позже. Выберите `Back`.
    - Выберите `Issuer` в `Download as Base64` и Скопируйте представленный текстовую строку в `EncodedBase64` поле и сохранить его для последующего использования. Мы называем его как `<addissuercertbase64-string>` позже. Выберите `Back`.
    - Выберите `Crl` в `Download as Base64` и Скопируйте представленный текстовую строку в `EncodedBase64` поле и сохранить его для последующего использования. Мы называем его как `<updatecrlbase64-string>` позже. Выберите `Back`.

1. Теперь задайте в PowerShell переменную с именем `$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Замените строки, переданный в качестве параметра значения Base64 строк, которые получены из веб-сайта.  

1. Повторите процесс завершения, начиная с `Register New` для клиента OPC. Существуют следующие различия, которые необходимо учитывать.
    - Используйте выходные данные журнала `opcclient`.
    - Выберите `Client` как тип приложения во время регистрации.
    - Используйте `$env:_CLIENT_OPT` как имя переменной PowerShell.

    > [!NOTE] 
    > При работе с этим сценарием, вы может распознать, `<addissuercertbase64-string>` и `<updatecrlbase64-string>` значения идентичны для `opcplc` и `opcclient`. Это справедливо для наших вариантов использования и может сэкономить время при выполнении действия.

**Краткое руководство**

В корне репозитория, выполните следующую команду PowerShell:

```
docker-compose -f connecttest.yml up
```

**Проверка**

Убедитесь, что эти два компонента не были существующего сертификата приложения. Проверьте выходные данные журнала. Ниже приведен выходных данных OPC PLC, и клиент OPC имеет результат, аналогичный журнала.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
Если сертификата приложения, удалите тома docker, как описано на этапе подготовки.

Проверьте, в журнале установки сертификата ЦС в хранилище OPC в издателя хранилище сертификатов также как и в хранилище сертификатов доверенных однорангового узла. Ниже приведен выходные данные журнала OPC PLC и клиент OPC имеет результат, аналогичный журнала. 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
OPC PLC теперь доверять всем клиентам OPC UA с использованием сертификатов, подписанные хранилище OPC.

Проверьте в журнале, что формате закрытого ключа распознается как PEM и новый сертификат приложения должна быть установлена. Ниже приведен выходные данные журнала OPC PLC и клиент OPC имеет результат, аналогичный журнала. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

Сертификат приложения и закрытый ключ теперь установлен в хранилище сертификатов приложения и приложения OPC UA.

Убедитесь, что можно успешно установить подключение между клиентом OPC и OPC PLC, и клиент OPC успешно может считывать данные из OPC PLC. Вы должны увидеть следующие выходные данные в клиенте OPC записи выходных данных:
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
Если вы видите эти выходные данные, OPC PLC, то теперь доверие клиента OPC и наоборот, так как обоим назначено теперь сертификаты, подписанные ЦС и оба доверяет сертификатам, подписанным этим ЦС.

### <a name="a-testbed-for-opc-publisher"></a>Характеристики для издателя OPC ###

**Краткое руководство**

В корне репозитория, выполните следующую команду PowerShell:
```
docker-compose -f testbed.yml up
```

**Проверка**
- Убедитесь, что данные отправлены для центра Интернета вещей, настроить, задав `_HUB_CS` с помощью [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) или [iothub-explorer](https://github.com/Azure/iothub-explorer).
- OPC тестовый клиент будет использовать вызовов прямых методов центра Интернета вещей и вызовы методов OPC настроить издатель OPC для публикации, Отмена публикации узлы из OPC Testserver.
- Просмотрите выходные данные для сообщения об ошибках.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как развернуть хранилище OPC в существующий проект, вот мы предлагаем:

> [!div class="nextstepaction"]
> [Развертывание OPC Двойника в существующий проект](howto-opc-twin-deploy-existing.md)