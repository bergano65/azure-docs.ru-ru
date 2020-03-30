---
title: Поддерживаемые функции, доступные в Центре безопасности Azure Документы Майкрософт
description: В этом документе приводится список служб, поддерживаемых Центром безопасности Azure.
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
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245243"
---
# <a name="feature-coverage-for-machines"></a>Покрытие функций для машин

В приведенных ниже таблицах показаны функции Центра безопасности Azure, доступные для виртуальных машин и серверов.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Поддерживаемые функции для виртуальных машин и серверов<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Компьютеры Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Функция**|**Виртуальные машины Azure**|**Масштабируемые наборы виртуальных машин Azure**|**Машины, не являемые лазурными средствами**|**Цены**
|[Интеграция Microsoft Defender ATP](security-center-wdatp.md)|✔</br>(на поддерживаемых версиях)|✔</br>(на поддерживаемых версиях)|✔|Standard|
|[Виртуальная машина поведенческой аналитики (и оповещения безопасности)](threat-protection.md)|✔|✔|✔|Рекомендации (бесплатно) </br></br> Предупреждения о безопасности (стандарт)|
|[Оповещений о безопасности без файлов](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Сетевые оповещения безопасности](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Просто-в-времени VM доступ](security-center-just-in-time.md)|✔|-|-|Standard|
|[Оценка уязвимости коренных народов](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Мониторинг целостности файлов](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Элементы управления адаптивной приложениями](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Карта сети](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Затвердевание адаптивной сети](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Адаптивные элементы управления сетью|✔|✔|-|Standard|
|[Отчеты & панели нормативных требований](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Рекомендации и защита от угроз на контейнерах IaaS, размещенных в Docker|-|-|-|Standard|
|Отсутствующие патчи ОС оценки|✔|✔|✔|Free|
|Оценка ошибок безопасности|✔|✔|✔|Free|
|[Оценка защиты конечных точек](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Free|
|Оценка шифрования дисков|✔|✔|-|Free|
|Оценка уязвимости третьих сторон|✔|-|-|Free|
|[Оценка сетевой безопасности](security-center-network-recommendations.md)|✔|✔|-|Free|


### <a name="linux-machines"></a>[Компьютеры Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Функция**|**Виртуальные машины Azure**|**Масштабируемые наборы виртуальных машин Azure**|**Машины, не являемые лазурными средствами**|**Цены**
|[Интеграция Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|Standard|
|[Виртуальная машина поведенческой аналитики (и оповещения безопасности)](security-center-alerts-iaas.md)|✔</br>(на поддерживаемых версиях)|✔</br>(на поддерживаемых версиях)|✔|Рекомендации (бесплатно) </br></br> Предупреждения о безопасности (стандарт)|
|[Оповещений о безопасности без файлов](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Сетевые оповещения безопасности](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Просто-в-времени VM доступ](security-center-just-in-time.md)|✔|-|-|Standard|
|[Оценка уязвимости коренных народов](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Мониторинг целостности файлов](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Элементы управления адаптивной приложениями](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Карта сети](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Затвердевание адаптивной сети](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Адаптивные элементы управления сетью|✔|✔|-|Standard|
|[Отчеты & панели нормативных требований](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Рекомендации и защита от угроз на контейнерах IaaS, размещенных в Docker|✔|✔|✔|Standard|
|Отсутствующие патчи ОС оценки|✔|✔|✔|Free|
|Оценка ошибок безопасности|✔|✔|✔|Free|
|[Оценка защиты конечных точек](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Free|
|Оценка шифрования дисков|✔|✔|-|Free|
|Оценка уязвимости третьих сторон|✔|-|-|Free|
|[Оценка сетевой безопасности](security-center-network-recommendations.md)|✔|✔|-|Free|

--- 


> [!TIP]
>Чтобы поэкспериментировать с функциями, которые доступны только на стандартном уровне ценообразования, пользователи бесплатного уровня могут зарегистрироваться в 30-дневной пробной версии. Для получения дополнительной информации смотрите [страницу ценообразования](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Поддерживаемые решения по защите конечных точек<a name="endpoint-supported"></a>

В таблице ниже приведена матрица условий:

 - Можно ли использовать Центр безопасности Azure для установки отдельных решений.
 - Какие решения по защите конечных точек может обнаружить Центр безопасности. Если обнаружено решение защиты конечных точек из этого списка, Центр безопасности не рекомендует устанавливать его.

Для получения информации о том, когда будут [Endpoint Protection Assessment and Recommendations](security-center-endpoint-protection.md)получены рекомендации для каждой из этих мер защиты, см.

| Защита конечных точек| Платформы | Установка центра безопасности | Обнаружение центра безопасности |
|------|------|-----|-----|
| Защитник Windows (антивредоносное ПО Майкрософт)| Windows Server 2016| Нет, встроен в ОС| Да |
| System Center Endpoint Protection (антивредоносное ПО Майкрософт) | Windows Server 2012 R2, 2012, 2008 R2 (см. примечание ниже) | Через расширение | Да |
| Trend Micro - Все версии | Семейство Windows Server  | нет | Да |
| Symantec v12.1.1100+| Семейство Windows Server  | нет | Да |
| McAfee v10+ | Семейство Windows Server  | нет | Да |
| McAfee v10+ | Семья Linux Сервер  | нет | Да**\*** |
| Софос V9| Семья Linux Сервер  | нет | Да**\***  |

 **\*** Состояние покрытия и вспомогательные данные в настоящее время доступны только в рабочей области Log Analytics, связанной с защищенными подписками. Это не отражено на портале Центра безопасности Azure.

> [!NOTE]
> - Чтобы обнаружить службу регистрации сертификатов для сетевых устройств (SCEP) на виртуальной машине Windows Server 2008 R2, служба SCEP должна быть установлена после установки PowerShell 3.0 (или более поздней версии).
> - Обнаружение защиты Trend Micro поддерживается агентами Deep Security.  Агенты OfficeScan не поддерживаются.


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [Центр безопасности собирает данные и агент по аналитике журналов.](security-center-enable-data-collection.md)
- Узнайте, как [Центр безопасности управляет и защищает данные.](security-center-data-security.md)
- Узнайте, как [спланировать и понять соображения проектирования, чтобы принять Центр безопасности Azure.](security-center-planning-and-operations-guide.md)
- Просмотрите [платформы, поддерживающие центр безопасности.](security-center-os-coverage.md)
- Узнайте больше о [защите угроз для windows и Linux-машин в Центре безопасности Azure.](threat-protection.md#windows-machines)
- Найдите [часто задаваемые вопросы о Центре безопасности Azure.](faq-general.md)