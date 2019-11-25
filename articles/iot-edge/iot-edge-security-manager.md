---
title: Защита устройств и ПО с помощью диспетчера безопасности Azure IoT Edge | Документация Майкрософт
description: Управляет состоянием безопасности устройства IoT Edge и целостностью служб безопасности.
services: iot-edge
keywords: безопасность, безопасный элемент, анклава, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 90cb7cf0a30ea0ebfe00454288de25ddf6e58d52
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457530"
---
# <a name="azure-iot-edge-security-manager"></a>Диспетчер безопасности Azure IoT Edge

Диспетчер безопасности Azure IoT Edge — это хорошо ограниченное ядро обеспечения ​​безопасности для защиты устройства IoT Edge и всех его компонентов путем абстрагирования защищенного кремниевого оборудования. It is the focal point for security hardening and provides technology integration point to original equipment manufacturers (OEM).

![Диспетчер безопасности Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

Диспетчер безопасности IoT Edge предназначен для защиты целостности устройства IoT Edge и всех встроенных программных операций. The security manager transitions trust from underlying hardware root of trust hardware (if available) to bootstrap the IoT Edge runtime and monitor ongoing operations.  По сути, диспетчер безопасности IoT Edge — это программное обеспечение, работающее вместе с безопасным оборудованием с микросхемами (где это доступно) и позволяющее обеспечить максимально возможные гарантии безопасности.  

В задачи диспетчера безопасности IoT Edge входят (это не исчерпывающий список):

* Безопасная и измеренная начальная загрузка устройства Azure IoT Edge.
* Подготовка удостоверения устройства и передача доверия, где это применимо.
* Размещение и защита компонентов облачных служб для устройства, например службы подготовки устройств.
* Безопасная подготовка модулей IoT Edge с уникальными идентификаторами.
* Хранение корня доверия оборудования с помощью нотариальных служб.
* Мониторинг целостности операций IoT Edge во время выполнения.

Диспетчер безопасности IoT Edge состоит из трех компонентов:

* управляющая программа безопасности IoT Edge;
* уровень абстракции платформы аппаратного модуля безопасности (HSM);
* необязательный, но очень рекомендуемый корень доверия кремниевого оборудования или HSM.

## <a name="the-iot-edge-security-daemon"></a>Управляющая программа безопасности IoT Edge

The IoT Edge security daemon is responsible for the logical operations of IoT Edge security manager. It represents a significant portion of the trusted computing base of the IoT Edge device. 

### <a name="design-principles"></a>Принципы проектирования

Управляющая программа безопасности IoT Edge придерживается двух основных принципов: максимальное увеличение операционной целостности, минимизация раздувания и обновления.

#### <a name="maximize-operational-integrity"></a>Максимальное увеличение операционной целостности

The IoT Edge security daemon operates with the highest integrity possible within the defense capability of any given root of trust hardware. При правильной интеграции корень доверия оборудования проверяет и отслеживает управляющую программу безопасности в статическом состоянии и во время выполнения, чтобы противостоять незаконному изменению.

Физический доступ всегда является угрозой для устройств Интернета вещей. Корень доверия оборудования играет важную роль в защите целостности управляющей программы безопасности IoT Edge.  Hardware root of trust come in two varieties:

* безопасные элементы для защиты конфиденциальной информации, такой как секреты и криптографические ключи;
* безопасные внутренние зоны для защиты секретов, например ключей, и уязвимых рабочих нагрузок, таких как выставление счетов и ценообразование.

Two kinds of execution environments exist to use hardware root of trust:

* The standard or rich execution environment (REE) that relies on the use of secure elements to protect sensitive information.
* The trusted execution environment (TEE) that relies on the use of secure enclave technology to protect sensitive information and offer protection to software execution.

For devices using secure enclaves as hardware root of trust, sensitive logic within IoT Edge security daemon should be inside the enclave.  Non-sensitive portions of the security daemon can be outside of the TEE.  In any case, original design manufacturers (ODM) and original equipment manufacturers (OEM) should extend trust from their HSM to measure and defend the integrity of the IoT Edge security daemon at boot and runtime.

#### <a name="minimize-bloat-and-churn"></a>Минимизация раздувания и обновления

Другой основной принцип управляющей программы безопасности IoT Edge заключается в минимизации обновлений.  Для высшего уровня доверия управляющая программа безопасности IoT Edge может тесно соединяться с корнем доверия оборудования устройства и работать как собственный код.  It's common for these types of realizations to update the daemon software through the hardware root of trust's secure update paths (as opposed to OS provided update mechanisms), which can be challenging in some scenarios.  While security renewal is recommended for IoT devices, excessive update requirements or large update payloads can expand the threat surface in many ways.  К примерам относятся пропуски обновлений, чтобы увеличить операционную доступность, или корень доверия устройства, слишком ограниченный, чтобы обрабатывать объемные полезные данные обновления.  As such, the design of IoT Edge security daemon is concise to keep the footprint and trusted computing base small and to minimize update requirements.

### <a name="architecture-of-iot-edge-security-daemon"></a>Архитектура управляющей программы безопасности IoT Edge

![Управляющая программа безопасности Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

The IoT Edge security daemon takes advantage of any available hardware root of trust technology for security hardening.  It also allows for split-world operation between a standard/rich execution environment (REE) and a trusted execution environment (TEE) when hardware technologies offer trusted execution environments. Role-specific interfaces enable the major components of IoT Edge to assure the integrity of the IoT Edge device and its operations.

#### <a name="cloud-interface"></a>Облачный интерфейс

The cloud interface allows the IoT Edge security daemon to access cloud services such as cloud compliments to device security like security renewal.  For example, the IoT Edge security daemon currently uses this interface to access the Azure IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/) for device identity lifecycle management.  

#### <a name="management-api"></a>Управление API

IoT Edge security daemon offers a management API, which is called by the IoT Edge agent when creating/starting/stopping/removing an IoT Edge module. The security daemon stores “registrations” for all active modules. Эти регистрации сопоставляют идентификатор модуля с некоторыми его свойствами. Примерами этих свойств являются идентификатор процесса (pid), выполняющегося в контейнере, или хэш содержимого контейнера Docker.

These properties are used by the workload API (described below) to verify that the caller is authorized to perform an action.

The management API is a privileged API, callable only from the IoT Edge agent.  Так как управляющая программа безопасности IoT Edge обеспечивает начальную загрузку и запускает агент IoT Edge, она может создать неявную регистрацию для агента IoT Edge после того, как подтвердит, что агент IoT Edge не был незаконно изменен. The same attestation process that the workload API uses also restricts access to the management API to only the IoT Edge agent.

#### <a name="container-api"></a>API контейнера

The container API interacts with the container system in use for module management, like Moby or Docker.

#### <a name="workload-api"></a>API рабочей нагрузки

The workload API is accessible to all modules. It provides proof of identity, either as an HSM rooted signed token or an X509 certificate, and the corresponding trust bundle to a module. Пакет доверия содержит сертификаты ЦС для всех других серверов, которым должны доверять модули.

The IoT Edge security daemon uses an attestation process to guard this API. When a module calls this API, the security daemon attempts to find a registration for the identity. В случае обнаружения она использует свойства регистрации для измерения модуля. If the result of the measurement process matches the registration, a new proof of identity is generated. Соответствующие сертификаты ЦС (пакет доверия) возвращаются в модуль.  Модуль использует этот сертификат для подключения к Центру Интернета вещей, другим модулям или запуска сервера. When the signed token or certificate nears expiration, it's the responsibility of the module to request a new certificate. 

### <a name="integration-and-maintenance"></a>Интеграция и обслуживание

Корпорация Майкрософт поддерживает основную базу кода для управляющей программы безопасности [IoT Edge на GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Установка и обновления

Установкой и обновлением управляющей программы безопасности IoT Edge управляет система управления пакетами операционной системы. Устройства IoT Edge с корнем доверия оборудования должны обеспечить дополнительное улучшение целостности управляющей программы, управляя жизненным циклом устройства с помощью систем управления загрузкой и обновлением. Device makers should explore these avenues based on their respective device capabilities.

#### <a name="versioning"></a>Управление версиями

Среда выполнения IoT Edge отслеживает и сообщает версию управляющей программы безопасности IoT Edge. Версия сообщается как атрибут *среда выполнения.платформа.версия* передаваемого свойства модуля агента IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Уровень абстракции платформы аппаратного модуля безопасности (HSM)

Уровень абстракции платформы HSM абстрагирует все корни доверия оборудования, чтобы избавить разработчика или пользователя IoT Edge от сложностей.  It includes a combination of application programming interface (API) and trans-domain communication procedures, for example communication between a standard execution environment and a secure enclave.  Фактическая реализация уровня абстракции платформы HSM зависит от конкретного используемого защищенного оборудования. Его существование позволяет использовать практически любое безопасное оборудование с микросхемами.

## <a name="secure-silicon-root-of-trust-hardware"></a>Корень доверия надежного кремниевого оборудования

Надежное кремниевое оборудование необходимо для закрепления доверия внутри аппаратного устройства IoT Edge.  Существует большое количество надежного кремниевого оборудования, включая доверенный платформенный модуль (TPM), встроенные защищенные элементы (eSM), ARM Trustzone, Intel SGX, а также настраиваемые технологии надежного кремниевого оборудования.  The use of secure silicon root of trust in devices is recommended given the threats associated with physical accessibility of IoT devices.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Интеграция и обслуживание диспетчера безопасности IoT Edge

Цель диспетчера безопасности IoT Edge — определить и изолировать компоненты, которые повышают безопасность и обеспечивают целостность платформы Azure IoT Edge. Третьи стороны, например изготовители, должны применять пользовательские функции безопасности, доступные для оборудования их устройств.  Изучите следующий раздел со ссылками, демонстрирующими усиление защиты диспетчера безопасности Интернета вещей Azure с помощью доверенного платформенного модуля (TPM) на платформах Linux и Windows. В этих примерах используется программное обеспечение или виртуальные модули TPM, но они непосредственно применяются к дискретным устройствам TPM.  

## <a name="next-steps"></a>Дальнейшие действия

Просмотрите блог, посвященный [обеспечению безопасности интеллектуальной границы](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Перейдите к созданию и подготовке устройства IoT Edge с помощью [виртуального доверенного платформенного модуля на виртуальной машине Linux](how-to-auto-provision-simulated-device-linux.md).

Перейдите к созданию и подготовке устройства IoT Edge с помощью [имитированного доверенного платформенного модуля в Windows](how-to-auto-provision-simulated-device-windows.md).
