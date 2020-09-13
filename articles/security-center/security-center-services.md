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
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: c4f3ccce03f91b0567980d55b59bfb15d6985bb7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299751"
---
# <a name="feature-coverage-for-machines"></a>Покрытие компонентов для компьютеров

На двух вкладках ниже показаны функции центра безопасности Azure, доступные для виртуальных машин и серверов Windows и Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Поддерживаемые функции для виртуальных машин и серверов <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Компьютеры Windows**](#tab/features-windows)

|**Компонент**|**Виртуальные машины Azure**|**Масштабируемые наборы виртуальных машин Azure**|**Компьютеры, не относящиеся к Azure**|**Цены**
|----|:----:|:----:|:----:|:----:|
|[Интеграция ATP в защитнике Майкрософт](security-center-wdatp.md)|✔</br>(в поддерживаемых версиях)|✔</br>(в поддерживаемых версиях)|✔|Standard|
|[Аналитика поведения виртуальных машин (и оповещения системы безопасности)](threat-protection.md)|✔|✔|✔|Standard|
|[Оповещения системы безопасности, не имеющие файлов](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Оповещения системы безопасности на основе сети](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[JIT-доступ к виртуальной машине](security-center-just-in-time.md)|✔|-|-|Standard|
|[Собственная оценка уязвимостей](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Standard|
|[Мониторинг целостности файлов](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Адаптивные элементы управления приложениями](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Карта сети](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Адаптивная защита сети](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|[& отчетов панели мониторинга соответствия нормативным требованиям](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Рекомендации и защита от угроз в контейнерах IaaS, размещенных в DOCKER|-|-|-|Standard|
|Оценка отсутствующих исправлений ОС|✔|✔|✔|Azure: Бесплатная<br><br>Не в Azure: Стандартный|
|Оценка ненастроек безопасности|✔|✔|✔|Azure: Бесплатная<br><br>Не в Azure: Стандартный|
|[Оценка Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Бесплатная<br><br>Не в Azure: Стандартный|
|Оценка шифрования диска|✔|✔|-|Free|
|Оценка уязвимостей сторонних производителей|✔|-|-|Free|
|[Оценка сетевой безопасности](security-center-network-recommendations.md)|✔|✔|-|Free|


### <a name="linux-machines"></a>[**Компьютеры Linux**](#tab/features-linux)

|**Компонент**|**Виртуальные машины Azure**|**Масштабируемые наборы виртуальных машин Azure**|**Компьютеры, не относящиеся к Azure**|**Цены**
|----|:----:|:----:|:----:|:----:|
|[Интеграция ATP в защитнике Майкрософт](security-center-wdatp.md)|-|-|-|Standard|
|[Аналитика поведения виртуальных машин (и оповещения системы безопасности)](security-center-alerts-iaas.md)|✔</br>(в поддерживаемых версиях)|✔</br>(в поддерживаемых версиях)|✔|Standard|
|[Оповещения системы безопасности, не имеющие файлов](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Оповещения системы безопасности на основе сети](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[JIT-доступ к виртуальной машине](security-center-just-in-time.md)|✔|-|-|Standard|
|[Собственная оценка уязвимостей](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Standard|
|[Мониторинг целостности файлов](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Адаптивные элементы управления приложениями](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Карта сети](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Адаптивная защита сети](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|[& отчетов панели мониторинга соответствия нормативным требованиям](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Рекомендации и защита от угроз в контейнерах IaaS, размещенных в DOCKER|✔|✔|✔|Standard|
|Оценка отсутствующих исправлений ОС|✔|✔|✔|Azure: Бесплатная<br><br>Не в Azure: Стандартный|
|Оценка ненастроек безопасности|✔|✔|✔|Azure: Бесплатная<br><br>Не в Azure: Стандартный|
|[Оценка Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Free|
|Оценка шифрования диска|✔|✔|-|Free|
|Оценка уязвимостей сторонних производителей|✔|-|-|Free|
|[Оценка сетевой безопасности](security-center-network-recommendations.md)|✔|✔|-|Free|

--- 


> [!TIP]
>Чтобы поэкспериментировать с функциями, которые доступны только в ценовой категории "Стандартный", пользователи уровня "бесплатный" могут зарегистрироваться на 30-дневную пробную версию. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Поддерживаемые решения для защиты конечных точек <a name="endpoint-supported"></a>

В таблице ниже приведена матрица условий:

 - Можно ли использовать Центр безопасности Azure для установки отдельных решений.
 - Какие решения по защите конечных точек может обнаружить Центр безопасности. Если обнаружено решение для защиты конечных точек из этого списка, центр безопасности не рекомендует устанавливать его.

Сведения о том, когда для каждой из этих защиты создаются рекомендации, см. в разделе [Endpoint Protection Оценка и рекомендации](security-center-endpoint-protection.md).

| Endpoint Protection| Платформы | Установка центра безопасности | Обнаружение центра безопасности |
|------|------|-----|-----|
| Антивирусная программа Microsoft Defender| Windows Server 2016 или более поздней версии| Нет, встроен в ОС| Да |
| System Center Endpoint Protection (антивредоносное ПО Майкрософт) | Windows Server 2012 R2, 2012, 2008 R2 (см. примечание ниже) | Через расширение | Да |
| Trend Micro — глубокая безопасность | Семейство Windows Server  | Нет | Да |
| Symantec v12.1.1100+| Семейство Windows Server  | Нет | Да |
| McAfee v10+ | Семейство Windows Server  | Нет | Да |
| McAfee v10+ | Семейство серверов Linux  | Нет | Да **\*** |
| Sophos V9 +| Семейство серверов Linux  | Нет | Да  **\***  |

 **\*** Состояние покрытия и поддерживающие данные в настоящее время доступны только в рабочей области Log Analytics, связанной с защищенными подписками. Он не отражается на портале Центра безопасности Azure.

> [!NOTE]
> При обнаружении System Center Endpoint Protection (SCEP) на виртуальной машине Windows Server 2008 R2 необходимо установить SCEP после PowerShell (версия 3.0 или более поздняя).


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [Центр безопасности собирает данные и агент log Analytics](security-center-enable-data-collection.md).
- Узнайте [, как центр безопасности управляет и защищает данные](security-center-data-security.md).
- Ознакомьтесь с [платформами, поддерживающими центр безопасности](security-center-os-coverage.md).