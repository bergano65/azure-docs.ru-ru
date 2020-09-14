---
title: Тестирование безопасности Azure v2 — безопасность конечных точек
description: Безопасность конечных точек системы безопасности Azure версии 2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: c04e4233ded34ceaeec9cd9afb240d3d1ac864e0
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059385"
---
# <a name="security-control-endpoint-security"></a>Управление безопасностью: безопасность конечных точек

Безопасность конечных точек охватывает элементы управления при обнаружении и отклике конечных точек. Сюда входит использование функции обнаружения конечных точек и реагирования (ЕДР) и службы защиты от вредоносных программ для конечных точек в средах Azure.

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1. Использование обнаружения конечных точек и ответа (ЕДР)

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| ES-1 | 8.1 | SI-2, SI-3, SC-3 |

Включите возможности обнаружения конечных точек и реагирования (ЕДР) для серверов и клиентов, а также Интегрируйте процессы SIEM и операций безопасности.

Усовершенствованная защита от угроз Microsoft Defender предоставляет возможности ЕДР в составе корпоративной платформы безопасности конечных точек для предотвращения, обнаружения, исследования и реагирования на сложные угрозы. 

- [Обзор расширенной защиты от угроз Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Служба ATP в защитнике Майкрософт для серверов Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Служба ATP в защитнике Майкрософт для серверов, отличных от Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов**:

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security)

- [Анализ угроз](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2. Использование централизованно управляемого современного программного обеспечения для защиты от вредоносных программ

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| ES-2 | 8.1 | SI-2, SI-3, SC-3 |

Использование централизованно управляемого решения для защиты от вредоносных программ на конечной точке с поддержкой реального времени и периодического сканирования

Центр безопасности Azure может автоматически обнаруживать использование ряда популярных решений для защиты от вредоносных программ для виртуальных машин, а также сообщать о состоянии запуска Endpoint Protection и делать рекомендации. 

Антивредоносное по Майкрософт для облачных служб Azure — это антивредоносное по по умолчанию для виртуальных машин Windows (ВМ). Для виртуальных машин Linux используйте стороннее решение для защиты от вредоносных программ.  Кроме того, для обнаружения вредоносных программ, отправленных в учетные записи хранения Azure, можно использовать обнаружение угроз центра безопасности Azure для служб данных. 

- [Настройка антивредоносного по Майкрософт для облачных служб и виртуальных машин](../fundamentals/antimalware.md)

- [Поддерживаемые решения для защиты конечных точек](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions-)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов**:

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security)

- [Анализ угроз](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: обеспечение обновления программного обеспечения и подписей для защиты от вредоносных программ

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| ES-3 | 8.2 | SI-2, SI-3 |

Регулярное обновление подписей для защиты от вредоносных программ. 

Следуйте рекомендациям в центре безопасности Azure: "вычисление &amp; приложений", чтобы обеспечить актуальность всех конечных точек с последними сигнатурами. Антивредоносная программа Майкрософт автоматически устанавливает последние сигнатуры и обновления антивирусного по по умолчанию. Для Linux используйте стороннее решение для защиты от вредоносных программ.

- [Развертывание антивредоносного по Майкрософт для облачных служб и виртуальных машин Azure](../fundamentals/antimalware.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов**:

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security)

- [Анализ угроз](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

