---
title: Контроль безопасности Azure - Журналирование и мониторинг
description: Регистрация и мониторинг контроля безопасности
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545506"
---
# <a name="security-control-logging-and-monitoring"></a>Контроль безопасности: ведение и мониторинг

Регистрация и мониторинг безопасности фокусируется на деятельности, связанной с созданием, приобретением и хранением журналов аудита для служб Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Используйте утвержденные источники синхронизации времени

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft поддерживает источники времени для ресурсов Azure, однако у вас есть возможность управлять настройками синхронизации времени для вычислительных ресурсов.

Как настроить синхронизацию времени для вычислительных ресурсов Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2: Настройка централизованного управления журналами безопасности

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 2.2 | 6.5, 6.6 | Customer |

Записи журналов через Azure Monitor для агрегирования данных безопасности, генерируемых устройствами конечных точек, сетевыми ресурсами и другими системами безопасности. В Azure Monitor используйте рабочее пространство журналов Analytics Workspace (ы) для запроса и выполнения аналитики, а также используйте учетные записи хранения Azure для долгосрочного/архивного хранения.

Кроме того, вы можете включить и на борт данных Azure Sentinel или сторонних SIEM. Как на борту Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Как собирать журналы и метрики платформы с помощью Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Как собрать внутренние журналы хоста Azure Virtual Machine с помощью Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Как начать работу с Azure Monitor и сторонней интеграцией SIEM:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Включить журнал аудита для ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 2.3 | 6.2, 6.3 | Customer |

Включить диагностические настройки на ресурсах Azure для доступа к аудиторским, безопасности и диагностическим журналам. В журналы активности, которые автоматически доступны, входят источник событий, дата, пользователь, метка времени, адреса источников, адреса назначения и другие полезные элементы.

Как собирать журналы и метрики платформы с помощью Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Понимание журналов и различных типов журналов в Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Сбор журналов безопасности из операционных систем

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 2.4 | 6.2, 6.3 | Customer |

Если вычислительный ресурс принадлежит корпорации Майкрософт, то корпорация Майкрософт отвечает за его мониторинг. Если вычислительный ресурс принадлежит вашей организации, вы несете ответственность за его мониторинг. Для мониторинга ОС можно использовать Центр безопасности Azure. Данные, собранные Центром безопасности с операционной системы, включают тип и версию ОС, журналы ОС (журналы событий Windows), ходовые процессы, имя машины, IP-адреса и регистрацию в пользователя. Агент аналитики журнала также собирает файлы сбоя дампа.

Как собрать внутренние журналы хоста Azure Virtual Machine с помощью Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Поймите сбор данных Центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5: Настройка хранения журналов безопасности

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 2.5 | 6.4 | Customer |

В Azure Monitor установите период хранения рабочего пространства аналитики журналов в соответствии с правилами соответствия организации. Используйте учетные записи хранения Azure для долгосрочного/архивного хранения.

Как установить параметры удержания журнала для рабочих областей анализа журналов:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6: Мониторинг и обзор журналы

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 2.6 | 6.7 | Customer |

Анализируйте и отслеживайте журналы для аномального поведения и регулярно просматривайте результаты. Используйте рабочее пространство аналитики журналов Azure Monitor для просмотра журналов и выполнения запросов на данных журнала.

Кроме того, вы можете включить и на борт данных Azure Sentinel или третьей стороной SIEM. 

Как на борту Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Понять рабочее пространство аналитики журнала:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Как выполнять пользовательские запросы в Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Включить оповещения об аномальной активности

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 2.7 | 6,8 | Customer |

Используйте Центр безопасности Azure с рабочим пространством аналитики журналов для мониторинга и оповещения об аномальной активности, найденной в журналах и событиях безопасности.

Кроме того, вы можете включить и на борт данных Azure Sentinel.

Как на борту Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Как управлять оповещениями в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Как упомяните данные журнала аналитики:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8: Централизованное заготовку от вредоносных программ

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 2.8 | 8.6 | Customer |

Включите сбор антивредоносных событий для виртуальных машин Azure и облачных служб.

Как настроить Microsoft Antimalware для виртуальных машин:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Как настроить Microsoft Antimalware для облачных служб:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Поймите Microsoft Antimalware:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9: Включить журнал запросов DNS

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 2,9 | 8,7 | Customer |

Реализация стороннего решения для регистрации DNS.

## <a name="210-enable-command-line-audit-logging"></a>2.10: Включить журнал аудита командной строки

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 2.1 | 8,8 | Customer |

Вручную настраивать журнал консолей и транскрипцию PowerShell на основе одного узла.

## <a name="next-steps"></a>Дальнейшие действия

Смотрите следующий контроль безопасности: [Identity и контроль доступа](security-control-identity-access-control.md)
