---
title: Поддерживаемые функции, доступные в Центре безопасности Azure | Документация Майкрософт
description: Этот документ содержит список поддерживаемых служб в Центре безопасности Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: a311439e8efc5481fbfd7431c1514ba6be576e39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858019"
---
# <a name="feature-coverage-for-machines"></a>Поддерживаемые функции для виртуальных машин

На двух вкладках ниже перечислены функции Центра безопасности Azure, доступные для виртуальных машин и серверов Windows и Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Поддерживаемые функции для виртуальных машин и серверов <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Компьютеры Windows**](#tab/features-windows)

|**Возможность**|**Виртуальные машины Azure**|**Масштабируемые наборы виртуальных машин Azure**|**Компьютеры, не связанные с Azure**|**Требуется ли Azure Defender**
|----|:----:|:----:|:----:|:----:|
|[Интеграция с ATP в Microsoft Defender](security-center-wdatp.md)|✔</br>(в поддерживаемых версиях)|✔</br>(в поддерживаемых версиях)|✔|Да|
|[Аналитика поведения виртуальных машин (и оповещения системы безопасности)](alerts-reference.md)|✔|✔|✔|Да|
|[Оповещения системы безопасности о бесфайловых угрозах](alerts-reference.md#alerts-windows)|✔|✔|✔|Да|
|[Оповещения системы безопасности об угрозах на уровне сети](other-threat-protections.md#network-layer)|✔|✔|-|Да|
|[JIT-доступ к виртуальной машине](security-center-just-in-time.md)|✔|-|-|Да|
|[Нативная оценка уязвимостей](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Да|
|[Мониторинг целостности файлов](security-center-file-integrity-monitoring.md)|✔|✔|✔|Да|
|[Адаптивные элементы управления приложениями](security-center-adaptive-application.md)|✔|-|✔|Да|
|[Карта сети](security-center-network-recommendations.md#network-map)|✔|✔|-|Да|
|[Адаптивная защита сети](security-center-adaptive-network-hardening.md)|✔|-|-|Да|
|[Соответствие нормативным требованиям: панель мониторинга и отчеты](security-center-compliance-dashboard.md)|✔|✔|✔|Да|
|Рекомендации и защита от угроз для IaaS-контейнеров Docker|-|-|-|Да|
|Оценка на предмет отсутствующих исправлений ОС|✔|✔|✔|Azure Нет<br><br>Не связанные с Azure: Да|
|Оценка на предмет неправильных настроек системы безопасности|✔|✔|✔|Azure Нет<br><br>Не связанные с Azure: Да|
|[Оценка защиты конечных точек](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure Нет<br><br>Не связанные с Azure: Да|
|Оценка шифрования дисков|✔</br>(в [поддерживаемых сценариях](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Нет|
|Оценка уязвимостей сторонних решений|✔|-|-|Нет|
|[Оценка безопасности сети](security-center-network-recommendations.md)|✔|✔|-|Нет|


### <a name="linux-machines"></a>[**Компьютеры Linux**](#tab/features-linux)

|**Возможность**|**Виртуальные машины Azure**|**Масштабируемые наборы виртуальных машин Azure**|**Компьютеры, не связанные с Azure**|**Требуется ли Azure Defender**
|----|:----:|:----:|:----:|:----:|
|[Интеграция с ATP в Microsoft Defender](security-center-wdatp.md)|-|-|-|Да|
|[Аналитика поведения виртуальных машин (и оповещения системы безопасности)](security-center-alerts-iaas.md)|✔</br>(в поддерживаемых версиях)|✔</br>(в поддерживаемых версиях)|✔|Да|
|[Оповещения системы безопасности о бесфайловых угрозах](alerts-reference.md#alerts-windows)|-|-|-|Да|
|[Оповещения системы безопасности об угрозах на уровне сети](other-threat-protections.md#network-layer)|✔|✔|-|Да|
|[JIT-доступ к виртуальной машине](security-center-just-in-time.md)|✔|-|-|Да|
|[Нативная оценка уязвимостей](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Да|
|[Мониторинг целостности файлов](security-center-file-integrity-monitoring.md)|✔|✔|✔|Да|
|[Адаптивные элементы управления приложениями](security-center-adaptive-application.md)|✔|-|✔|Да|
|[Карта сети](security-center-network-recommendations.md#network-map)|✔|✔|-|Да|
|[Адаптивная защита сети](security-center-adaptive-network-hardening.md)|✔|-|-|Да|
|[Соответствие нормативным требованиям: панель мониторинга и отчеты](security-center-compliance-dashboard.md)|✔|✔|✔|Да|
|Рекомендации и защита от угроз для IaaS-контейнеров Docker|✔|✔|✔|Да|
|Оценка на предмет отсутствующих исправлений ОС|✔|✔|✔|Azure Нет<br><br>Не связанные с Azure: Да|
|Оценка на предмет неправильных настроек системы безопасности|✔|✔|✔|Azure Нет<br><br>Не связанные с Azure: Да|
|[Оценка защиты конечных точек](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Нет|
|Оценка шифрования дисков|✔</br>(в [поддерживаемых сценариях](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Нет|
|Оценка уязвимостей сторонних решений|✔|-|-|Нет|
|[Оценка безопасности сети](security-center-network-recommendations.md)|✔|✔|-|Нет|

--- 


> [!TIP]
>Чтобы проверить работу функций, доступных только с Azure Defender, вы можете зарегистрироваться для получения 30-дневной бесплатной пробной версии этой службы. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Поддерживаемые решения для защиты конечных точек <a name="endpoint-supported"></a>

В таблице ниже приведена матрица условий:

 - Можно ли использовать Центр безопасности Azure для установки отдельных решений.
 - Какие решения по защите конечных точек может обнаружить Центр безопасности. Если Центр безопасности обнаружит решение, указанное в списке ниже, не следует устанавливать его.

Сведения о том, на основе чего создаются рекомендации по установке каждого из этих решений, см. в статье об [оценке и рекомендациях, связанных с защитой конечных точек](security-center-endpoint-protection.md).

| Endpoint Protection| Платформы | Установка центра безопасности | Обнаружение центра безопасности |
|------|------|-----|-----|
| Антивирусная программа Microsoft Defender| Windows Server 2016 или более поздней версии.| Нет, встроен в ОС| Да |
| System Center Endpoint Protection (антивредоносное ПО Майкрософт) | Windows Server 2012 R2, 2012, 2008 R2 (см. примечание ниже) | Через расширение | Да |
| Trend Micro — агент Deep Security | Семейство Windows Server  | Нет | Да |
| Symantec v12.1.1100+| Семейство Windows Server  | Нет | Да |
| McAfee v10+ | Семейство Windows Server  | Нет | Да |
| McAfee v10+ | Семейство Linux Server  | Нет | Да **\*** |
| Sophos, начиная с версии 9| Семейство Linux Server  | Нет | Да **\***  |

 **\*** Сведения о поддержке и вспомогательные данные сейчас доступны только в рабочей области Log Analytics, связанной с защищенными подписками. Они не отображаются на портале Центра безопасности Azure.

> [!NOTE]
> Чтобы служба System Center Endpoint Protection (SCEP) была доступна для обнаружения на виртуальной машине Windows Server 2008 R2, службу нужно установить после установки PowerShell 3.0 или более поздней версии.


## <a name="next-steps"></a>Следующие шаги

- Узнайте, как [в Центре безопасности собираются данные с помощью агента Log Analytics](security-center-enable-data-collection.md).
- Узнайте, как [Центр безопасности управляет данными и защищает их](security-center-data-security.md).
- Просмотрите [платформы, которые поддерживают Центр безопасности](security-center-os-coverage.md).
