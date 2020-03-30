---
title: Поддержка платформы пакетов SDK для устройств Azure IoT | Документация Майкрософт
description: SDK с открытым исходным кодом доступны на GitHub в C, .NET (C), Java, Node.js и Python, чтобы подключить устройства к Azure IoT Hub и службе обеспечения устройств (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045117"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Поддержка платформы SDKs устройства Azure IoT

Корпорация Майкрософт стремится постоянно расширять вселенную устройств Azure IoT Hub. Корпорация Майкрософт публикует SDK с открытым исходным кодом на GitHub, чтобы помочь подключить устройства к концентратору Azure IoT и службе обеспечения устройств. SDK-устройства доступны для C, .NET (C), Java, Node.js и Python. Корпорация Майкрософт тестирует каждый SDK, чтобы убедиться, что он работает на поддерживаемых конфигурациях, подробно описанных для него в разделе [Microsoft SDKs и поддержки платформы устройств.](#microsoft-sdks-and-device-platform-support)

В дополнение к устройству SDK, Microsoft предоставляет несколько других возможностей для расширения возможностей клиентов и разработчиков для подключения своих устройств к Azure IoT:

* Корпорация Майкрософт сотрудничает с несколькими компаниями-партнерами, помогая им публиковать наборы для разработки на основе Azure IoT C SDK для своих аппаратных платформ.

* Корпорация Майкрософт работает с надежными партнерами майкрософт, чтобы предоставить постоянно расширяющийся набор устройств, которые были протестированы и сертифицированы для Azure IoT. Для текущего списка этих устройств [Azure certified for IoT device catalog](https://catalog.azureiotsolutions.com/)см.

* Корпорация Майкрософт предоставляет слой абстракции платформы (PAL) в Azure IoT Hub Device C SDK, который помогает разработчикам легко портировать SDK на свою платформу. Чтобы узнать больше, смотрите [руководство по переносу C SDK.](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)

Эта тема содержит информацию о SDK Майкрософт и конфигурациях платформы, которые они поддерживают, а также о каждом из других вариантов, перечисленных выше.

## <a name="microsoft-sdks-and-device-platform-support"></a>SDK и поддержка платформы устройств Майкрософт

Корпорация Майкрософт публикует SDK с открытым исходным кодом на GitHub для следующих языков: C, .NET (C), Node.js, Java и Python. В этом разделе перечислены SDK и их зависимости. SDKs поддерживаются на любой платформе устройства, которая удовлетворяет эти зависимости.

Для каждого из перечисленных SDK, Microsoft:

* Непрерывно строит и запускает сквозные тесты против основной ветви соответствующего SDK в GitHub на нескольких популярных платформах.  Чтобы обеспечить тестирование в различных версиях компилятора, мы обычно тестируем последнюю версию LTS и самую популярную версию.

* Предоставляет инструкции по установке или установку пакетов, если это применимо.

* Полностью поддерживает SDK на GitHub с открытым исходным кодом, путь для вкладов клиентов, и взаимодействие команды продукта с gitHub вопросов.

### <a name="c-sdk"></a>Пакет C SDK

Устройство [SDK Azure IoT Hub C](https://github.com/Azure/azure-iot-sdk-c) тестируется и поддерживает следующие конфигурации.

| OS                  | Библиотека TLS                  | Дополнительные требования                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL, или BearSSL | Розетки Беркли</br></br>Интерфейс портативной операционной системы (POSIX)                       |
| iOS 12.2            | OpenSSL.                      | XCode эмулируется в OSX 10.13.4                                                               |
| Семейство Windows 10   | SChannel                     |                                                                                             |
| Мбед ОС 5.4         | Мбед TLS 2                   | [Комплект разработчиков MXChip IoT](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Лазурная сфера ОС     | WolfSSL                      | [Лазурная сфера MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 или ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Пакет SDK для Python

[Устройство SDK Для концентратора Azure IoT](https://github.com/Azure/azure-iot-sdk-python) Hub тестируется и поддерживает следующие конфигурации.

| OS                  | Компилятор                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7., 3.5 или более поздние |
| MacOS High Sierra   | Python 2.7., 3.5 или более поздние |
| Семейство Windows 10   | Python 2.7., 3.5 или более поздние |

Только версия Python 3.5.3 или позже поддерживают асинхронные AIS, мы рекомендуем использовать версию 3.7 или позже.

### <a name="net-sdk"></a>Пакет SDK для .NET

Устройство [SDK Azure IoT Hub .NET (C)](https://github.com/Azure/azure-iot-sdk-csharp) тестируется и поддерживает следующие конфигурации.

| OS                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 Настольные и серверные S-Куи   | .NET Core 2.1, .NET Framework 4.5.1, или .NET Framework 4.7 |

SDK .NET также может быть использован с Windows IoT Core с [агентом устройств Azure](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) или [пользовательским NTService, который может использовать RPC для связи с приложениями UWP.](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/)

### <a name="nodejs-sdk"></a>Пакет SDK для Node.js

[Устройство SDK Azure IoT Hub Node.js](https://github.com/Azure/azure-iot-sdk-node) тестируется и поддерживает следующие конфигурации.

| OS                  | Версия узла    |
|---------------------|-----------------|
| Linux               | LTS и ток |
| Семейство Windows 10   | LTS и ток |

### <a name="java-sdk"></a>Пакет SDK для Java

[Устройство SDK Azure IoT Hub Java](https://github.com/Azure/azure-iot-sdk-java) тестируется и поддерживает следующие конфигурации.

| OS                     | Версия Java |
|------------------------|--------------|
| API 28 для Android         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 семейство x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Партнер поддерживает наборы для разработки

Корпорация Майкрософт работает с различными партнерами, предоставляя наборы для разработки нескольких микропроцессорных архитектур. Эти партнеры перепортили Azure IoT C SDK на свою платформу. Партнеры создают и поддерживают слой абстракции платформы (PAL) SDK. Корпорация Майкрософт работает с этими партнерами, чтобы обеспечить расширенную поддержку.

| Партнер             | Устройства                            | Ссылка                     | Поддержка |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [ESP-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Модем Qualcomm MDM9206 LTE IoT     | [Qualcomm LTE для пакета SDK IoT](https://developer.qualcomm.com/software/lte-iot-sdk) | [Форум](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | Серии STM32L4 <br/> Серии STM32F4 <br/>  Серии STM32F7 <br/>  Пакет обнаружения STM32L4 для узла Интернета вещей    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Поддержка](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF LaunchPad </br> CC3220S LaunchPad </br> CC3235SF LaunchPad </br> CC3235S LaunchPad </br> MSP432E4 LaunchPad | [Run a simple C sample on a SimpleLink device](https://github.com/TexasInstruments/azure-iot-pal-simplelink) (Запуск простого примера C на устройстве SimpleLink) | [Форум TI E2E](https://e2e.ti.com) <br/> [Форум TI E2E для CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Форум TI E2E для MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Перенос Microsoft Azure IoT C SDK

Если платформа устройства не охвачена одним из предыдущих разделов, можно рассмотреть возможность переноса Azure IoT C SDK. Портирование C SDK в первую очередь предполагает реализацию слоя абстракции платформы (PAL) SDK. PAL определяет примитивы, которые обеспечивают клей между устройством и функциями более высокого уровня в SDK. Для получения дополнительной [информации](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)см.

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Партнеры Microsoft и сертифицированные устройства Azure IoT

Корпорация Майкрософт работает с рядом партнеров, чтобы постоянно расширять вселенную Azure IoT с помощью проверенных и сертифицированных устройств Azure IoT.

* Чтобы просмотреть сертифицированные устройства Azure IoT, смотрите в [каталоге устройств IoT, сертифицированных Microsoft Azure.](https://catalog.azureiotsolutions.com/)

* Подробнее о экосистеме Azure, сертифицированной для Экосистемы IoT, можно узнать о [экосистеме, сертифицированной для IoT.](https://catalog.azureiotsolutions.com/register)

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Подключение к концентратору IoT без SDK

Если вы не можете использовать один из SDK-концентратора IoT, вы можете подключиться непосредственно к Концентратору IoT, используя [API IoT Hub REST](https://docs.microsoft.com/rest/api/iothub/) из любого приложения, способного отправлять и получать запросы и ответы HTTPS.

## <a name="support-and-other-resources"></a>Поддержка и другие ресурсы

Если возникли проблемы при использовании SDK-устройства Azure IoT, существует несколько способов получения поддержки. Вы можете попробовать один из следующих каналов:

**Сообщение об ошибках** - Ошибки в SDK устройства могут быть зарегистрированы на странице проблем соответствующего проекта GitHub. Внесенные исправления очень быстро попадают из проекта в обновление продукта.

* [Вопросы Azure IoT концентратор C SDK](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Вопросы Azure IoT Hub .NET (КЗ) SDK](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Проблемы СДК Azure IoT концентратора Java](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Вопросы Azure IoT концентратор узел SDK](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Проблемы с концентратором Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python/issues)

**Команда поддержки клиентов Майкрософт** — Пользователи, имеющие [план поддержки,](https://azure.microsoft.com/support/plans/) могут задействовать группу поддержки майкрософт, создав новый запрос поддержки непосредственно с [портала Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

**Запросы функций** - Запросы функций Функции Azure IoT отслеживаются на [странице Голоса пользователя](https://feedback.azure.com/forums/321918-azure-iot)продукта.

## <a name="next-steps"></a>Дальнейшие действия

* [Пакеты SDK для устройств и служб](iot-hub-devguide-sdks.md)
* [Руководство по переносу](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
