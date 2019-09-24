---
title: Платформы, поддерживаемые центром безопасности Azure | Документация Майкрософт
description: В этом документе представлен список платформ, поддерживаемых центром безопасности Azure.
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
ms.openlocfilehash: 1d13db922ae84e4032304a8865ba6fcdafa65748
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201638"
---
# <a name="supported-platforms"></a>Поддерживаемые платформы 

## Виртуальные машины и серверы<a name="vm-server"></a>

Центр безопасности поддерживает виртуальные машины и серверы в различных гибридных средах:

* Только Azure
* Azure и локальная среда
* Azure и другие облака
* Azure, другие облака и локальная среда

Для среды Azure, активированной в подписке Azure, центр безопасности Azure автоматически обнаружит ресурсы IaaS, развернутые в подписке.

> [!NOTE]
> Чтобы получить полный набор функций безопасности, необходимо установить [агент log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent), который используется центром безопасности Azure, установлен и [правильно настроен для отправки данных в центр безопасности Azure](security-center-enable-data-collection.md#manual-agent).


В следующих разделах перечислены поддерживаемые серверные операционные системы, на которых можно запустить [агент log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent), используемый центром безопасности Azure.

### Операционные системы Windows Server<a name="os-windows"></a>

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Интеграция с Microsoft Defender ATP поддерживает только Windows Server 2012 R2 и Windows Server 2016.

Дополнительные сведения о поддерживаемых функциях для операционных систем Windows, перечисленных выше, см. в разделе [Поддерживаемые функции виртуальных машин и серверов](security-center-services.md##vm-server-features).

### Операционные системы Linux<a name="os-linux"></a>

64-разрядная

* CentOS 6 и 7
* Amazon Linux 2017.09
* Oracle Linux 6 и 7
* Red Hat Enterprise Linux Server 6 и 7
* Debian GNU/Linux 8 и 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS и 18.04 LTS
* SUSE Linux Enterprise Server 12

32-битная
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 и 9
* Ubuntu Linux 14,04 LTS и 16,04 LTS

> [!NOTE]
> Так как список поддерживаемых операционных систем Linux постоянно меняется, если вы предпочитаете, щелкните [здесь](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) , чтобы просмотреть самый актуальный список поддерживаемых версий, на тот случай, если были внесены изменения с момента последней публикации этого раздела.

Дополнительные сведения о поддерживаемых функциях для операционных систем Linux, перечисленных выше, см. в разделе [Поддерживаемые функции виртуальных машин и серверов](security-center-services.md##vm-server-features).

### Управляемые службы виртуальных машин<a name="virtual-machine"></a>

Виртуальные машины также создаются в подписке клиента как часть некоторых управляемых служб Azure, таких как Azure Kubernetes (AKS), Azure Databricks и т. д. Эти виртуальные машины также обнаруживаются центром безопасности Azure, и агент log Analytics можно установить и настроить в соответствии с поддерживаемыми [операционными системами Windows или Linux](#os-windows), приведенными выше.

### Облачные службы<a name="cloud-services"></a>

Также поддерживаются виртуальные машины, работающие в облачной службе. Мониторинг выполняется только для облачных служб и рабочих ролей, запущенных в слотах рабочей среды. Дополнительные сведения см. в статье [Общие сведения об облачных службах Azure](../cloud-services/cloud-services-choose-me.md).

## Службы PaaS<a name="paas-services"></a>

Центр безопасности Azure поддерживает следующие ресурсы Azure PaaS:

* SQL
* PostGreSQL
* MySQL
* Cosmos DB
* Учетная запись хранения
* Служба приложений
* Функция
* Облачная служба
* Вирт. сеть
* Subnet
* NIC
* Группа безопасности сети
* Учетная запись пакетной службы
* Учетная запись Service Fabric
* Учетная запись автоматизации
* Балансировщик нагрузки
* Найти
* Пространство имен служебной шины
* Stream Analytics
* Пространство имен концентратора событий
* Приложения логики
* Redis
* Data Lake Analytics
* Data Lake Store
* Хранилище ключей

Дополнительные сведения о поддерживаемых функциях для приведенного выше списка ресурсов PaaS см. в разделе [службы PaaS поддерживаемые функции](security-center-services.md#paas-services).

## <a name="next-steps"></a>Следующие шаги

- Узнайте, как [Центр безопасности собирает данные и агент log Analytics](security-center-enable-data-collection.md).
- Узнайте [, как центр безопасности управляет и защищает данные](security-center-data-security.md).
- Узнайте, как [спланировать работу в Центре безопасности Azure, и получите рекомендации по переходу к его использованию](security-center-planning-and-operations-guide.md).
- Сведения о [функциях, доступных для различных облачных сред](security-center-services.md).
- Дополнительные сведения об [обнаружении угроз для виртуальных машин & серверах в центре безопасности Azure](security-center-alerts-iaas.md).
- Ознакомьтесь с [часто задаваемыми вопросами об использовании Центра безопасности Azure](security-center-faq.md).
- Изучите [записи блога, посвященные безопасности и соответствию требованиям в Azure](https://blogs.msdn.com/b/azuresecurity/).
