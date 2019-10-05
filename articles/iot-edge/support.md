---
title: Поддерживаемые операционные системы и подсистемы контейнеров в Azure IoT Edge | Документация Майкрософт
description: Узнайте, в каких операционных системах можно запустить управляющую программу и среду выполнения Azure IoT Edge, а также какие подсистемы контейнеров поддерживают рабочие устройства.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 781884b0973ca42db542f53d452dbaaf7a7f1fa7
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973275"
---
# <a name="azure-iot-edge-supported-systems"></a>Поддерживаемые Azure IoT Edge системы

В этой статье содержатся сведения о том, какие системы и компоненты поддерживаются IoT Edge, как официально, так и в предварительной версии. 

Если при использовании службы Azure IoT Edge возникают проблемы, существует несколько способов поиска поддержки. Для поддержки воспользуйтесь одним из следующих каналов:

**Отчеты об ошибках.** Основная часть усилий по разработке продукта Azure IoT Edge, прилагается в рамках проекта IoT Edge с открытым кодом. Вы можете сообщить об ошибках на соответствующей [странице](https://github.com/azure/iotedge/issues) для этого проекта. Внесенные исправления очень быстро попадают из проекта в обновление продукта.

**Служба поддержки пользователей корпорации Майкрософт**. Пользователи, приобретающие [план поддержки,](https://azure.microsoft.com/support/plans/) могут обращаться в службу поддержки пользователей корпорации Майкрософт, создав запрос прямо на [портале Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Запросы функций.** Для продукта Azure IoT Edge запросы функций отслеживаются на специальной странице [User Voice](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Подсистемы контейнеров

Azure IoT Edge модули реализуются как контейнеры, поэтому IoT Edge требуется обработчик контейнера для их запуска. Корпорация Майкрософт предоставляет для этого подсистему контейнеров moby-engine. Этот модуль контейнера основан на проекте с открытым исходным кодом значок Кита. Также часто используются подсистемы контейнеров Docker CE и Docker EE. Они также основаны на проекте значок Кита с открытым исходным кодом и совместимы с Azure IoT Edge. Корпорация Майкрософт предоставляет оптимальную поддержку для систем, использующих эти механизмы контейнеров. Однако корпорация Майкрософт не может отправить исправления для проблем. По этой причине мы рекомендуем использовать для рабочей среды только подсистему moby-engine.

<br>
<center>

@no__t 0Moby в качестве среды выполнения контейнеров @ no__t-1 @ no__t-2

## <a name="operating-systems"></a>Операционные системы
Azure IoT Edge работает в большинстве операционных систем, которые могут выполнять контейнеры; Однако все эти системы не поддерживаются одинаково. Операционные системы разделены на несколько уровней, которые определяют предоставляемый пользователям уровень поддержки.
* Поддерживаются системы уровня 1. Для систем уровня 1 Корпорация Майкрософт:
    * выполняет автоматические тесты для этой операционной системы;
    * предоставляет для них пакеты установки.
* Системы уровня 2 совместимы с Azure IoT Edge и могут использоваться относительно легко. Для систем уровня 2:
    * Корпорация Майкрософт выполнила нерегламентированное тестирование на платформах или знает, что партнер успешно работал Azure IoT Edge на платформе.
    * для этих платформ могут подойти пакеты установки, предназначенные для других платформ.
    
Семейство ОС узла всегда должно совпадать с семейством гостевой ОС, используемой в контейнере модуля. Другими словами, контейнеры Linux можно использовать только в Linux, а контейнеры Windows — только в Windows. При использовании Windows поддерживаются только изолированные контейнеры обработки, а не изолированные контейнеры Hyper-V.  

<br>
<center>

@no__t 0Host OS соответствует гостевой ОС @ no__t-1 @ no__t-2

### <a name="tier-1"></a>Уровень 1

Системы, перечисленные в следующей таблице, поддерживаются корпорацией Майкрософт как общедоступной, так и в общедоступной предварительной версии и тестируются с каждым новым выпуском. 

| Операционная система | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Общедоступная предварительная версия  |
| [Ubuntu Server 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Общедоступная предварительная версия |
| [Windows 10 IOT базовая](https://docs.microsoft.com/windows/iot-core/windows-iot-core), сборка 17763 | ![Windows IoT базовая и AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IOT Корпоративная](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), сборка 17763 | ![Windows 10 IoT Корпоративная + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), сборка 17763 | ![Windows Server 2019 и AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), сборка 17763 | ![Windows Server IoT 2019 и AMD64](./media/tutorial-c-module/green-check.png) |  |  |


Перечисленные выше операционные системы Windows являются требованиями для устройств, которые работают с контейнерами Windows в Windows, что является единственной поддерживаемой конфигурацией для рабочей среды. Пакеты установки Azure IoT Edge для Windows позволяют использовать контейнеры Linux в Windows. Однако эта конфигурация предназначена только для разработки и тестирования. Дополнительные сведения см. в статье [использование IOT EDGE в Windows для запуска контейнеров Linux](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Уровень 2

Системы, перечисленные в следующей таблице, считаются совместимыми с Azure IoT Edge, но не проверяются и не обслуживаются корпорацией Майкрософт.

| Операционная система | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7,5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS и AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Внедренная ОС Linux Flex](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Внедрение Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Обучение Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Обучение Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Версия операционной системы лампочки для Linux Embedded](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Инструктор Embedded Linux лампочка и AMD64](./media/tutorial-c-module/green-check.png) |  | ![Инструктор Embedded Linux лампочка, ОС + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7,5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Обмотка River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Обмотка River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [йокто](https://www.yoctoproject.org/) | ![Йокто и AMD64](./media/tutorial-c-module/green-check.png) | ![Йокто + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Йокто + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian бустер <sup>1</sup> |  | ![Raspbian бустер + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian бустер + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 систем, включая распиан бустер, используют версию OpenSSL, которая IOT EDGE не поддерживается. Используйте следующую команду, чтобы установить более раннюю версию, прежде чем устанавливать IoT Edge: 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Виртуальные машины
Azure IoT Edge может выполняться на виртуальных машинах. Использование виртуальной машины в качестве устройства IoT Edge является наиболее распространенным, когда клиенты хотят расширить существующую инфраструктуру с помощью пограничной аналитики. Семейство ОС узла виртуальной машины должно совпадать с семейством гостевой ОС, используемой в контейнере модуля. Это требование аналогично тому, когда Azure IoT Edge выполняется непосредственно на устройстве. Azure IoT Edge не зависит от используемой технологии виртуализации и работает на виртуальных машинах на базе таких платформ, как Hyper-V и vSphere.

<br>
<center>

![Azure IoT Edge на виртуальной машине @ no__t-1 @ no__t-2

## <a name="minimum-system-requirements"></a>Минимальные системные требования
Azure IoT Edge отлично работает на различных устройствах, от Raspberry Pi3 до серверного оборудования. Выбор подходящего оборудования для вашего сценария зависит от рабочих нагрузок, которые необходимо выполнить. Принятие окончательного решения может быть затруднительно. Но можно легко запустить прототип решения на традиционных переносных или настольных компьютерах.

Эксперименты во время создания прототипов помогут вам сделать окончательный выбор устройства. Необходимо учитывать следующие вопросы: 

* Сколько модулей находится в рабочей нагрузке?
* Сколько слоев выполняет общий доступ к контейнерам модулей?
* На каком языке написаны ваши модули? 
* Какой объем данных будут обрабатывать ваши модули?
* Требуются ли вашим модулям специализированное оборудование для ускорения рабочих нагрузок?
* Каковы желаемые характеристики производительности вашего решения?
* Что такое бюджет оборудования?
