---
title: Поддерживаемые операционные системы и подсистемы контейнеров в Azure IoT Edge
description: Узнайте, в каких операционных системах можно запустить управляющую программу и среду выполнения Azure IoT Edge, а также какие подсистемы контейнеров поддерживают рабочие устройства.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b1bd437da50ae5989e46ac5c5f881b28b0e99703
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539908"
---
# <a name="azure-iot-edge-supported-systems"></a>Поддерживаемые Azure IoT Edge системы

В этой статье приведены сведения о системах и компонентах, поддерживаемых IoT Edge официально или в предварительной версии.

В случае возникновения проблем во время использования службы Azure IoT Edge есть несколько способов обратиться за помощью. Используйте один из приведенных ниже каналов, чтобы получить поддержку.

**Отчеты об ошибках.** Основная часть усилий по разработке продукта Azure IoT Edge, прилагается в рамках проекта IoT Edge с открытым кодом. Вы можете сообщить об ошибках на соответствующей [странице](https://github.com/azure/iotedge/issues) для этого проекта. Внесенные исправления очень быстро попадают из проекта в обновление продукта.

**Служба поддержки пользователей корпорации Майкрософт**. Пользователи, приобретающие [план поддержки,](https://azure.microsoft.com/support/plans/) могут обращаться в службу поддержки пользователей корпорации Майкрософт, создав запрос прямо на [портале Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Запросы функций.** Для продукта Azure IoT Edge запросы функций отслеживаются на специальной странице [User Voice](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Подсистемы контейнеров

Для запуска модулей Azure IoT Edge требуется подсистема контейнеров, так как модули реализуются в виде контейнеров. Корпорация Майкрософт предоставляет для этого подсистему контейнеров moby-engine. Эта подсистема контейнеров основана на проекте Moby с открытым кодом. Также часто используются подсистемы контейнеров Docker CE и Docker EE. Они также основаны на проекте Moby с открытым кодом и полностью совместимы с Azure IoT Edge. Корпорация Майкрософт поддерживает системы, использующие эти подсистемы контейнеров, по принципу "насколько возможно", но не может исправить все возможные проблемы, связанные с их использованием. По этой причине мы рекомендуем использовать для рабочей среды только подсистему moby-engine.

<br>
<center>

![Подсистема Moby как среда выполнения контейнеров](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Операционные системы

Azure IoT Edge работает на большинстве операционных систем, поддерживающих выполнение контейнеров, но уровень поддержки отличается. Операционные системы разделены на несколько уровней, которые определяют предоставляемый пользователям уровень поддержки.

* Поддерживаются системы уровня 1. Для систем уровня 1 корпорация Майкрософт:
  * выполняет автоматические тесты для этой операционной системы;
  * предоставляет для них пакеты установки.
* Системы уровня 2 совместимы с Azure IoT Edge, то есть их использование не вызывает особых сложностей. Для систем уровня 2:
  * корпорация Майкрософт проводила неофициальное тестирование на этой платформе или имеет информацию, что ее партнеры успешно запустили Azure IoT Edge на этой платформе;
  * для этих платформ могут подойти пакеты установки, предназначенные для других платформ.

Семейство ОС узла всегда должно совпадать с семейством гостевой ОС, используемой в контейнере модуля. Другими словами, контейнеры Linux можно использовать только в Linux, а контейнеры Windows — только в Windows. В Windows поддерживаются только контейнеры с изолированными процессами, а не изолированные контейнеры Hyper-V.  

<br>
<center>

![ОС узла совпадает с гостевой ОС](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Уровень 1

Системы, перечисленные в приведенной ниже таблице, поддерживаются корпорацией Майкрософт как в общедоступной версии, так и в общедоступной предварительной версии и тестируются с каждым новым выпуском. 

| Операционная система | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Растяжение ОС Raspberry Pi |  | ![Растягивание ОС Raspberry Pi + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Общедоступная предварительная версия  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Общедоступная предварительная версия |
| [Windows 10 IoT Корпоративная](/windows/iot-core/windows-iot-enterprise), сборка 17763 | ![Windows 10 IoT Корпоративная + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Базовая](/windows/iot-core/windows-iot-core), сборка 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](/windows-server/get-started-19/rel-notes-19), сборка 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](/windows/iot-core/windows-server), сборка 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

Перечисленные выше операционные системы Windows являются требованиями к устройствам, на которых запускаются контейнеры Windows, что является единственной поддерживаемой конфигурацией для рабочей среды. Установочные пакеты Azure IoT Edge для Windows позволяют использовать контейнеры Linux в Windows, но эта конфигурация предназначена только для разработки и тестирования. 

### <a name="tier-2"></a>Уровень 2

Системы, перечисленные в приведенной ниже таблице, считаются совместимыми с Azure IoT Edge, но не тестируются и не обслуживаются корпорацией Майкрософт.

| Операционная система | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Внедренная ОС Linux Flex](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Внедренная ОС Linux Flex + AMD64](./media/tutorial-c-module/green-check.png) | ![Внедренная ОС Linux Flex + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Внедренная ОС Linux Flex + ARM64](./media/tutorial-c-module/green-check.png) |
| [Внедренная ОС Linux Omni](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Внедренная ОС Linux Omni + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Внедренная ОС Linux Omni + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS бустер |  | ![Raspberry Pi OS бустер + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS бустер + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20,04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> пакеты Debian 9 из [репозитория Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases) должны работать с Ubuntu 20,04.

## <a name="releases"></a>Выпуски

Ресурсы и заметки о выпуске IoT Edge доступны на странице [выпусков azure-iotedge](https://github.com/Azure/azure-iotedge/releases). Этот раздел содержит сведения из этих заметок о выпуске, которые помогут вам визуализировать компоненты каждой версии.

Компоненты IoT Edge можно установить или обновить по отдельности. Они имеют обратимую совместимость с компонентами предыдущих версий. В приведенной ниже таблице перечислены компоненты, которые включены в каждый выпуск.

| Release | Управляющая программа безопасности | Центр Edge<br>Агент Edge | Libiothsm | Moby |
|--|--|--|--|--|
| **1.0.10** | 1.0.10 | 1.0.10 | 1.0.10 |  |
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

IoT Edge использует пакет SDK Microsoft. Azure. Devices. Client. Дополнительные сведения см. в [репозитории GitHub пакета SDK для Azure IoT на C#](https://github.com/Azure/azure-iot-sdk-csharp) или на странице [справочных сведений об Azure SDK для .NET](/dotnet/api/overview/azure/iot/client). В приведенном ниже списке указана версия клиентского пакета SDK, на основе которого тестируется каждый выпуск.

* **IOT EDGE 1.0.10**: КЛИЕНТСКИЙ пакет SDK 1.28.0
* **IoT Edge 1.0.9**: клиентский пакет SDK 1.21.1
* **IoT Edge 1.0.8**: клиентский пакет SDK 1.20.3
* **IoT Edge 1.0.7**: клиентский пакет SDK 1.20.1
* **IoT Edge 1.0.6**: клиентский пакет SDK 1.17.1
* **IoT Edge 1.0.5**: клиентский пакет SDK 1.17.1

## <a name="virtual-machines"></a>Виртуальные машины

Azure IoT Edge может выполняться на виртуальных машинах. Использование виртуальной машины в качестве устройства IoT Edge часто необходимо, когда клиенты хотят расширить существующую инфраструктуру с помощью аналитики Edge. Семейство ОС узла виртуальной машины должно совпадать с семейством гостевой ОС, используемой в контейнере модуля. Здесь действует то же требование, что и при запуске непосредственно на устройстве Azure IoT Edge. Azure IoT Edge не зависит от используемой технологии виртуализации и работает на виртуальных машинах на базе таких платформ, как Hyper-V и vSphere.

<br>
<center>

![Azure IoT Edge на виртуальной машине](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Минимальные системные требования

Azure IoT Edge отлично работает на различных устройствах, от Raspberry Pi3 до серверного оборудования. Выбор подходящего оборудования для вашего сценария зависит от рабочих нагрузок, которые вы хотите запускать. Принятие окончательного решения может быть затруднительно. Но можно легко запустить прототип решения на традиционных переносных или настольных компьютерах.

Эксперименты во время создания прототипов помогут вам сделать окончательный выбор устройства. Вопросы, на которые вам следует ответить, чтобы принять решение:

* Сколько модулей составляют рабочую нагрузку?
* Сколько уровней ваши контейнеры модулей совместно используют?
* На каком языке написаны модули?
* Сколько данных будут обрабатывать модули?
* Нужно ли модулям специализированное оборудование для увеличения рабочих нагрузок?
* Каковы желаемые характеристики производительности вашего решения?
* Каков ваш бюджет на оборудование?
