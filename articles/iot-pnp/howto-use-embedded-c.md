---
title: Использование Plug and Play Интернета вещей на ограниченных устройствах | Документация Майкрософт
description: Узнайте, как можно реализовать Plug and Play IoT на ограниченных устройствах с помощью пакета SDK для Embedded C или Azure RTO.
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: edcfcda8de1fade605c409537155231d90d5849e
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91610081"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>Реализация Plug and Play Интернета вещей на ограниченных устройствах

Если вы разрабатываете для *ограниченного использования устройств*, вы можете использовать IOT Plug and Play с [пакетом Azure SDK для внедренных клиентских библиотек IOT C](https://aka.ms/embeddedcsdk) или [Azure RTO](https://docs.microsoft.com/azure/rtos/overview-rtos). Эта статья содержит ссылки и ресурсы для этих ограниченных сценариев.

## <a name="use-the-sdk-for-embedded-c"></a>Использование пакета SDK для Embedded C

Пакет SDK для Embedded C предлагает упрощенное решение для подключения ограниченных устройств к службам Интернета вещей Azure, включая использование [соглашений Plug and Play IOT](concepts-convention.md). Следующие ссылки включают примеры для реального устройства, а также для образовательных и отладочных целей.

### <a name="use-a-real-device"></a>Использование реального устройства

Полное комплексное руководство по использованию пакета SDK для Embedded C, службы подготовки устройств и Интернета вещей Plug and Play на реальном устройстве см. в статье о [повторном назначении PIC для WX разработки для подключения к Azure через службу подготовки устройств центра Интернета вещей](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Вводные примеры

Пакет SDK для внедренного репозитория C содержит [несколько примеров](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) , демонстрирующих использование IOT Plug and Play.

> [!NOTE]
> Эти примеры показаны в Windows и Linux для образовательных и отладочных целей. В рабочем сценарии образцы предназначены только для устройств с ограниченным доступом.

- [Пример термостата с пакетом SDK для Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Пример контроллера температуры с пакетом SDK для Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Использование Azure RTO

Azure RTO включает упрощенный слой, который добавляет собственное подключение к облачным службам Azure IoT. Этот уровень предоставляет простой механизм подключения ограниченных устройств к Azure IoT с использованием дополнительных функций Azure RTO. Дополнительные сведения см. в разделе [что такое Microsoft Azure RTO](https://docs.microsoft.com/azure/rtos/overview-rtos).

### <a name="toolchains"></a>Цепочек инструментов

Примеры Azure RTO предоставляются с помощью различных сочетаний IDE и цепочки инструментов, таких как:

- ИАР: интегрированная среда разработки [WORKBENCH](https://www.iar.com/iar-embedded-workbench/) ИАР
- GCC/CMak: сборка поверх системы сборки [CMAK](https://cmake.org/) с открытым исходным кодом и [внедренного цепочки инструментов GNU ARM](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- Мкуекспрессо: [Интегрированная среда разработки НКСП мкукспрессо](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: [Интегрированная среда разработки Стмикроелектроник STM32Cube](https://www.st.com/en/development-tools/stm32cubeide.html)
- МПЛАБ: [Интегрированная среда разработки Мплаб X](https://www.microchip.com/mplab/mplab-x-ide) в микросхеме

### <a name="samples"></a>Примеры

Примеры, в которых показано, как приступить к работе на разных устройствах с помощью Azure RTO и IoT Plug and Play, см. в следующей таблице:

Изготовитель | Устройство | Примеры |
| --- | --- | --- |
| Микросхемы | [ATSAME54 — КСПРО](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMAK](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [ИАР](https://aka.ms/azrtos-sample/e54-iar) • [мплаб](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC или CMak](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| нксп | [MIMXRT1060 — ЕВК](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMAK](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [ИАР](https://aka.ms/azrtos-sample/rt1060-iar) • [мкукспрессо](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| стмикроелектроникс | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [ИАР](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| стмикроелектроникс | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMAK](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [ИАР](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| стмикроелектроникс | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMAK](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [ИАР](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали о вариантах реализации Plug and Play Интернета вещей на ограниченных устройствах, рекомендуем следующий шаг — узнать о [соглашениях Plug and Play IOT](concepts-convention.md).