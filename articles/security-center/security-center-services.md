---
title: Поддерживаемые функции, доступные в центре безопасности Azure | Документация Майкрософт
description: В этом документе представлен список служб, поддерживаемых центром безопасности Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 275598aa50c252512348f4a04543e1beaf538626
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529486"
---
# <a name="supported-features-available-in-azure-security-center"></a>Поддерживаемые функции, доступные в центре безопасности Azure

> [!NOTE]
>Некоторые функции доступны только на уровне "Стандартный". Если вы еще не подписались на уровень "Стандартный" центра безопасности, доступен бесплатный пробный период. Дополнительные сведения см. на [странице цен на центр безопасности](https://azure.microsoft.com/pricing/details/security-center/).

В следующих разделах показаны функции центра безопасности, доступные для [поддерживаемых платформ](security-center-os-coverage.md).

* [Виртуальные машины и серверы](#vm-server-features)
* [Службы PaaS](#paas-services)


## Поддерживаемые функции виртуальных машин и серверов<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Сервер|Windows|||Linux|||Стоимость|
|----|----|----|----|----|----|----|----|
|**Environment**|**Azure**||**Не в Azure**|**Azure**||**Не в Azure**||
||**Виртуальная машина**|**масштабируемый набор виртуальных машин**;||**Виртуальная машина**|**масштабируемый набор виртуальных машин**;|
|[Интеграция ATP в защитнике Майкрософт](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (в поддерживаемых версиях)|✔ (в поддерживаемых версиях)|✔|X|X|X|Стандарт|
|[Оповещения обнаружения угроз для аналитики поведения виртуальной машины](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (в поддерживаемых версиях)|✔ (в поддерживаемых версиях)|✔|Обнаружение угроз (бесплатное) (Standard)|
|[Оповещения об обнаружении угроз, не имеющие файлов](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|X|X|X|Стандарт|
|[Оповещения об обнаружении угроз на основе сети](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|X|✔|✔|X|Стандарт|
|[JIT-доступ к виртуальной машине](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|X|X|✔|X|X|Стандарт|
|[Мониторинг целостности файлов](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Стандарт|
|[Адаптивные элементы управления приложением](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|X|✔|✔|X|✔|Стандарт|
|[Сетевая схема](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|X|✔|✔|X|Стандарт|
|[Адаптивное усиление защиты сети](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|X|X|✔|X|X|Стандарт|
|Адаптивные элементы управления сетью|✔|✔|X|✔|✔|X|Стандарт|
|[& Отчетов панели мониторинга соответствия нормативным требованиям](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Стандарт|
|Рекомендации и обнаружение угроз в контейнерах IaaS, размещенных в DOCKER|X|X|X|✔|✔|✔|Стандарт|
|Оценка отсутствующих исправлений ОС|✔|✔|✔|✔|✔|✔|Бесплатные|
|Оценка ненастроек безопасности|✔|✔|✔|✔|✔|✔|Бесплатные|
|[Оценка Endpoint Protection](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|X|X|X|Бесплатные|
|Оценка шифрования диска|✔|✔|X|✔|✔|X|Бесплатные|
|Оценка уязвимостей сторонних производителей|✔|X|X|✔|X|X|Бесплатные|
|[Оценка сетевой безопасности](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)|✔|✔|X|✔|✔|X|Бесплатные|

### Поддерживаемые решения для защиты конечных точек<a name="endpoint-supported"></a>

В таблице ниже приведена матрица условий:

 - Можно ли использовать Центр безопасности Azure для установки отдельных решений.
 - Какие решения по защите конечных точек может обнаружить Центр безопасности. Если обнаружено решение для защиты конечных точек из этого списка, центр безопасности не рекомендует устанавливать его.

Сведения о том, когда для каждой из этих защиты создаются рекомендации, см. в разделе [Endpoint Protection Оценка и рекомендации](security-center-endpoint-protection.md).

| Endpoint Protection| Платформы | Установка центра безопасности | Обнаружение центра безопасности |
|------|------|-----|-----|
| Защитник Windows (антивредоносное ПО Майкрософт)| Windows Server 2016| Нет, встроен в ОС| ДА |
| System Center Endpoint Protection (антивредоносное ПО Майкрософт) | Windows Server 2012 R2, 2012, 2008 R2 (см. примечание ниже) | Через расширение | ДА |
| Trend Micro — все версии * | Семейство Windows Server  | Нет | ДА |
| Symantec v12.1.1100+| Семейство Windows Server  | Нет | ДА |
| McAfee v10+ | Семейство Windows Server  | Нет | ДА |
| McAfee v10+ | Семейство серверов Linux  | Нет | Да **\*** |
| Sophos V9 +| Семейство серверов Linux  | Нет | Да **\***  |

 **\*** Состояние покрытия и поддерживающие данные в настоящее время доступны только в рабочей области Log Analytics, связанной с защищенными подписками. Он не отражается на портале Центра безопасности Azure.

> [!NOTE]
>
> - Чтобы обнаружить службу регистрации сертификатов для сетевых устройств (SCEP) на виртуальной машине Windows Server 2008 R2, служба SCEP должна быть установлена после установки PowerShell 3.0 (или более поздней версии).
> - Обнаружение микрозащиты Trend Micro поддерживается для агентов глубокой безопасности.  Агенты OfficeScan не поддерживаются.


## Поддерживаемые функции <a name="paas-services"></a> служб PaaS

Центр безопасности Azure поддерживает следующие ресурсы PaaS:

|Служба|Рекомендации (бесплатные)|Обнаружение угроз (стандартная)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB|X| ✔|
|Хранилище BLOB-объектов|✔| ✔|
|Учетная запись хранения|✔| X|
|Служба приложений|✔| ✔|
|Функция|✔| X|
|Облачная служба|✔| X|
|Виртуальная сеть|✔| X|
|Подсеть|✔| X|
|Сетевая карта|✔| X|
|Группа безопасности сети (NSG)|✔| X|
|Subscription|✔ * *| ✔|
|Учетная запись Пакетной службы|✔| X|
|Учетная запись Service Fabric|✔| X|
|Учетная запись службы автоматизации|✔| X|
|Подсистема балансировки нагрузки|✔| X|
|служба поиска.|✔| X|
|Пространство имен служебной шины|✔| X|
|Stream Analytics|✔| X|
|Пространство имен концентратора событий|✔| X|
|Логические приложения|✔| X|
|Redis|✔| X|
|Аналитика озера данных|✔| X|
|Data Lake Storage|✔| X|
|Key Vault;|✔| X|

\* В настоящее время эти возможности поддерживаются в общедоступной предварительной версии.

рекомендации по \* \* Azure Active Directory (Azure AD) доступны только для стандартных подписок.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [Центр безопасности собирает данные и агент log Analytics](security-center-enable-data-collection.md).
- Узнайте [, как центр безопасности управляет и защищает данные](security-center-data-security.md).
- Узнайте, как [спланировать работу в Центре безопасности Azure, и получите рекомендации по переходу к его использованию](security-center-planning-and-operations-guide.md).
- Ознакомьтесь с [платформами, поддерживающими центр безопасности](security-center-os-coverage.md).
- Дополнительные сведения об [обнаружении угроз для виртуальных машин & серверах в центре безопасности Azure](security-center-alerts-iaas.md).
- Ознакомьтесь с [часто задаваемыми вопросами об использовании Центра безопасности Azure](security-center-faq.md).
- Изучите [записи блога, посвященные безопасности и соответствию требованиям в Azure](https://blogs.msdn.com/b/azuresecurity/).
