---
title: Платформы, поддерживаемые Центром безопасности Azure (ru) Документы Майкрософт
description: В этом документе приводится список платформ, поддерживаемых Центром безопасности Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: f9b948714f72ba02a100d9941721f073953bf22a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473238"
---
# <a name="supported-platforms"></a>Поддерживаемые платформы 

## <a name="virtual-machines--servers"></a>Виртуальные машины / серверы<a name="vm-server"></a>

Центр безопасности поддерживает виртуальные машины / серверы на различных типах гибридных сред:

* Только Лазурный
* Лазурный и натерритории
* Лазурный и другие облака
* Лазурный, другие облака, и на территории

Для среды Azure, активированной в подписке Azure, Центр безопасности Azure автоматически обнаружит ресурсы IaaS, развернутые в подписи.

> [!NOTE]
> Чтобы получить полный набор функций безопасности, необходимо иметь [агент аналитики журнала,](../azure-monitor/platform/agents-overview.md#log-analytics-agent)который используется Центром безопасности Azure, установленный и [правильно настроенный для отправки данных в Центр безопасности Azure.](security-center-enable-data-collection.md#manual-agent)

В следующих разделах перечисляются поддерживаемые серверные операционные системы, на которых может работать [агент аналитики журналов,](../azure-monitor/platform/agents-overview.md#log-analytics-agent)который используется Центром безопасности Azure.

### <a name="windows-server-operating-systems"></a>Операционные системы сервера Windows<a name="os-windows"></a>

|OS|Поддерживается Центром безопасности Azure|Поддержка интеграции с АТС Microsoft Defender|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Чтобы узнать больше о поддерживаемых функциях для операционных систем Windows, перечисленных выше, [см.](security-center-services.md#vm-server-features)

### <a name="windows-operating-systems"></a>Операционные системы Windows<a name="os-windows (non-server)"></a>

Центр безопасности Azure интегрируется с службами Azure для мониторинга и защиты виртуальных машин на базе Windows.

### <a name="linux-operating-systems"></a>Операционные системы Linux<a name="os-linux"></a>

64-разрядная

* CentOS 6 и 7
* Amazon Linux 2017.09
* Oracle Linux 6 и Oracle Linux 7
* Red Hat Enterprise Linux Server 6 и 7
* Debian GNU/Linux 8 и 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS и 18.04 LTS
* SUSE Linux Enterprise Server 12

32-битная
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 и 9
* Ubuntu Linux 14.04 LTS, и 16.04 LTS

> [!NOTE]
> Поскольку список поддерживаемых операционных систем Linux постоянно меняется, если вы предпочитаете, нажмите [здесь,](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) чтобы просмотреть самый современный список поддерживаемых версий, в случае, если произошли изменения с момента последнего опубликования этой темы.

Чтобы узнать больше о поддерживаемых функциях для операционных систем Linux, перечисленных выше, [см.](security-center-services.md#vm-server-features)

### <a name="managed-virtual-machine-services"></a>Управляемые услуги виртуальных машин<a name="virtual-machine"></a>

Виртуальные машины также создаются в подписке клиентов как часть некоторых управляемых сервисов Azure, таких как Azure Kubernetes (AKS), Azure Databricks и многое другое. Эти виртуальные машины также обнаружены Центром безопасности Azure, и агент по аналитике журнала может быть установлен и настроен в соответствии с поддерживаемыми [операционными системами Windows/Linux,](#os-windows)перечисленными выше.

### <a name="cloud-services"></a>Облачные службы<a name="cloud-services"></a>

Поддерживаемы также виртуальные машины, запускаемые в облачном сервисе. Мониторинг выполняется только для облачных служб и рабочих ролей, запущенных в слотах рабочей среды. Дополнительные сведения см. в статье [Общие сведения об облачных службах Azure](../cloud-services/cloud-services-choose-me.md).

Защита виртуальных машин, проживающих в Azure Stack, также поддерживается. Для получения дополнительной информации об интеграции Центра безопасности с Azure Stack см. [На борту виртуальных машин Azure Stack в Центре безопасности.](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)

## <a name="next-steps"></a>Следующие шаги

- Узнайте, как [Центр безопасности собирает данные и агент по аналитике журналов.](security-center-enable-data-collection.md)
- Узнайте, как [Центр безопасности управляет и защищает данные.](security-center-data-security.md)
- Узнайте, как [спланировать и понять соображения проектирования, чтобы принять Центр безопасности Azure.](security-center-planning-and-operations-guide.md)
- Узнайте о [функциях, доступных для различных облачных сред.](security-center-services.md)
- Узнайте больше о [защите угроз для windows и Linux-машин в Центре безопасности Azure.](threat-protection.md#windows-machines)
