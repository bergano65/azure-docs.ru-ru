---
title: Управление безопасностью Azure — ведение журнала и мониторинг
description: Ведение журнала и мониторинг управления безопасностью
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ceb82bf729470f9996bc9ecc766329a0d1242ec4
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930035"
---
# <a name="security-control-logging-and-monitoring"></a>Управление безопасностью: ведение журнала и мониторинг

Журналы безопасности и мониторинг посвящены действиям, связанным с включением, получением и хранением журналов аудита для служб Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2,1: используйте источники синхронизации утвержденного времени

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 2.1 | 6.1 | Майкрософт |

Корпорация Майкрософт поддерживает источники времени для ресурсов Azure. Однако вы можете управлять параметрами синхронизации времени для ресурсов вычислений.

Как настроить синхронизацию времени для ресурсов вычислений Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2,2: Настройка централизованного управления журналом безопасности

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 2.2 | 6,5, 6,6 | Customer |

Прием журналов с помощью Azure Monitor для объединения данных безопасности, создаваемых устройствами конечных точек, сетевыми ресурсами и другими системами безопасности. В Azure Monitor используйте рабочие области Log Analytics для запроса и выполнения анализа и используйте учетные записи хранения Azure для долгосрочного и архивного хранения.

Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM. Как подключить метку Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Как получить журналы и метрики платформы с помощью Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Как получить журналы внутреннего узла виртуальной машины Azure с помощью Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Как приступить к работе с Azure Monitor и интеграцией SIEM сторонних производителей:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Включите ведение журнала аудита для ресурсов Azure.

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 2.3 | 6,2, 6,3 | Customer |

Включите параметры диагностики для ресурсов Azure, чтобы получить доступ к журналам аудита, безопасности и диагностики. Журналы действий, которые автоматически доступны, включают источник событий, дату, пользователя, метку времени, исходные адреса, адреса назначения и другие полезные элементы.

Как получить журналы и метрики платформы с помощью Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Общие сведения о ведении журналов и различных типах журналов в Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4: получение журналов безопасности из операционных систем

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 2.4 | 6,2, 6,3 | Customer |

Если ресурсы для вычислений принадлежат корпорации Майкрософт, корпорация Майкрософт несет ответственность за мониторинг ИТ-ресурсов. Если ресурс-источник принадлежит вашей организации, вы отвечаете за его мониторинг. Для мониторинга операционной системы можно использовать центр безопасности Azure. Данные, собираемые центром безопасности из операционной системы, включают в себя тип ОС и версию, ОПЕРАЦИОНную систему (журналы событий Windows), запущенные процессы, имя компьютера, IP-адреса и вошедший в систему пользователь. Агент Log Analytics также собирает файлы аварийного дампа.

Как получить журналы внутреннего узла виртуальной машины Azure с помощью Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Общие сведения о сборе данных в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2,5: Настройка хранения журнала безопасности

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 2.5 | 6.4 | Customer |

В Azure Monitor задайте срок хранения Log Analytics рабочей области согласно нормативным требованиям Организации. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения.

Как задать параметры хранения журнала для рабочих областей Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2,6: мониторинг и просмотр журналов

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 2.6 | 6.7 | Customer |

Анализируйте и отслеживайте журналы на предмет аномального поведения и регулярно проверяйте результаты. Используйте рабочую область Log Analytics Azure Monitor, чтобы просматривать журналы и выполнять запросы к данным журнала.

Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM. 

Как подключить метку Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Общие сведения о Log Analytics рабочей области:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Как выполнять пользовательские запросы в Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Включение оповещений для аномальных действий

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 2.7 | 6,8 | Customer |

Используйте центр безопасности Azure с рабочей областью Log Analytics для мониторинга и оповещения о аномальных действиях, обнаруженных в журналах и событиях безопасности.

Кроме того, вы можете включить и подключить данные в Azure Sentinel.

Как подключить метку Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Управление оповещениями в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Как создавать оповещения для данных журнала Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2,8: централизованное ведение журнала защиты от вредоносных программ

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 2.8 | 8,6 | Customer |

Включите сбор событий защиты от вредоносных программ для виртуальных машин Azure и облачных служб.

Как настроить антивредоносное по Майкрософт для виртуальных машин:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Как настроить антивредоносное по Майкрософт для облачных служб:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Знакомство с антивредоносным по Майкрософт:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2,9: Включение ведения журнала запросов DNS

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 2,9 | 8,7 | Customer |

Реализуйте стороннее решение для ведения журнала DNS.

## <a name="210-enable-command-line-audit-logging"></a>2,10: Включение ведения журнала аудита в командной строке

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 2.1 | 8.8 | Customer |

Вручную настройте ведение журнала консоли и запись PowerShell для каждого узла.

## <a name="next-steps"></a>Дальнейшие действия

См. следующий контроль безопасности: [Идентификация и управление доступом](security-control-identity-access-control.md) .
