---
title: Подключение устройства в Azure IoT Central | Документация Майкрософт
description: В этой статье описаны ключевые понятия, связанные с подключением устройства в Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 412a678465589da87c713a55a7b67193b254bf96
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434930"
---
# <a name="get-connected-to-azure-iot-central-preview-features"></a>Подключение к Azure IoT Central (предварительные версии функций)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

В этой статье описаны ключевые понятия, связанные с подключением устройства в Microsoft Azure IoT Central.

Azure IoT Central использует [службу подготовки устройств для центра Интернета вещей Azure (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) для управления регистрацией и подключением устройств.

Использование DPS позволяет:

- IoT Central для поддержки адаптации и подключения устройств в масштабе.
- Вы создаете учетные данные устройства и настраиваете устройства в автономном режиме без регистрации устройств с помощью IoT Central пользовательского интерфейса.
- Устройства для подключения с использованием подписанных URL-адресов (SAS).
- Устройства для подключения с использованием стандартных отраслевых сертификатов X. 509.
- Вы используете собственные идентификаторы устройств для регистрации устройств в IoT Central. Использование собственных идентификаторов устройств упрощает интеграцию с существующими системами резервного копирования.
- Единый последовательный способ подключения устройств к IoT Central.

В этой статье описываются следующие варианты использования.

- [Быстрое подключение одного устройства с помощью SAS](#connect-a-single-device)
- [Подключение устройств при масштабировании с помощью SAS](#connect-devices-at-scale-using-sas)
- [Подключайтесь к устройствам с помощью сертификатов X. 509](#connect-devices-using-x509-certificates) . Это рекомендуемый подход для рабочих сред.
- [Подключение без предварительной регистрации устройства](#connect-without-registering-devices)
- [Подключение устройств с помощью функций Plug and Play Интернета вещей](#connect-devices-with-iot-plug-and-play)

## <a name="connect-a-single-device"></a>Подключение одного устройства

Этот подход удобен при экспериментах с IoT Central или тестированием устройств. Вы можете использовать сведения о подключении устройства из приложения IoT Central, чтобы подключить устройство к IoT Central приложению с помощью службы подготовки устройств (DPS). Пример кода клиента для устройства DPS можно найти на следующих языках:

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

## <a name="connect-devices-at-scale-using-sas"></a>Подключение устройств при масштабировании с помощью SAS

Чтобы подключить устройства к IoT Central в масштабе с помощью SAS, необходимо зарегистрировать и настроить устройства.

### <a name="register-devices-in-bulk"></a>Регистрация устройств в оптовой единице

Чтобы зарегистрировать большое количество устройств в IoT Central приложении, используйте CSV-файл для [импорта идентификаторов устройств и устройств](howto-manage-devices.md#import-devices).

Чтобы получить сведения о подключении для импортированных устройств, [экспортируйте CSV-файл из приложения IOT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Чтобы узнать, как можно подключить устройства без их предварительной регистрации в IoT Central, см. раздел [подключение без предварительной регистрации устройств](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Настройка устройств

Используйте сведения о подключении из файла экспорта в коде устройства, чтобы разрешить устройствам подключаться и передавать данные в приложение IoT Central в Интернет вещей. Дополнительные сведения о подключении устройств см. в разделе [дальнейшие действия](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Подключение устройств с помощью сертификатов X. 509

В рабочей среде рекомендуемым механизмом проверки подлинности устройств для IoT Central является использование сертификатов X. 509. Дополнительные сведения см. в статье [Device Authentication using X.509 CA Certificates](../../iot-hub/iot-hub-x509ca-overview.md) (Аутентификация устройств с помощью сертификатов ЦС X.509).

Следующие шаги описывают подключение устройств к IoT Central с помощью сертификатов X. 509:

1. В приложении IoT Central _добавьте и проверьте промежуточный или корневой сертификат X. 509_ , который вы используете для создания сертификатов устройств.

    - Перейдите в раздел **администрирование > подключение устройства > сертификаты (X. 509)** и добавьте корневой или промежуточный сертификат X. 509, который вы используете для создания сертификатов конечных устройств.

      ![Параметры подключения](media/overview-iot-central-get-connected/connection-settings.png)

      Если имеется нарушение безопасности или основной сертификат настроен на истечение срока действия, используйте дополнительный сертификат для сокращения времени простоя. При обновлении основного сертификата можно продолжить подготавливать устройства с помощью вторичного сертификата.

    - Проверка принадлежности сертификата гарантирует, что у отправки сертификата есть закрытый ключ сертификата. Чтобы проверить сертификат, сделайте следующее:
        - Нажмите кнопку рядом с **кодом проверки** , чтобы создать код.
        - Создайте сертификат проверки X. 509 с кодом проверки, созданным на предыдущем шаге. Сохраните сертификат как CER-файл.
        - Отправьте подписанный сертификат проверки и нажмите кнопку " **проверить**".

          ![Параметры подключения](media/overview-iot-central-get-connected/verify-cert.png)

1. Используйте CSV-файл для _импорта и регистрации устройств_ в приложении IOT Central.

1. _Настройте устройства._ Создайте конечные сертификаты, используя переданный корневой сертификат. Используйте **идентификатор устройства** в качестве значения CNAME в конечных сертификатах. ИДЕНТИФИКАТОР устройства должен быть в нижнем регистре. Затем запрограммировать устройства с помощью сведений о службе подготовки. Когда устройство переключается на первый компьютер, оно получает сведения о подключении для IoT Central приложения из DPS.

### <a name="further-reference"></a>Дополнительные справочные материалы

- Пример реализации для [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Пример клиента устройства на языке C.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Только для целей тестирования

Для тестирования можно использовать эти служебные программы для создания сертификатов ЦС и сертификатов устройств.

- Если вы используете устройство DevKit, это [средство командной строки](https://aka.ms/iotcentral-docs-dicetool) создает сертификат ЦС, который можно добавить в приложение IOT Central для проверки сертификатов.

- Используйте это [средство командной строки](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) для:
  - Создайте цепочку сертификатов. Выполните шаг 2 в статье GitHub.
  - Сохраните сертификаты в виде CER – файлов, чтобы отправить их в приложение IoT Central.
  - Используйте код проверки из приложения IoT Central, чтобы создать сертификат проверки. Выполните шаг 3 в статье GitHub.
  - Создайте конечные сертификаты для устройств, используя идентификаторы устройств в качестве параметра для средства. Выполните шаг 4 в статье GitHub.

## <a name="connect-without-registering-devices"></a>Подключение без регистрации устройств

Ключевой сценарий, IoT Central, позволяет изготовителям оборудования выполнять массовое изготовление устройств, которые могут подключаться к приложению IoT Central без предварительной регистрации. Изготовитель должен создать подходящие учетные данные и настроить устройства в фабрике. Когда устройство впервые включается в первый раз, оно автоматически подключается к IoT Central приложению. Оператор IoT Central должен утвердить устройство, прежде чем оно сможет передавать данные.

Следующая схема описывает этот поток:

![Параметры подключения](media/overview-iot-central-get-connected/device-connection-flow1.png)

Следующие шаги описывают этот процесс более подробно. Эти действия немного отличаются в зависимости от того, используете ли вы сертификат SAS или X. 509 для проверки подлинности устройства:

1. Настройте параметры подключения:

    - **Сертификаты X. 509:** [добавьте и проверьте корневой/промежуточный сертификат](#connect-devices-using-x509-certificates) и используйте его для создания сертификатов устройств на следующем шаге.
    - **SAS:** Скопируйте первичный ключ. Этот ключ представляет собой ключ SAS группы для приложения IoT Central. Используйте ключ, чтобы создать ключи SAS устройства на следующем шаге.
    ![Параметры подключения SAS](media/overview-iot-central-get-connected/connection-settings-sas.png)

1. Создание учетных данных устройства
    - **Сертификаты X. 509:** Создайте конечные сертификаты для устройств, используя корневой или промежуточный сертификат, добавленный в приложение IoT Central. Убедитесь, что в конечных сертификатах используется **идентификатор устройства** в нижнем регистре (CNAME). Для целей тестирования используйте это [средство командной строки](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) для создания сертификатов устройств.
    - **SAS:** Используйте это [средство командной строки](https://www.npmjs.com/package/dps-keygen) для создания ключей SAS устройства. Используйте групповой **первичный ключ** из предыдущего шага. ИДЕНТИФИКАТОР устройства должен быть в нижнем регистре.

      Чтобы установить [служебную программу генератора ключей](https://github.com/Azure/dps-keygen), выполните следующую команду:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Чтобы создать ключ устройства из первичного ключа SAS группы, выполните следующую команду:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Чтобы настроить устройства, Flash каждое устройство с **идентификатором области**, **идентификатором устройства**и **сертификатом устройства X. 509** или **ключом SAS**.

1. Затем включите устройство, чтобы оно подключаться к приложению IoT Central. При переключении на устройство оно сначала подключается к DP для получения сведений о регистрации IoT Central.

1. Изначально подключенное устройство отображается как **несвязанное** на странице **устройства** . Состояние подготовки устройства — **Зарегистрировано**. **Перенесите** устройство в соответствующий шаблон устройства и утвердите устройство для подключения к приложению IOT Central. Затем устройство может получить строку подключения из центра Интернета вещей и начать отправку данных. Подготовка устройства завершена, и состояние подготовки теперь **подготовлено**.

## <a name="individual-enrollment-based-device-connectivity"></a>Подключение отдельных устройств на основе регистрации

Для клиентов, подключающихся к устройствам с учетными данными для проверки подлинности отдельных устройств, можно выбрать вариант. Отдельная регистрация — это запись для одного устройства, которое может подключаться. При аттестации таких устройств используются сертификаты X.509 конечных объектов или токены SAS (от физических или виртуальных доверенных платформенных модулей). ИДЕНТИФИКАТОР устройства (идентификатор регистрации) в отдельной регистрации состоит из буквенно-цифровых символов и может содержать дефисы. Дополнительные сведения об индивидуальных регистрациях см. [здесь](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> При создании отдельной регистрации для устройства оно имеет приоритет над аттестацией по умолчанию группы, основанной на регистрации (SAS, X509) в приложении.

### <a name="creating-individual-enrollments"></a>Создание индивидуальных регистраций
IoT Central поддерживает следующие механизмы аттестации

1. **Аттестация симметричных ключей:** Аттестация симметричных ключей — это простой подход к проверке подлинности устройства с помощью экземпляра службы подготовки устройств. Для создания отдельной регистрации с симметричными ключами; Откройте диалоговое окно Подключение, выберите отдельная регистрация и механизм "SAS" и введите первичный и вторичный ключи. Ключи SAS должны быть в кодировке Base64. Ниже приведена [ссылка](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) на примеры кода, помогающие написать код устройства для подготовки устройств с помощью симметричных ключей и индивидуальных регистраций.
1. **Сертификаты X. 509:** Сертификаты X. 509, как указано в заголовке, — это механизм аттестации на основе сертификатов, отличный способ масштабирования рабочей среды. Чтобы создать отдельную регистрацию с симметричными ключами, выберите отдельную регистрацию и механизм "X. 509" и отправьте первичные и вторичные сертификаты и сохраните их, чтобы создать регистрацию. Ниже приведена [ссылка](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) на примеры кода, помогающие написать код устройства для подготовки устройств с помощью X509. Сертификаты устройств, используемые с [индивидуальной](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment) записью регистрации, имеют требование, что имя субъекта должно быть задано как идентификатор устройства (идентификатор регистрации) отдельной записи регистрации.
1. **Аттестация TPM:** TPM означает доверенный платформенный модуль (TPM) и является аппаратным модулем безопасности (HSM) и является одним из наиболее безопасных способов подключения устройств.  В этой статье предполагается, что используется дискретный, микропрограммный или интегрированный модуль TPM. Эмулированные программными средствами модули TPM хорошо подходят для прототипирования или тестирования. Но они не обеспечивают такой же уровень безопасности, как дискретные, микропрограммные или интегрированные TPM. Мы не рекомендуем использовать программные модули TPM в рабочей среде. Чтобы создать отдельную регистрацию с симметричными ключами, выберите отдельную регистрацию и механизм "TPM" и введите ключи подтверждения для создания регистрации. Дополнительные сведения о типах доверенных ПЛАТФОРМЕНных модулей можно узнать [здесь](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation). Ниже приведена [ссылка](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/TpmSample) на примеры кода, помогающие написать код устройства для подготовки устройств с помощью TPM. Чтобы создать аттестацию на основе TPM, введите ключ подтверждения и сохраните.

## <a name="connect-devices-with-iot-plug-and-play"></a>Подключение устройств к Plug and Play Интернета вещей

Одной из основных функций Интернета вещей Plug and Play с IoT Central является возможность автоматического связывания шаблонов устройств при подключении устройства. Наряду с учетными данными устройства устройства теперь могут отправить **капабилитимоделид** как часть вызова регистрации устройства, а IOT Central обнаружит и свяжет шаблон устройства. Процесс обнаружения выполняется в следующем порядке:

1. Связывается с шаблоном устройства, если он уже опубликован в IoT Central приложении.
1. Извлекает из общедоступного репозитория опубликованных и сертифицированных моделей возможностей.

Ниже приведен формат дополнительных полезных данных, которые устройство будет передавать во время вызова функции регистрации DPS.

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

> [!NOTE]
> Обратите внимание, что параметр автоматического утверждения должен быть включен для автоматического подключения устройств, обнаружения модели и начала отправки данных.

## <a name="device-status"></a>состояние устройства;

Когда реальное устройство подключается к IoT Centralному приложению, его состояние устройства изменяется следующим образом:

1. Состояние устройства **регистрируется**первыми. Это состояние означает, что устройство создано в IoT Central и имеет идентификатор устройства. Устройство регистрируется в следующих случаях:
    - На странице **устройства** будет добавлено новое реальное устройство.
    - Набор устройств добавляется с помощью **импорта** на странице **устройства** .

1. Состояние устройства изменяется на **подготовлено** , когда устройство, подключенное к IOT Centralному приложению с действительными учетными данными, завершает этап подготовки. На этом шаге устройство получает строку подключения из центра Интернета вещей. Теперь устройство может подключиться к центру Интернета вещей и начать отправку данных.

1. Оператор может блокировать устройство. Если устройство заблокировано, оно не может отправить данные в приложение IoT Central. Заблокированные устройства имеют состояние **заблокировано**. Оператор должен сбросить устройство, прежде чем оно сможет возобновить отправку данных. Когда оператор разблокирует устройство, состояние возвращается к предыдущему значению, **зарегистрированному** или **подготовленному**.

1. Состояние устройства **ожидает утверждения** , что означает, что параметр **автоматического утверждения** отключен и требует, чтобы все устройства, подключающиеся к IOT Central, были явно утверждены оператором. Устройства, не зарегистрированные вручную на странице " **устройства** ", но подключенные с действительными учетными данными, будут иметь состояние устройства **ожидание утверждения**. Операторы могут утверждать эти устройства на странице **устройства** с помощью кнопки **утвердить** .

1. Состояние устройства не **связано** . Это означает, что устройства, подключающиеся к IOT Central, не имеют связанного с ними шаблона устройства. Обычно это происходит в следующих случаях.
    - Набор устройств добавляется с помощью **импорта** на странице **устройства** без указания шаблона устройства.
    - Устройства, не зарегистрированные вручную на странице **устройства** , подключенные с действительными учетными данными, но без указания идентификатора шаблона во время регистрации.  
Оператор может связать устройство с шаблоном на странице **устройства** с помощью кнопки **Миграция** .

## <a name="sdk-support"></a>Поддержка пакетов SDK

Пакеты SDK для устройств Azure предлагают самый простой способ реализации кода устройства. Доступны следующие пакеты SDK для устройств:

- [Пакет SDK для Azure IoT для C](https://github.com/azure/azure-iot-sdk-c).
- [Пакет SDK для Azure IoT для Python](https://github.com/azure/azure-iot-sdk-python).
- [Пакет SDK для Azure IoT для Node.js](https://github.com/azure/azure-iot-sdk-node).
- [Пакет SDK для Azure IoT для Java](https://github.com/azure/azure-iot-sdk-java).
- [Пакет SDK для Azure IoT для .NET](https://github.com/azure/azure-iot-sdk-csharp).

### <a name="sdk-features-and-iot-hub-connectivity"></a>Функции пакета SDK и возможность подключения Центра Интернета вещей

Для всего обмена данными устройства с Центром Интернета вещей используются следующие варианты подключения Центра Интернета вещей:

- [передача сообщений с устройства в облако](../../iot-hub/iot-hub-devguide-messages-d2c.md);
- [Двойники устройств](../../iot-hub/iot-hub-devguide-device-twins.md)

В следующей таблице содержатся сведения о том, каким образом возможности устройства Azure IoT Central сопоставляются с возможностями Центра Интернета вещей.

| Центр Azure IoT | Центр Интернета вещей Azure |
| ----------- | ------- |
| Измерения. Телеметрия | Передача сообщений с устройства в облако |
| Свойства устройства | Сообщаемые свойства двойника устройства |
| Настройки | Требуемые и передаваемые свойства двойника устройства |

Дополнительные сведения об использовании пакетов SDK для устройств см. в статье [Подключение устройства девдив Kit к приложению Azure IOT Central](howto-connect-devkit.md) для примера кода.

### <a name="protocols"></a>Протоколы

Пакеты SDK для устройства поддерживают следующие сетевые протоколы для подключения к Центру Интернета вещей.

- MQTT;
- AMQP;
- HTTPS

Дополнительные сведения об этих разных протоколах и рекомендации по их выбору см. в статье [Справочник — выбор протокола связи](../../iot-hub/iot-hub-devguide-protocols.md).

Если ваше устройство не может использовать любой из поддерживаемых протоколов, вы можете использовать Azure IoT Edge для преобразования протокола. IoT Edge поддерживает другие сценарии интеллектуальной обработки на пограничном устройстве, позволяющие снизить нагрузку на пограничное устройство из приложения Azure IoT Central.

## <a name="security"></a>Безопасность

Все данные, передаваемые между устройствами и Azure IoT Central, зашифрованы. Центр Интернета вещей выполняет проверку подлинности каждого запроса с устройства, которое подключается к любой конечной точке Центра Интернета вещей, обращенной к устройству. Чтобы избежать обмена учетными данными по сети, для проверки подлинности устройство использует подписанные маркеры. Дополнительные сведения см. в статье [Управление доступом к Центру Интернета вещей](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали о подключении устройств в Azure IoT Central, ниже приведены предлагаемые дальнейшие действия.

- [Подготовка и подключение устройства DevKit](howto-connect-devkit.md)
- [ПАКЕТ SDK для C: подготовка пакета SDK для клиента устройства](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
