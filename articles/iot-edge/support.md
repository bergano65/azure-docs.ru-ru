---
title: Поддерживаемые операционные системы, контейнерные двигатели - Azure IoT Edge
description: Узнайте, в каких операционных системах можно запустить управляющую программу и среду выполнения Azure IoT Edge, а также какие подсистемы контейнеров поддерживают рабочие устройства.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c0a5d0c590f8c395c2afe366a00fcb9c83ce46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536944"
---
# <a name="azure-iot-edge-supported-systems"></a>Поддерживаемые Azure IoT Edge системы

В этой статье приводится подробная информация о том, какие системы и компоненты поддерживаются IoT Edge, будь то официально или в предварительном просмотре.

Если возникли проблемы при использовании службы Azure IoT Edge, существует несколько способов получения поддержки. Попробуйте один из следующих каналов поддержки:

**Отчеты об ошибках.** Основная часть усилий по разработке продукта Azure IoT Edge, прилагается в рамках проекта IoT Edge с открытым кодом. Вы можете сообщить об ошибках на соответствующей [странице](https://github.com/azure/iotedge/issues) для этого проекта. Внесенные исправления очень быстро попадают из проекта в обновление продукта.

**Служба поддержки пользователей корпорации Майкрософт**. Пользователи, приобретающие [план поддержки,](https://azure.microsoft.com/support/plans/) могут обращаться в службу поддержки пользователей корпорации Майкрософт, создав запрос прямо на [портале Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Запросы функций** - Продукт Azure IoT Edge отслеживает запросы функций на [странице Голоса пользователя](https://feedback.azure.com/forums/907045-azure-iot-edge)продукта.

## <a name="container-engines"></a>Подсистемы контейнеров

Модули Azure IoT Edge реализованы в качестве контейнеров, поэтому IoT Edge нужен контейнерный движок для их запуска. Корпорация Майкрософт предоставляет для этого подсистему контейнеров moby-engine. Этот контейнерный двигатель основан на проекте moby с открытым исходным кодом. Также часто используются подсистемы контейнеров Docker CE и Docker EE. Они также основаны на проекте Moby с открытым исходным кодом и совместимы с Azure IoT Edge. Корпорация Майкрософт обеспечивает наилучшую поддержку усилий для систем, использующих эти контейнерные двигатели; однако, Microsoft не может отправить исправления для проблем в них. По этой причине мы рекомендуем использовать для рабочей среды только подсистему moby-engine.

<br>
<center>

![Двигатель Moby как время выполнения контейнера](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Операционные системы

Azure IoT Edge работает на большинстве операционных систем, которые могут запускать контейнеры; однако не все из этих систем поддерживаются в равной степени. Операционные системы разделены на несколько уровней, которые определяют предоставляемый пользователям уровень поддержки.

* Поддерживаются системы уровня 1. Для систем первого уровня корпорация Майкрософт:
  * выполняет автоматические тесты для этой операционной системы;
  * предоставляет для них пакеты установки.
* Системы уровня 2 совместимы с Azure IoT Edge и могут быть использованы относительно легко. Для систем уровня 2:
  * Корпорация Майкрософт провела неофициальное тестирование на платформах или знает о партнере, успешно работая на платформе Azure IoT Edge
  * для этих платформ могут подойти пакеты установки, предназначенные для других платформ.

Семейство ОС узла всегда должно совпадать с семейством гостевой ОС, используемой в контейнере модуля. Другими словами, контейнеры Linux можно использовать только в Linux, а контейнеры Windows — только в Windows. При использовании Windows поддерживаются только изолированные контейнеры, а не изолированные контейнеры Hyper-V.  

<br>
<center>

![ОС узла совпадает с гостевой ОС](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Уровень 1

Системы, перечисленные в следующей таблице, поддерживаются корпорацией Майкрософт, как правило, доступны, либо в открытом предварительном просмотре, и тестируются с каждым новым выпуском. 

| Операционная система | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Распбиан стретч и ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Сервер 16.04](./media/tutorial-c-module/green-check.png) |  | Общедоступная предварительная версия  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Сервер 18.04](./media/tutorial-c-module/green-check.png) |  | Общедоступная предварительная версия |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), сборка 17763 | ![Ядро IoT Windows - AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT предприятие](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), построить 17763 | ![Windows 10 IoT Предприятие и AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), сборка 17763 | ![Windows Server 2019 г. AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), сборка 17763 | ![IoT сервера Windows 2019 г. AMD64](./media/tutorial-c-module/green-check.png) |  |  |

Приведенные выше операционные системы Windows являются требованиями к устройствам, запускаемым контейнерами Windows на Windows, которая является единственной поддерживаемой конфигурацией для производства. Пакеты установки Azure IoT Edge для Windows позволяют использовать контейнеры Linux в Windows; однако эта конфигурация предназначена только для разработки и тестирования. Для получения дополнительной [информации см.](how-to-install-iot-edge-windows-with-linux.md)

### <a name="tier-2"></a>Уровень 2

Системы, перечисленные в следующей таблице, считаются совместимыми с Azure IoT Edge, но не тестируются и не поддерживаются корпорацией Майкрософт.

| Операционная система | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![Центос и AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS и ARM32v7](./media/tutorial-c-module/green-check.png) | ![Центос и АРМ64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 и AMD64](./media/tutorial-c-module/green-check.png) | ![Дебиан 8 и ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 и ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 и AMD64](./media/tutorial-c-module/green-check.png) | ![Дебиан 9 и ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 и ARM64](./media/tutorial-c-module/green-check.png) |
| [Дебиан 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 и AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 и ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 и ARM64](./media/tutorial-c-module/green-check.png) |
| [Наставник Встраивает Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Наставник Встраивает Linux Flex OS и AMD64](./media/tutorial-c-module/green-check.png) | ![Наставник Встраивает Linux Flex OS и ARM32v7](./media/tutorial-c-module/green-check.png) | ![Наставник Встраивает Linux Flex OS и ARM64](./media/tutorial-c-module/green-check.png) |
| [Наставник Встраивает Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Наставник Встраивает Linux Omni OS и AMD64](./media/tutorial-c-module/green-check.png) |  | ![Наставник Встраивает Linux Omni OS](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![РХЕЛ 7,5 и AMD64](./media/tutorial-c-module/green-check.png) | ![RhEL 7.5 - ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 - АРМ64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Убунту 16.04 - AMD64](./media/tutorial-c-module/green-check.png) | ![Убунту 16.04 - ARM32v7](./media/tutorial-c-module/green-check.png) | ![Убунту 16.04 - ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Увунту 18.04 - AMD64](./media/tutorial-c-module/green-check.png) | ![Убунту 18.04 - ARM32v7](./media/tutorial-c-module/green-check.png) | ![Убунту 18.04 - ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Ветер-река 8 и AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto и AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto и ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto и ARM64](./media/tutorial-c-module/green-check.png) |
| Распбиан Бастер <sup>1</sup> |  | ![Распбиан Бастер и ARM32v7](./media/tutorial-c-module/green-check.png) | ![Распбиан Бастер и ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Системы Debian 10, включая Raspian Buster, используют версию OpenSSL, которую IoT Edge не поддерживает. Используйте следующую команду для установки более ранней версии перед установкой IoT Edge:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="releases"></a>Выпуски

Ресурсы выпуска IoT Edge и заметки о выпуске доступны на странице [релизов azure-iotedge.](https://github.com/Azure/azure-iotedge/releases) Этот раздел отражает информацию из этих примечаний к выпуску, чтобы помочь вам визуализировать компоненты каждой версии легче.

Компоненты IoT Edge могут быть установлены или обновлены по отдельности, а также в обратном порядке совместимы с компонентами из старых версий. В следующей таблице перечислены компоненты, включенные в каждый выпуск:

| Release   | Безопасность daemon  | Край концентратор<br>Агент края | Либиотсм | Моби  |
| --------- | ---------------- | ---------------------- | --------- | ----- |
| **1.0.9** | 1.0.9            | 1.0.9                  | 1.0.9     |       |
| **1.0.8** | 1.0.8            | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7       | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6       | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5            | 1.0.5                  | 1.0.5     | 3.0.2 |

IoT Edge использует Microsoft.Azure.Device.Client SDK. Для получения дополнительной информации смотрите [репо Azure IoT C sDK GitHub](https://github.com/Azure/azure-iot-sdk-csharp) или [Azure SDK для справочного контента .NET.](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) В следующем списке показана версия клиента SDK, против которого каждый релиз тестируется:

* **IoT Edge 1.0.9**: Клиент SDK 1.21.1
* **IoT Edge 1.0.8**: Клиент SDK 1.20.3
* **IoT Edge 1.0.7**: Клиент SDK 1.20.1
* **IoT Edge 1.0.6**: Клиент SDK 1.17.1
* **IoT Edge 1.0.5**: Клиент SDK 1.17.1

## <a name="virtual-machines"></a>Виртуальные машины

Azure IoT Edge может выполняться на виртуальных машинах. Использование виртуальной машины в качестве устройства IoT Edge является обычным явлением, когда клиенты хотят увеличить существующую инфраструктуру с помощью упокоя края. Семейство ОС узла виртуальной машины должно совпадать с семейством гостевой ОС, используемой в контейнере модуля. Это требование такое же, как и при запуске Azure IoT Edge непосредственно на устройстве. Azure IoT Edge не зависит от используемой технологии виртуализации и работает на виртуальных машинах на базе таких платформ, как Hyper-V и vSphere.

<br>
<center>

![Azure IoT Edge на виртуальной машине](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Минимальные системные требования

Azure IoT Edge отлично работает на различных устройствах, от Raspberry Pi3 до серверного оборудования. Выбор правильного оборудования для сценария зависит от рабочих нагрузок, которые вы хотите запустить. Принятие окончательного решения может быть затруднительно. Но можно легко запустить прототип решения на традиционных переносных или настольных компьютерах.

Эксперименты во время создания прототипов помогут вам сделать окончательный выбор устройства. Вопросы, которые следует рассмотреть, включают:

* Сколько модулей в вашей рабочей нагрузке?
* Сколько слоев разделяют контейнеры модулей?
* На каком языке написаны ваши модули?
* Сколько данных будет обрабатываться вашими модулями?
* Нужны ли вашим модулям какое-либо специализированное оборудование для ускорения их рабочих нагрузок?
* Каковы желаемые характеристики производительности вашего решения?
* Каков ваш аппаратный бюджет?
