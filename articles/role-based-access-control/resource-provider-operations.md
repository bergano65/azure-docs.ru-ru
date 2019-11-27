---
title: Операции поставщиков ресурсов Azure Resource Manager | Документация Майкрософт
description: Списки операций, доступных для поставщиков ресурсов Microsoft Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 092c3b4ac6ce163e9fcf8aaad9e74f398559e9e2
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546321"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Операции поставщиков ресурсов Azure Resource Manager

В этой статье перечислены операции, доступные в каждом поставщике ресурсов Azure Resource Manager. Эти операции могут использоваться в [настраиваемых ролях](custom-roles.md), чтобы обеспечить детализированное [управление доступом на основе ролей (RBAC)](overview.md) для ресурсов в Azure. Строки операций имеют следующий формат: `{Company}.{ProviderName}/{resourceType}/{action}`. Сведения о том, как пространства имен поставщиков ресурсов сопоставляются со службами Azure, см. [в разделе Сопоставление поставщика ресурсов со службой](../azure-resource-manager/azure-services-resource-providers.md).

Операции в поставщиках ресурсов всегда развиваются. Чтобы получить список последних операций, используйте команду [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) или [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.AAD/domainServices/delete | Удаление службы домена |
> | Действия | Microsoft.AAD/domainServices/oucontainer/delete | Удаление контейнера подразделения |
> | Действия | Microsoft.AAD/domainServices/oucontainer/read | Чтение контейнеров подразделения |
> | Действия | Microsoft.AAD/domainServices/oucontainer/write | Запись в контейнер подразделения |
> | Действия | Microsoft.AAD/domainServices/read | Чтение доменных служб |
> | Действия | Microsoft. AAD/domainServices/Репликасетс/Delete | Удаление сайта кластера |
> | Действия | Microsoft. AAD/domainServices/Репликасетс/чтение | Чтение сайта кластера |
> | Действия | Microsoft. AAD/domainServices/Репликасетс/запись | Запись сайта кластера |
> | Действия | Microsoft.AAD/domainServices/write | Запись службы домена |
> | Действия | Microsoft.AAD/locations/operationresults/read |  |
> | Действия | Microsoft.AAD/Operations/read |  |
> | Действия | Microsoft.AAD/register/action | Регистрация службы домена |
> | Действия | Microsoft.AAD/unregister/action | Отмена регистрации службы домена |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | microsoft.aadiam/diagnosticsettings/delete | Удаление параметра диагностики. |
> | Действия | microsoft.aadiam/diagnosticsettings/read | Чтение параметра диагностики. |
> | Действия | microsoft.aadiam/diagnosticsettings/write | Запись параметра диагностики. |
> | Действия | microsoft.aadiam/diagnosticsettingscategories/read | Чтение категорий параметра диагностики. |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Addons/operations/read | Получает поддерживаемые операции RP. |
> | Действия | Microsoft.Addons/register/action | Регистрирует указанную подписку в Microsoft.Addons. |
> | Действия | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Выводит сведения о текущем плане поддержки для указанной подписки. |
> | Действия | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Удаляет указанный план поддержки Canonical. |
> | Действия | Microsoft.Addons/supportProviders/supportPlanTypes/read | Получает состояние указанного плана поддержки Canonical. |
> | Действия | Microsoft.Addons/supportProviders/supportPlanTypes/write | Добавляет указанный тип плана поддержки Canonical. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ADHybridHealthService/addsservices/action | Создает лес для клиента. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Получает все серверы для указанного имени службы. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/alerts/read | Получает сведения об оповещениях для леса, например идентификатор оповещения, дата получения, последнее обнаружение, описание, последнее обновление, уровень и состояние оповещения, а также ссылки на устранение неполадок с оповещением и т. д. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/configuration/read | Получает конфигурацию службы для леса. Например, имя леса, функциональный уровень, роль хозяина именования доменов FSMO, роль хозяина схемы FSMO и т. д. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/delete | Удаляет службу и ее серверы, а также данные о работоспособности. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Получает сведения о доменах и сайтах для леса. Например, состояние работоспособности, активные оповещения, разрешенные оповещения, такие свойства, как функциональный уровень домена, лес, хозяин инфраструктуры, PDC, хозяин RID и т. д.  |
> | Действия | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Получает параметр настройки пользователя для леса.<br>Например, значения MetricCounterName, такие как ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Параметры диаграммы пользовательского интерфейса и т. д. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Получает сводку для данного леса, например имя леса, количество доменов в лесу, количество сайтов, сведения о сайтах и т. д. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Получает список поддерживаемых метрик для данной службы.<br>Например, число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы ADSync. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Для данной службы этот API получает информацию о метриках.<br>Например, этот API можно использовать для получения следующей информации: число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы синхронизации. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Этот API возвращает список всех подключенных служб ADDomainServices для клиента категории "Премиум". |
> | Действия | Microsoft.ADHybridHealthService/addsservices/read | Получает сведения о службе для указанного имени службы. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Получает сведения о репликации всех серверов для указанного имени службы. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Получает число контроллеров домена и их ошибок репликации, если таковые имеются. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Получает полный список контроллеров домена и сведений о репликации для данного леса. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Добавляет экземпляр сервера в службу. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Во время регистрации сервера ADDomainService этот API вызывается, чтобы получить учетные данные для подключения новых серверов. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Удаляет сервер для данной службы и клиента. |
> | Действия | Microsoft.ADHybridHealthService/addsservices/write | Создает или обновляет экземпляр ADDomainService для клиента. |
> | Действия | Microsoft.ADHybridHealthService/configuration/action | Обновляет конфигурацию клиента. |
> | Действия | Microsoft.ADHybridHealthService/configuration/read | Считывает конфигурацию клиента. |
> | Действия | Microsoft.ADHybridHealthService/configuration/write | Создает конфигурацию клиента. |
> | Действия | Microsoft.ADHybridHealthService/logs/contents/read | Получает содержимое журналов установки и регистрации агента, хранимых в большом двоичном объекте. |
> | Действия | Microsoft.ADHybridHealthService/logs/read | Получает журналы установки и регистрации агента для клиента. |
> | Действия | Microsoft.ADHybridHealthService/operations/read | Возвращает список операций, поддерживаемых системой. |
> | Действия | Microsoft.ADHybridHealthService/register/action | Регистрирует поставщик ресурсов службы работоспособности ADHybrid и позволяет создавать ресурсы службы работоспособности ADHybrid. |
> | Действия | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Получает список доступных регионов, используемых командой DevOps для поддержки инцидентов клиентов. |
> | Действия | Microsoft.ADHybridHealthService/reports/badpassword/read | Получает список попыток неправильного ввода пароля для всех пользователей в службе федерации Active Directory. |
> | Действия | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Получает универсальный код ресурса SAS большого двоичного объекта, содержащий состояние и конечный результат недавно поставленного в очередь задания отчета для определения частоты попыток неправильного ввода имени пользователя или пароля на каждый идентификатор пользователя по каждому IP-адресу в день для данного клиента. |
> | Действия | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Получает список клиентов, получивших согласие DevOps. Обычно используется для поддержки клиентов. |
> | Действия | Microsoft.ADHybridHealthService/reports/isdevops/read | Получение значения, указывающего, имеет ли клиент согласие DevOps. |
> | Действия | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Обновляет идентификатор пользователя (идентификатор объекта) выбранного клиента DevOps. |
> | Действия | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Возвращает выбранное развертывание для данного клиента. |
> | Действия | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Получает расположение хранилища клиента на основе идентификатора клиента. |
> | Действия | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Возвращает географическое расположение, из которого будет осуществляться доступ к данных. |
> | Действия | Microsoft.ADHybridHealthService/services/action | Обновляет экземпляр службы в клиенте. |
> | Действия | Microsoft.ADHybridHealthService/services/alerts/read | Считывает оповещения для службы. |
> | Действия | Microsoft.ADHybridHealthService/services/alerts/read | Считывает оповещения для службы. |
> | Действия | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | На основе имени функции проверяет, имеется ли у службы все необходимое для использования этой функции. |
> | Действия | Microsoft.ADHybridHealthService/services/delete | Удаляет экземпляр службы из клиента. |
> | Действия | Microsoft.ADHybridHealthService/services/exporterrors/read | Возвращает ошибки экспорта для данной службы синхронизации. |
> | Действия | Microsoft.ADHybridHealthService/services/exportstatus/read | Возвращает состояние экспорта для данной службы. |
> | Действия | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Получает обратную связь об оповещениях для данной службы и сервера. |
> | Действия | Microsoft.ADHybridHealthService/services/metricmetadata/read | Получает список поддерживаемых метрик для данной службы.<br>Например, число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы ADSync. |
> | Действия | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Этот API получает среднее значение метрик для данной службы.<br>Например, этот API можно использовать для получения следующей информации: число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы синхронизации. |
> | Действия | Microsoft.ADHybridHealthService/services/metrics/groups/read | Для данной службы этот API получает информацию о метриках.<br>Например, этот API можно использовать для получения следующей информации: число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы синхронизации. |
> | Действия | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Этот API получает агрегированное представление о метриках для данной службы.<br>Например, этот API можно использовать для получения следующей информации: число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы синхронизации. |
> | Действия | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Добавляет или обновляет конфигурацию мониторинга для службы. |
> | Действия | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Получает конфигурации мониторинга для данной службы. |
> | Действия | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Добавляет или обновляет конфигурации мониторинга для службы. |
> | Действия | Microsoft.ADHybridHealthService/services/premiumcheck/read | Этот API возвращает список всех подключенных служб для клиента категории "Премиум". |
> | Действия | Microsoft.ADHybridHealthService/services/read | Считывает экземпляры службы в клиенте. |
> | Действия | Microsoft. Адхибридхеалссервице/Services/Reports/Блобурис/Read | Возвращает все нерискованные URI отчетов IP за последние семь дней. |
> | Действия | Microsoft.ADHybridHealthService/services/reports/details/read | Возвращает отчет о первых 50 пользователях, вводивших неправильный пароль, за последние 7 дней. |
> | Действия | Microsoft. Адхибридхеалссервице/Services/Reports/Женератеблобури/Action | Создает рискованный IP-отчет и возвращает URI, указывающий на него. |
> | Действия | Microsoft.ADHybridHealthService/services/servicemembers/action | Создает экземпляр службы в клиенте. |
> | Действия | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Считывает оповещения для сервера. |
> | Действия | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Во время регистрации сервера этот API вызывается, чтобы получить учетные данные для подключения новых серверов. |
> | Действия | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Для данного сервера этот API возвращает список типов данных, которые передаются серверами, и самое позднее время каждой отправки. |
> | Действия | Microsoft.ADHybridHealthService/services/servicemembers/delete | Удаляет экземпляр службы из службы. |
> | Действия | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Возвращает сведения об ошибке экспорта данных синхронизации для указанной службы синхронизации. |
> | Действия | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Для данной службы этот API получает информацию о метриках.<br>Например, этот API можно использовать для получения следующей информации: число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы синхронизации. |
> | Действия | Microsoft. Адхибридхеалссервице/Services/сервицемемберс/метрики/чтение | Возвращает список соединителей и имена профилей выполнения для заданной службы и члена службы. |
> | Действия | Microsoft.ADHybridHealthService/services/servicemembers/read | Считывает экземпляр сервера в службе. |
> | Действия | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Получает конфигурацию службы для данного клиента. |
> | Действия | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Получает состояние разрешенных функций для данного клиента. |
> | Действия | Microsoft.ADHybridHealthService/services/write | Создает экземпляр службы в клиенте. |
> | Действия | Microsoft.ADHybridHealthService/unregister/action | Отменяет регистрацию подписки для поставщика ресурсов службы работоспособности ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Advisor/configurations/read | Получение конфигураций |
> | Действия | Microsoft.Advisor/configurations/write | Создание или обновление конфигурации. |
> | Действия | Microsoft.Advisor/generateRecommendations/action | Создает рекомендации. |
> | Действия | Microsoft.Advisor/generateRecommendations/read | Возвращает состояние создания рекомендаций. |
> | Действия | Microsoft. Advisor/метаданные/чтение | Получить метаданные |
> | Действия | Microsoft.Advisor/operations/read | Получение операций для Microsoft Advisor. |
> | Действия | Microsoft.Advisor/recommendations/available/action | В Microsoft Advisor доступна новая рекомендация. |
> | Действия | Microsoft.Advisor/recommendations/read | Считывает рекомендации. |
> | Действия | Microsoft.Advisor/recommendations/suppressions/delete | Удаляет подавления. |
> | Действия | Microsoft.Advisor/recommendations/suppressions/read | Возвращает подавления. |
> | Действия | Microsoft.Advisor/recommendations/suppressions/write | Создает или обновляет подавления. |
> | Действия | Microsoft.Advisor/register/action | Регистрирует подписку для Microsoft Advisor. |
> | Действия | Microsoft.Advisor/suppressions/delete | Удаляет подавления. |
> | Действия | Microsoft.Advisor/suppressions/read | Возвращает подавления. |
> | Действия | Microsoft.Advisor/suppressions/write | Создает или обновляет подавления. |
> | Действия | Microsoft.Advisor/unregister/action | Отмена регистрации подписки для Microsoft Advisor. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.AlertsManagement/actionRules/delete | Удаление правила действия в рамках заданной подписки. |
> | Действия | Microsoft.AlertsManagement/actionRules/read | Получение всех правил действий для фильтров входящих данных. |
> | Действия | Microsoft.AlertsManagement/actionRules/write | Создание или обновление правила действия в рамках заданной подписки. |
> | Действия | Microsoft.AlertsManagement/alerts/changestate/action | Изменение состояния оповещения. |
> | Действия | Microsoft.AlertsManagement/alerts/diagnostics/read | Получение всех данных диагностики для оповещения. |
> | Действия | Microsoft.AlertsManagement/alerts/history/read | Получение журнала оповещения. |
> | Действия | Microsoft.AlertsManagement/alerts/read | Получение всех оповещений для фильтров входа. |
> | Действия | Microsoft.AlertsManagement/alertsList/read | Получение всех оповещений для фильтров входящих данных в рамках подписок. |
> | Действия | Microsoft. Алертсманажемент/Алертсметадата/Read | Получение метаданных предупреждений для входного параметра. |
> | Действия | Microsoft.AlertsManagement/alertsSummary/read | Получение сводки оповещений. |
> | Действия | Microsoft.AlertsManagement/alertsSummaryList/read | Получение сводки оповещений для подписок. |
> | Действия | Microsoft.AlertsManagement/Operations/read | Чтение предоставленных операций. |
> | Действия | Microsoft.AlertsManagement/register/action | Регистрация подписки для Управления оповещениями (Майкрософт). |
> | Действия | Microsoft. Алертсманажемент/Смартдетекторалертрулес/Delete | Удаление правила оповещения Smart детектора в заданной подписке |
> | Действия | Microsoft. Алертсманажемент/Смартдетекторалертрулес/Read | Получить все правила генерации оповещений смарт-детекторов для входных фильтров |
> | Действия | Microsoft. Алертсманажемент/Смартдетекторалертрулес/запись | Создание или обновление правила генерации оповещений Smart детектор в заданной подписке |
> | Действия | Microsoft.AlertsManagement/smartGroups/changestate/action | Изменение состояния смарт-группы. |
> | Действия | Microsoft.AlertsManagement/smartGroups/history/read | Получение журнала смарт-группы. |
> | Действия | Microsoft.AlertsManagement/smartGroups/read | Получение всех смарт-групп для фильтров ввода. |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Проверяет, является ли данное имя сервера анализа данных допустимым и неиспользуемым. |
> | Действия | Microsoft.AnalysisServices/locations/operationresults/read | Получение сведений о результате указанной операции. |
> | Действия | Microsoft.AnalysisServices/locations/operationstatuses/read | Извлечение сведений о состоянии указанной операции. |
> | Действия | Microsoft.AnalysisServices/operations/read | Извлечение сведений об операциях. |
> | Действия | Microsoft.AnalysisServices/register/action | Регистрация поставщика ресурсов для служб Analysis Services. |
> | Действия | Microsoft.AnalysisServices/servers/delete | Удаляет сервер анализа данных. |
> | Действия | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Отображение состояния шлюза, связанного с сервером. |
> | Действия | Microsoft.AnalysisServices/servers/read | Извлекает информацию об указанном сервере анализа данных. |
> | Действия | Microsoft.AnalysisServices/servers/resume/action | Возобновляет работу сервера анализа данных. |
> | Действия | Microsoft.AnalysisServices/servers/skus/read | Получение доступных сведений о номере SKU для сервера. |
> | Действия | Microsoft.AnalysisServices/servers/suspend/action | Приостанавливает сервер анализа данных. |
> | Действия | Microsoft.AnalysisServices/servers/write | Создает или обновляет указанный сервер анализа данных. |
> | Действия | Microsoft.AnalysisServices/skus/read | Извлечение сведений о номерах SKU. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ApiManagement/checkNameAvailability/read | Проверяет, доступно ли указанное имя службы. |
> | Действия | Microsoft.ApiManagement/operations/read | Чтение всех операций API для ресурса Microsoft.ApiManagement. |
> | Действия | Microsoft.ApiManagement/register/action | Регистрирует подписку для поставщика ресурсов Microsoft.ApiManagement. |
> | Действия | Microsoft.ApiManagement/reports/read | Получение отчетов, объединенных по периодам времени, географическим регионам, разработчикам, продуктам, API-интерфейсам, операциям, подпискам и запросам. |
> | Действия | Microsoft.ApiManagement/service/apis/delete | Удаляет указанный API экземпляра службы управления API. |
> | Действия | Microsoft.ApiManagement/service/apis/diagnostics/delete | Удаляет указанное средство диагностики из API. |
> | Действия | Microsoft.ApiManagement/service/apis/diagnostics/read | Список всех средств диагностики API. или возвращает сведения о диагностике для API, заданного его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/apis/diagnostics/write | Создает новую диагностику для API или обновляет существующую. или обновляет сведения о диагностике для API, заданного его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Удаляет указанный комментарий из проблемы. |
> | Действия | Microsoft.ApiManagement/service/apis/issues/attachments/read | Список всех вложений для проблемы, связанной с указанным API. или возвращает сведения о вложении проблем для API, заданного его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/apis/issues/attachments/write | Создает новое вложение для проблемы в API или обновляет существующее. |
> | Действия | Microsoft.ApiManagement/service/apis/issues/comments/delete | Удаляет указанный комментарий из проблемы. |
> | Действия | Microsoft.ApiManagement/service/apis/issues/comments/read | Список всех комментариев для проблемы, связанной с указанным API. или возвращает сведения о комментарии к вопросу для API, заданного его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/apis/issues/comments/write | Создает новый комментарий для проблемы в API или обновляет существующий. |
> | Действия | Microsoft.ApiManagement/service/apis/issues/delete | Удаляет указанную ошибку из API. |
> | Действия | Microsoft.ApiManagement/service/apis/issues/read | Список всех проблем, связанных с указанным API. или возвращает сведения о проблемах для API, заданного его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/apis/issues/write | Создает новую ошибку для API или обновляет существующую. или обновляет существующую ошибку для API. |
> | Действия | Microsoft.ApiManagement/service/apis/operations/delete | Удаляет указанную операцию в API. |
> | Действия | Microsoft.ApiManagement/service/apis/operations/policies/delete | Удаляет конфигурацию политики в операции API. |
> | Действия | Microsoft.ApiManagement/service/apis/operations/policies/read | Получите список конфигурации политики на уровне операций API. или получите конфигурацию политики на уровне операций API. |
> | Действия | Microsoft.ApiManagement/service/apis/operations/policies/write | Создает или обновляет конфигурацию политики для уровня операций API. |
> | Действия | Microsoft.ApiManagement/service/apis/operations/policy/delete | Удаление конфигурации политики на уровне операций |
> | Действия | Microsoft.ApiManagement/service/apis/operations/policy/read | Получение конфигурации политики на уровне операций |
> | Действия | Microsoft.ApiManagement/service/apis/operations/policy/write | Создание конфигурации политики на уровне операций |
> | Действия | Microsoft.ApiManagement/service/apis/operations/read | Перечисляет коллекцию операций для указанного API. или возвращает сведения об операции API, указанной в ее идентификаторе. |
> | Действия | Microsoft.ApiManagement/service/apis/operations/tags/delete | Отсоедините тег от операции. |
> | Действия | Microsoft.ApiManagement/service/apis/operations/tags/read | Список всех тегов, связанных с операцией. или получить тег, связанный с операцией. |
> | Действия | Microsoft.ApiManagement/service/apis/operations/tags/write | Назначьте тег операции. |
> | Действия | Microsoft.ApiManagement/service/apis/operations/write | Создает новую операцию в API или обновляет существующую. или обновляет сведения об операции в API, указанном с помощью ее идентификатора. |
> | Действия | Microsoft.ApiManagement/service/apis/operationsByTags/read | Содержит коллекцию операций, связанных с тегами. |
> | Действия | Microsoft.ApiManagement/service/apis/policies/delete | Удаляет конфигурацию политики в API. |
> | Действия | Microsoft.ApiManagement/service/apis/policies/read | Получите конфигурацию политики на уровне API. или получите конфигурацию политики на уровне API. |
> | Действия | Microsoft.ApiManagement/service/apis/policies/write | Создает или обновляет конфигурацию политики для API. |
> | Действия | Microsoft.ApiManagement/service/apis/policy/delete | Удаление конфигурации политики на уровне API |
> | Действия | Microsoft.ApiManagement/service/apis/policy/read | Получение конфигурации политики на уровне API |
> | Действия | Microsoft.ApiManagement/service/apis/policy/write | Создание конфигурации политики на уровне API |
> | Действия | Microsoft.ApiManagement/service/apis/products/read | Список всех продуктов, частью которых является API. |
> | Действия | Microsoft.ApiManagement/service/apis/read | Список всех API экземпляра службы управления API. или возвращает сведения об API, заданном по его идентификатору. |
> | Действия | Microsoft.ApiManagement/service/apis/releases/delete | Удаляет все выпуски API или удаляет указанный выпуск в API. |
> | Действия | Microsoft.ApiManagement/service/apis/releases/read | Выводит список всех выпусков API.<br>Версия API создается при изменении текущей редакции API.<br>Выпуски также используются для отката к предыдущим редакциям.<br>Результаты будут разбиваться на страницы и могут быть ограничены параметрами $top и $skip.<br>или возвращает сведения о выпуске API. |
> | Действия | Microsoft.ApiManagement/service/apis/releases/write | Создает новый выпуск для API. или обновляет сведения о выпуске API, заданного его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/apis/revisions/delete | Удаление всех редакций API. |
> | Действия | Microsoft.ApiManagement/service/apis/revisions/read | Перечисляет все редакции API. |
> | Действия | Microsoft.ApiManagement/service/apis/schemas/delete | Удаляет конфигурацию схемы в API. |
> | Действия | Microsoft.ApiManagement/service/apis/schemas/read | Получите конфигурацию схемы на уровне API. или получите конфигурацию схемы на уровне API. |
> | Действия | Microsoft.ApiManagement/service/apis/schemas/write | Создает или обновляет конфигурацию схемы для API. |
> | Действия | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Удалите описание тега для API. |
> | Действия | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Список всех описаний тегов в области API. Модель, похожая на Swagger-Тагдескриптион, определена на уровне API, но тег может быть назначен для операций или получения описания тега в области API. |
> | Действия | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Создайте или обновите описание тега в области действия API. |
> | Действия | Microsoft.ApiManagement/service/apis/tags/delete | Отсоедините тег от API. |
> | Действия | Microsoft.ApiManagement/service/apis/tags/read | Список всех тегов, связанных с API. или получить тег, связанный с API. |
> | Действия | Microsoft.ApiManagement/service/apis/tags/write | Назначьте тегу API. |
> | Действия | Microsoft.ApiManagement/service/apis/write | Создает новый или обновляет существующий указанный API экземпляра службы управления API. или обновляет указанный API экземпляра службы управления API. |
> | Действия | Microsoft.ApiManagement/service/apisByTags/read | Содержит коллекцию интерфейсов API, связанных с тегами. |
> | Действия | Microsoft.ApiManagement/service/apiVersionSets/delete | Удаляет конкретный набор версий API. |
> | Действия | Microsoft.ApiManagement/service/apiVersionSets/read | Содержит коллекцию наборов версий API в указанном экземпляре службы. или возвращает сведения о наборе версий API, заданного его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Получение списка сущностей версии. |
> | Действия | Microsoft.ApiManagement/service/apiVersionSets/write | Создает или обновляет набор версий API. или обновляет сведения о наборе версий API, заданном идентификатором. |
> | Действия | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Обновляет ресурсы Microsoft.ApiManagement, работающие в виртуальной сети, для применения обновленных параметров сети. |
> | Действия | Microsoft.ApiManagement/service/authorizationServers/delete | Удаляет конкретный экземпляр сервера авторизации. |
> | Действия | Microsoft.ApiManagement/service/authorizationServers/read | Содержит коллекцию серверов авторизации, определенных в экземпляре службы. или возвращает сведения о сервере авторизации, указанном по его идентификатору. |
> | Действия | Microsoft.ApiManagement/service/authorizationServers/write | Создает новый сервер авторизации или обновляет существующий. или обновляет сведения о сервере авторизации, указанном с помощью его идентификатора. |
> | Действия | Microsoft.ApiManagement/service/backends/delete | Удаляет указанный сервер. |
> | Действия | Microsoft.ApiManagement/service/backends/read | Перечисляет коллекцию интерфейсов в указанном экземпляре службы. или возвращает сведения о серверной части, указанной в ее идентификаторе. |
> | Действия | Microsoft.ApiManagement/service/backends/reconnect/action | Уведомляет прокси-сервер APIM о создании нового соединения с серверной частью по истечении заданного времени ожидания. Если время ожидания не указано, используется время ожидания, равное 2 минутам. |
> | Действия | Microsoft.ApiManagement/service/backends/write | Создает или обновляет серверную часть. или обновляет существующую серверную часть. |
> | Действия | Microsoft.ApiManagement/service/backup/action | Архивирует службу управления API в указанный контейнер в предоставленной пользователем учетной записи хранения. |
> | Действия | Microsoft. ApiManagement/Service/кэши/удаление | Удаляет конкретный кэш. |
> | Действия | Microsoft. ApiManagement/служба/кэш/чтение | Перечисляет коллекцию всех внешних кэшей в указанном экземпляре службы. или возвращает сведения о кэше, заданном его идентификатором. |
> | Действия | Microsoft. ApiManagement/служба/кэши/запись | Создает или обновляет внешний кэш для использования в экземпляре управления API. или обновляет сведения о кэше, заданном его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/certificates/delete | Удаляет конкретный сертификат. |
> | Действия | Microsoft.ApiManagement/service/certificates/read | Перечисляет коллекцию всех сертификатов в указанном экземпляре службы. или возвращает сведения о сертификате, указанном по его идентификатору. |
> | Действия | Microsoft.ApiManagement/service/certificates/write | Создает или обновляет сертификат, используемый для проверки подлинности в серверной части. |
> | Действия | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Удаляет указанный элемент содержимого. |
> | Действия | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Возвращает список элементов содержимого или сведения об элементе содержимого. |
> | Действия | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Создает новый элемент содержимого или обновляет указанный элемент содержимого. |
> | Действия | Microsoft.ApiManagement/service/contentTypes/read | Возвращает список типов содержимого. |
> | Действия | Microsoft.ApiManagement/service/delete | Удаляет экземпляр службы управления API. |
> | Действия | Microsoft.ApiManagement/service/diagnostics/delete | Удаляет указанное средство диагностики. |
> | Действия | Microsoft.ApiManagement/service/diagnostics/read | Список всех диагностических сведений об экземпляре службы управления API. или возвращает сведения о диагностике, заданном идентификатором. |
> | Действия | Microsoft.ApiManagement/service/diagnostics/write | Создает новую диагностику или обновляет существующую. или обновляет сведения о диагностике, заданные по идентификатору. |
> | Действия | Microsoft. ApiManagement/служба/шлюзы/действие | Извлекает конфигурацию шлюза. или обновляет пульс шлюза. |
> | Действия | Microsoft. ApiManagement/служба/шлюзы/удаление | Удаляет конкретный шлюз. |
> | Действия | Microsoft. ApiManagement/служба/шлюзы/ключи/действие | Получает ключи шлюза. |
> | Действия | Microsoft. ApiManagement/служба/шлюзы/чтение | Содержит коллекцию шлюзов, зарегистрированных в экземпляре службы. или возвращает сведения о шлюзе, заданном его идентификатором. |
> | Действия | Microsoft. ApiManagement/служба/шлюзы/Реженератепримарикэй/действие | Повторно создает ключ первичного шлюза инвалидатионг все созданные им токены. |
> | Действия | Microsoft. ApiManagement/служба/шлюзы/Реженератесекондарикэй/действие | Повторно создает вторичный ключ шлюза инвалидатионг все созданные им токены. |
> | Действия | Microsoft. ApiManagement/служба/шлюзы, токен или действие | Возвращает маркер авторизации общего доступа для шлюза. |
> | Действия | Microsoft. ApiManagement/служба/шлюзы/запись | Создает или обновляет шлюз, используемый в экземпляре управления API. или обновляет сведения о шлюзе, заданном идентификатором. |
> | Действия | Microsoft.ApiManagement/service/getssotoken/action | Возвращает маркер единого входа, который может использоваться для входа на портал прежней версии службы управления API от имени администратора. |
> | Действия | Microsoft.ApiManagement/service/groups/delete | Удаляет определенную группу экземпляра службы управления API. |
> | Действия | Microsoft.ApiManagement/service/groups/read | Перечисляет коллекцию групп, определенных в экземпляре службы. или возвращает сведения о группе, заданной ее идентификатором. |
> | Действия | Microsoft.ApiManagement/service/groups/users/delete | Удалить существующего пользователя из существующей группы. |
> | Действия | Microsoft.ApiManagement/service/groups/users/read | Содержит коллекцию сущностей пользователей, связанных с группой. |
> | Действия | Microsoft.ApiManagement/service/groups/users/write | Добавляет существующего пользователя в существующий продукт. |
> | Действия | Microsoft.ApiManagement/service/groups/write | Создает или обновляет группу. или обновляет сведения о группе, указанной в ее идентификаторе. |
> | Действия | Microsoft.ApiManagement/service/identityProviders/delete | Удаляет указанную конфигурацию поставщика удостоверений. |
> | Действия | Microsoft.ApiManagement/service/identityProviders/read | Содержит коллекцию поставщиков удостоверений, настроенных в указанном экземпляре службы. или возвращает сведения о конфигурации поставщика удостоверений, настроенного в указанном экземпляре службы. |
> | Действия | Microsoft.ApiManagement/service/identityProviders/write | Создает или обновляет конфигурацию IdentityProvider. или обновляет существующую конфигурацию IdentityProvider. |
> | Действия | Microsoft. ApiManagement/Service/проблемы/чтение | Перечисляет коллекцию проблем в указанном экземпляре службы. или возвращает сведения о проблемах управления API |
> | Действия | Microsoft.ApiManagement/service/locations/networkstatus/read | Получение состояния доступа к сетевым ресурсам, от которых зависит служба в расположении. |
> | Действия | Microsoft.ApiManagement/service/loggers/delete | Удаляет указанное средство ведения журнала. |
> | Действия | Microsoft.ApiManagement/service/loggers/read | Перечисляет коллекцию средств ведения журнала в указанном экземпляре службы. или возвращает сведения о средстве ведения журнала, заданном его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/loggers/write | Создает или обновляет средство ведения журнала. или обновляет существующее средство ведения журнала. |
> | Действия | Microsoft.ApiManagement/service/managedeployments/action | Позволяет изменить номер SKU или единицы, а также добавить или удалить региональные развертывания службы управления API. |
> | Действия | Microsoft.ApiManagement/service/networkstatus/read | Получение состояния доступа к сетевым ресурсам, от которых зависит служба. |
> | Действия | Microsoft.ApiManagement/service/notifications/action | Отправление уведомления указанному пользователю. |
> | Действия | Microsoft.ApiManagement/service/notifications/read | Перечисляет коллекцию свойств, определенных в экземпляре службы. или возвращает сведения об уведомлении, заданном его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Удаляет сообщение электронной почты из списка уведомлений. |
> | Действия | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Возвращает список электронных писем получателей уведомлений, подписанных на уведомление. |
> | Действия | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Добавляет адрес электронной почты в список получателей уведомления. |
> | Действия | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Удаляет пользователя управления API из списка уведомлений. |
> | Действия | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Получает список пользователей уведомлений, подписанных на уведомление. |
> | Действия | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Добавляет пользователя управления API в список получателей уведомления. |
> | Действия | Microsoft.ApiManagement/service/notifications/write | Создание или обновление уведомления издателя управления API. |
> | Действия | Microsoft.ApiManagement/service/openidConnectProviders/delete | Удаляет конкретного поставщика OpenID Connect Connect экземпляра службы управления API. |
> | Действия | Microsoft.ApiManagement/service/openidConnectProviders/read | Список всех поставщиков OpenID Connect Connect. или возвращает конкретного поставщика OpenID Connect Connect. |
> | Действия | Microsoft.ApiManagement/service/openidConnectProviders/write | Создает или обновляет поставщик OpenID Connect Connect. или обновляет конкретного поставщика OpenID Connect Connect. |
> | Действия | Microsoft.ApiManagement/service/operationresults/read | Возвращает текущее состояние длительной операции. |
> | Действия | Microsoft.ApiManagement/service/policies/delete | Удаляет конфигурацию глобальной политики службы управления API. |
> | Действия | Microsoft.ApiManagement/service/policies/read | Список всех определений глобальных политик службы управления API. или получите определение глобальной политики службы управления API. |
> | Действия | Microsoft.ApiManagement/service/policies/write | Создает или обновляет конфигурацию глобальной политики службы управления API. |
> | Действия | Microsoft. ApiManagement/служба/политика/удаление | Удаление конфигурации политики на уровне клиента |
> | Действия | Microsoft. ApiManagement/служба/политика/чтение | Получение конфигурации политики на уровне клиента |
> | Действия | Microsoft. ApiManagement/служба/политика/запись | Создание конфигурации политики на уровне клиента |
> | Действия | Microsoft. ApiManagement/Service/Полицидескриптионс/Read | Список всех описаний политик. |
> | Действия | Microsoft.ApiManagement/service/policySnippets/read | Список всех фрагментов политик. |
> | Действия | Microsoft.ApiManagement/service/portalsettings/read | Получите параметры входа для портала или получите параметры регистрации для портала или получите параметры делегирования для портала. |
> | Действия | Microsoft.ApiManagement/service/portalsettings/write | Обновите параметры входа. или создайте или обновите параметры входа. или обновить параметры регистрации или обновить параметры регистрации или изменить параметры делегирования. или создайте или обновите параметры делегирования. |
> | Действия | Microsoft.ApiManagement/service/products/apis/delete | Удаляет указанный API из указанного продукта. |
> | Действия | Microsoft.ApiManagement/service/products/apis/read | Содержит коллекцию интерфейсов API, связанных с продуктом. |
> | Действия | Microsoft.ApiManagement/service/products/apis/write | Добавляет API к указанному продукту. |
> | Действия | Microsoft.ApiManagement/service/products/delete | Удаление продукта. |
> | Действия | Microsoft.ApiManagement/service/products/groups/delete | Удаляет связь между указанной группой и продуктом. |
> | Действия | Microsoft.ApiManagement/service/products/groups/read | Перечисляет коллекцию групп разработчиков, связанных с указанным продуктом. |
> | Действия | Microsoft.ApiManagement/service/products/groups/write | Добавляет связь между указанной группой разработчиков и указанным продуктом. |
> | Действия | Microsoft.ApiManagement/service/products/policies/delete | Удаляет конфигурацию политики в продукте. |
> | Действия | Microsoft.ApiManagement/service/products/policies/read | Получение конфигурации политики на уровне продукта. или получите конфигурацию политики на уровне продукта. |
> | Действия | Microsoft.ApiManagement/service/products/policies/write | Создает или обновляет конфигурацию политики для продукта. |
> | Действия | Microsoft.ApiManagement/service/products/policy/delete | Удаление конфигурации политики на уровне продукта |
> | Действия | Microsoft.ApiManagement/service/products/policy/read | Получение конфигурации политики на уровне продукта |
> | Действия | Microsoft.ApiManagement/service/products/policy/write | Создание конфигурации политики на уровне продукта |
> | Действия | Microsoft.ApiManagement/service/products/read | Перечисляет коллекцию продуктов в указанном экземпляре службы. или возвращает сведения о продукте, указанном по его идентификатору. |
> | Действия | Microsoft.ApiManagement/service/products/subscriptions/read | Перечисляет коллекцию подписок на указанный продукт. |
> | Действия | Microsoft.ApiManagement/service/products/tags/delete | Отсоедините тег от продукта. |
> | Действия | Microsoft.ApiManagement/service/products/tags/read | Список всех тегов, связанных с продуктом. или получить тег, связанный с продуктом. |
> | Действия | Microsoft.ApiManagement/service/products/tags/write | Назначьте тег продукту. |
> | Действия | Microsoft.ApiManagement/service/products/write | Создает или обновляет продукт. или обновить существующие сведения о продукте. |
> | Действия | Microsoft.ApiManagement/service/productsByTags/read | Содержит коллекцию продуктов, связанных с тегами. |
> | Действия | Microsoft.ApiManagement/service/properties/delete | Удаляет конкретное свойство из экземпляра службы управления API. |
> | Действия | Microsoft.ApiManagement/service/properties/read | Перечисляет коллекцию свойств, определенных в экземпляре службы. или возвращает сведения о свойстве, заданном его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/properties/write | Создает или обновляет свойство. или обновляет конкретное свойство. |
> | Действия | Microsoft.ApiManagement/service/quotas/periods/read | Получение значения счетчика квоты за период. |
> | Действия | Microsoft.ApiManagement/service/quotas/periods/write | Настройка текущего значения счетчика квоты. |
> | Действия | Microsoft.ApiManagement/service/quotas/read | Получение значений квоты. |
> | Действия | Microsoft.ApiManagement/service/quotas/write | Настройка текущего значения счетчика квоты. |
> | Действия | Microsoft.ApiManagement/service/read | Чтение метаданных для экземпляра службы управления API. |
> | Действия | Microsoft. ApiManagement/служба/регионы/чтение | Список всех регионов Azure, в которых существует служба. |
> | Действия | Microsoft.ApiManagement/service/reports/read | Получение отчета, объединенного по периодам времени, по географическим регионам или по разработчикам;<br>получение отчета, объединенного по продуктам;<br>получение отчета, объединенного по API-интерфейсам, операциям или по подпискам;<br>получение запросов, передающих данные. |
> | Действия | Microsoft.ApiManagement/service/restore/action | Восстановление службы управления API из указанного контейнера в предоставленной пользователем учетной записи хранения. |
> | Действия | Microsoft.ApiManagement/service/subscriptions/delete | Удаляет указанную подписку. |
> | Действия | Microsoft.ApiManagement/service/subscriptions/read | Список всех подписок экземпляра службы управления API. или возвращает указанную сущность подписки. |
> | Действия | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Повторно создает первичный ключ существующей подписки экземпляра службы управления API. |
> | Действия | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Повторно создает вторичный ключ существующей подписки экземпляра службы управления API. |
> | Действия | Microsoft.ApiManagement/service/subscriptions/write | Создает или обновляет подписку указанного пользователя на указанный продукт. или обновляет сведения о подписке, заданной ее идентификатором. |
> | Действия | Microsoft.ApiManagement/service/tagResources/read | Содержит коллекцию ресурсов, связанных с тегами. |
> | Действия | Microsoft.ApiManagement/service/tags/delete | Удаляет конкретный тег экземпляра службы управления API. |
> | Действия | Microsoft.ApiManagement/service/tags/read | Содержит коллекцию тегов, определенных в экземпляре службы. или возвращает сведения о теге, указанном с помощью его идентификатора. |
> | Действия | Microsoft.ApiManagement/service/tags/write | Создает тег. или обновляет сведения тега, указанного его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/templates/delete | Сброс к шаблону электронного сообщения по умолчанию службы управления API. |
> | Действия | Microsoft.ApiManagement/service/templates/read | Получение всех шаблонов сообщений или сведений о шаблоне сообщения службы управления API. |
> | Действия | Microsoft.ApiManagement/service/templates/write | Создание или обновление шаблона сообщения службы управления API. |
> | Действия | Microsoft.ApiManagement/service/tenant/delete | Удаляет конфигурацию политики для клиента. |
> | Действия | Microsoft.ApiManagement/service/tenant/deploy/action | Выполняет задачу развертывания для применения изменений из указанной ветви Git к конфигурации в базе данных. |
> | Действия | Microsoft.ApiManagement/service/tenant/operationResults/read | Возвращает список результатов операций или результат определенной операции. |
> | Действия | Microsoft.ApiManagement/service/tenant/read | Получите определение глобальной политики службы управления API. или получить сведения о доступе к клиенту |
> | Действия | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Повторно создает первичный ключ доступа. |
> | Действия | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Повторно создает вторичный ключ доступа. |
> | Действия | Microsoft.ApiManagement/service/tenant/save/action | Создает фиксацию с использованием моментального снимка конфигурации для указанной ветви в репозитории. |
> | Действия | Microsoft.ApiManagement/service/tenant/syncState/read | Возвращает состояние последней синхронизации с Git. |
> | Действия | Microsoft.ApiManagement/service/tenant/validate/action | Проверяет изменения из указанной ветви Git. |
> | Действия | Microsoft.ApiManagement/service/tenant/write | Установка конфигурации политики для клиента или обновление сведений о доступе к клиенту. |
> | Действия | Microsoft.ApiManagement/service/updatecertificate/action | Передает SSL-сертификата для службы управления API. |
> | Действия | Microsoft.ApiManagement/service/updatehostname/action | Позволяет настроить, обновить или удалить имена личных доменов для службы управления API. |
> | Действия | Microsoft.ApiManagement/service/users/action | Регистрация нового пользователя. |
> | Действия | Microsoft.ApiManagement/service/users/confirmations/send/action | Отправляет подтверждение. |
> | Действия | Microsoft.ApiManagement/service/users/delete | Удаляет конкретного пользователя. |
> | Действия | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Извлекает URL-адрес перенаправления, содержащий маркер проверки подлинности для подписания данного пользователя на портал разработчика. |
> | Действия | Microsoft.ApiManagement/service/users/groups/read | Список всех групп пользователей. |
> | Действия | Microsoft. ApiManagement/Service/Users/identitys/Read | Список всех удостоверений пользователей. |
> | Действия | Microsoft.ApiManagement/service/users/keys/read | Получение ключей, связанных с пользователем |
> | Действия | Microsoft.ApiManagement/service/users/read | Перечисляет коллекцию зарегистрированных пользователей в указанном экземпляре службы. или возвращает сведения о пользователе, заданном его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/users/subscriptions/read | Перечисляет коллекцию подписок указанного пользователя. |
> | Действия | Microsoft.ApiManagement/service/users/token/action | Возвращает маркер авторизации общего доступа для пользователя. |
> | Действия | Microsoft.ApiManagement/service/users/write | Создает или обновляет пользователя. или обновляет сведения о пользователе, определяемые его идентификатором. |
> | Действия | Microsoft.ApiManagement/service/write | Создание экземпляра службы управления API. |
> | Действия | Microsoft.ApiManagement/unregister/action | Отменяет регистрацию подписки для поставщика ресурсов Microsoft.ApiManagement. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Authorization/classicAdministrators/delete | Удаляет учетные данные администратора из подписки. |
> | Действия | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Получает сведения о состоянии операции администратора подписки. |
> | Действия | Microsoft.Authorization/classicAdministrators/read | Считывает администраторов для подписки. |
> | Действия | Microsoft.Authorization/classicAdministrators/write | Добавляет или изменяет администратора подписки. |
> | Действия | Microsoft.Authorization/denyAssignments/delete | Удаление запрета назначения в указанной области. |
> | Действия | Microsoft.Authorization/denyAssignments/read | Получение сведений о запрете назначения. |
> | Действия | Microsoft.Authorization/denyAssignments/write | Создание запрета назначения в указанной области. |
> | Действия | Microsoft.Authorization/elevateAccess/action | Предоставляет вызывающему доступ с правами администратора для области действия клиента. |
> | Действия | Microsoft.Authorization/locks/delete | Удаляет блокировки в указанной области. |
> | Действия | Microsoft.Authorization/locks/read | Возвращает блокировки в указанной области. |
> | Действия | Microsoft.Authorization/locks/write | Добавляет блокировки в указанной области. |
> | Действия | Microsoft.Authorization/operations/read | Получает список операций. |
> | Действия | Microsoft.Authorization/permissions/read | Выводит список всех разрешений, которые есть у вызывающего в заданной области. |
> | Действия | Microsoft. Authorization/политики, аудит/действие | Действие, предпринимаемое в результате оценки политики Azure с результатом "Audit" |
> | Действия | Microsoft. Authorization/Policies/помощью параметров auditifnotexists/действие | Действие, выполняемое в результате оценки политики Azure с результатом "помощью параметров auditifnotexists" |
> | Действия | Microsoft. Authorization/политики/Deny/Action | Действие, выполняемое в результате оценки политики Azure с результатом "отказ" |
> | Действия | Microsoft. Authorization/Policies/deployIfNotExists/действие | Действие, выполняемое в результате оценки политики Azure с результатом "deployIfNotExists" |
> | Действия | Microsoft.Authorization/policyAssignments/delete | Удаляет назначение политики в указанной области. |
> | Действия | Microsoft.Authorization/policyAssignments/read | Возвращает сведения о назначении политики. |
> | Действия | Microsoft.Authorization/policyAssignments/write | Создает назначение политики в указанной области. |
> | Действия | Microsoft.Authorization/policyDefinitions/delete | Удаляет определение политики. |
> | Действия | Microsoft.Authorization/policyDefinitions/read | Возвращает сведения об определении политики. |
> | Действия | Microsoft.Authorization/policyDefinitions/write | Создает определение настраиваемой политики. |
> | Действия | Microsoft.Authorization/policySetDefinitions/delete | Удаление определения набора политик. |
> | Действия | Microsoft.Authorization/policySetDefinitions/read | Получение сведений об определении набора политик. |
> | Действия | Microsoft.Authorization/policySetDefinitions/write | Создание пользовательского определения набора политик. |
> | Действия | Microsoft.Authorization/providerOperations/read | Возвращает операции для всех поставщиков ресурсов, которые могут быть использованы в определениях ролей. |
> | Действия | Microsoft.Authorization/roleAssignments/delete | Здесь описывается удаление назначения роли в указанной области. |
> | Действия | Microsoft.Authorization/roleAssignments/read | Возвращает сведения о назначении роли. |
> | Действия | Microsoft.Authorization/roleAssignments/write. | Создает назначение роли в указанной области. |
> | Действия | Microsoft.Authorization/roleDefinitions/delete | Удаляет указанное определение настраиваемой роли. |
> | Действия | Microsoft.Authorization/roleDefinitions/read | Возвращает сведения об определении роли. |
> | Действия | Microsoft.Authorization/roleDefinitions/write | Создает или обновляет определение настраиваемой роли с помощью указанных разрешений и назначаемых областей. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Чтение сведений о регистрации Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Запись запроса на повторное создание ключей Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/certificates/delete | Удаляет ресурс сертификата службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/certificates/getCount/action | Считывает число сертификатов. |
> | Действия | Microsoft.Automation/automationAccounts/certificates/read | Возвращает ресурс сертификата службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/certificates/write | Создает или обновляет ресурс сертификата службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/compilationjobs/read | Чтение содержимого компиляции Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/compilationjobs/read | Чтение содержимого компиляции Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/compilationjobs/write | Запись содержимого компиляции Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/compilationjobs/write | Запись содержимого компиляции Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/configurations/content/read | Считывает мультимедийное содержимое конфигурации. |
> | Действия | Microsoft.Automation/automationAccounts/configurations/delete | Удаление содержимого Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/configurations/getCount/action | Чтение количества содержимого Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/configurations/read | Возвращает содержимое Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/configurations/write | Запись содержимого Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/connections/delete | Удаляет ресурс подключения службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/connections/getCount/action | Считывает число подключений. |
> | Действия | Microsoft.Automation/automationAccounts/connections/read | Возвращает ресурс подключения службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/connections/write | Создает или обновляет ресурс подключения службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/connectionTypes/delete | Удаляет ресурс типа подключения службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/connectionTypes/read | Возвращает ресурс типа подключения службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/connectionTypes/write | Создает ресурс типа подключения службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/credentials/delete | Удаляет ресурс учетных данных службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/credentials/getCount/action | Считывает количество учетных данных. |
> | Действия | Microsoft.Automation/automationAccounts/credentials/read | Возвращает ресурс учетных данных службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/credentials/write | Создает или обновляет ресурс учетных данных службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/delete | Удаляет учетную запись службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Удаляет ресурсы гибридной рабочей роли Runbook. |
> | Действия | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Считывает ресурсы гибридной рабочей роли Runbook. |
> | Действия | Microsoft.Automation/automationAccounts/jobs/output/read | Возвращает выходные данные задания. |
> | Действия | Microsoft.Automation/automationAccounts/jobs/read | Возвращает задание службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/jobs/resume/action | Возобновляет выполнение задания службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Возвращает содержимое runbook службы автоматизации Azure во время выполнения задания. |
> | Действия | Microsoft.Automation/automationAccounts/jobs/stop/action | Останавливает задание службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/jobs/streams/read | Возвращает поток задания службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/jobs/streams/read | Возвращает поток задания службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/jobs/suspend/action | Приостанавливает задание службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/jobs/write | Создает задание службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/jobSchedules/delete | Удаляет расписание заданий службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/jobSchedules/read | Возвращает расписание заданий службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/jobSchedules/write | Создает расписание заданий службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Получает рабочую область, связанную с учетной записью службы автоматизации. |
> | Действия | Microsoft.Automation/automationAccounts/listKeys/action | Считывает ключи для учетной записи службы автоматизации. |
> | Действия | Microsoft.Automation/automationAccounts/modules/activities/read | Получение действий службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/modules/delete | Удаляет модуль PowerShell службы автоматизации Azure |
> | Действия | Microsoft.Automation/automationAccounts/modules/getCount/action | Возвращает число модулей PowerShell в рамках учетной записи службы автоматизации |
> | Действия | Microsoft.Automation/automationAccounts/modules/read | Получает модуль PowerShell службы автоматизации Azure |
> | Действия | Microsoft.Automation/automationAccounts/modules/write | Создает или обновляет модуль PowerShell службы автоматизации Azure |
> | Действия | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Удаление конфигурации узла Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Чтение содержимого конфигурации узла Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Чтение конфигурации узла Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Запись конфигурации узла Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/nodecounts/read | Считывает общее число узлов указанного типа. |
> | Действия | Microsoft.Automation/automationAccounts/nodes/delete | Удаление узлов Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/nodes/read | Чтение узлов Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Считывает содержимое отчета Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/nodes/reports/read | Чтение отчетов Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/nodes/write | Создает или обновляет узлы Azure Automation DSC. |
> | Действия | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Получение полей типов службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/python2Packages/delete | Удаляет пакет Python 2 службы автоматизации Azure |
> | Действия | Microsoft.Automation/automationAccounts/python2Packages/read | Получает пакет Python 2 службы автоматизации Azure |
> | Действия | Microsoft.Automation/automationAccounts/python2Packages/write | Создает или обновляет пакет Python 2 службы автоматизации Azure |
> | Действия | Microsoft.Automation/automationAccounts/python3Packages/delete | Удаляет пакет Python 3 службы автоматизации Azure |
> | Действия | Microsoft.Automation/automationAccounts/python3Packages/read | Получает пакет Python 3 службы автоматизации Azure |
> | Действия | Microsoft.Automation/automationAccounts/python3Packages/write | Создает или обновляет пакет Python 3 службы автоматизации Azure |
> | Действия | Microsoft.Automation/automationAccounts/read | Возвращает учетную запись службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/content/read | Возвращает содержимое runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/delete | Удаляет runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Создает содержимое черновика runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Возвращает результаты операции черновика runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/draft/read | Возвращает черновик runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Возвращает тестовое задание черновика runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Возобновляет выполнение тестового задания черновика runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Останавливает тестовое задание черновика runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Приостанавливает тестовое задание черновика runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Создает тестовое задание черновика runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Отменяет правки в черновике runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Возвращает число модулей runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/publish/action | Публикует черновик runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/read | Возвращает runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/runbooks/write | Создает или обновляет runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/schedules/delete | Удаляет ресурс расписания службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/schedules/getCount/action | Возвращает количество расписаний службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/schedules/read | Возвращает ресурс расписания службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/schedules/write | Создает или обновляет ресурс расписания службы автоматизации Azure. |
> | Действия | Microsoft. Automation/automationAccounts/Софтвареупдатеконфигуратионмачинерунс/чтение | Возвращает запуск компьютера конфигурации обновления программного обеспечения службы автоматизации Azure. |
> | Действия | Microsoft. Automation/automationAccounts/Софтвареупдатеконфигуратионрунс/чтение | Получает запуск конфигурации обновления программного обеспечения службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Удаляет конфигурацию обновления программного обеспечения Azure Automation |
> | Действия | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Удаляет конфигурацию обновления программного обеспечения Azure Automation |
> | Действия | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Получает конфигурации обновления программного обеспечения Azure Automation |
> | Действия | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Получает конфигурации обновления программного обеспечения Azure Automation |
> | Действия | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Создает или обновляет конфигурацию обновлений программного обеспечения Azure Automation |
> | Действия | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Создает или обновляет конфигурацию обновлений программного обеспечения Azure Automation |
> | Действия | Microsoft.Automation/automationAccounts/statistics/read | Получение статистики из службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Получает компьютер для развертывания обновления службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Получает задание исправления управления обновлениями для службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/usages/read | Получение данных использования из службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/variables/delete | Удаляет ресурс переменной службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/variables/read | Считывает ресурс переменной службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/variables/write | Создает или обновляет ресурс переменной службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/watchers/delete | Удаляет задание наблюдателя из службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/watchers/read | Получает задание наблюдателя из службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/watchers/start/action | Запускает задание наблюдателя из службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/watchers/stop/action | Останавливает выполнение заданий наблюдателя из службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/watchers/streams/read | Получение потока заданий наблюдателя из службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Удаляет действия заданий наблюдателя из службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Получает действия заданий наблюдателя из службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Создает действия заданий наблюдателя из службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/watchers/write | Создает задание наблюдателя из службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/webhooks/action | Создает универсальный код ресурса (URI) для webhook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/webhooks/delete | Удаляет webhook службы автоматизации Azure.  |
> | Действия | Microsoft.Automation/automationAccounts/webhooks/read | Считывает runbook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/webhooks/write | Создает или обновляет webhook службы автоматизации Azure. |
> | Действия | Microsoft.Automation/automationAccounts/write | Создает или обновляет учетную запись службы автоматизации Azure. |
> | Действия | Microsoft.Automation/operations/read | Получает доступные операции для ресурсов службы автоматизации Azure. |
> | Действия | Microsoft.Automation/register/action | Регистрирует подписку в службе автоматизации Azure. |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Удаление ресурса каталога B2C. |
> | Действия | Microsoft.AzureActiveDirectory/b2cDirectories/read | Просмотр ресурса каталога B2C. |
> | Действия | Microsoft.AzureActiveDirectory/b2cDirectories/write | Создание или обновление ресурса каталога B2C. |
> | Действия | Microsoft. AzureActiveDirectory/b2ctenants/Read | Список всех клиентов B2C, членом которых является пользователь |
> | Действия | Microsoft.AzureActiveDirectory/operations/read | Чтение всех операций API, доступных для поставщика ресурсов Microsoft.AzureActiveDirectory. |
> | Действия | Microsoft.AzureActiveDirectory/register/action | Регистрация подписки в поставщике ресурсов Microsoft.AzureActiveDirectory. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.AzureStack/Operations/read | Получение свойств операции поставщика ресурсов. |
> | Действия | Microsoft.AzureStack/register/action | Регистрация подписки в поставщике ресурсов Microsoft.AzureStack. |
> | Действия | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Удаление подписки клиента Azure Stack. |
> | Действия | Microsoft.AzureStack/registrations/customerSubscriptions/read | Получение свойств подписки клиента Azure Stack. |
> | Действия | Microsoft.AzureStack/registrations/customerSubscriptions/write | Создание или обновление подписки клиента Azure Stack. |
> | Действия | Microsoft.AzureStack/registrations/delete | Удаление регистрации в Azure Stack. |
> | Действия | Microsoft.AzureStack/registrations/getActivationKey/action | Получение последнего созданного ключа активации Azure Stack. |
> | Действия | Microsoft.AzureStack/registrations/products/listDetails/action | Возвращает подробные сведения о продукте из Azure Stack Marketplace. |
> | Действия | Microsoft.AzureStack/registrations/products/read | Получает свойства продукта из Azure Stack Marketplace. |
> | Действия | Microsoft. AzureStack/регистрация/продукты/Уплоадпродуктлог/действие | Состояние операции продукта "запись Azure Stack Marketplace" и метка времени |
> | Действия | Microsoft.AzureStack/registrations/read | Получение свойств регистрации в Azure Stack. |
> | Действия | Microsoft.AzureStack/registrations/write | Создание или обновление регистрации в Azure Stack. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Batch/batchAccounts/applications/delete | Удаляет приложение. |
> | Действия | Microsoft.Batch/batchAccounts/applications/read | Выводит список приложений или возвращает свойства приложения. |
> | Действия | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Активирует пакет приложения. |
> | Действия | Microsoft.Batch/batchAccounts/applications/versions/delete | Удаляет пакет приложения. |
> | Действия | Microsoft.Batch/batchAccounts/applications/versions/read | Возвращает свойства пакета приложения. |
> | Действия | Microsoft.Batch/batchAccounts/applications/versions/write | Создает новый пакет приложения или обновляет существующий. |
> | Действия | Microsoft.Batch/batchAccounts/applications/write | Создает новое приложение или обновляет существующее. |
> | Действия | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Получение результатов длительных операций с сертификатом в учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Отмена неудачного удаления сертификата в учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/certificates/delete | Удаление сертификата из учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/certificates/read | Вывод списка сертификатов в учетной записи пакетной службы или получение свойств сертификата. |
> | Действия | Microsoft.Batch/batchAccounts/certificates/write | Создание нового сертификата в учетной записи пакетной службы или обновление существующего сертификата. |
> | Действия | Microsoft.Batch/batchAccounts/delete | Удаляет учетную запись пакетной службы. |
> | DataAction | Microsoft. Batch/Батчаккаунтс/задания/Удаление | Удаляет задание из учетной записи пакетной службы. |
> | DataAction | Microsoft. Batch/Батчаккаунтс/задания/чтение | Выводит список заданий в учетной записи пакетной службы или получает свойства задания. |
> | DataAction | Microsoft. Batch/Батчаккаунтс/задания/запись | Создает новое задание в учетной записи пакетной службы или обновляет существующее задание. |
> | DataAction | Microsoft. Batch/Батчаккаунтс/Жобсчедулес/удаление | Удаление расписания задания из учетной записи пакетной службы |
> | DataAction | Microsoft. Batch/Батчаккаунтс/Жобсчедулес/чтение | Выводит список расписаний заданий в учетной записи пакетной службы или получает свойства расписания задания. |
> | DataAction | Microsoft. Batch/Батчаккаунтс/Жобсчедулес/запись | Создает новое расписание заданий в учетной записи пакетной службы или обновляет существующее расписание заданий. |
> | Действия | Microsoft.Batch/batchAccounts/listkeys/action | Отображает ключи доступа для учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/operationResults/read | Получение результатов длительных операций в учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/poolOperationResults/read | Получение результатов длительных операций с пулом в учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/pools/delete | Удаление пула из учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Отключение автоматического масштабирования в пуле учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/pools/read | Вывод списка пулов в учетной записи пакетной службы или свойств пула. |
> | Действия | Microsoft.Batch/batchAccounts/pools/stopResize/action | Остановка текущей операции изменения размера в пуле учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/pools/write | Создание нового пула в учетной записи пакетной службы или обновление существующего пула. |
> | Действия | Microsoft.Batch/batchAccounts/read | Выводит список учетных записей пакетной службы или возвращает свойства учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/regeneratekeys/action | Повторно создает ключи доступа для учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Синхронизирует ключи доступа для автоматической учетной записи хранения, настроенной для учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/batchAccounts/write | Создает новую учетную запись пакетной службы или обновляет существующую. |
> | Действия | Microsoft.Batch/locations/accountOperationResults/read | Получение результатов длительных операций в учетной записи пакетной службы. |
> | Действия | Microsoft.Batch/locations/checkNameAvailability/action | Проверка того, является ли имя учетной записи допустимым и неиспользуемым. |
> | Действия | Microsoft.Batch/locations/quotas/read | Возвращает квоты пакетной службы для указанной подписки в заданном регионе Azure. |
> | Действия | Microsoft.Batch/operations/read | Перечисляет операции, доступные в поставщике ресурсов Microsoft.Batch. |
> | Действия | Microsoft.Batch/register/action | Регистрирует подписку для поставщика ресурсов пакетной службы и позволяет создавать учетные записи пакетной службы. |
> | Действия | Microsoft.Batch/unregister/action | Отмена регистрации подписки для поставщика ресурсов пакетной службы, которая не позволяет создавать учетные записи пакетной службы. |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Billing/Биллингаккаунтс/соглашения/чтение |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпермиссионс/Read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Биллингпермиссионс/Read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Customers/Read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/счета/прайс-лист/Загрузка/действие |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингпермиссионс/Read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингсубскриптионс/Move/Action |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингсубскриптионс/действие |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингсубскриптионс/Валидатемовилигибилити/Action |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/Move/Action |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/продукты/действие |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/Валидатемовилигибилити/Action |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Write |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/прайс-лист/действие |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Write |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Write |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Биллингсубскриптионс/Read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Customers/Биллингпермиссионс/Read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Customers/Read |  |
> | Действия | Microsoft.Billing/billingAccounts/departments/read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Енроллментаккаунтс/Биллингпермиссионс/Read |  |
> | Действия | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Енроллментдепартментс/Биллингпермиссионс/Read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Листинвоицесектионсвискреатесубскриптионпермиссион/Action |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Products/Read |  |
> | Действия | Microsoft.Billing/billingAccounts/read |  |
> | Действия | Microsoft. Billing/Биллингаккаунтс/Write |  |
> | Действия | Microsoft.Billing/departments/read |  |
> | Действия | Microsoft.Billing/register/action |  |
> | Действия | Microsoft. Billing/Валидатеаддресс/действие |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.BingMaps/mapApis/Delete | Операция удаления. |
> | Действия | Microsoft.BingMaps/mapApis/listSecrets/action | Отображает секреты. |
> | Действия | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Считывает маркер авторизации единого входа для ресурса. |
> | Действия | Microsoft.BingMaps/mapApis/Read | Операция чтения. |
> | Действия | Microsoft.BingMaps/mapApis/regenerateKey/action | Повторно создает ключ. |
> | Действия | Microsoft.BingMaps/mapApis/Write | Операции записи. |
> | Действия | Microsoft.BingMaps/Operations/read | Описание операции. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Блокчейн/Блоккчаинмемберс/Delete | Удаляет существующий элемент Блокчейн. |
> | Действия | Microsoft. Блокчейн/Блоккчаинмемберс/Листапикэйс/Action | Возвращает или перечисляет существующие ключи API-элементов Блокчейн. |
> | Действия | Microsoft. Блокчейн/Блоккчаинмемберс/Read | Возвращает или перечисляет существующие элементы Блокчейн. |
> | DataAction | Microsoft. Блокчейн/Блоккчаинмемберс/Трансактионнодес/Connect/Action | Подключается к узлу транзакции-члена Блокчейн. |
> | Действия | Microsoft. Блокчейн/Блоккчаинмемберс/Трансактионнодес/Delete | Удаляет существующий узел транзакции элемента Блокчейн. |
> | Действия | Microsoft. Блокчейн/Блоккчаинмемберс/Трансактионнодес/Листапикэйс/действие | Возвращает или перечисляет существующие ключи API узла транзакции Блокчейн-члена. |
> | Действия | Microsoft. Блокчейн/Блоккчаинмемберс/Трансактионнодес/Read | Возвращает или перечисляет существующие узлы транзакций с элементами Блокчейн. |
> | Действия | Microsoft. Блокчейн/Блоккчаинмемберс/Трансактионнодес/запись | Создает или обновляет узел транзакции элемента Блокчейн. |
> | Действия | Microsoft. Блокчейн/Блоккчаинмемберс/запись | Создает или обновляет элемент Блокчейн. |
> | Действия | Microsoft. Блокчейн/Locations/Блоккчаинмембероператионресултс/Read | Возвращает результаты операции для элементов Блокчейн. |
> | Действия | Microsoft. Блокчейн/Locations/Чеккнамеаваилабилити/Action | Проверяет, является ли имя ресурса допустимым и не используется. |
> | Действия | Microsoft. Блокчейн/операции/чтение | Вывод списка всех операций в поставщике ресурсов Microsoft Блокчейн. |
> | Действия | Microsoft. Блокчейн/Register/действие | Регистрирует подписку для поставщика ресурсов Блокчейн. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Чтение артефактов схемы. |
> | Действия | Microsoft.Blueprint/blueprintAssignments/delete | Удаление любых артефактов схемы |
> | Действия | Microsoft.Blueprint/blueprintAssignments/read | Чтение артефактов схемы. |
> | Действия | Microsoft. чертеж/Блуепринтассигнментс/вхоисблуепринт/действие | Получите идентификатор объекта субъекта-службы для схем Azure. |
> | Действия | Microsoft.Blueprint/blueprintAssignments/write | Создание или изменение любых артефактов схемы |
> | Действия | Microsoft.Blueprint/blueprints/artifacts/delete | Удаление любых артефактов схемы |
> | Действия | Microsoft.Blueprint/blueprints/artifacts/read | Чтение артефактов схемы. |
> | Действия | Microsoft.Blueprint/blueprints/artifacts/write | Создание или изменение любых артефактов схемы |
> | Действия | Microsoft.Blueprint/blueprints/delete | Удаление схем. |
> | Действия | Microsoft.Blueprint/blueprints/read | Чтение схем. |
> | Действия | Microsoft.Blueprint/blueprints/versions/artifacts/read | Чтение артефактов схемы. |
> | Действия | Microsoft.Blueprint/blueprints/versions/delete | Удаление схем. |
> | Действия | Microsoft.Blueprint/blueprints/versions/read | Чтение схем. |
> | Действия | Microsoft.Blueprint/blueprints/versions/write | Создание или обновление схем. |
> | Действия | Microsoft.Blueprint/blueprints/write | Создание или обновление схем. |
> | Действия | Microsoft.Blueprint/register/action | Регистрация поставщика ресурсов схемы Azure. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.BotService/botServices/channels/delete | Удаление службы бота. |
> | Действия | Microsoft.BotService/botServices/channels/read | Чтение службы бота. |
> | Действия | Microsoft.BotService/botServices/channels/write | Запись службы бота. |
> | Действия | Microsoft.BotService/botServices/connections/delete | Удаление службы бота. |
> | Действия | Microsoft.BotService/botServices/connections/read | Чтение службы бота. |
> | Действия | Microsoft.BotService/botServices/connections/write | Запись службы бота. |
> | Действия | Microsoft.BotService/botServices/delete | Удаление службы бота. |
> | Действия | Microsoft.BotService/botServices/read | Чтение службы бота. |
> | Действия | Microsoft.BotService/botServices/write | Запись службы бота. |
> | Действия | Microsoft.BotService/locations/operationresults/read | Чтение состояния асинхронной операции. |
> | Действия | Microsoft.BotService/Operations/read | Чтение операций для всех типов ресурсов. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Cache/checknameavailability/action | Проверяет, доступно ли имя для нового кэша Redis. |
> | Действия | Microsoft.Cache/locations/operationresults/read | Получение результата длительных операций, для которых заголовок Location был ранее возвращен клиенту. |
> | Действия | Microsoft.Cache/operations/read | Вывод списка операций, которые поддерживает поставщик Microsoft.Cache. |
> | Действия | Microsoft.Cache/redis/delete | Удаляет весь кэш Redis. |
> | Действия | Microsoft.Cache/redis/export/action | Экспортирует данные Redis в большие двоичные объекты с префиксом в службе хранилища в указанном формате. |
> | Действия | Microsoft.Cache/redis/firewallRules/delete | Удаляет правила брандмауэра для IP-адресов для кэша Redis. |
> | Действия | Microsoft.Cache/redis/firewallRules/read | Возвращает правила брандмауэра для IP-адресов для кэша Redis. |
> | Действия | Microsoft.Cache/redis/firewallRules/write | Изменяет правила брандмауэра для IP-адресов для кэша Redis. |
> | Действия | Microsoft.Cache/redis/forceReboot/action | Принудительно перезапускает экземпляр кэша. При этом возможна потеря данных. |
> | Действия | Microsoft.Cache/redis/import/action | Импортирует в Redis данные в указанном формате из нескольких больших двоичных объектов. |
> | Действия | Microsoft.Cache/redis/linkedservers/delete | Удаляет связанный сервер из кэша Redis. |
> | Действия | Microsoft.Cache/redis/linkedservers/read | Возвращает связанные серверы для кэша Redis. |
> | Действия | Microsoft.Cache/redis/linkedservers/write | Добавляет связанный сервер для кэша Redis. |
> | Действия | Microsoft.Cache/redis/listKeys/action | Отображает значения ключей доступа к кэшу Redis на портале управления. |
> | Действия | Microsoft.Cache/redis/listUpgradeNotifications/read | Выводит список последних уведомлений об обновлениях для клиента кэша. |
> | Действия | Microsoft.Cache/redis/metricDefinitions/read | Возвращает доступные метрики для кэша Redis. |
> | Действия | Microsoft.Cache/redis/patchSchedules/delete | Удаляет расписание исправления для кэша Redis. |
> | Действия | Microsoft.Cache/redis/patchSchedules/read | Возвращает расписание исправления для кэша Redis. |
> | Действия | Microsoft.Cache/redis/patchSchedules/write | Изменяет расписание исправления для кэша Redis. |
> | Действия | Microsoft.Cache/redis/read | Отображает параметры и конфигурацию кэша Redis на портале управления. |
> | Действия | Microsoft.Cache/redis/regenerateKey/action | Изменяет значения ключей доступа к кэшу Redis на портале управления. |
> | Действия | Microsoft.Cache/redis/start/action | Запускает экземпляр кэша. |
> | Действия | Microsoft.Cache/redis/stop/action | Останавливает экземпляр кэша. |
> | Действия | Microsoft.Cache/redis/write | Изменяет параметры и конфигурацию кэша Redis на портале управления. |
> | Действия | Microsoft.Cache/register/action | Регистрирует поставщик ресурсов Microsoft.Cache для подписки. |
> | Действия | Microsoft.Cache/unregister/action | Отменяет регистрацию поставщика ресурсов Microsoft.Cache для подписки. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Capacity/appliedreservations/read | Чтение всех резервирований. |
> | Действия | Microsoft. Capacity/калкулатиксчанже/действие | Вычисление суммы и цены обмена для новой покупки и возвращение ошибок политики. |
> | Действия | Microsoft.Capacity/calculateprice/action | Вычисление цены резервирования. |
> | Действия | Microsoft.Capacity/catalogs/read | Чтение каталога резервирования. |
> | Действия | Microsoft.Capacity/checkoffers/action | Проверка наличия предложений для подписок. |
> | Действия | Microsoft.Capacity/checkscopes/action | Проверка наличия подписок. |
> | Действия | Microsoft.Capacity/commercialreservationorders/read | Получение заказов на резервирование, созданных в любом клиенте. |
> | Действия | Microsoft. Capacity/Exchange/действие | Обмен любыми резервированиями |
> | Действия | Microsoft.Capacity/operations/read | Чтение любой операции. |
> | Действия | Microsoft.Capacity/register/action | Регистрация поставщика ресурсов емкости и разрешение создания ресурсов емкости. |
> | Действия | Microsoft.Capacity/reservationorders/action | Обновление резервирования. |
> | Действия | Microsoft.Capacity/reservationorders/availablescopes/action | Поиск любой доступной области. |
> | Действия | Microsoft. Capacity/ресерватионордерс/калкулатерефунд/действие | Вычисление суммы возврата и цены на новую покупку и возвращение ошибок политики. |
> | Действия | Microsoft.Capacity/reservationorders/delete | Удаление резервирования. |
> | Действия | Microsoft.Capacity/reservationorders/merge/action | Объединение любого резервирования. |
> | Действия | Microsoft.Capacity/reservationorders/read | Чтение всех резервирований. |
> | Действия | Microsoft.Capacity/reservationorders/reservations/action | Обновление резервирования. |
> | Действия | Microsoft. Capacity/ресерватионордерс/резервирования/аваилаблескопес/действие | Поиск любой доступной области. |
> | Действия | Microsoft.Capacity/reservationorders/reservations/delete | Удаление резервирования. |
> | Действия | Microsoft.Capacity/reservationorders/reservations/read | Чтение всех резервирований. |
> | Действия | Microsoft.Capacity/reservationorders/reservations/revisions/read | Чтение всех резервирований. |
> | Действия | Microsoft.Capacity/reservationorders/reservations/write | Создание резервирования. |
> | Действия | Microsoft.Capacity/reservationorders/return/action | Возвращение любого резервирования. |
> | Действия | Microsoft.Capacity/reservationorders/split/action | Разделение любого резервирования. |
> | Действия | Microsoft.Capacity/reservationorders/swap/action | Переключение любого резервирования. |
> | Действия | Microsoft.Capacity/reservationorders/write | Создание резервирования. |
> | Действия | Microsoft.Capacity/tenants/register/action | Регистрация любого клиента. |
> | Действия | Microsoft.Capacity/unregister/action | Отмена регистрации любого клиента. |
> | Действия | Microsoft.Capacity/validatereservationorder/action | Проверка любого резервирования. |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Действия | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Действия | Microsoft.Cdn/edgenodes/delete |  |
> | Действия | Microsoft.Cdn/edgenodes/read |  |
> | Действия | Microsoft.Cdn/edgenodes/write |  |
> | Действия | Microsoft.Cdn/operationresults/delete |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Действия | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Действия | Microsoft.Cdn/operationresults/read |  |
> | Действия | Microsoft.Cdn/operationresults/write |  |
> | Действия | Microsoft.Cdn/operations/read |  |
> | Действия | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Действия | Microsoft.Cdn/profiles/delete |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/read |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Действия | Microsoft.Cdn/profiles/endpoints/write |  |
> | Действия | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Действия | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Действия | Microsoft.Cdn/profiles/read |  |
> | Действия | Microsoft.Cdn/profiles/write |  |
> | Действия | Microsoft.Cdn/register/action | Регистрация подписки для поставщика ресурсов CDN и разрешение создания профилей CDN. |
> | Действия | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Удаляет существующий сертификат. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Возвращает список сертификатов. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Добавляет новый сертификат или обновляет существующий. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/Delete | Удаляет существующий сертификат службы приложений. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/Read | Возвращает список заказов сертификатов. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Повторно отправляет существующий заказ сертификата. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Обновляет существующий заказ сертификата. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Повторно отправляет сообщение сертификата. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Повторно отправляет сообщения запроса на другой электронный адрес. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Извлекает печать сайта для выданного сертификата службы приложений. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Извлекает список действий сертификата. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Извлекает журнал электронных сообщений для сертификата. |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | проверка принадлежности домена; |
> | Действия | Microsoft.CertificateRegistration/certificateOrders/Write | Добавляет новый заказ сертификата или обновляет существующий. |
> | Действия | Microsoft.CertificateRegistration/operations/Read | Вывод списка всех операций из регистрации сертификата службы приложений. |
> | Действия | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Подготавливает субъект-службу для субъекта приложения-службы. |
> | Действия | Microsoft.CertificateRegistration/register/action | Регистрирует поставщик сертификатов Майкрософт для подписки. |
> | Действия | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Проверяет объект покупки сертификата без его отправки. |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ClassicCompute/capabilities/read | Отображает возможности. |
> | Действия | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Проверяет доступность заданного доменного имени. |
> | Действия | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Получает сведения о доступности указанного доменного имени. |
> | Действия | Microsoft.ClassicCompute/domainNames/active/write | Задает активное доменное имя. |
> | Действия | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Отображает группу доступности для ресурса. |
> | Действия | Microsoft.ClassicCompute/domainNames/capabilities/read | Отображает возможности доменного имени. |
> | Действия | Microsoft.ClassicCompute/domainNames/delete | Удаляет доменные имена для ресурсов. |
> | Действия | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Отображает слоты развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Получение роли в слоте развертывания доменного имени |
> | Действия | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Получение экземпляра роли в слоте развертывания доменного имени |
> | Действия | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Получение состояния слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Добавление состояния слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Получение домена обновления для слота развертывания доменного имени |
> | Действия | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Обновление домена обновления для слота развертывания доменного имени |
> | Действия | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Создает или обновляет развертывание. |
> | Действия | Microsoft.ClassicCompute/domainNames/extensions/delete | Удаляет расширения доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Считывает состояние операции расширений доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/extensions/read | Возвращает расширения доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/extensions/write | Добавляет расширения доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Удаляет внутреннюю подсистему балансировки нагрузки. |
> | Действия | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Считывает состояние операции внутренних подсистем балансировки нагрузки доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Возвращает внутренние подсистемы балансировки нагрузки. |
> | Действия | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Создает внутреннюю подсистему балансировки нагрузки. |
> | Действия | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Считывает состояние операции наборов конечных точек с балансировкой нагрузки доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Получение наборов конечных точек с балансировкой нагрузки. |
> | Действия | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Добавление наборов конечных точек с балансировкой нагрузки. |
> | Действия | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Получение состояния операции доменного имени. |
> | Действия | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Считывает состояние операции расширений доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/read | Возвращает доменные имена для ресурсов. |
> | Действия | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Удаляет используемые сертификаты службы. |
> | Действия | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Считывает состояние операции сертификатов службы доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Возвращает используемые сертификаты службы. |
> | Действия | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Добавляет или изменяет используемые сертификаты службы. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Прерывает миграцию слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Фиксирует миграцию слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/delete | Удаляет заданный слот развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Считывает состояние операции слотов доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Подготавливает миграцию слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/read | Отображает слоты развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Удаляет ссылку на расширение для роли слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Считывает состояние операции ссылок на расширение ролей слотов доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Возвращает ссылку на расширение для роли слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Добавляет или изменяет ссылку на расширение для роли слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Получение определения метрики роли для доменного имени. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Получение метрики роли для доменного имени. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Получение состояния операции для роли слота доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметры диагностики. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Добавляет или изменяет параметры диагностики. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Возвращает определения метрик. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/read | Возвращает роль для слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Скачивает файл подключения к удаленному рабочему столу для экземпляра роли в роли слота доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Получает состояние операции для экземпляра роли в роли слота доменных имен. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Возвращает экземпляр роли. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Повторная сборка экземпляра роли. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Пересоздает образ экземпляра роли. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Перезапускает экземпляры роли. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Получение номера SKU роли для слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/roles/write | Добавление роли для слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/start/action | Запускает слот развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Задает состояние остановки для слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Задает состояние запуска для слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/stop/action | Приостанавливает слот развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Обновляет домен. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Проверяет миграцию слота развертывания. |
> | Действия | Microsoft.ClassicCompute/domainNames/slots/write | Создает или обновляет развертывание. |
> | Действия | Microsoft.ClassicCompute/domainNames/swap/action | Переключает промежуточный слот на рабочий слот. |
> | Действия | Microsoft.ClassicCompute/domainNames/write | Добавляет или изменяет доменные имена для ресурсов. |
> | Действия | Microsoft.ClassicCompute/moveSubscriptionResources/action | Перемещает все классические ресурсы в другую подписку. |
> | Действия | Microsoft.ClassicCompute/operatingSystemFamilies/read | Вывод списка семейств гостевых операционных систем, доступных в Microsoft Azure, а также версий операционных систем в каждом таком семействе. |
> | Действия | Microsoft.ClassicCompute/operatingSystems/read | Выводит список версий гостевой операционной системы, доступных в Microsoft Azure. |
> | Действия | Microsoft.ClassicCompute/operations/read | Получает список операций. |
> | Действия | Microsoft.ClassicCompute/operationStatuses/read | Считывает состояние операции ресурса. |
> | Действия | Microsoft.ClassicCompute/quotas/read | Возвращает квоту для подписки. |
> | Действия | Microsoft.ClassicCompute/register/action | Выполняет регистрацию в классической вычислительной среде. |
> | Действия | Microsoft.ClassicCompute/resourceTypes/skus/read | Возвращает список номеров SKU для поддерживаемых типов ресурсов. |
> | Действия | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Проверяет доступность классической операции перемещения для подписки. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Удаляет группу безопасности сети, связанную с виртуальной машиной. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Считывает состояние операции групп безопасности сети, связанных с виртуальными машинами. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Возвращает группу безопасности сети, связанную с виртуальной машиной. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Добавляет группу безопасности сети, связанную с виртуальной машиной. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Возвращает возможные асинхронные операции. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Подключает диск данных к виртуальной машине. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/capture/action | Запись виртуальной машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/delete | Удаляет виртуальные машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Отключает диск данных от виртуальной машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Получение параметров диагностики виртуальной машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/disks/read | Извлекает список дисков данных. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Скачивает RDP-файл для виртуальной машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Считывает состояние операции расширений виртуальной машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/extensions/read | Возвращает расширение виртуальной машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/extensions/write | Размещает расширение виртуальной машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Получение определения метрики виртуальной машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/metrics/read | Возвращает метрики. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Удаляет группу безопасности сети, связанную с сетевым интерфейсом. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Считывает состояние операции групп безопасности сети, связанных с виртуальными машинами. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Возвращает группу безопасности сети, связанную с сетевым интерфейсом. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Добавляет группу безопасности сети, связанную с сетевым интерфейсом. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Считывает состояние операции виртуальных машин. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Выполнение обслуживания виртуальной машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметры диагностики. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Добавляет или изменяет параметры диагностики. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Возвращает определения метрик. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/read | Извлекает список виртуальных машин. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Повторно развертывает виртуальную машину. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/restart/action | Перезапускает виртуальные машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Завершает работу виртуальной машины. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/start/action | Запустите виртуальную машину. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/stop/action | Останавливает виртуальную машину. |
> | Действия | Microsoft.ClassicCompute/virtualMachines/write | Добавляет или изменяет виртуальные машины. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Получение состояния операции для перекрестных подключений ExpressRoute. |
> | Действия | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Удаление пиринга перекрестных подключений ExpressRoute. |
> | Действия | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Получение состояния операции для пиринга перекрестных подключений ExpressRoute. |
> | Действия | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Получение пиринга перекрестных подключений ExpressRoute. |
> | Действия | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Добавление пиринга перекрестных подключений ExpressRoute. |
> | Действия | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Получение перекрестных подключений ExpressRoute. |
> | Действия | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Добавление перекрестных подключений ExpressRoute. |
> | Действия | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Извлекает список поддерживаемых устройств. |
> | Действия | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Удаляет группу безопасности сети. |
> | Действия | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Считывает состояние операции группы безопасности сети. |
> | Действия | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики групп безопасности сети. |
> | Действия | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики для групп безопасности сети. Эта операция осуществляется при задействовании поставщика ресурсов аналитических сведений. |
> | Действия | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Получение событий группы безопасности сети. |
> | Действия | Microsoft.ClassicNetwork/networkSecurityGroups/read | Возвращает группу безопасности сети. |
> | Действия | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Удаляет правило безопасности. |
> | Действия | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Считывает состояние операции правил безопасности группы безопасности сети. |
> | Действия | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Возвращает правило безопасности. |
> | Действия | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Добавляет или обновляет правило безопасности. |
> | Действия | Microsoft.ClassicNetwork/networkSecurityGroups/write | Добавляет новую группу безопасности сети. |
> | Действия | Microsoft.ClassicNetwork/operations/read | Получение операций классической сети. |
> | Действия | Microsoft.ClassicNetwork/quotas/read | Возвращает квоту для подписки. |
> | Действия | Microsoft.ClassicNetwork/register/action | Выполняет регистрацию в классической сети. |
> | Действия | Microsoft.ClassicNetwork/reservedIps/delete | Удаляет зарезервированный IP-адрес. |
> | Действия | Microsoft.ClassicNetwork/reservedIps/join/action | Присоединяет зарезервированный IP-адрес. |
> | Действия | Microsoft.ClassicNetwork/reservedIps/link/action | Привязывает зарезервированный IP-адрес. |
> | Действия | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Считывает состояние операции для зарезервированных IP-адресов. |
> | Действия | Microsoft.ClassicNetwork/reservedIps/read | Возвращает зарезервированные IP-адреса. |
> | Действия | Microsoft.ClassicNetwork/reservedIps/write | Добавляет новый зарезервированный IP-адрес. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Прерывает перенос виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Отображает возможности. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Проверяет доступность заданного IP-адреса в виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Фиксирует перенос виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/delete | Удаляет виртуальную сеть. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Отменяет отмену сертификата клиента. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Считывает отозванные сертификаты клиента. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Отменяет сертификат клиента. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Удаляет сертификат клиента шлюза виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Скачивает сертификат по отпечатку. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Отображает пакет сертификата шлюза виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Выполняет поиск корневых сертификатов клиента. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Передает новый корневой сертификат клиента. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Устанавливает подключение шлюза типа "сеть — сеть". |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Отключает подключение шлюза типа "сеть — сеть". |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Извлекает список подключений. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Тестирует подключение шлюза типа "сеть — сеть". |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Удаляет шлюз виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Скачивает сценарий конфигурации устройства. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Скачивает данные диагностики шлюза. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Извлекает ключ службы канала. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Отображает пакет шлюза виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Считывает состояние операции шлюзов виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Возвращает пакет шлюза виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Возвращает шлюзы виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Запускает диагностику шлюза виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Останавливает диагностику шлюза виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Добавляет шлюз виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/join/action | Подключает виртуальную сеть. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Считывает состояние операции виртуальных сетей. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Создает пиринг между виртуальными сетями. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Подготавливает перенос виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/read | Возвращает виртуальную сеть. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Удаляет удаленный прокси-сервер пиринга виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Получает удаленный прокси-сервер пиринга виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Добавляет или обновляет удаленный прокси-сервер пиринга виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Удаляет группу безопасности сети, связанную с подсетью. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Считывает состояние операции группы безопасности сети, связанной с подсетью виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Возвращает группу безопасности сети, связанную с подсетью. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Добавляет группу безопасности сети, связанную с подсетью. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Проверяет перенос виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Получает пиринг виртуальной сети. |
> | Действия | Microsoft.ClassicNetwork/virtualNetworks/write | Добавляет новую виртуальную сеть. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ClassicStorage/capabilities/read | Отображает возможности. |
> | Действия | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Проверяет доступность учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Получает сведения о доступности учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/disks/read | Возвращает диск в учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/images/operationstatuses/read | Получает состояние операции создания образа. |
> | Действия | Microsoft.ClassicStorage/images/read | Возвращает образ. |
> | Действия | Microsoft.ClassicStorage/operations/read | Получает операции классического хранилища. |
> | Действия | Microsoft.ClassicStorage/osImages/read | Возвращает образ операционной системы. |
> | Действия | Microsoft.ClassicStorage/osPlatformImages/read | Получает образ платформы операционной системы. |
> | Действия | Microsoft.ClassicStorage/publicImages/read | Возвращает общедоступный образ виртуальной машины. |
> | Действия | Microsoft.ClassicStorage/quotas/read | Возвращает квоту для подписки. |
> | Действия | Microsoft.ClassicStorage/register/action | Выполняет регистрацию в классической службе хранилища. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Прерывает перенос учетной записи хранения. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Блобсервицес/providers/Microsoft. Insights/diagnosticSettings/Read | Возвращает параметры диагностики. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Блобсервицес/providers/Microsoft. Insights/diagnosticSettings/запись | Добавляет или изменяет параметры диагностики. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Блобсервицес/providers/Microsoft. Insights/metricDefinitions/Read | Возвращает определения метрик. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Фиксирует перенос учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/delete | Удаляет учетную запись хранения. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/disks/delete | Удаляет заданный диск из учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Считывает состояние операции ресурса. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/disks/read | Возвращает диск в учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/disks/write | Добавляет диск учетной записи хранения. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Филесервицес/providers/Microsoft. Insights/diagnosticSettings/Read | Возвращает параметры диагностики. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Филесервицес/providers/Microsoft. Insights/diagnosticSettings/запись | Добавляет или изменяет параметры диагностики. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Филесервицес/providers/Microsoft. Insights/metricDefinitions/Read | Возвращает определения метрик. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/images/delete | Удаляет заданный образ из учетной записи хранения. (Не рекомендуется к использованию. Следует использовать Microsoft.ClassicStorage/storageAccounts/vmImages.) |
> | Действия | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Возвращает состояние операции создания образа учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/images/read | Возвращает образ в учетной записи хранения. (Не рекомендуется к использованию. Следует использовать Microsoft.ClassicStorage/storageAccounts/vmImages.) |
> | Действия | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Считывает состояние операции ресурса. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Удаляет заданный образ операционной системы из учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/osImages/read | Возвращает заданный образ операционной системы в учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/osImages/write | Добавляет образ операционной системы указанной учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Подготавливает перенос учетной записи хранения. |
> | Действия | Microsoft. Классикстораже/storageAccounts/providers/Microsoft. Insights/diagnosticSettings/Read | Возвращает параметры диагностики. |
> | Действия | Microsoft. Классикстораже/storageAccounts/providers/Microsoft. Insights/diagnosticSettings/запись | Добавляет или изменяет параметры диагностики. |
> | Действия | Microsoft. Классикстораже/storageAccounts/providers/Microsoft. Insights/metricDefinitions/Read | Возвращает определения метрик. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Куеуесервицес/providers/Microsoft. Insights/diagnosticSettings/Read | Возвращает параметры диагностики. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Куеуесервицес/providers/Microsoft. Insights/diagnosticSettings/запись | Добавляет или изменяет параметры диагностики. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Куеуесервицес/providers/Microsoft. Insights/metricDefinitions/Read | Возвращает определения метрик. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/read | Возвращает учетную запись хранения для заданной учетной записи. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Повторно создает существующие ключи доступа для учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Возвращает параметры диагностики. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Добавляет или изменяет параметры диагностики. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Возвращает определения метрик. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Возвращает метрики. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/services/read | Возвращает доступные службы. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Таблесервицес/providers/Microsoft. Insights/diagnosticSettings/Read | Возвращает параметры диагностики. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Таблесервицес/providers/Microsoft. Insights/diagnosticSettings/запись | Добавляет или изменяет параметры диагностики. |
> | Действия | Microsoft. Классикстораже/storageAccounts/Таблесервицес/providers/Microsoft. Insights/metricDefinitions/Read | Возвращает определения метрик. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Проверяет перенос учетной записи хранения. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Удаляет указанный образ виртуальной машины. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Получает состояние операции создания указанного образа виртуальной машины. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Возвращает образ виртуальной машины. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Добавляет указанный образ виртуальной машины. |
> | Действия | Microsoft.ClassicStorage/storageAccounts/write | Добавляет новую учетную запись хранения. |
> | Действия | Microsoft.ClassicStorage/vmImages/read | Выводит список образов виртуальных машин. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | DataAction | Microsoft. CognitiveServices/Accounts/Автозаполнение, Поиск и действие | Эта операция предоставляет предложения для данного запроса или частичного запроса. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Компутервисион/анализ/действие | Эта операция извлекает богатый набор визуальных функций на основе содержимого изображения.  |
> | DataAction | Microsoft. CognitiveServices/Accounts/Компутервисион/ареаофинтерест/Action | Эта операция возвращает ограничивающий прямоугольник вокруг наиболее важной области изображения. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Компутервисион/описание/действие | Эта операция создает описание образа на удобочитаемом языке с помощью полных предложений.<br> Описание основано на коллекции тегов содержимого, которые также возвращаются операцией.<br>Для каждого изображения может быть создано более одного описания.<br> Описания упорядочены по оценкам достоверности.<br>Все описания приведены на английском языке. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Компутервисион/обнаружение/действие | Эта операция выполняет обнаружение объектов для указанного образа.  |
> | DataAction | Microsoft. CognitiveServices/Accounts/Компутервисион/generatethumbnail/Action | Эта операция создает эскиз изображения с заданной пользователем шириной и высотой.<br> По умолчанию служба анализирует изображение, определяет нужный регион (окупаемость инвестиций) и создает координаты обрезки на основе рентабельности инвестиций.<br> Интеллектуальная обрезка помогает при указании пропорций, отличающемся от типа входного изображения. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Компутервисион/Models/Analyze/Action | Эта операция распознает содержимое в образе, применяя модель для конкретного домена.<br> Список моделей для конкретных доменов, которые поддерживаются API компьютерного зрения, можно получить с помощью запроса GET/Моделс.<br> В настоящее время API предоставляет следующие модели для конкретных доменов: знаменитостей, ориентиры. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Компутервисион/Models/Read | Эта операция возвращает список моделей домена, которые поддерживаются API компьютерного зрения.  В настоящее время API поддерживает следующие модели для конкретных доменов: распознаватель знаменитости, распознаватель ориентиров. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Компутервисион/OCR/Action | Оптическое распознавание символов (OCR) определяет текст в изображении и извлекает распознанные символы в поток символов, пригодный для использования компьютером.    |
> | DataAction | Microsoft. CognitiveServices/Accounts/Компутервисион/рекогнизетекст/Action | Используйте этот интерфейс для получения результата операции Распознавание текста. При использовании интерфейса Распознавание текста ответ содержит поле "Operation-Location". Поле "Operation-Location" содержит URL-адрес, который необходимо использовать для операции получения результата операции Get Распознавание текста. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Компутервисион/Tag/действие | Эта операция создает список слов или тегов, относящихся к содержимому предоставляемого изображения.<br>API компьютерного зрения может возвращать Теги на основе объектов, живых существ, пейзажей или действий, найденных в образах.<br>В отличие от категорий, теги не упорядочены в соответствии с иерархической системой классификации, но соответствуют содержимому изображения.<br>Теги могут содержать указания, чтобы избежать неоднозначности или предоставить контекст, например тег "целло" может сопровождаться указанием "музыкальное средство".<br>Все теги доступны на английском языке. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Компутервисион/текстоператионс/Read | Этот интерфейс используется для получения результата операции распознавания текста. URL-адрес этого интерфейса должен быть получен из поля <b>"Operation-Location"</b> , возвращенного из интерфейса распознавание текста. |
> | Действия | Microsoft.CognitiveServices/accounts/delete | Удаляет учетные записи API. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Ентитисеарч/Поиск/действие | Получение сущностей и размещение результатов для заданного запроса. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/обнаружение/действие | Обнаруживать человеческих лиц в изображении, возвращать прямоугольники лицом и при необходимости с Фацеидс, ориентирами и атрибутами. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, фацелистс/Delete | Удаляет указанный список лиц. Связанные изображения лицевой стороны в списке лиц также будут удалены. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, фацелистс/персистедфацес/Delete | Удаление грани из списка лиц с помощью указанных Фацелистид и Персиситедфацеид. Изображение, связанное с лицевой стороны, будет удалено. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/фацелистс/персистедфацес/запись | Добавление лица в указанный список лиц, до 1 000 лиц. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, фацелистс/Read | Получение Фацелистид списка лиц, имени, userData и лиц в списке лиц. Список лиц с именами Фацелистид, Name и userData. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, фацелистс/Write | Создайте пустой список лиц с заданными пользователем Фацелистид, именем и необязательным параметром userData. В список лиц, имеющих до 64, разрешены сведения об обновлении списка лиц, включая имя и userData. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, финдсимиларс/действие | FaceId, заданный лицевой стороной запроса, для поиска похожих сторон из faceId массива, списка лиц или большого списка лиц. faceId |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, группа или действие | Разделите потенциальные лица на группы на основе сходства лиц. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, Поиск/действие | "один ко многим" для поиска ближайших совпадений лица, отвечающего конкретным запросам, от группы людей или большой группы лиц. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжефацелистс/Delete | Удаление указанного большого списка лиц. Связанные изображения лицевой стороны в списке больших лиц также будут удалены. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжефацелистс/персистедфацес/Delete | Удаление лица из большого списка с помощью указанных Ларжефацелистид и Персиситедфацеид. Изображение, связанное с лицевой стороны, будет удалено. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжефацелистс/персистедфацес/Read | Получите сохраненное лицо в большом списке лиц, Ларжефацелистид и Персистедфацеид. Список лиц Персистедфацеид и userData в указанном большом списке лиц. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/ларжефацелистс/персистедфацес/запись | Добавление лица в указанный большой список лиц, до 1 000 000 лиц. Обновляет поле userData указанного лица в большом списке лиц по его Персистедфацеид. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжефацелистс/Read | Получите Ларжефацелистид списка больших лиц, имя, userData. Вывод списка больших лиц — сведения о Ларжефацелистид, Name и userData. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжефацелистс, обучение и действие | Отправьте задачу по обучению большого списка лиц. Обучение — это важный этап, который может использовать только обученный большой список лиц. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/ларжефацелистс/Train/Read | Чтобы проверить состояние обучения для большого списка лиц, завершено или по-прежнему. LargeFaceListное обучение — это асинхронная операция |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжефацелистс/Write | Создайте пустой список больших лиц с заданным пользователем Ларжефацелистид, именем и необязательным параметром userData. Обновление сведений о большом списке лиц, включая имя и userData. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжеперсонграупс/Delete | Удаление существующей большой группы людей с указанным Персонграупид. Сохраненные данные в большой группе людей будут удалены. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжеперсонграупс/persons/Action | Создание нового пользователя в указанной большой группе людей. Чтобы добавить лицо для этого человека, позвоните по адресу. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжеперсонграупс/persons/Delete | Удаление существующего пользователя из большой группы людей. Все сохраненные личные данные и изображения лиц в записи пользователя будут удалены. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/ларжеперсонграупс/persons/персистедфацес/Delete | Удаление лица из человека в большой группе людей. Данные о лицах и изображения, связанные с этой записью лица, также будут удалены. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/ларжеперсонграупс/persons/персистедфацес/Read | Получение сведений о лице. Сохраненное лицо Person задается его Ларжеперсонграупид, personId и Персистедфацеид. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/ларжеперсонграупс/persons/персистедфацес/Write | Добавьте изображение лица в крупную группу людей для идентификации или проверки личности. Для работы с изображением поля userData, сохраненного лицом Person. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжеперсонграупс/persons/Read | Получите имя пользователя и userData, а также сохраненное Фацеидс, представляющее зарегистрированное изображение лица. Вывод сведений обо всех лицах в указанной большой группе людей, включая personId, Name, userData и Персистедфацеидс. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжеперсонграупс/persons/Write | Обновление имени или userData пользователя. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжеперсонграупс/Read | Получите сведения о большой группе людей, включая ее имя и userData. Этот API возвращает большой список сведений о группе людей со всеми существующими Ларжепесонграупид, именем и userData группы больших людей. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжеперсонграупс, обучение и действие | Отправка задачи обучения большой группы людей. Обучение — это важный этап, который может использовать только обученная большая группа людей. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/ларжеперсонграупс/Train/Read | Для проверки состояния обучения больших групп людей завершено или по-прежнему. Ларжеперсонграупное обучение — это асинхронная операция |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, ларжеперсонграупс/Write | Создание большой группы людей с заданными пользователем Ларжеперсонграупид, именем и необязательным. Обновление имеющегося имени группы больших людей и userData. Свойства сохраняются без изменений, если они не находятся в тексте запроса. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, персонграупс/Delete | Удаление существующей группы лиц с указанным Персонграупид. Сохраненные данные в этой группе пользователей будут удалены. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, персонграупс/persons/Action | Создание нового пользователя в указанной группе лиц. Чтобы добавить лицо для этого человека, позвоните по адресу. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, персонграупс/persons/Delete | Удаление существующего пользователя из группы людей. Все сохраненные личные данные и изображения лиц в записи пользователя будут удалены. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/персонграупс/persons/персистедфацес/Delete | Удаление лица из человека в группе людей. Данные о лицах и изображения, связанные с этой записью лица, также будут удалены. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/персонграупс/persons/персистедфацес/Read | Получение сведений о лице. Сохраненное лицо Person задается его Персонграупид, personId и Персистедфацеид. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/персонграупс/persons/персистедфацес/Write | Добавление изображения лица к лицу в группу лиц для идентификации или проверки личности лица. Для работы с изображением нескольких обновлений поля userData пользователя, сохраненного лицом. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, персонграупс/persons/Read | Получите имя пользователя и userData, а также сохраненное Фацеидс, представляющее зарегистрированное изображение лица. Вывод сведений обо всех лицах в указанной группе людей, включая personId, Name, userData и Персистедфацеидс, зарегистрированных. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, персонграупс/persons/Write | Обновление имени или userData пользователя. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, персонграупс/Read | Получение имени группы людей и userData. Чтобы получить сведения о лице в этой Персонграуп, используйте список Песонграупид, имя и userData группы людей. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, персонграупс, обучение и действие | Отправка задачи обучения группе Person. Обучение — это важный этап, который может использовать только обученная группа лиц. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/персонграупс/Train/Read | Чтобы проверить состояние обучения группы лиц завершено или по-прежнему текущее. Персонграупное обучение — это асинхронная операция, активируемая |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, персонграупс/Write | Создайте новую группу Person с указанными Персонграупид, именем и предоставленным пользователем userData. Обновите имя существующей группы людей и userData. Свойства сохраняются без изменений, если они не находятся в тексте запроса. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face, проверка/действие | Проверьте, принадлежат ли два лица одному человеку или принадлежит ли одна из них пользователю. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Имажесеарч/Detail/Action | Возвращает ценную информацию о изображении, например на веб-страницах, содержащих изображение. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Имажесеарч/Поиск/действие | Получение соответствующих изображений для данного запроса. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Имажесеарч/тенденция/действие | Получите образами тенденции. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Иммерсивереадер/жетконтентмоделфорреадер/Action | Создает иммерсивное сеанс чтения |
> | DataAction | Microsoft. CognitiveServices/Accounts/Инкрекогнизер/Recognize/действие | Учитывая набор данных Stroke, анализирует содержимое и создает список распознаваемых сущностей, включая распознанный текст. |
> | Действия | Microsoft.CognitiveServices/accounts/listKeys/action | Отображает ключи. |
> | DataAction | Microsoft. CognitiveServices/Accounts/LUIS/Predict/Action | Возвращает прогноз опубликованной конечной точки для данного запроса. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Невссеарч/категорисеарч/Action | Возвращает Новости для указанной категории. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Невссеарч/Поиск/действие | Получение новостей статей, актуальных для данного запроса. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Невссеарч/трендингтопикс/Action | Получение разделов о тенденциях, определяемых Bing. Это те же разделы, которые отображаются в баннере в нижней части домашней страницы Bing. |
> | Действия | Microsoft.CognitiveServices/accounts/read | Считывает учетные записи API. |
> | Действия | Microsoft.CognitiveServices/accounts/regenerateKey/action | Повторно создает ключ. |
> | Действия | Microsoft.CognitiveServices/accounts/skus/read | Считывает доступные номера SKU для существующего ресурса. |
> | DataAction | Microsoft. CognitiveServices/Accounts/проверкой орфографии/действие | Получение результата запроса проверки орфографии с помощью GET или POST. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Текстаналитикс/сущности/действие | API возвращает список известных сущностей и общие именованные сущности (\"Person\", \"расположение\", \"Организации\" т. д.) в определенном документе. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Текстаналитикс/фраз/Action | API возвращает список строк, указывающих основные тезисы входного текста. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Текстаналитикс/Languages/Action | API возвращает определенный язык и оценку в баллах между 0 и 1. Оценки, близкие к 1, указывают 100 % достоверность, что определенный язык имеет значение true. Поддерживается около 120 языков. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Текстаналитикс/тональности/Action | API возвращает оценку в баллах между 0 и 1.<br>Если оценка приближается к 1, то у текста позитивная тональность, а если к 0, то негативная.<br>Оценка 0,5 указывает на отсутствие тональности (например,<br>Инструкция фактоид). |
> | Действия | Microsoft.CognitiveServices/accounts/usages/read | Возвращает данные об использовании квоты для существующего ресурса. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Видеосеарч/Detail/Action | Получите ценные сведения о видео, например связанные видео. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Видеосеарч/Поиск/действие | Получение видеороликов, относящихся к определенному запросу. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Видеосеарч/тенденция/действие | Получите видео о тенденциях в настоящее время. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Висуалсеарч/Поиск/действие | Возвращает список тегов, относящихся к предоставленному изображению |
> | DataAction | Microsoft. CognitiveServices/Accounts/Search, Поиск/действие | Получение результатов поиска в Интернете, изображениях, новостях и & видео для заданного запроса. |
> | Действия | Microsoft.CognitiveServices/accounts/write | Записывает учетные записи API. |
> | Действия | Microsoft. CognitiveServices/Чеккдомаинаваилабилити/действие | Считывает доступные номера SKU для подписки. |
> | Действия | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Считывает доступные номера SKU для подписки. |
> | Действия | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Считывает доступные номера SKU для подписки. |
> | Действия | Microsoft. CognitiveServices/Locations/Делетевиртуалнетворкорсубнетс/Action | Уведомление от Microsoft. Network об удалении VirtualNetworks или подсетей. |
> | Действия | Microsoft.CognitiveServices/Operations/read | Вывод списка всех доступных операций. |
> | Действия | Microsoft.CognitiveServices/register/action | Регистрация подписки для Cognitive Services. |
> | Действия | Microsoft.CognitiveServices/register/action | Регистрация подписки для Cognitive Services. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Commerce/RateCard/read | Возвращает данные предложения, метаданные ресурсов и единиц измерения и тарифы для заданной подписки. |
> | Действия | Microsoft.Commerce/UsageAggregates/read | Извлекает данные о потреблении ресурсов Microsoft Azure по подписке. Результат содержит статистические данные об использовании, а также сведения о подписке и ресурсах за определенный диапазон времени. |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Compute/availabilitySets/delete | Удаляет группу доступности. |
> | Действия | Microsoft.Compute/availabilitySets/read | Возвращает свойства группы доступности. |
> | Действия | Microsoft.Compute/availabilitySets/vmSizes/read | Выводит список доступных размеров для создания или обновления виртуальной машины в группе доступности. |
> | Действия | Microsoft.Compute/availabilitySets/write | Создает новую группу доступности или обновляет существующую. |
> | Действия | Microsoft. COMPUTE/Дискенкриптионсетс/удаление | Удаление набора шифрования дисков |
> | Действия | Microsoft. COMPUTE/Дискенкриптионсетс/чтение | Получение свойств набора шифрования дисков |
> | Действия | Microsoft. COMPUTE/Дискенкриптионсетс/запись | Создать новый или обновить существующий набор шифрования дисков |
> | Действия | Microsoft.Compute/disks/beginGetAccess/action | Возвращает универсальный код ресурса (URI) SAS диска для доступа к большому двоичному объекту. |
> | Действия | Microsoft.Compute/disks/delete | Удаляет диск. |
> | Действия | Microsoft.Compute/disks/endGetAccess/action | Отменяет универсальный код ресурса (URI) SAS диска. |
> | Действия | Microsoft.Compute/disks/read | Возвращает свойства диска. |
> | Действия | Microsoft.Compute/disks/write | Создает новый диск или обновляет существующий. |
> | Действия | Microsoft.Compute/galleries/delete | Удаляет коллекцию. |
> | Действия | Microsoft.Compute/galleries/images/delete | Удаляет образ из коллекции. |
> | Действия | Microsoft.Compute/galleries/images/read | Получает свойства образа коллекции. |
> | Действия | Microsoft.Compute/galleries/images/versions/delete | Удаляет версию образа коллекции. |
> | Действия | Microsoft.Compute/galleries/images/versions/read | Получает свойства версии образа коллекции. |
> | Действия | Microsoft.Compute/galleries/images/versions/write | Создает новую версию образа коллекции или обновляет существующую версию. |
> | Действия | Microsoft.Compute/galleries/images/write | Создает новый образ коллекции или обновляет существующий. |
> | Действия | Microsoft.Compute/galleries/read | Получает свойства коллекции. |
> | Действия | Microsoft.Compute/galleries/write | Создает новую коллекцию или обновляет существующую. |
> | Действия | Microsoft. COMPUTE/Хостграупс/удаление | Удаляет группу узлов |
> | Действия | Microsoft. COMPUTE/Хостграупс/hosts/Delete | Удаляет узел |
> | Действия | Microsoft. COMPUTE/Хостграупс/hosts/Read | Получение свойств узла |
> | Действия | Microsoft. COMPUTE/Хостграупс/hosts/Write | Создает новый узел или обновляет существующий. |
> | Действия | Microsoft. COMPUTE/Хостграупс/чтение | Получение свойств группы узлов |
> | Действия | Microsoft. COMPUTE/Хостграупс/запись | Создает новую группу узлов или обновляет существующую. |
> | Действия | Microsoft.Compute/images/delete | Удаляет кластер. |
> | Действия | Microsoft.Compute/images/read | Возвращает свойства образа. |
> | Действия | Microsoft.Compute/images/write | Создает новый образ или обновляет существующий. |
> | Действия | Microsoft.Compute/locations/capsOperations/read | Получение состояния асинхронной операции ограничения. |
> | Действия | Microsoft.Compute/locations/diskOperations/read | Получение состояния асинхронной операции диска. |
> | Действия | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Создание журналов, позволяющих показать общее количество запросов за интервал времени, чтобы помочь в диагностике регулирования. |
> | Действия | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Создание журналов, позволяющих показать вычисленное количество регулируемых запросов, сгруппированных по имени ресурса, имени операции или примененной политике регулирования. |
> | Действия | Microsoft.Compute/locations/operations/read | Возвращает состояние асинхронной операции. |
> | Действия | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Получение свойств предложения образа платформы. |
> | Действия | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Получение свойств номера Sku образа платформы. |
> | Действия | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Получение свойств версии образа платформы. |
> | Действия | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Получение свойств типа расширения виртуальной машины. |
> | Действия | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Получение свойств версии расширения виртуальной машины. |
> | Действия | Microsoft.Compute/locations/publishers/read | Получение свойств издателя. |
> | Действия | Microsoft.Compute/locations/runCommands/read | Вывод списка доступных команд запуска в расположении. |
> | Действия | Microsoft.Compute/locations/usages/read | Возвращает ограничения службы и текущее количество использованных вычислительных ресурсов подписки в расположении. |
> | Действия | Microsoft.Compute/locations/vmSizes/read | Выводит список доступных размеров виртуальных машин в расположении. |
> | Действия | Microsoft.Compute/operations/read | Выводит список операций, доступных в поставщике ресурсов Microsoft.Compute. |
> | Действия | Microsoft. COMPUTE/Проксимитиплацементграупс/удаление | Удаляет группу размещения с учетом расположения |
> | Действия | Microsoft. COMPUTE/Проксимитиплацементграупс/чтение | Получение свойств группы размещения с учетом расположения |
> | Действия | Microsoft. COMPUTE/Проксимитиплацементграупс/запись | Создает новую группу размещения с учетом расположения или обновляет существующую. |
> | Действия | Microsoft.Compute/register/action | Регистрирует подписку в поставщике ресурсов Microsoft.Compute. |
> | Действия | Microsoft.Compute/restorePointCollections/delete | Удаляет коллекцию точек восстановления и содержащиеся в ней точки восстановления. |
> | Действия | Microsoft.Compute/restorePointCollections/read | Возвращает свойства коллекции точек восстановления. |
> | Действия | Microsoft.Compute/restorePointCollections/restorePoints/delete | Удаляет точку восстановления. |
> | Действия | Microsoft.Compute/restorePointCollections/restorePoints/read | Возвращает свойства точки восстановления. |
> | Действия | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Возвращает свойства точки восстановления вместе с универсальными кодами ресурсов (URI) SAS больших двоичных объектов. |
> | Действия | Microsoft.Compute/restorePointCollections/restorePoints/write | Создает точку восстановления. |
> | Действия | Microsoft.Compute/restorePointCollections/write | Создает новую коллекцию точек восстановления или обновляет существующую. |
> | Действия | Microsoft.Compute/sharedVMImages/delete | Удаление образа общей виртуальной машины. |
> | Действия | Microsoft.Compute/sharedVMImages/read | Получение свойств образа общей виртуальной машины. |
> | Действия | Microsoft.Compute/sharedVMImages/versions/delete | Удаление версии образа общей виртуальной машины. |
> | Действия | Microsoft.Compute/sharedVMImages/versions/read | Получение свойств версии образа общей виртуальной машины. |
> | Действия | Microsoft.Compute/sharedVMImages/versions/replicate/action | Репликация версии образа общей виртуальной машины в целевые регионы. |
> | Действия | Microsoft.Compute/sharedVMImages/versions/write | Создание новой версии образа общей виртуальной машины или обновление существующей. |
> | Действия | Microsoft.Compute/sharedVMImages/write | Создание нового образа общей виртуальной машины или обновление существующего. |
> | Действия | Microsoft.Compute/skus/read | Получение списка номеров SKU Microsoft.Compute для вашей подписки. |
> | Действия | Microsoft.Compute/snapshots/beginGetAccess/action | Получение универсального кода ресурса (URI) SAS моментального снимка для доступа к большому двоичному объекту. |
> | Действия | Microsoft.Compute/snapshots/delete | Удаляет моментальный снимок. |
> | Действия | Microsoft.Compute/snapshots/endGetAccess/action | Отмена универсального кода ресурса (URI) SAS моментального снимка. |
> | Действия | Microsoft.Compute/snapshots/read | Возвращает свойства моментального снимка. |
> | Действия | Microsoft.Compute/snapshots/write | Создает новый моментальный снимок или обновляет существующий. |
> | Действия | Microsoft. COMPUTE, отмена регистрации или действие | Отмена регистрации подписки с помощью поставщика ресурсов Microsoft. COMPUTE. |
> | Действия | Microsoft.Compute/virtualMachines/capture/action | Записывает виртуальную машину, копируя ее виртуальных жесткие диски, и создает шаблон, который может использоваться для создания идентичных виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Преобразует диски на основе больших двоичных объектов виртуальной машины в управляемые диски. |
> | Действия | Microsoft.Compute/virtualMachines/deallocate/action | Завершает работу виртуальной машины и освобождает ее вычислительные ресурсы. |
> | Действия | Microsoft.Compute/virtualMachines/delete | Удаляет виртуальную машину. |
> | Действия | Microsoft.Compute/virtualMachines/extensions/delete | Удаляет расширение виртуальной машины. |
> | Действия | Microsoft.Compute/virtualMachines/extensions/read | Возвращает свойства расширения виртуальной машины. |
> | Действия | Microsoft.Compute/virtualMachines/extensions/write | Создает новое расширение виртуальной машины или обновляет существующее. |
> | Действия | Microsoft.Compute/virtualMachines/generalize/action | Задает состояние "Универсальная" для виртуальной машины и подготавливает ее для записи. |
> | Действия | Microsoft.Compute/virtualMachines/instanceView/read | Возвращает состояние выполнения виртуальной машины и ее ресурсов. |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Вход в систему на виртуальной машине в качестве обычного пользователя. |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Вход в систему на виртуальной машине с правами администратора Windows или привилегированного пользователя Linux. |
> | Действия | Microsoft.Compute/virtualMachines/performMaintenance/action | Выполнение операции обслуживания виртуальной машины. |
> | Действия | Microsoft.Compute/virtualMachines/powerOff/action | Завершает работу виртуальной машины. Обратите внимание, что плата за виртуальную машину по-прежнему будет взиматься. |
> | Действия | Microsoft.Compute/virtualMachines/read | Возвращает свойства виртуальной машины. |
> | Действия | Microsoft.Compute/virtualMachines/redeploy/action | Повторно развертывает виртуальную машину. |
> | Действия | Microsoft.Compute/virtualMachines/reimage/action | Пересоздание образа виртуальной машины, которая использует разностный диск. |
> | Действия | Microsoft.Compute/virtualMachines/restart/action | Перезапускает виртуальную машину. |
> | Действия | Microsoft.Compute/virtualMachines/runCommand/action | Выполнение предопределенного скрипта на виртуальной машине. |
> | Действия | Microsoft.Compute/virtualMachines/start/action | Запускает виртуальную машину. |
> | Действия | Microsoft.Compute/virtualMachines/vmSizes/read | Выводит список доступных размеров для обновления виртуальной машины. |
> | Действия | Microsoft.Compute/virtualMachines/write | Создает новую виртуальную машину или обновляет существующую. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Завершение работы вычислительных ресурсов и их освобождение для экземпляров виртуальной машины в масштабируемом наборе виртуальных машин.  |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/delete | Удаление масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/delete/action | Удаление экземпляров масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Удаление расширения масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Получение свойств расширения масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Создание расширения масштабируемого набора виртуальных машин или обновление существующего. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Ручное выполнение зависшего обновления доменов обновления платформы в масштабируемом наборе виртуальных машин Service Fabric. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Получение представления экземпляров масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Выполнение ручного обновления экземпляров до последней модели масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Получение свойства всех сетевых интерфейсов масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Запускает последовательное обновление для перевода всех экземпляров масштабируемых наборов виртуальных машин на последнюю доступную версию ОС образа платформы. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Получение журнала обновлений ОС для масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Выполнение планового обслуживания экземпляров масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Завершение работы экземпляров масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Получение свойств всех общедоступных IP-адресов масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/read | Получение свойств масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Повторное развертывание экземпляров масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Повторное создание образов экземпляров масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Пересоздает образы всех дисков (диска ОС и дисков данных) для экземпляров масштабируемого набора виртуальных машин.  |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/restart/action | Перезапуск экземпляров масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Отмена последовательного обновления масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Получение состояния последнего последовательного обновления масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/scale/action | Проверка того, возможно ли увеличение или уменьшение числа экземпляров масштабируемого набора виртуальных машин до указанного значения. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/skus/read | Вывод списка действительных номеров SKU для существующего масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/start/action | Запуск экземпляров масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Завершает работу вычислительных ресурсов и освобождает их для виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Удаляет конкретную виртуальную машину в масштабируемом наборе виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Извлекает представление экземпляров виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Получение свойства общедоступного IP-адреса, созданного с использованием масштабируемого набора виртуальных машин. Масштабируемый набор виртуальных машин может создать не более одного общедоступного IP-адреса на IP-конфигурацию (частный IP-адрес). |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Получение свойств одной или всех IP-конфигураций сетевого интерфейса, созданного с помощью масштабируемого набора виртуальных машин. IP-конфигурации представляют частные IP-адреса. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Получение свойств одного или всех сетевых интерфейсов виртуальной машины, созданных с помощью масштабируемого набора виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Выполнение планового обслуживания экземпляров виртуальных машин в масштабируемом наборе. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Завершает работу экземпляра виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Извлекает свойства виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Повторное развертывание образа виртуальной машины в масштабируемом наборе. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Повторное создание образа экземпляра виртуальной машины в масштабируемом наборе. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Пересоздает образы всех дисков (диска ОС и дисков данных) для экземпляра виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Перезапускает экземпляр виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Выполняет предварительно определенный сценарий в экземпляре виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Запускает экземпляр виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Обновление свойства виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действия | Microsoft. COMPUTE/virtualMachineScaleSets/Вмсизес/чтение | Список доступных размеров для создания или обновления виртуальной машины в масштабируемом наборе виртуальных машин |
> | Действия | Microsoft.Compute/virtualMachineScaleSets/write | Создание нового масштабируемого набора виртуальных машин или обновление существующего. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Consumption/balances/read | Получение списка сводки использования за период выставления счетов для группы управления. |
> | Действия | Microsoft.Consumption/budgets/delete | Удаление бюджетов подписки или группы управления. |
> | Действия | Microsoft.Consumption/budgets/read | Получение списка бюджетов подписки или группы управления. |
> | Действия | Microsoft.Consumption/budgets/write | Создание или обновление бюджетов подписки или группы управления. |
> | Действия | Microsoft.Consumption/charges/read | Вывод списка расходов. |
> | Действия | Microsoft.Consumption/credits/read | Вывод списка кредитов. |
> | Действия | Microsoft.Consumption/events/read | Вывод списка событий. |
> | Действия | Microsoft.Consumption/forecasts/read | Вывод списка прогнозов. |
> | Действия | Microsoft.Consumption/slots/read | Вывод списка слотов. |
> | Действия | Microsoft.Consumption/marketplaces/read | Вывод сведений об использовании ресурсов Marketplace для области подписок EA и WebDirect. |
> | Действия | Microsoft.Consumption/operationresults/read | Вывод списка результатов операций. |
> | Действия | Microsoft.Consumption/operations/read | Вывод списка всех поддерживаемых операций для поставщика ресурсов Microsoft.Consumption. |
> | Действия | Microsoft.Consumption/operationstatus/read | Вывод списка состояний операций. |
> | Действия | Microsoft.Consumption/pricesheets/read | Вывод расценок для подписки или группы управления. |
> | Действия | Microsoft.Consumption/register/action | Регистрация в поставщике используемых ресурсов. |
> | Действия | Microsoft.Consumption/reservationDetails/read | Вывод сведений об использовании зарезервированных экземпляров по порядку резервирования или группам управления. Данные относятся к одному экземпляру на уровне дня. |
> | Действия | Microsoft.Consumption/reservationRecommendations/read | Вывод одной рекомендации или нескольких общих рекомендаций для зарезервированных экземпляров в подписке. |
> | Действия | Microsoft.Consumption/reservationSummaries/read | Вывод сводки использования зарезервированных экземпляров по порядку резервирования или группам управления. Сводка формируется на ежемесячном или ежедневном уровне. |
> | Действия | Microsoft.Consumption/reservationTransactions/read | Вывод журнала транзакций для зарезервированных экземпляров по группам управления. |
> | Действия | Microsoft.Consumption/tags/read | Вывод списка тегов для EA и подписок. |
> | Действия | Microsoft. потребление/клиенты/чтение | Список клиентов |
> | Действия | Microsoft.Consumption/tenants/register/action | Регистрация действия для области Microsoft.Consumption клиентом. |
> | Действия | Microsoft.Consumption/terms/read | Вывод условий подписки или группы управления. |
> | Действия | Microsoft.Consumption/usageDetails/read | Вывод сведений об использовании для области подписок EA и WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Выполнение в указанном контейнере. |
> | Действия | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Получение журналов для конкретного контейнера. |
> | Действия | Microsoft.ContainerInstance/containerGroups/delete | Удаление конкретной группы контейнеров. |
> | Действия | Microsoft. Контаинеринстанце/Контаинерграупс/Оператионресултс/Read | Получить результат асинхронной операции |
> | Действия | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для группы контейнеров. |
> | Действия | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для группы контейнеров. |
> | Действия | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для группы контейнеров. |
> | Действия | Microsoft.ContainerInstance/containerGroups/read | Получение всех групп контейнеров. |
> | Действия | Microsoft.ContainerInstance/containerGroups/restart/action | Перезапускает конкретную группу контейнеров. |
> | Действия | Microsoft.ContainerInstance/containerGroups/start/action | Запуск конкретной группы контейнеров. |
> | Действия | Microsoft.ContainerInstance/containerGroups/stop/action | Останавливает конкретную группу контейнеров. Вычислительные ресурсы будут отозваны, и выставление счетов прекратится. |
> | Действия | Microsoft.ContainerInstance/containerGroups/write | Создание или обновление конкретной группы контейнеров. |
> | Действия | Microsoft.ContainerInstance/locations/cachedImages/read | Получение кэшированных изображений для подписки в регионе. |
> | Действия | Microsoft.ContainerInstance/locations/capabilities/read | Получение возможностей для региона. |
> | Действия | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Уведомление Microsoft.ContainerInstance о том, что идет удаление виртуальной сети или подсети. |
> | Действия | Microsoft. Контаинеринстанце/Locations/Operations/Read | Перечислите операции для службы экземпляра контейнера Azure. |
> | Действия | Microsoft.ContainerInstance/locations/usages/read | Получение данных об использовании для определенного региона. |
> | Действия | Microsoft. Контаинеринстанце/операции/чтение | Перечислите операции для службы экземпляра контейнера Azure. |
> | Действия | Microsoft.ContainerInstance/register/action | Регистрирует подписку в поставщике ресурсов экземпляра контейнера и включает возможность создания групп контейнеров. |
> | Действия | Microsoft. Контаинеринстанце/сервицеассоЦиатионлинкс/Delete | Удалите ссылку на ассоциацию службы, созданную поставщиком ресурсов экземпляра контейнера Azure в подсети. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ContainerRegistry/checkNameAvailability/read | Проверка возможности использования имени реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Уведомляет Microsoft.ContainerRegistry о том, что удаляется виртуальная сеть или подсеть. |
> | Действия | Microsoft.ContainerRegistry/locations/operationResults/read | Получение результата асинхронной операции. |
> | Действия | Microsoft.ContainerRegistry/operations/read | Вывод списка всех доступных операций API REST реестра контейнеров Azure. |
> | Действия | Microsoft.ContainerRegistry/register/action | Регистрирует подписку для поставщика ресурсов реестра контейнеров и позволяет создавать реестры контейнеров. |
> | Действия | Microsoft. ContainerRegistry/реестры/артефакты/удаление | Удаление артефакта в реестре контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/builds/cancel/action | Отменяет существующую сборку. |
> | Действия | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Возвращает ссылку для скачивания журналов сборки. |
> | Действия | Microsoft.ContainerRegistry/registries/builds/read | Получает свойства указанной сборки или списка сборок для указанного реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/builds/write | Обновляет сборку для реестра контейнеров с указанными параметрами. |
> | Действия | Microsoft.ContainerRegistry/registries/buildTasks/delete | Удаляет задание сборки из реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Перечисляет свойства системы управления версиями для задачи сборки. |
> | Действия | Microsoft.ContainerRegistry/registries/buildTasks/read | Получает свойства указанной задачи сборки или списка задач сборок для указанного реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Удаляет шаг создания из задачи сборки. |
> | Действия | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Перечисляет аргументы сборки для шага сборки, включая аргументы секретов. |
> | Действия | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Получает свойства указанного шага сборки или списка шагов сборок для указанной задачи сборки. |
> | Действия | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Создает или обновляет шаг сборки для задачи сборки с указанными параметрами. |
> | Действия | Microsoft.ContainerRegistry/registries/buildTasks/write | Создает или обновляет задачу сборки для реестра контейнеров с указанными параметрами. |
> | Действия | Microsoft.ContainerRegistry/registries/delete | Удаление реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Удаление фильтра сетки событий из реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Получение свойств указанного фильтра сетки событий или списка всех фильтров сетки событий для указанного реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Создание или обновление фильтра сетки событий для реестра контейнеров с указанными параметрами. |
> | Действия | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Получает расположение для передачи, чтобы пользователь мог передать источник. |
> | Действия | Microsoft.ContainerRegistry/registries/importImage/action | Импорт образа в реестр контейнеров с указанными параметрами. |
> | Действия | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Получение расположения URL-адреса передачи источника для реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/listCredentials/action | Выводит список учетных данных входа для указанного реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/listPolicies/read | Выводит список политик для указанного реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/listUsages/read | Получение сведений об использовании квот для указанного реестра контейнеров. |
> | Действия | Microsoft. ContainerRegistry/реестры/метаданные/чтение | Возвращает метаданные определенного репозитория для реестра контейнеров. |
> | Действия | Microsoft. ContainerRegistry/реестры/метаданные/запись | Обновляет метаданные репозитория для реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/operationStatuses/read | Получение состояния асинхронной операции реестра. |
> | Действия | Microsoft.ContainerRegistry/registries/pull/read | Извлечение или получение образов из реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/push/write | Передача или запись образов в реестр контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/quarantineRead/read | Извлечение или получение помещенных в карантин образов из реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Запись и изменение состояния карантина образов, помещенных в карантин. |
> | Действия | Microsoft.ContainerRegistry/registries/queueBuild/action | Создает новую сборку на основе параметров запроса и добавляет ее в очередь сборки. |
> | Действия | Microsoft.ContainerRegistry/registries/read | Получение свойств указанного реестра контейнеров или вывод списка всех реестров контейнеров в указанной группе ресурсов или подписке. |
> | Действия | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Повторное создание учетных данных входа для указанного реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/replications/delete | Удаление репликации из реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Получение состояния асинхронной операции репликации. |
> | Действия | Microsoft.ContainerRegistry/registries/replications/read | Получение свойств указанной репликации или списка репликаций для указанного реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/replications/write | Создание или обновление репликаций для реестра контейнеров с указанными параметрами. |
> | Действия | Microsoft.ContainerRegistry/registries/runs/cancel/action | Отмена существующего выполнения. |
> | Действия | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Получение URL-адреса SAS журнала для выполнения. |
> | Действия | Microsoft.ContainerRegistry/registries/runs/read | Получение свойств выполнения по реестру контейнера или списку запусков. |
> | Действия | Microsoft.ContainerRegistry/registries/runs/write | Обновление выполнения. |
> | Действия | Microsoft.ContainerRegistry/registries/scheduleRun/action | Планирование запуска по реестру контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/sign/write | Передача или извлечение метаданных доверия к содержимому для реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/tasks/delete | Удаление задачи для реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Список всех сведений о задаче для реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/tasks/read | Получение задачи для реестра контейнеров или списка всех задач. |
> | Действия | Microsoft.ContainerRegistry/registries/tasks/write | Создание или обновление задачи для реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/updatePolicies/write | Обновляет политики для указанного реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/webhooks/delete | Удаление веб-перехватчика из реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Получение конфигурации URI службы и пользовательских заголовков для веб-перехватчика. |
> | Действия | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Получение списка последних событий для указанного веб-перехватчика. |
> | Действия | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Получение состояния асинхронной операции веб-перехватчика. |
> | Действия | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Запуск события проверки связи для отправки веб-перехватчику. |
> | Действия | Microsoft.ContainerRegistry/registries/webhooks/read | Получение свойств указанного веб-перехватчика или списка веб-перехватчиков для указанного реестра контейнеров. |
> | Действия | Microsoft.ContainerRegistry/registries/webhooks/write | Создание или обновление веб-перехватчика для реестра контейнеров с указанными параметрами. |
> | Действия | Microsoft.ContainerRegistry/registries/write | Создание или обновление реестра контейнеров с указанными параметрами. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ContainerService/containerServices/delete | Удаление службы контейнеров. |
> | Действия | Microsoft.ContainerService/containerServices/read | Получение службы контейнеров. |
> | Действия | Microsoft.ContainerService/containerServices/write | Создание новой службы контейнеров или обновление существующей. |
> | Действия | Microsoft.ContainerService/locations/operationresults/read | Получает состояние результата асинхронной операции. |
> | Действия | Microsoft.ContainerService/locations/operations/read | Возвращает состояние асинхронной операции. |
> | Действия | Microsoft.ContainerService/locations/orchestrators/read | Выводит список поддерживаемых операций. |
> | Действия | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Получение профиля доступа управляемого кластера по имени роли с помощью вывода учетных данных. |
> | Действия | Microsoft.ContainerService/managedClusters/accessProfiles/read | Получение профиля доступа управляемого кластера по имени роли. |
> | Действия | Microsoft. ContainerService/Манажедклустерс/Ажентпулс/Delete | Удаляет пул агентов. |
> | Действия | Microsoft. ContainerService/Манажедклустерс/Ажентпулс/Read | Возвращает Пул агентов |
> | Действия | Microsoft. ContainerService/Манажедклустерс/Ажентпулс/Упградепрофилес/Read | Возвращает профиль обновления пула агентов. |
> | Действия | Microsoft. ContainerService/Манажедклустерс/Ажентпулс/запись | Создает новый пул агентов или обновляет существующий. |
> | Действия | Microsoft.ContainerService/managedClusters/delete | Удаление управляемого кластера. |
> | Действия | Microsoft. ContainerService/Манажедклустерс/детекторы/чтение | Получить средство обнаружения управляемого кластера |
> | Действия | Microsoft. ContainerService/Манажедклустерс/Диагностиксстате/Read | Возвращает состояние диагностики кластера. |
> | Действия | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Перечисляет учетные данные clusterAdmin управляемого кластера. |
> | Действия | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Перечисляет учетные данные clusterUser управляемого кластера. |
> | Действия | Microsoft. ContainerService/Манажедклустерс/Приватиндпоинтконнектионсаппровал/Action | Определяет, разрешено ли пользователю утверждать подключение частной конечной точки |
> | Действия | Microsoft.ContainerService/managedClusters/read | Получение управляемого кластера. |
> | Действия | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Сброс профиля AAD управляемого кластера. |
> | Действия | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Сброс профиля субъекта-службы управляемого кластера. |
> | Действия | Microsoft. ContainerService/Манажедклустерс/Упградепрофилес/Read | Возвращает профиль обновления кластера. |
> | Действия | Microsoft.ContainerService/managedClusters/write | Создание нового управляемого кластера или обновление имеющегося. |
> | Действия | Microsoft.ContainerService/openShiftClusters/delete | Удаление кластера с открытым сдвигом |
> | Действия | Microsoft.ContainerService/openShiftClusters/read | Получение кластера с открытым сдвигом |
> | Действия | Microsoft.ContainerService/openShiftClusters/write | Создание нового кластера Open Shift или обновление имеющегося |
> | Действия | Microsoft.ContainerService/openShiftManagedClusters/delete | Удаление управляемого кластера с открытым сдвигом |
> | Действия | Microsoft.ContainerService/openShiftManagedClusters/read | Получение управляемого кластера с открытым сдвигом |
> | Действия | Microsoft.ContainerService/openShiftManagedClusters/write | Создание нового управляемого кластера Open Shift или обновление имеющегося |
> | Действия | Microsoft.ContainerService/operations/read | Выводит список операций, доступных в поставщике ресурсов Microsoft.ContainerService. |
> | Действия | Microsoft.ContainerService/register/action | Регистрирует подписку в поставщике ресурсов Microsoft.ContainerService. |
> | Действия | Microsoft.ContainerService/unregister/action | Отменяет регистрацию подписки в поставщике ресурсов Microsoft.ContainerService. |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ContentModerator/applications/delete | Операция удаления. |
> | Действия | Microsoft.ContentModerator/applications/listSecrets/action | Выводит список секретов. |
> | Действия | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Считывает маркеры единого входа. |
> | Действия | Microsoft.ContentModerator/applications/read | Операция чтения. |
> | Действия | Microsoft.ContentModerator/applications/write | Операции записи. |
> | Действия | Microsoft.ContentModerator/applications/write | Операции записи. |
> | Действия | Microsoft.ContentModerator/listCommunicationPreference/action | Отображает параметры связи. |
> | Действия | Microsoft.ContentModerator/operations/read | Считывает операции. |
> | Действия | Microsoft.ContentModerator/updateCommunicationPreference/action | Обновляет параметры связи. |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Костманажемент/Клаудконнекторс/Delete | Удаляет указанный значение cloudconnector. |
> | Действия | Microsoft. Костманажемент/Клаудконнекторс/Read | Перечислите Клаудконнекторс для пользователя, прошедшего проверку подлинности. |
> | Действия | Microsoft. Костманажемент/Клаудконнекторс/запись | Создание или обновление указанного значение cloudconnector. |
> | Действия | Microsoft.CostManagement/dimensions/read | Вывод списка всех поддерживаемых измерений в области. |
> | Действия | Microsoft.CostManagement/exports/action | Выполнение указанной операции экспорта. |
> | Действия | Microsoft.CostManagement/exports/delete | Удаление указанной операции экспорта. |
> | Действия | Microsoft.CostManagement/exports/read | Вывод списка операций экспорта по области. |
> | Действия | Microsoft. Костманажемент/EXPORTS/Run/Action | Запустите экспорты. |
> | Действия | Microsoft.CostManagement/exports/write | Создание или обновление указанной операции экспорта. |
> | Действия | Microsoft. Костманажемент/Екстерналбиллингаккаунтс/Екстерналсубскриптионс/Read | Перечислите Екстерналсубскриптионс в Екстерналбиллингаккаунт для пользователя, прошедшего проверку подлинности. |
> | Действия | Microsoft. Костманажемент/Екстерналбиллингаккаунтс/Read | Перечислите Екстерналбиллингаккаунтс для пользователя, прошедшего проверку подлинности. |
> | Действия | Microsoft. Костманажемент/Екстерналсубскриптионс/Read | Перечислите Екстерналсубскриптионс для пользователя, прошедшего проверку подлинности. |
> | Действия | Microsoft. Костманажемент/Екстерналсубскриптионс/запись | Обновление связанной группы управления Екстерналсубскриптион |
> | Действия | Microsoft.CostManagement/query/action | Запрос данных об использовании в области. |
> | Действия | Microsoft.CostManagement/query/read | Запрос данных об использовании в области. |
> | Действия | Microsoft. Костманажемент/Register/действие | Регистрация действия для области действия Microsoft. Костманажемент по подписке. |
> | Действия | Microsoft.CostManagement/reports/action | Планирование создания отчетов о данных об использовании в области. |
> | Действия | Microsoft.CostManagement/reports/read | Планирование создания отчетов о данных об использовании в области. |
> | Действия | Microsoft. Костманажемент/клиенты/регистрация/действие | Регистрация действия для области действия Microsoft. Костманажемент клиентом. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Позволяет заказать прием посылки для обратной отправки. |
> | Действия | Microsoft.DataBox/jobs/cancel/action | Отменяет выполняемый заказ. |
> | Действия | Microsoft.DataBox/jobs/delete | Удаляет заказы. |
> | Действия | Microsoft.DataBox/jobs/listCredentials/action | Выводит список незашифрованных учетных данных, связанных с заказом. |
> | Действия | Microsoft.DataBox/jobs/read | Выводит список заказов или получает их. |
> | Действия | Microsoft.DataBox/jobs/write | Создает или обновляет заказы. |
> | Действия | Microsoft.DataBox/locations/availableSkus/action | Этот метод возвращает список доступных номеров SKU. |
> | Действия | Microsoft. Датабокс/Locations/Аваилаблескус/Read | Перечисление или получение доступных номеров SKU |
> | Действия | Microsoft.DataBox/locations/operationResults/read | Вывод списка или получение результатов операции. |
> | Действия | Microsoft. Датабокс/Locations/Регионконфигуратион/Action | Этот метод возвращает конфигурации для региона. |
> | Действия | Microsoft.DataBox/locations/validateAddress/action | Проверяет адрес доставки и предлагает альтернативные адреса, если они доступны. |
> | Действия | Microsoft. Датабокс/Locations/Валидатеинпутс/Action | Этот метод выполняет все типы проверок. |
> | Действия | Microsoft. Датабокс/операции/чтение | Перечисление или получение операций |
> | Действия | Microsoft.DataBox/register/action | Регистрация поставщика Microsoft.Databox. |
> | Действия | Microsoft. Датабокс/Subscriptions/resourceGroups/Мовересаурцес/Action | Этот метод выполняет перемещение ресурса. |
> | Действия | Microsoft. Датабокс/Subscriptions/resourceGroups/Валидатемовересаурцес/Action | Этот метод проверяет, разрешено ли перемещение ресурсов. |
> | Действия | Microsoft. Датабокс/отменить регистрацию или действие | Отмена регистрации поставщика Microsoft. Датабокс |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Выводит список оповещений или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Выводит список оповещений или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Удаляет расписания пропускной способности |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Бандвидссчедулес/Оператионресултс/Read | Выводит или возвращает результат операции. |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Выводит список расписаний пропускной способности или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Выводит список расписаний пропускной способности или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Создает или обновляет расписания пропускной способности |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Удаляет устройства Data Box Edge |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Загружает обновления на устройстве |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Жетекстендединформатион/Action | Извлекает расширенные сведения о ресурсе |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Устанавливает обновления на устройство |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Выводит список заданий или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Выводит список параметров сети устройства или возвращает их |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Nodes/Read | Выводит список узлов или возвращает их. |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Оператионресултс/Read | Выводит или возвращает результат операции. |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Оператионсстатус/Read | Выводит или возвращает состояние операции. |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Orders/Delete | Удаляет заказы. |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Orders/Оператионресултс/Read | Выводит или возвращает результат операции. |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/заказы/чтение | Перечисление или получение заказов |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/заказы/чтение | Перечисление или получение заказов |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/заказы/запись | Создает или обновляет заказы. |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Выводит список устройств Data Box Edge или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Выводит список устройств Data Box Edge или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Выводит список устройств Data Box Edge или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Удаляет роли. |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Roles/Оператионресултс/Read | Выводит или возвращает результат операции. |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Выводит список ролей или возвращает их. |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Выводит список ролей или возвращает их. |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Создает или обновляет роли. |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Проверяет на наличие обновлений |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/securitySettings/Оператионресултс/Read | Выводит или возвращает результат операции. |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Обновляет параметры безопасности |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Удаляет общедоступные ресурсы |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/shares/Оператионресултс/Read | Выводит или возвращает результат операции. |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Выводит список общедоступных ресурсов или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Выводит список общедоступных ресурсов или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Обновление метаданных общего ресурса с помощью данных из облака |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Создает общие папки или обновляет их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Удаляет данные учетной записи хранения |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Сторажеаккаунткредентиалс/Оператионресултс/Read | Выводит или возвращает результат операции. |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Выводит список данных учетной записи хранения или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Выводит список данных учетной записи хранения или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Создает данные учетной записи хранения или обновляет их |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Triggers/Delete | Удаляет триггеры. |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Triggers/Оператионресултс/Read | Выводит или возвращает результат операции. |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/триггеры/чтение | Перечисление или получение триггеров |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/триггеры/чтение | Перечисление или получение триггеров |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Triggers/Write | Создает или обновляет триггеры. |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Выводит сводку обновлений или возвращает ее |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Отправляет сертификат для регистрации устройства |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Удаляет пользователей общих ресурсов |
> | Действия | Microsoft. Датабокседже/Датабокседжедевицес/Users/Оператионресултс/Read | Выводит или возвращает результат операции. |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Выводит список пользователей общих ресурсов или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Выводит список пользователей общих ресурсов или возвращает их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Создает пользователей общих ресурсов или обновляет их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Создает устройства Data Box Edge или обновляет их |
> | Действия | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Создает устройства Data Box Edge или обновляет их |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. кирпичы/расположения/ЖетнетворкполиЦиес/действие | Получение политик намерения сети для подсети в зависимости от расположения, используемого NRP |
> | Действия | Microsoft.Databricks/register/action | Регистрация в Databricks. |
> | Действия | Microsoft.Databricks/workspaces/delete | Удаление рабочей области Databricks. |
> | Действия | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Задание доступных параметров диагностики для рабочей области Databricks |
> | Действия | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Добавляет или изменяет параметры диагностики. |
> | Действия | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Получение доступных определений журнала для рабочего пространства Databricks |
> | Действия | Microsoft.Databricks/workspaces/read | Извлечение списка рабочих областей Databricks. |
> | Действия | Microsoft.Databricks/workspaces/write | Создание рабочей области Databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.DataCatalog/catalogs/delete | Удаление ресурса каталогов для поставщика ресурсов каталога данных. |
> | Действия | Microsoft.DataCatalog/catalogs/read | Считывает ресурс каталогов для поставщика ресурсов каталога данных. |
> | Действия | Microsoft.DataCatalog/catalogs/write | Ресурс записи каталогов для поставщика ресурсов каталога данных. |
> | Действия | Microsoft. catalog/Чеккнамеаваилабилити/чтение | Проверьте доступность имени каталога для поставщика ресурсов каталога данных. |
> | Действия | Microsoft. catalog/Каталогизация/удаление | Удаление ресурса каталога данных для поставщика ресурсов каталога. |
> | Действия | Microsoft. catalog/Каталогизация/чтение | Чтение ресурса каталога данных для поставщика ресурсов каталога. |
> | Действия | Microsoft. catalog/Каталогизация/запись | Запись ресурса каталога данных для поставщика ресурсов каталога. |
> | Действия | Microsoft.DataCatalog/operations/read | Считывает все доступные операции в поставщике ресурсов каталога данных. |
> | Действия | Microsoft.DataCatalog/register/action | Регистрация подписки для поставщика ресурсов каталога данных |
> | Действия | Microsoft.DataCatalog/unregister/action | Отмена регистрации подписки для поставщика ресурсов каталога данных |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Проверяет, доступно ли имя фабрики данных для использования. |
> | Действия | Microsoft.DataFactory/datafactories/activitywindows/read | Считывает окна действий в фабрике данных с указанными параметрами. |
> | Действия | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Считывает окна действий для действия конвейера с указанными параметрами. |
> | Действия | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Считывает окна действий для конвейера с указанными параметрами. |
> | Действия | Microsoft.DataFactory/datafactories/datapipelines/delete | Удаляет конвейер. |
> | Действия | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Приостанавливает конвейер. |
> | Действия | Microsoft.DataFactory/datafactories/datapipelines/read | Считывает конвейер. |
> | Действия | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Возобновляет работу конвейера. |
> | Действия | Microsoft.DataFactory/datafactories/datapipelines/update/action | Обновляет конвейер. |
> | Действия | Microsoft.DataFactory/datafactories/datapipelines/write | Создает или обновляет конвейер. |
> | Действия | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Считывает окна действий для набора данных с указанными параметрами. |
> | Действия | Microsoft.DataFactory/datafactories/datasets/delete | Удаляет набор данных. |
> | Действия | Microsoft.DataFactory/datafactories/datasets/read | Считывает набор данных. |
> | Действия | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Считывает выполнение среза данных для заданного набора данных с указанным временем запуска. |
> | Действия | Microsoft.DataFactory/datafactories/datasets/slices/read | Возвращает срезы данных за указанный период. |
> | Действия | Microsoft.DataFactory/datafactories/datasets/slices/write | Обновляет состояние среза данных. |
> | Действия | Microsoft.DataFactory/datafactories/datasets/write | Создает или обновляет набор данных. |
> | Действия | Microsoft.DataFactory/datafactories/delete | Удаляет фабрику данных. |
> | Действия | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Считывает сведения о подключении для шлюза. |
> | Действия | Microsoft.DataFactory/datafactories/gateways/delete | Удаляет шлюз. |
> | Действия | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Выводит список ключей аутентификации для шлюза. |
> | Действия | Microsoft.DataFactory/datafactories/gateways/read | Считывает шлюз. |
> | Действия | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Повторно создает ключи аутентификации для шлюза. |
> | Действия | Microsoft.DataFactory/datafactories/gateways/write | Создает или обновляет шлюз. |
> | Действия | Microsoft.DataFactory/datafactories/linkedServices/delete | Удаляет связанную службу. |
> | Действия | Microsoft.DataFactory/datafactories/linkedServices/read | Считывает связанную службу. |
> | Действия | Microsoft.DataFactory/datafactories/linkedServices/write | Создает или обновляет связанную службу. |
> | Действия | Microsoft.DataFactory/datafactories/read | Считывает фабрику данных. |
> | Действия | Microsoft.DataFactory/datafactories/runs/loginfo/read | Считывает универсальный код ресурса SAS контейнера больших двоичных объектов, содержащего журналы. |
> | Действия | Microsoft.DataFactory/datafactories/tables/delete | Удаляет набор данных. |
> | Действия | Microsoft.DataFactory/datafactories/tables/read | Считывает набор данных. |
> | Действия | Microsoft.DataFactory/datafactories/tables/write | Создает или обновляет набор данных. |
> | Действия | Microsoft.DataFactory/datafactories/write | Создает или обновляет фабрику данных. |
> | Действия | Microsoft.DataFactory/factories/cancelpipelinerun/action | Отменяет запуск конвейера, указанный идентификатором запуска. |
> | Действия | Microsoft. канцелсандбокспипелинерун/фабрики//действие | Отменяет запуск отладки для конвейера. |
> | Действия | Microsoft. креатедатафловдебугсессион/фабрики//действие | Создает сеанс отладки потока данных. |
> | Действия | Microsoft. DataFlow/фабрики/поток данных/удаление | Удаляет поток данных. |
> | Действия | Microsoft. DataFlow/фабрики/поток данных/чтение | Считывает поток данных. |
> | Действия | Microsoft. DataFlow/фабрики/потоки данных/запись | Создание или обновление потока данных |
> | Действия | Microsoft.DataFactory/factories/datasets/delete | Удаляет набор данных. |
> | Действия | Microsoft.DataFactory/factories/datasets/read | Считывает набор данных. |
> | Действия | Microsoft.DataFactory/factories/datasets/write | Создает или обновляет набор данных. |
> | Действия | Microsoft. дебугпипелинерунс/фабрики//отмена/действие | Отменяет запуск отладки для конвейера. |
> | Действия | Microsoft.DataFactory/factories/delete | Удаляет фабрику данных. |
> | Действия | Microsoft. делетедатафловдебугсессион/фабрики//действие | Удаляет сеанс отладки потока данных. |
> | Действия | Microsoft. жетдатапланеакцесс/фабрики//действие | Получает доступ к службе DataPlane ADF. |
> | Действия | Microsoft. жетдатапланеакцесс/фабрики//чтение | Считывает данные о доступе к службе DataPlane ADF. |
> | Действия | Microsoft. жетфеатуревалуе/фабрики//действие | Получает значение функции управления раскрытием для конкретного расположения. |
> | Действия | Microsoft. жетфеатуревалуе/фабрики//чтение | Считывает значение функции управления раскрытием для конкретного расположения. |
> | Действия | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Получает маркер доступа GitHub. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/delete | Удаляет среду выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Считывает сведения о подключении к среде выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Получает метаданные среды выполнения интеграции SSIS для указанной среды Integration Runtime. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Считывает состояние среды выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Создает ссылку на связанную Integration Runtime для указанной общей среды Integration Runtime. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Выводит список ключей аутентификации для среды выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Возвращает данные мониторинга для среды выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Удаляет узел для указанной среды выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Возвращает IP-адрес для указанного узла среды выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Считывает узел для указанной среды Integration Runtime. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Обновляет узел локальной среды выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/read | Считывает среду выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Обновляет метаданные среды выполнения интеграции SSIS для указанной среды Integration Runtime. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Повторно создает ключи проверки подлинности для указанной среды выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Удаляет связанные ссылки Integration Runtime из указанной среды Integration Runtime. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/start/action | Запускает среду выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Останавливает среду выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Синхронизирует учетные данные для указанной среды выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Обновляет указанную среду выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/integrationruntimes/write | Создает или обновляет среду выполнения интеграции. |
> | Действия | Microsoft.DataFactory/factories/linkedServices/delete | Удаляет связанную службу. |
> | Действия | Microsoft.DataFactory/factories/linkedServices/read | Считывает связанную службу. |
> | Действия | Microsoft.DataFactory/factories/linkedServices/write | Создает или обновляет связанную службу. |
> | Действия | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Считывает выполнения действий для указанного идентификатора запуска конвейера. |
> | Действия | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Отменяет запуск конвейера, указанный идентификатором запуска. |
> | Действия | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Запрашивает выполнения действий для указанного идентификатора запуска конвейера. |
> | Действия | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Считывает результат выполнения действий запросов для указанного идентификатора запуска конвейера. |
> | Действия | Microsoft.DataFactory/factories/pipelineruns/read | Считывает выполнения конвейера. |
> | Действия | Microsoft.DataFactory/factories/pipelines/createrun/action | Создает выполнение для конвейера. |
> | Действия | Microsoft.DataFactory/factories/pipelines/delete | Удаляет конвейер. |
> | Действия | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Возвращает ход выполнения действий. |
> | Действия | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Считывает выполнение конвейера. |
> | Действия | Microsoft.DataFactory/factories/pipelines/read | Считывает конвейер. |
> | Действия | Microsoft. в-принципе, фабрики, конвейеры, песочница/действие | Создает среду выполнения отладки для конвейера. |
> | Действия | Microsoft., фабрики, конвейеры, "песочница", "Создание/действие" | Создает среду выполнения отладки для конвейера. |
> | Действия | Microsoft., фабрики, конвейеры, "песочница"/"Запуск/действие" | Создает запуск отладки для конвейера. |
> | Действия | Microsoft.DataFactory/factories/pipelines/write | Создает или обновляет конвейер. |
> | Действия | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Запрашивает данные о выполнении конвейера отладки. |
> | Действия | Microsoft.DataFactory/factories/querypipelineruns/action | Запрашивает выполнения конвейера. |
> | Действия | Microsoft.DataFactory/factories/querypipelineruns/read | Считывает результат выполнения конвейера запросов. |
> | Действия | Microsoft.DataFactory/factories/querytriggerruns/action | Запрашивает выполнения триггеров. |
> | Действия | Microsoft.DataFactory/factories/querytriggerruns/read | Считывает результат запусков триггера. |
> | Действия | Microsoft.DataFactory/factories/read | Считывает фабрику данных. |
> | Действия | Microsoft. сандбокспипелинерунс/фабрики//Сандбоксактивитирунс/чтение | Возвращает сведения о запуске отладки для действия. |
> | Действия | Microsoft. стартдатафловдебугсессион/фабрики//действие | Запускает сеанс отладки потока данных. |
> | Действия | Microsoft. субмитдатафловфорпревиев/фабрики//действие | Отправьте поток данных, чтобы получить предварительный просмотр данных с помощью сеанса отладки. |
> | Действия | Microsoft.DataFactory/factories/triggerruns/read | Считывает выполнения триггеров. |
> | Действия | Microsoft.DataFactory/factories/triggers/delete | Удаляет триггер. |
> | Действия | Microsoft.DataFactory/factories/triggers/read | Считывает триггер. |
> | Действия | Microsoft.DataFactory/factories/triggers/start/action | Запускает триггер. |
> | Действия | Microsoft.DataFactory/factories/triggers/stop/action | Останавливает триггер. |
> | Действия | Microsoft.DataFactory/factories/triggers/triggerruns/read | Считывает выполнения триггеров. |
> | Действия | Microsoft.DataFactory/factories/triggers/write | Создает или обновляет триггер. |
> | Действия | Microsoft.DataFactory/factories/write | Создает или обновляет фабрику данных. |
> | Действия | Microsoft.DataFactory/locations/configureFactoryRepo/action | Настройка репозитория для производства. |
> | Действия | Microsoft.DataFactory/locations/getFeatureValue/action | Получает значение функции управления раскрытием для конкретного расположения. |
> | Действия | Microsoft.DataFactory/locations/getFeatureValue/read | Считывает значение функции управления раскрытием для конкретного расположения. |
> | Действия | Microsoft.DataFactory/operations/read | Считывает все операции в поставщике Фабрики данных Майкрософт. |
> | Действия | Microsoft.DataFactory/register/action | Регистрирует подписку для поставщика ресурсов фабрики данных. |
> | Действия | Microsoft.DataFactory/unregister/action | Отменяет регистрацию подписки для поставщика ресурсов фабрики данных. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Удаление политики вычислений. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Получение сведений о политике вычислений. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Создание или обновление политики вычислений. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Отмена связи учетной записи Data Lake Store с учетной записью Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Получение данных о связанной учетной записи Data Lake Store для учетной записи Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Создание или обновление связанной учетной записи Data Lake Store для учетной записи Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/delete | Удаляет учетную запись Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Удаляет правило брандмауэра. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Возвращает сведения о правиле брандмауэра. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Создает или обновляет правило брандмауэра. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Получение результата операции учетной записи Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/read | Получение сведений о существующей учетной записи Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Получение списка токенов SAS для контейнеров хранилища учетной записи хранения, связанной с учетной записью Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Получение контейнеров учетной записи хранения, связанной с учетной записью Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Удаление связи учетной записи хранения с учетной записью Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Получение сведений об учетной записи хранения, связанной с учетной записью Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Создание или обновление связанной учетной записи хранения для учетной записи Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Предоставляет разрешения на отмену заданий, отправленных другими пользователями. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Передача значения SystemMaxAnalyticsUnits для учетных записей DataLakeAnalytics. |
> | Действия | Microsoft. Data Lake Analytics/Accounts/Виртуалнетворкрулес/Delete | Удаление правила виртуальной сети. |
> | Действия | Microsoft. Data Lake Analytics/Accounts/Виртуалнетворкрулес/Read | Получение сведений о правиле виртуальной сети. |
> | Действия | Microsoft. Data Lake Analytics/Accounts/Виртуалнетворкрулес/Write | Создайте или обновите правило виртуальной сети. |
> | Действия | Microsoft.DataLakeAnalytics/accounts/write | Создание или обновление учетной записи Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/locations/capability/read | Получение сведений о возможностях подписки в отношении использования Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Проверка доступности имени учетной записи Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/locations/operationResults/read | Получение результата операции учетной записи Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/locations/usages/read | Получение сведений об использовании квот подписки, относящихся к Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/operations/read | Получение доступных операций Data Lake Analytics. |
> | Действия | Microsoft.DataLakeAnalytics/register/action | Регистрация подписки для Data Lake Analytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.DataLakeStore/accounts/delete | Удаление учетной записи Data Lake Store. |
> | Действия | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Включение хранилища ключей для учетной записи Data Lake Store. |
> | Действия | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Удаление фильтра EventGrid. |
> | Действия | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Получение фильтра EventGrid. |
> | Действия | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Создание или обновление фильтра EventGrid. |
> | Действия | Microsoft.DataLakeStore/accounts/firewallRules/delete | Удаляет правило брандмауэра. |
> | Действия | Microsoft.DataLakeStore/accounts/firewallRules/read | Возвращает сведения о правиле брандмауэра. |
> | Действия | Microsoft.DataLakeStore/accounts/firewallRules/write | Создает или обновляет правило брандмауэра. |
> | Действия | Microsoft.DataLakeStore/accounts/operationResults/read | Получение результата операции учетной записи Data Lake Store. |
> | Действия | Microsoft.DataLakeStore/accounts/read | Получение сведений о существующей учетной записи Data Lake Store. |
> | Действия | Microsoft. Data Lake Store/Accounts/shares/Delete | Удаление общей папки. |
> | Действия | Microsoft. Data Lake Store/Accounts/shares/Read | Получение сведений о общей папке. |
> | Действия | Microsoft. Data Lake Store/Accounts/shares/Write | Создайте или обновите общую папку. |
> | Действия | Microsoft.DataLakeStore/accounts/Superuser/action; | Предоставление прав суперпользователя в хранилище Data Lake Store при предоставлении разрешения Microsoft.Authorization/roleAssignments/write. |
> | Действия | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Удаляет доверенный поставщик удостоверений. |
> | Действия | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Возвращает сведения о доверенном поставщике удостоверений. |
> | Действия | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Создает или обновляет доверенный поставщик удостоверений. |
> | Действия | Microsoft. Data Lake Store/Accounts/Виртуалнетворкрулес/Delete | Удаление правила виртуальной сети. |
> | Действия | Microsoft. Data Lake Store/Accounts/Виртуалнетворкрулес/Read | Получение сведений о правиле виртуальной сети. |
> | Действия | Microsoft. Data Lake Store/Accounts/Виртуалнетворкрулес/Write | Создайте или обновите правило виртуальной сети. |
> | Действия | Microsoft.DataLakeStore/accounts/write | Создание или обновление учетной записи Data Lake Store. |
> | Действия | Microsoft.DataLakeStore/locations/capability/read | Получение сведений о возможностях подписки в отношении использования Data Lake Store. |
> | Действия | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Проверка доступности имени учетной записи Data Lake Store. |
> | Действия | Microsoft.DataLakeStore/locations/operationResults/read | Получение результата операции учетной записи Data Lake Store. |
> | Действия | Microsoft.DataLakeStore/locations/usages/read | Получение сведений об использовании квот подписки, относящихся к Data Lake Store. |
> | Действия | Microsoft.DataLakeStore/operations/read | Получение доступных операций Data Lake Store. |
> | Действия | Microsoft.DataLakeStore/register/action | Регистрация подписки для Data Lake Store. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.DataMigration/locations/operationResults/read | Получение состояния длительной операции, связанной с ответом 202 (принято) |
> | Действия | Microsoft.DataMigration/locations/operationStatuses/read | Получение состояния длительной операции, связанной с ответом 202 (принято) |
> | Действия | Microsoft.DataMigration/register/action | Регистрация подписки в поставщике Azure Database Migration Service. |
> | Действия | Microsoft. Migration/Services/Аддворкер/действие | Добавляет рабочую роль DMS в рабочие роли службы доступных. |
> | Действия | Microsoft.DataMigration/services/checkStatus/action | Проверка развертывания и выполнения службы. |
> | Действия | Microsoft. Migration/Services/Конфигуреворкер/действие | Настраивает рабочую роль DMS для рабочих ролей доступных службы. |
> | Действия | Microsoft.DataMigration/services/delete | Удаление ресурса и всех его дочерних элементов. |
> | Действия | Microsoft.DataMigration/services/projects/accessArtifacts/action | Создание URL-адреса для операций GET и PUT с артефактами проекта. |
> | Действия | Microsoft.DataMigration/services/projects/delete | Удаление ресурса и всех его дочерних элементов. |
> | Действия | Microsoft.DataMigration/services/projects/files/delete | Удаление ресурса и всех его дочерних элементов. |
> | Действия | Microsoft.DataMigration/services/projects/files/read | Считывание сведений о ресурсах. |
> | Действия | Microsoft.DataMigration/services/projects/files/read/action | Получает URL-адрес для чтения содержимого файла. |
> | Действия | Microsoft.DataMigration/services/projects/files/readWrite/action | Получает URL-адрес для чтения или записи содержимого файла. |
> | Действия | Microsoft.DataMigration/services/projects/files/write | Создание или обновление ресурсов и их свойств. |
> | Действия | Microsoft.DataMigration/services/projects/read | Считывание сведений о ресурсах. |
> | Действия | Microsoft.DataMigration/services/projects/tasks/cancel/action | Отмена задачи (если она выполняется). |
> | Действия | Microsoft.DataMigration/services/projects/tasks/delete | Удаление ресурса и всех его дочерних элементов. |
> | Действия | Microsoft.DataMigration/services/projects/tasks/read | Считывание сведений о ресурсах. |
> | Действия | Microsoft.DataMigration/services/projects/tasks/write | Запуск задач Azure Database Migration Service. |
> | Действия | Microsoft.DataMigration/services/projects/write | Запуск задач Azure Database Migration Service. |
> | Действия | Microsoft.DataMigration/services/read | Считывание сведений о ресурсах. |
> | Действия | Microsoft. Migration/Services/Ремовеворкер/действие | Удаляет рабочую роль DMS для рабочих ролей доступных службы. |
> | Действия | Microsoft. Migration/Services/Сервицетаскс/отмена/действие | Отмена задачи (если она выполняется). |
> | Действия | Microsoft. migrations/Services/Сервицетаскс/Delete | Удаление ресурса и всех его дочерних элементов. |
> | Действия | Microsoft. Migration, Services/Сервицетаскс/Read | Считывание сведений о ресурсах. |
> | Действия | Microsoft. Migration, Services/Сервицетаскс/Write | Запуск задач Azure Database Migration Service. |
> | Действия | Microsoft.DataMigration/services/slots/delete | Удаление ресурса и всех его дочерних элементов. |
> | Действия | Microsoft.DataMigration/services/slots/read | Считывание сведений о ресурсах. |
> | Действия | Microsoft.DataMigration/services/slots/write | Создание или обновление ресурсов и их свойств. |
> | Действия | Microsoft.DataMigration/services/start/action | Запуск службы DMS для возобновления обработки миграции. |
> | Действия | Microsoft.DataMigration/services/stop/action | Остановка службы DMS для сокращения затрат на нее. |
> | Действия | Microsoft. Migration/Services/Упдатеажентконфиг/действие | Обновляет конфигурацию агента DMS с указанными значениями. |
> | Действия | Microsoft.DataMigration/services/write | Создание или обновление ресурсов и их свойств. |
> | Действия | Microsoft.DataMigration/skus/read | Получение списка номеров SKU, поддерживаемых ресурсами DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Дбформариадб/Чеккнамеаваилабилити/действие | Проверка того, доступно ли данное имя сервера для подготовки по всему миру для данной подписки. |
> | Действия | Microsoft. Дбформариадб/Locations/Азуреасинкоператион/Read | Возврат результатов операции сервера MariaDB |
> | Действия | Microsoft. Дбформариадб/Locations/Оператионресултс/Read | Вернуть результаты операции сервера MariaDB на основе ResourceGroup |
> | Действия | Microsoft. Дбформариадб/Locations/Оператионресултс/Read | Возврат результатов операции сервера MariaDB |
> | Действия | Microsoft.DBforMariaDB/locations/performanceTiers/read | Получение списка доступных уровней производительности. |
> | Действия | Microsoft. Дбформариадб/Locations/СекуритялертполиЦиесазуреасинкоператион/Read | Возвращает список результатов операции обнаружения угроз сервера. |
> | Действия | Microsoft. Дбформариадб/Locations/СекуритялертполиЦиесоператионресултс/Read | Возвращает список результатов операции обнаружения угроз сервера. |
> | Действия | Microsoft. Дбформариадб/операции/чтение | Возвращает список операций MariaDB. |
> | Действия | Microsoft.DBforMariaDB/performanceTiers/read | Получение списка доступных уровней производительности. |
> | Действия | Microsoft. Дбформариадб/Register/действие | Регистрация поставщика ресурсов MariaDB |
> | Действия | Microsoft. Дбформариадб/Servers/Administrators/Delete | Удаляет существующего администратора сервера MariaDB. |
> | Действия | Microsoft. Дбформариадб/Servers/Administrators/Read | Возвращает список администраторов сервера MariaDB. |
> | Действия | Microsoft. Дбформариадб/Servers/Administrators/Write | Создает или обновляет администратора сервера MariaDB с указанными параметрами. |
> | Действия | Microsoft. Дбформариадб/серверы/Advisor/Креатерекоммендедактионсессион/действие | Создание нового сеанса действия рекомендации |
> | Действия | Microsoft. Дбформариадб/серверы/помощники/чтение | Возвращает список консультантов. |
> | Действия | Microsoft. Дбформариадб/серверы/помощники/чтение | Возврат помощника |
> | Действия | Microsoft. Дбформариадб/серверы/Advisor/Рекоммендедактионс/чтение | Возвращает список рекомендуемых действий |
> | Действия | Microsoft. Дбформариадб/серверы/Advisor/Рекоммендедактионс/чтение | Возвращает список рекомендуемых действий |
> | Действия | Microsoft. Дбформариадб/серверы/Advisor/Рекоммендедактионс/чтение | Возврат рекомендуемого действия |
> | Действия | Microsoft.DBforMariaDB/servers/configurations/read | Возвращает список конфигураций для сервера или получает свойства для указанной конфигурации. |
> | Действия | Microsoft.DBforMariaDB/servers/configurations/write | Обновляет значение для указанной конфигурации. |
> | Действия | Microsoft. Дбформариадб/Servers/databases/Delete | Удаляет существующую базу данных MariaDB. |
> | Действия | Microsoft. Дбформариадб/Servers/databases/Read | Возвращает список баз данных MariaDB или свойства для указанной базы данных. |
> | Действия | Microsoft. Дбформариадб/серверы/базы данных/запись | Создает базу данных MariaDB с указанными параметрами или обновляет свойства для указанной базы данных. |
> | Действия | Microsoft.DBforMariaDB/servers/delete | Удаление существующего сервера. |
> | Действия | Microsoft.DBforMariaDB/servers/firewallRules/delete | Удаление существующего правила брандмауэра. |
> | Действия | Microsoft.DBforMariaDB/servers/firewallRules/read | Возвращение списка правил брандмауэра для сервера или получение свойств для указанного правила брандмауэра. |
> | Действия | Microsoft.DBforMariaDB/servers/firewallRules/write | Создание правила брандмауэра с указанными параметрами или обновление существующего правила. |
> | Действия | Microsoft. Дбформариадб/серверы/файлы журнала/чтение | Возврат списка файлов журнала MariaDB. |
> | Действия | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса. |
> | Действия | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Получает доступные журналы для серверов MariaDB. |
> | Действия | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Возвращает типы метрик, доступных для баз данных. |
> | Действия | Microsoft. Дбформариадб/Servers/Куеритекстс/Action | Возврат текста для списка запросов |
> | Действия | Microsoft. Дбформариадб/Servers/Куеритекстс/Action | Возвращает текст запроса. |
> | Действия | Microsoft.DBforMariaDB/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Действия | Microsoft.DBforMariaDB/servers/recoverableServers/read | Возвращает восстанавливаемые данные сервера MariaDB |
> | Действия | Microsoft. Дбформариадб/серверы/реплики/чтение | Получение реплик чтения MariaDB сервера |
> | Действия | Microsoft. Дбформариадб/Servers/Restart/Action | Перезапускает указанный сервер. |
> | Действия | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Извлекает сведения о политике обнаружения угроз, настроенной на данном сервере. |
> | Действия | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Изменение политики обнаружения угроз для заданного сервера. |
> | Действия | Microsoft. Дбформариадб/Servers/Топкуеристатистикс/Read | Возвращает список статистики запросов для наиболее ресурсоемких запросов. |
> | Действия | Microsoft. Дбформариадб/Servers/Топкуеристатистикс/Read | Возврат статистики запроса |
> | Действия | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Обновляет конфигурации для указанного сервера. |
> | Действия | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Удаление существующего правила виртуальной сети. |
> | Действия | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Возвращение списка правил виртуальной сети или свойств указанного правила виртуальной сети. |
> | Действия | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Создание правила виртуальной сети с указанными параметрами либо обновление свойств или тегов указанного правила виртуальной сети. |
> | Действия | Microsoft. Дбформариадб/Servers/Ваитстатистикс/Read | Возвращает статистику ожидания для экземпляра |
> | Действия | Microsoft. Дбформариадб/Servers/Ваитстатистикс/Read | Возврат статистики ожидания |
> | Действия | Microsoft.DBforMariaDB/servers/write | Создание сервера с указанными параметрами либо обновление свойств или тегов указанного сервера. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Дбформискл/Чеккнамеаваилабилити/действие | Проверка того, доступно ли данное имя сервера для подготовки по всему миру для данной подписки. |
> | Действия | Microsoft. Дбформискл/Locations/Азуреасинкоператион/Read | Возврат результатов операции MySQL Server |
> | Действия | Microsoft. Дбформискл/Locations/Оператионресултс/Read | Вернуть результаты операции MySQL на основе ResourceGroup |
> | Действия | Microsoft. Дбформискл/Locations/Оператионресултс/Read | Возврат результатов операции MySQL Server |
> | Действия | Microsoft.DBforMySQL/locations/performanceTiers/read | Получение списка доступных уровней производительности. |
> | Действия | Microsoft. Дбформискл/Locations/СекуритялертполиЦиесазуреасинкоператион/Read | Возвращает список результатов операции обнаружения угроз сервера. |
> | Действия | Microsoft. Дбформискл/Locations/СекуритялертполиЦиесоператионресултс/Read | Возвращает список результатов операции обнаружения угроз сервера. |
> | Действия | Microsoft. Дбформискл/операции/чтение | Возврат списка операций MySQL. |
> | Действия | Microsoft.DBforMySQL/performanceTiers/read | Получение списка доступных уровней производительности. |
> | Действия | Microsoft. Дбформискл/Register/действие | Регистрация поставщика ресурсов MySQL |
> | Действия | Microsoft. Дбформискл/Servers/Administrators/Delete | Удаляет существующего администратора сервера MySQL. |
> | Действия | Microsoft. Дбформискл/Servers/Administrators/Read | Возвращает список администраторов сервера MySQL. |
> | Действия | Microsoft. Дбформискл/Servers/Administrators/Write | Создает или обновляет администратора MySQL Server с указанными параметрами. |
> | Действия | Microsoft. Дбформискл/серверы/Advisor/Креатерекоммендедактионсессион/действие | Создание нового сеанса действия рекомендации |
> | Действия | Microsoft. Дбформискл/серверы/помощники/чтение | Возвращает список консультантов. |
> | Действия | Microsoft. Дбформискл/серверы/помощники/чтение | Возврат помощника |
> | Действия | Microsoft. Дбформискл/серверы/Advisor/Рекоммендедактионс/чтение | Возвращает список рекомендуемых действий |
> | Действия | Microsoft. Дбформискл/серверы/Advisor/Рекоммендедактионс/чтение | Возвращает список рекомендуемых действий |
> | Действия | Microsoft. Дбформискл/серверы/Advisor/Рекоммендедактионс/чтение | Возврат рекомендуемого действия |
> | Действия | Microsoft.DBforMySQL/servers/configurations/read | Возвращает список конфигураций для сервера или получает свойства для указанной конфигурации. |
> | Действия | Microsoft.DBforMySQL/servers/configurations/write | Обновляет значение для указанной конфигурации. |
> | Действия | Microsoft. Дбформискл/Servers/databases/Delete | Удаляет существующую базу данных MySQL. |
> | Действия | Microsoft. Дбформискл/Servers/databases/Read | Возврат списка баз данных MySQL или получение свойств для указанной базы данных. |
> | Действия | Microsoft. Дбформискл/серверы/базы данных/запись | Создает базу данных MySQL с указанными параметрами или обновляет свойства указанной базы данных. |
> | Действия | Microsoft.DBforMySQL/servers/delete | Удаление существующего сервера. |
> | Действия | Microsoft.DBforMySQL/servers/firewallRules/delete | Удаление существующего правила брандмауэра. |
> | Действия | Microsoft.DBforMySQL/servers/firewallRules/read | Возвращение списка правил брандмауэра для сервера или получение свойств для указанного правила брандмауэра. |
> | Действия | Microsoft.DBforMySQL/servers/firewallRules/write | Создание правила брандмауэра с указанными параметрами или обновление существующего правила. |
> | Действия | Microsoft. Дбформискл/серверы/файлы журнала/чтение | Возврат списка файлов журнала PostgreSQL. |
> | Действия | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса. |
> | Действия | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Получает доступные журналы для серверов MySQL |
> | Действия | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Возвращает типы метрик, доступных для баз данных. |
> | Действия | Microsoft. Дбформискл/Servers/Куеритекстс/Action | Возврат текста для списка запросов |
> | Действия | Microsoft. Дбформискл/Servers/Куеритекстс/Action | Возвращает текст запроса. |
> | Действия | Microsoft.DBforMySQL/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Действия | Microsoft.DBforMySQL/servers/recoverableServers/read | Возвращение восстанавливаемых данных MySQL Server. |
> | Действия | Microsoft. Дбформискл/серверы/реплики/чтение | Получение реплик чтения для сервера MySQL |
> | Действия | Microsoft. Дбформискл/Servers/Restart/Action | Перезапускает указанный сервер. |
> | Действия | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Извлекает сведения о политике обнаружения угроз, настроенной на данном сервере. |
> | Действия | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Изменение политики обнаружения угроз для заданного сервера. |
> | Действия | Microsoft. Дбформискл/Servers/Топкуеристатистикс/Read | Возвращает список статистики запросов для наиболее ресурсоемких запросов. |
> | Действия | Microsoft. Дбформискл/Servers/Топкуеристатистикс/Read | Возврат статистики запроса |
> | Действия | Microsoft.DBforMySQL/servers/updateConfigurations/action | Обновляет конфигурации для указанного сервера. |
> | Действия | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Удаление существующего правила виртуальной сети. |
> | Действия | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Возвращение списка правил виртуальной сети или свойств указанного правила виртуальной сети. |
> | Действия | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Создание правила виртуальной сети с указанными параметрами либо обновление свойств или тегов указанного правила виртуальной сети. |
> | Действия | Microsoft. Дбформискл/Servers/Ваитстатистикс/Read | Возвращает статистику ожидания для экземпляра |
> | Действия | Microsoft. Дбформискл/Servers/Ваитстатистикс/Read | Возврат статистики ожидания |
> | Действия | Microsoft.DBforMySQL/servers/write | Создание сервера с указанными параметрами либо обновление свойств или тегов указанного сервера. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Дбфорпостгрескл/Чеккнамеаваилабилити/действие | Проверка того, доступно ли данное имя сервера для подготовки по всему миру для данной подписки. |
> | Действия | Microsoft. Дбфорпостгрескл/Locations/Азуреасинкоператион/Read | Возврат результатов операции сервера PostgreSQL |
> | Действия | Microsoft. Дбфорпостгрескл/Locations/Оператионресултс/Read | Вернуть результаты операции сервера PostgreSQL на основе ResourceGroup |
> | Действия | Microsoft. Дбфорпостгрескл/Locations/Оператионресултс/Read | Возврат результатов операции сервера PostgreSQL |
> | Действия | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Получение списка доступных уровней производительности. |
> | Действия | Microsoft. Дбфорпостгрескл/Locations/Приватиндпоинтконнектионазуреасинкоператион/Read | Возвращает результат операции подключения к частной конечной точке |
> | Действия | Microsoft. Дбфорпостгрескл/Locations/Приватиндпоинтконнектионоператионресултс/Read | Возвращает результат операции подключения к частной конечной точке |
> | Действия | Microsoft. Дбфорпостгрескл/Locations/Приватиндпоинтконнектионпроксязуреасинкоператион/Read | Возвращает результат для операции прокси-сервера подключения частной конечной точки |
> | Действия | Microsoft. Дбфорпостгрескл/Locations/Приватиндпоинтконнектионпроксйоператионресултс/Read | Возвращает результат для операции прокси-сервера подключения частной конечной точки |
> | Действия | Microsoft. Дбфорпостгрескл/Locations/СекуритялертполиЦиесазуреасинкоператион/Read | Возвращает список результатов операции обнаружения угроз сервера. |
> | Действия | Microsoft. Дбфорпостгрескл/Locations/СекуритялертполиЦиесоператионресултс/Read | Возвращает список результатов операции обнаружения угроз сервера. |
> | Действия | Microsoft. Дбфорпостгрескл/операции/чтение | Возвращает список операций PostgreSQL. |
> | Действия | Microsoft.DBforPostgreSQL/performanceTiers/read | Получение списка доступных уровней производительности. |
> | Действия | Microsoft. Дбфорпостгрескл/Register/действие | Регистрация поставщика ресурсов PostgreSQL |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Administrators/Delete | Удаляет существующего администратора сервера PostgreSQL. |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Administrators/Read | Возвращает список администраторов сервера PostgreSQL. |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Administrators/Write | Создает или обновляет администратора сервера PostgreSQL с указанными параметрами. |
> | Действия | Microsoft.DBforPostgreSQL/servers/advisors/read | Возвращает список консультантов. |
> | Действия | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Возвращает список рекомендуемых действий |
> | Действия | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Предоставление рекомендаций |
> | Действия | Microsoft.DBforPostgreSQL/servers/configurations/read | Возвращает список конфигураций для сервера или получает свойства для указанной конфигурации. |
> | Действия | Microsoft.DBforPostgreSQL/servers/configurations/write | Обновляет значение для указанной конфигурации. |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/databases/Delete | Удаляет существующую базу данных PostgreSQL. |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/databases/Read | Возвращает список баз данных PostgreSQL или свойства для указанной базы данных. |
> | Действия | Microsoft. Дбфорпостгрескл/серверы/базы данных/запись | Создает базу данных PostgreSQL с указанными параметрами или обновляет свойства для указанной базы данных. |
> | Действия | Microsoft.DBforPostgreSQL/servers/delete | Удаление существующего сервера. |
> | Действия | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Удаление существующего правила брандмауэра. |
> | Действия | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Возвращение списка правил брандмауэра для сервера или получение свойств для указанного правила брандмауэра. |
> | Действия | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Создание правила брандмауэра с указанными параметрами или обновление существующего правила. |
> | Действия | Microsoft. Дбфорпостгрескл/серверы/файлы журнала/чтение | Возврат списка файлов журнала PostgreSQL. |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Приватиндпоинтконнектионпроксиес/Delete | Удаляет существующий прокси-сервер подключения частной конечной точки. |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Приватиндпоинтконнектионпроксиес/Read | Возвращает список прокси-серверов подключения частной конечной точки или получает свойства для указанного прокси подключения к частной конечной точке. |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Приватиндпоинтконнектионпроксиес/проверка/действие | Проверяет подключение к частной конечной точке создать вызов с NRP стороны |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Приватиндпоинтконнектионпроксиес/Write | Создает прокси-сервер подключения частной конечной точки с указанными параметрами или обновляет свойства или теги для указанного прокси-сервера подключения частной конечной точки. |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Приватиндпоинтконнектионс/Delete | Удаляет существующее подключение к частной конечной точке |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Приватиндпоинтконнектионс/Read | Возвращает список подключений к частным конечным точкам или возвращает свойства для указанного подключения к частной конечной точке. |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Приватиндпоинтконнектионс/Write | Утверждает или отклоняет существующее подключение к частной конечной точке |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Привателинкресаурцес/Read | Получение ресурсов частной ссылки для соответствующего сервера PostgreSQL |
> | Действия | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса. |
> | Действия | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Получает доступные журналы серверов Postgres. |
> | Действия | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Возвращает типы метрик, доступных для баз данных. |
> | Действия | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Возвращает текст запроса. |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Куеритекстс/Read | Возврат текста для списка запросов |
> | Действия | Microsoft.DBforPostgreSQL/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Действия | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Возвращение восстанавливаемых данных PostgreSQL Server. |
> | Действия | Microsoft. Дбфорпостгрескл/серверы/реплики/чтение | Получение реплик чтения PostgreSQL сервера |
> | Действия | Microsoft. Дбфорпостгрескл/Servers/Restart/Action | Перезапускает указанный сервер. |
> | Действия | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Извлекает сведения о политике обнаружения угроз, настроенной на данном сервере. |
> | Действия | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Изменение политики обнаружения угроз для заданного сервера. |
> | Действия | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Возвращает список статистики запросов для наиболее ресурсоемких запросов. |
> | Действия | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Обновляет конфигурации для указанного сервера. |
> | Действия | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Удаление существующего правила виртуальной сети. |
> | Действия | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Возвращение списка правил виртуальной сети или свойств указанного правила виртуальной сети. |
> | Действия | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Создание правила виртуальной сети с указанными параметрами либо обновление свойств или тегов указанного правила виртуальной сети. |
> | Действия | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Возвращает статистику ожидания для экземпляра |
> | Действия | Microsoft.DBforPostgreSQL/servers/write | Создание сервера с указанными параметрами либо обновление свойств или тегов указанного сервера. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/конфигурации/чтение | Возвращает список конфигураций для сервера или получает свойства для указанной конфигурации. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/конфигурации/запись | Обновляет значение для указанной конфигурации. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/Delete | Удаление существующего сервера. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/Фиреваллрулес/Delete | Удаление существующего правила брандмауэра. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/Фиреваллрулес/Read | Возвращение списка правил брандмауэра для сервера или получение свойств для указанного правила брандмауэра. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/Фиреваллрулес/запись | Создание правила брандмауэра с указанными параметрами или обновление существующего правила. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/providers/Microsoft. Insights/diagnosticSettings/Read | Получение параметра диагностики для ресурса. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/providers/Microsoft. Insights/diagnosticSettings/запись | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/providers/Microsoft. Insights/Логдефинитионс/Read | Получает доступные журналы серверов Postgres. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/providers/Microsoft. Insights/metricDefinitions/Read | Возвращает типы метрик, доступных для баз данных. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/Read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/Упдатеконфигуратионс/Action | Обновляет конфигурации для указанного сервера. |
> | Действия | Microsoft. Дбфорпостгрескл/serversv2/запись | Создание сервера с указанными параметрами либо обновление свойств или тегов указанного сервера. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Devices/Account/diagnosticSettings/Read | Возвращает параметр диагностики для ресурса. |
> | Действия | Microsoft. Devices/Account/diagnosticSettings/Write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft. Devices/Account/Логдефинитионс/Read | Получение доступных определений журналов для службы Центра Интернета вещей. |
> | Действия | Microsoft. Devices/Account/metricDefinitions/Read | Возвращает доступные метрики для службы Центра Интернета вещей. |
> | Действия | Microsoft.Devices/checkNameAvailability/Action | Проверяет, доступно ли имя Центра Интернета вещей. |
> | Действия | Microsoft.Devices/checkNameAvailability/Action | Проверяет, доступно ли имя Центра Интернета вещей. |
> | Действия | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Проверяет, доступно ли имя службы подготовки. |
> | Действия | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Проверяет, доступно ли имя службы подготовки. |
> | Действия | Microsoft. Devices/Дигиталтвинс/Delete | Удаление существующей учетной записи Digital двойников и всех ее дочерних элементов |
> | Действия | Microsoft. Devices/Дигиталтвинс/оператионресултс/Read | Получение состояния операции по учетной записи Digital двойников |
> | Действия | Microsoft. Devices/Дигиталтвинс/Read | Возвращает список учетных записей Digital двойников, связанных с подпиской. |
> | Действия | Microsoft. Devices/Дигиталтвинс/SKU/чтение | Получение списка допустимых номеров SKU для цифровых двойников учетных записей |
> | Действия | Microsoft. Devices/Дигиталтвинс/Write | Создание новой учетной записи двойников digit |
> | Действия | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действия | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Удаляет сертификат. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Создание кода проверки. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Возвращает сертификат. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Проверка ресурса сертификата. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Создание или обновление сертификата. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Удаление ресурса клиента Центра Интернета вещей. |
> | Действия | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действия | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Удаляет группу потребителей концентратора событий. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Возвращает группы потребителей концентратора событий. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Создает группу потребителей концентратора событий. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Экспортирует устройства. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Получение ресурса статистики клиента Центра Интернета вещей. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Импортирует устройства. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Получение ключа клиента Центра Интернета вещей. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Возвращает сведения о заданиях, отправленных в заданный Центр Интернета вещей. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Получение ключей клиента Центра Интернета вещей. |
> | Действия | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Получение доступных определений журналов для службы Центра Интернета вещей. |
> | Действия | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Возвращает доступные метрики для службы Центра Интернета вещей. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Возвращает метрики квоты. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/Read | Получение ресурса клиента Центра Интернета вещей. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Тестирует сообщение с помощью всех существующих маршрутов. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Тестирует сообщение с помощью предоставленного тестового маршрута. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Возвращает сведения о работоспособности всех конечных точек маршрутизации для Центра Интернета вещей. |
> | Действия | Microsoft.Devices/elasticPools/iotHubTenants/Write | Создание или обновление ресурса клиента Центра Интернета вещей. |
> | Действия | Microsoft.Devices/ElasticPools/metricDefinitions/read | Возвращает доступные метрики для службы Центра Интернета вещей. |
> | Действия | Microsoft.Devices/iotHubs/certificates/Delete | Удаляет сертификат. |
> | Действия | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Создание кода проверки. |
> | Действия | Microsoft.Devices/iotHubs/certificates/Read | Возвращает сертификат. |
> | Действия | Microsoft.Devices/iotHubs/certificates/verify/Action | Проверка ресурса сертификата. |
> | Действия | Microsoft.Devices/iotHubs/certificates/Write | Создание или обновление сертификата. |
> | Действия | Microsoft.Devices/iotHubs/Delete | Удаляет ресурс Центра Интернета вещей. |
> | Действия | Microsoft.Devices/IotHubs/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действия | Microsoft.Devices/IotHubs/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Удаление фильтра сетки событий. |
> | Действия | Microsoft.Devices/iotHubs/eventGridFilters/Read | Получение фильтра сетки событий. |
> | Действия | Microsoft.Devices/iotHubs/eventGridFilters/Write | Создание фильтра сетки событий или обновление существующего. |
> | Действия | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Удаляет группу потребителей концентратора событий. |
> | Действия | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Возвращает группы потребителей концентратора событий. |
> | Действия | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Создает группу потребителей концентратора событий. |
> | Действия | Microsoft.Devices/iotHubs/exportDevices/Action | Экспортирует устройства. |
> | Действия | Microsoft.Devices/iotHubs/importDevices/Action | Импортирует устройства. |
> | Действия | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Возвращает ключ Центра Интернета вещей для указанного имени. |
> | Действия | Microsoft.Devices/iotHubs/iotHubStats/Read | Возвращает статистику Центра Интернета вещей. |
> | Действия | Microsoft.Devices/iotHubs/jobs/Read | Возвращает сведения о заданиях, отправленных в заданный Центр Интернета вещей. |
> | Действия | Microsoft.Devices/iotHubs/listkeys/Action | Возвращает все ключи Центра Интернета вещей. |
> | Действия | Microsoft.Devices/IotHubs/logDefinitions/read | Получение доступных определений журналов для службы Центра Интернета вещей. |
> | Действия | Microsoft.Devices/IotHubs/metricDefinitions/read | Возвращает доступные метрики для службы Центра Интернета вещей. |
> | Действия | Microsoft.Devices/iotHubs/operationresults/Read | Получение результата операции (устаревший API). |
> | Действия | Microsoft.Devices/iotHubs/quotaMetrics/Read | Возвращает метрики квоты. |
> | Действия | Microsoft.Devices/iotHubs/Read | Возвращает ресурсы Центра Интернета вещей. |
> | Действия | Microsoft.Devices/iotHubs/routing/$testall/Action | Тестирует сообщение с помощью всех существующих маршрутов. |
> | Действия | Microsoft.Devices/iotHubs/routing/$testnew/Action | Тестирует сообщение с помощью предоставленного тестового маршрута. |
> | Действия | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Возвращает сведения о работоспособности всех конечных точек маршрутизации для Центра Интернета вещей. |
> | Действия | Microsoft.Devices/iotHubs/skus/Read | Возвращает допустимые номера SKU Центра Интернета вещей. |
> | Действия | Microsoft.Devices/iotHubs/Write | Создает или обновляет ресурс Центра Интернета вещей. |
> | Действия | Microsoft.Devices/locations/operationresults/Read | Получает результат операции на основе расположения. |
> | Действия | Microsoft.Devices/operationresults/Read | Получение результата операции. |
> | Действия | Microsoft.Devices/operations/Read | Возвращает все операции поставщика ресурсов. |
> | Действия | Microsoft.Devices/provisioningServices/certificates/Delete | Удаляет сертификат. |
> | Действия | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Создание кода проверки. |
> | Действия | Microsoft.Devices/provisioningServices/certificates/Read | Возвращает сертификат. |
> | Действия | Microsoft.Devices/provisioningServices/certificates/verify/Action | Проверка ресурса сертификата. |
> | Действия | Microsoft.Devices/provisioningServices/certificates/Write | Создание или обновление сертификата. |
> | Действия | Microsoft.Devices/provisioningServices/Delete | Удаление ресурса службы политики диагностики (DPS) Центра Интернета вещей. |
> | Действия | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действия | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Получение ключей DPS Центра Интернета вещей для имени ключа. |
> | Действия | Microsoft.Devices/provisioningServices/listkeys/Action | Получение ключей DPS Центра Интернета вещей. |
> | Действия | Microsoft.Devices/provisioningServices/logDefinitions/read | Получение доступных определений журналов для службы подготовки. |
> | Действия | Microsoft.Devices/provisioningServices/metricDefinitions/read | Получение доступных метрик для службы подготовки. |
> | Действия | Microsoft.Devices/provisioningServices/operationresults/Read | Получение результата операции DPS. |
> | Действия | Microsoft.Devices/provisioningServices/Read | Получение ресурса DPS Центра Интернета вещей. |
> | Действия | Microsoft.Devices/provisioningServices/skus/Read | Получение действительных номеров SKU для DPS Центра Интернета вещей. |
> | Действия | Microsoft.Devices/provisioningServices/Write | Создание ресурса DPS для Центра Интернета вещей. |
> | Действия | Microsoft.Devices/register/action | Регистрирует подписку в поставщике ресурсов Центра Интернета вещей и позволяет создавать ресурсы Центра Интернета вещей. |
> | Действия | Microsoft.Devices/usages/Read | Возвращает данные об использовании подписки для данного поставщика. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.DevSpaces/controllers/delete | Удаление контроллера Azure Dev Spaces Controller и служб плоскости данных |
> | Действия | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Сведения о подключении списка для инфраструктуры контроллера Azure Dev Spaces |
> | Действия | Microsoft.DevSpaces/controllers/read | Чтение свойств контроллера Azure Dev Spaces |
> | Действия | Microsoft.DevSpaces/controllers/write | Создание или обновление свойств контроллера Azure Dev Spaces |
> | Действия | Microsoft. Девспацес/Locations/Чеккконтаинерхостмаппинг/Action | Проверка существующего сопоставления контроллера для узла контейнера |
> | Действия | Microsoft. Девспацес/Locations/оператионресултс/Read | Чтение состояния асинхронной операции |
> | Действия | Microsoft.DevSpaces/register/action | Создание поставщика ресурсов Microsoft Dev Spaces с подпиской |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.DevTestLab/labCenters/delete | Удаление центров лабораторий. |
> | Действия | Microsoft.DevTestLab/labCenters/read | Чтение центров лабораторий. |
> | Действия | Microsoft.DevTestLab/labCenters/write | Добавление или изменение центров лабораторий. |
> | Действия | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Считывает шаблоны Azure Resource Manager. |
> | Действия | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Создает шаблон ARM для заданного артефакта, передает необходимые файлы в учетную запись хранения и проверяет созданный артефакт. |
> | Действия | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Считывает артефакты. |
> | Действия | Microsoft.DevTestLab/labs/artifactSources/delete | Удаляет источники артефактов. |
> | Действия | Microsoft.DevTestLab/labs/artifactSources/read | Считывает источники артефактов. |
> | Действия | Microsoft.DevTestLab/labs/artifactSources/write | Добавляет или изменяет источники артефактов. |
> | Действия | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Отправляет заявку на доступ к случайной запрашиваемой виртуальной машине в лаборатории. |
> | Действия | Microsoft.DevTestLab/labs/costs/read | Считывает затраты. |
> | Действия | Microsoft.DevTestLab/labs/costs/write | Добавляет или изменяет затраты. |
> | Действия | Microsoft.DevTestLab/labs/CreateEnvironment/action | Создает виртуальные машины в лаборатории. |
> | Действия | Microsoft.DevTestLab/labs/customImages/delete | Удаляет пользовательские образы. |
> | Действия | Microsoft.DevTestLab/labs/customImages/read | Считывает пользовательские образы. |
> | Действия | Microsoft.DevTestLab/labs/customImages/write | Добавляет или изменяет пользовательские образы. |
> | Действия | Microsoft.DevTestLab/labs/delete | Удаляет лаборатории. |
> | Действия | Microsoft. DevTestLab/Labs/Енсурекуррентусерпрофиле/действие | Убедитесь, что текущий пользователь имеет допустимый профиль в лаборатории. |
> | Действия | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Экспортирует данные об использовании ресурсов лаборатории в учетную запись хранения. |
> | Действия | Microsoft.DevTestLab/labs/formulas/delete | Удаляет формулы. |
> | Действия | Microsoft.DevTestLab/labs/formulas/read | Считывает формулы. |
> | Действия | Microsoft.DevTestLab/labs/formulas/write | Добавляет или изменяет формулы. |
> | Действия | Microsoft.DevTestLab/labs/galleryImages/read | Считывает образы из коллекции. |
> | Действия | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Создает универсальный код ресурса (URI) для передачи настраиваемых образов дисков в лабораторию. |
> | Действия | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Импорт виртуальной машины в другую лабораторию. |
> | Действия | Microsoft.DevTestLab/labs/ListVhds/action | Выводит список образов дисков, доступных для создания пользовательского образа. |
> | Действия | Microsoft.DevTestLab/labs/notificationChannels/delete | Удаление каналов уведомления. |
> | Действия | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Отправляет уведомление в указанный канал. |
> | Действия | Microsoft.DevTestLab/labs/notificationChannels/read | Чтение каналов уведомлений. |
> | Действия | Microsoft.DevTestLab/labs/notificationChannels/write | Добавление или изменение каналов уведомления. |
> | Действия | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Оценивает политику лаборатории. |
> | Действия | Microsoft.DevTestLab/labs/policySets/policies/delete | Удаляет политики. |
> | Действия | Microsoft.DevTestLab/labs/policySets/policies/read | Считывает политики. |
> | Действия | Microsoft.DevTestLab/labs/policySets/policies/write | Добавляет или изменяет политики. |
> | Действия | Microsoft.DevTestLab/labs/policySets/read | Чтение наборов политик. |
> | Действия | Microsoft.DevTestLab/labs/read | Считывает лаборатории. |
> | Действия | Microsoft.DevTestLab/labs/schedules/delete | Удаляет расписания. |
> | Действия | Microsoft.DevTestLab/labs/schedules/Execute/action | Выполняет расписание. |
> | Действия | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Выводит список всех применимых расписаний. |
> | Действия | Microsoft.DevTestLab/labs/schedules/read | Считывает расписания. |
> | Действия | Microsoft.DevTestLab/labs/schedules/write | Добавляет или изменяет расписания. |
> | Действия | Microsoft.DevTestLab/labs/serviceRunners/delete | Удаляет средства выполнения служб. |
> | Действия | Microsoft.DevTestLab/labs/serviceRunners/read | Считывает средства выполнения служб. |
> | Действия | Microsoft.DevTestLab/labs/serviceRunners/write | Добавляет или изменяет средства выполнения служб. |
> | Действия | Microsoft. DevTestLab/Labs/Шаредгаллериес/удаление | Удаление общих галерей. |
> | Действия | Microsoft. DevTestLab/Labs/Шаредгаллериес/чтение | Чтение общих галерей. |
> | Действия | Microsoft. DevTestLab/Labs/Шаредгаллериес/Шаредимажес/удалить | Удаление общих образов. |
> | Действия | Microsoft. DevTestLab/Labs/Шаредгаллериес/Шаредимажес/чтение | Чтение общих образов. |
> | Действия | Microsoft. DevTestLab/Labs/Шаредгаллериес/Шаредимажес/запись | Добавление или изменение общих образов. |
> | Действия | Microsoft. DevTestLab/Labs/Шаредгаллериес/запись | Добавление или изменение общих галерей. |
> | Действия | Microsoft.DevTestLab/labs/users/delete | Удаляет профили пользователей. |
> | Действия | Microsoft.DevTestLab/labs/users/disks/Attach/action | Подключает диск к виртуальной машине и создает аренду для него. |
> | Действия | Microsoft.DevTestLab/labs/users/disks/delete | Удаляет диски. |
> | Действия | Microsoft.DevTestLab/labs/users/disks/Detach/action | Отключает диск от виртуальной машины и прерывает его аренду. |
> | Действия | Microsoft.DevTestLab/labs/users/disks/read | Считывает диски. |
> | Действия | Microsoft.DevTestLab/labs/users/disks/write | Добавляет или изменяет диски. |
> | Действия | Microsoft.DevTestLab/labs/users/environments/delete | Удаляет среды. |
> | Действия | Microsoft.DevTestLab/labs/users/environments/read | Считывает среды. |
> | Действия | Microsoft.DevTestLab/labs/users/environments/write | Добавляет или изменяет среды. |
> | Действия | Microsoft.DevTestLab/labs/users/read | Считывает профили пользователей. |
> | Действия | Microsoft.DevTestLab/labs/users/secrets/delete | Удаляет секреты. |
> | Действия | Microsoft.DevTestLab/labs/users/secrets/read | Считывает секреты. |
> | Действия | Microsoft.DevTestLab/labs/users/secrets/write | Добавляет или изменяет секреты. |
> | Действия | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Удаление платформ Service Fabric. |
> | Действия | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Выводит список применимых расписаний запуска и остановки, если они есть. |
> | Действия | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Чтение платформ Service Fabric. |
> | Действия | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Удаляет расписания. |
> | Действия | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Выполняет расписание. |
> | Действия | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Считывает расписания. |
> | Действия | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Добавляет или изменяет расписания. |
> | Действия | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Запуск платформы Service Fabric. |
> | Действия | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Остановка платформы Service Fabric. |
> | Действия | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Добавление или изменение платформ Service Fabric. |
> | Действия | Microsoft.DevTestLab/labs/users/write | Добавляет или изменяет профили пользователей. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Подключает новый или существующий диск данных к виртуальной машине. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Применяет артефакты к виртуальной машине. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Присваивает владение существующей виртуальной машиной. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/delete | Удаляет виртуальные машины. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Отключает указанный диск от виртуальной машины. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Получает строку, которая представляет содержание RDP-файла для виртуальной машины. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Выводит список применимых расписаний запуска и остановки, если они есть. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/read | Считывает виртуальные машины. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Повторное развертывание виртуальной машины. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Изменение размера виртуальной машины. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Перезапуск виртуальной машины. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Удаляет расписания. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Выполняет расписание. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Считывает расписания. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Добавляет или изменяет расписания. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Запускает виртуальную машину. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Остановка виртуальной машины |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Переносит все диски с данными, подключенные к виртуальной машине, к текущему пользователю. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Отказ от владения существующей виртуальной машиной. |
> | Действия | Microsoft.DevTestLab/labs/virtualMachines/write | Добавляет или изменяет виртуальные машины. |
> | Действия | Microsoft. DevTestLab/Labs/virtualNetworks/Бастионхостс/удалить | Удалите бастионхостс. |
> | Действия | Microsoft. DevTestLab/Labs/virtualNetworks/Бастионхостс/чтение | Чтение бастионхостс. |
> | Действия | Microsoft. DevTestLab/Labs/virtualNetworks/Бастионхостс/запись | Добавьте или измените бастионхостс. |
> | Действия | Microsoft.DevTestLab/labs/virtualNetworks/delete | Удаляет виртуальные сети. |
> | Действия | Microsoft.DevTestLab/labs/virtualNetworks/read | Считывает виртуальные сети. |
> | Действия | Microsoft.DevTestLab/labs/virtualNetworks/write | Добавляет или изменяет виртуальные сети. |
> | Действия | Microsoft.DevTestLab/labs/vmPools/delete | Удаление пулов виртуальных машин. |
> | Действия | Microsoft.DevTestLab/labs/vmPools/read | Считывание пулов виртуальных машин. |
> | Действия | Microsoft.DevTestLab/labs/vmPools/write | Добавление или изменение пулов виртуальных машин. |
> | Действия | Microsoft.DevTestLab/labs/write | Добавляет или изменяет лаборатории. |
> | Действия | Microsoft.DevTestLab/locations/operations/read | Считывает операции. |
> | Действия | Microsoft.DevTestLab/register/action | Регистрирует подписку. |
> | Действия | Microsoft.DevTestLab/schedules/delete | Удаляет расписания. |
> | Действия | Microsoft.DevTestLab/schedules/Execute/action | Выполняет расписание. |
> | Действия | Microsoft.DevTestLab/schedules/read | Считывает расписания. |
> | Действия | Microsoft.DevTestLab/schedules/Retarget/action | Обновляет идентификатор целевого ресурса расписания. |
> | Действия | Microsoft.DevTestLab/schedules/write | Добавляет или изменяет расписания. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.DocumentDB/databaseAccountNames/read | Проверяет доступность имени. |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/коллекции/удаление | Удаляет коллекцию. Применимо только к типам API: "MongoDB". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/коллекции/Оператионресултс/чтение | Чтение состояния асинхронной операции. Применимо только к типам API: "MongoDB". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/коллекции/чтение | Считывает коллекцию или список всех коллекций. Применимо только к типам API: "MongoDB". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/databases/Collections/Settings/Оператионресултс/Read | Чтение состояния асинхронной операции. Применимо только к типам API: "MongoDB". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных, коллекции, параметры/чтение | Считывает пропускную способность коллекции. Применимо только к типам API: "MongoDB". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/коллекции/параметры/запись | Обновите пропускную способность коллекции. Применимо только к типам API: "MongoDB". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/коллекции/запись | Создание или обновление коллекции. Применимо только к типам API: "MongoDB". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных, контейнеры или удаление | Удаление контейнера. Применимо только к типам API: "SQL". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/контейнеры/Оператионресултс/чтение | Чтение состояния асинхронной операции. Применимо только к типам API: "SQL". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных и контейнеры/чтение | Чтение контейнера или перечисление всех контейнеров. Применимо только к типам API: "SQL". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных, контейнеры, параметры, Оператионресултс/чтение | Чтение состояния асинхронной операции. Применимо только к типам API: "SQL". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных, контейнеры, параметры/чтение | Считывает пропускную способность контейнера. Применимо только к типам API: "SQL". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных, контейнеры, параметры/запись | Обновите пропускную способность контейнера. Применимо только к типам API: "SQL". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных и контейнеры/запись | Создание или обновление контейнера. Применимо только к типам API: "SQL". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/удаление | Удаление базы данных. Применимо только к типам API: "SQL", "MongoDB", "гремлин'". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных, графы и удаление | Удаление диаграммы. Применимо только к типам API: "гремлин'". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/графы/Оператионресултс/чтение | Чтение состояния асинхронной операции. Применимо только к типам API: "гремлин'". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/графы/чтение | Чтение диаграммы или составление списка всех диаграмм. Применимо только к типам API: "гремлин'". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных, графы, параметры, Оператионресултс/чтение | Чтение состояния асинхронной операции. Применимо только к типам API: "гремлин'". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных, графы, параметры/чтение | Считывает пропускную способность графа. Применимо только к типам API: "гремлин'". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных, графы, параметры/запись | Обновите пропускную способность графа. Применимо только к типам API: "гремлин'". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/графики/запись | Создание или обновление графа. Применимо только к типам API: "гремлин'". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/databases/Оператионресултс/Read | Чтение состояния асинхронной операции. Применимо только к типам API: "SQL", "MongoDB", "гремлин'". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/чтение | Чтение базы данных или вывод списка всех баз данных. Применимо только к типам API: "SQL", "MongoDB", "гремлин'". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/параметры/Оператионресултс/чтение | Чтение состояния асинхронной операции. Применимо только к типам API: "SQL", "MongoDB", "гремлин'". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/параметры/чтение | Считывает пропускную способность базы данных. Применимо только к типам API: "SQL", "MongoDB", "гремлин'". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/параметры/запись | Обновите пропускную способность базы данных. Применимо только к типам API: "SQL", "MongoDB", "гремлин'". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/базы данных/запись | создавать базу данных; Применимо только к типам API: "SQL", "MongoDB", "гремлин'". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/Delete | Удаление пространства ключей. Применимо только к типам API: "Cassandra". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/Оператионресултс/Read | Чтение состояния асинхронной операции. Применимо только к типам API: "Cassandra". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/Read | Прочтите пространства ключей или перечислите все пространств ключей. Применимо только к типам API: "Cassandra". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/Settings/Оператионресултс/Read | Чтение состояния асинхронной операции. Применимо только к типам API: "Cassandra". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/параметры/чтение | Прочтите пропускную способность пространства ключей. Применимо только к типам API: "Cassandra". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/параметры/запись | Обновление пропускной способности пространства ключей. Применимо только к типам API: "Cassandra". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/Tables/Delete | Удаление таблицы. Применимо только к типам API: "Cassandra". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/Tables/Оператионресултс/Read | Чтение состояния асинхронной операции. Применимо только к типам API: "Cassandra". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/Tables/Read | Считывает таблицу или список всех таблиц. Применимо только к типам API: "Cassandra". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/Tables/Settings/Оператионресултс/Read | Чтение состояния асинхронной операции. Применимо только к типам API: "Cassandra". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/таблицы/параметры/чтение | Чтение пропускной способности таблицы. Применимо только к типам API: "Cassandra". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/таблицы/параметры/запись | Обновите пропускную способность таблицы. Применимо только к типам API: "Cassandra". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/Tables/Write | Создание или обновление таблицы. Применимо только к типам API: "Cassandra". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/пространств ключей/Write | Создайте пространства ключей. Применимо только к типам API: "Cassandra". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/таблицы/удаление | Удаление таблицы. Применимо только к типам API: "Table". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/Tables/Оператионресултс/Read | Чтение состояния асинхронной операции. Применимо только к типам API: "Table". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/таблицы/чтение | Считывает таблицу или список всех таблиц. Применимо только к типам API: "Table". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/Tables/Settings/Оператионресултс/Read | Чтение состояния асинхронной операции. Применимо только к типам API: "Table". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/таблицы/параметры/чтение | Чтение пропускной способности таблицы. Применимо только к типам API: "Table". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/таблицы/параметры/запись | Обновите пропускную способность таблицы. Применимо только к типам API: "Table". Применимо только к типам параметров: "пропускная способность". |
> | Действия | Microsoft. DocumentDB/databaseAccounts/API/таблицы/запись | Создание или обновление таблицы. Применимо только к типам API: "Table". |
> | Действия | Microsoft.DocumentDB/databaseAccounts/backup/action | Отправка запроса на настройку резервного копирования |
> | Действия | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Изменяет группу ресурсов для учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Считывает определения метрик коллекции. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Считывает метрики коллекции. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Чтение метрик уровня ключа раздела для учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Чтение метрик уровня раздела для учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Чтение разделов учетной записи базы данных в коллекции. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Чтение сведений об использовании уровня раздела для учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Считывает данные об использовании коллекции. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Считывает определения метрик базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Считывает метрики базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Считывает данные об использовании базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/delete | Удаляет учетные записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Изменяет приоритеты отработки отказа для регионов учетной записи базы данных. Используется для выполнения операции отработки отказа вручную. |
> | Действия | Microsoft. DocumentDB/databaseAccounts/Жетбаккупполици/действие | Получение политики резервного копирования учетной записи базы данных |
> | Действия | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Возвращает строки подключения для учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Выводит список ключей учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Считывает определения метрик учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/metrics/read | Считывает метрики учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Отключение региона учетной записи базы данных.  |
> | Действия | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Подключение региона учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Чтение состояния асинхронной операции. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Чтение метрик задержки. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/percentile/read | Чтение процентилей задержек репликации. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Чтение метрик задержки для определенного источника и целевого региона. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Чтение метрик задержки для определенного целевого региона. |
> | Действия | Microsoft. DocumentDB/databaseAccounts/Приватиндпоинтконнектионпроксиес/Delete | Удаление прокси-сервера подключения частной конечной точки учетной записи базы данных |
> | Действия | Microsoft. DocumentDB/databaseAccounts/Приватиндпоинтконнектионпроксиес/Read | Чтение прокси подключения частной конечной точки учетной записи базы данных |
> | Действия | Microsoft. DocumentDB/databaseAccounts/Приватиндпоинтконнектионпроксиес/проверка/действие | Проверка прокси-сервера подключения частной конечной точки учетной записи базы данных |
> | Действия | Microsoft. DocumentDB/databaseAccounts/Приватиндпоинтконнектионпроксиес/запись | Создание или обновление прокси-сервера подключения к частной конечной точке учетной записи базы данных |
> | Действия | Microsoft.DocumentDB/databaseAccounts/read | Считывает учетную запись базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Считывает ключи только для чтения учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Считывает ключи только для чтения учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Выполняет ротацию ключей учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Чтение метрик региональной коллекции. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Чтение метрик уровня ключа раздела для учетной записи региональной базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Чтение метрик уровня раздела для учетной записи региональной базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Чтение разделов учетной записи региональной базы данных в коллекции. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Чтение метрик учетной записи базы данных и региона. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/restore/action | Отправка запроса восстановления |
> | Действия | Microsoft.DocumentDB/databaseAccounts/usages/read | Считывает данные об использовании учетной записи базы данных. |
> | Действия | Microsoft.DocumentDB/databaseAccounts/write | Обновляет учетные записи базы данных. |
> | Действия | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Уведомление Microsoft.DocumentDB о том, что идет удаление виртуальной сети или подсети. |
> | Действия | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Чтение состояния асинхронной операции удаления виртуальной сети или подсетей. |
> | Действия | Microsoft. DocumentDB/Locations/Оператионсстатус/Read | Чтение состояния асинхронных операций |
> | Действия | Microsoft.DocumentDB/operationResults/read | Чтение состояния асинхронной операции. |
> | Действия | Microsoft.DocumentDB/operations/read | Чтение операций, доступных для Microsoft DocumentDB.  |
> | Действия | Microsoft.DocumentDB/register/action |  Регистрация поставщика ресурсов Microsoft DocumentDB для подписки. |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.DomainRegistration/checkDomainAvailability/Action | Проверяет, доступен ли домен для приобретения. |
> | Действия | Microsoft.DomainRegistration/domains/Delete | Удаляет существующий домен. |
> | Действия | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Удаление идентификатора владения. |
> | Действия | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Вывод списка идентификаторов владения. |
> | Действия | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Получение идентификатора владения. |
> | Действия | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Создание или обновление идентификатора. |
> | Действия | Microsoft.DomainRegistration/domains/operationresults/Read | Возвращает операцию домена. |
> | Действия | Microsoft.DomainRegistration/domains/Read | Возвращает список доменов. |
> | Действия | Microsoft.DomainRegistration/domains/Read | Получение домена. |
> | Действия | Microsoft.DomainRegistration/domains/renew/Action | Обновление существующего домена. |
> | Действия | Microsoft.DomainRegistration/domains/Write | Добавляет новый домен или обновляет существующий. |
> | Действия | Microsoft.DomainRegistration/generateSsoRequest/Action | Создает запрос на вход в центр управления доменами. |
> | Действия | Microsoft.DomainRegistration/listDomainRecommendations/Action | Извлекает список рекомендуемых доменов на основе ключевых слов. |
> | Действия | Microsoft.DomainRegistration/operations/Read | Вывод списка всех операций из регистрации домена службы приложений. |
> | Действия | Microsoft.DomainRegistration/register/action | Регистрирует поставщик ресурсов доменов Майкрософт для подписки. |
> | Действия | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Вывод списка соглашений. |
> | Действия | Microsoft.DomainRegistration/topLevelDomains/Read | Получение доменов верхнего уровня. |
> | Действия | Microsoft.DomainRegistration/topLevelDomains/Read | Получение домена верхнего уровня. |
> | Действия | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Проверяет объект покупки домена без его отправки. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.EventGrid/domains/delete | Удаление домена |
> | Действия | Microsoft.EventGrid/domains/listKeys/action | Получение списка ключей для домена |
> | Действия | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Получает доступные метрики для доменов |
> | Действия | Microsoft.EventGrid/domains/read | Чтение домена |
> | Действия | Microsoft.EventGrid/domains/regenerateKey/action | Повторное создание ключа для домена |
> | Действия | Microsoft. EventGrid/Domains/Topics/Delete | Удалить раздел домена |
> | Действия | Microsoft.EventGrid/domains/topics/read | Чтение раздела домена |
> | Действия | Microsoft. EventGrid/Domains/Topics/Write | Создание или обновление раздела предметной области |
> | Действия | Microsoft.EventGrid/domains/write | Создание или обновление домена |
> | Действия | Microsoft.EventGrid/eventSubscriptions/delete | Удаление подписки |
> | Действия | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Получение полного URL-адреса подписки на события. |
> | Действия | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для подписок на события. |
> | Действия | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Получение или обновление параметра диагностики для подписок на события. |
> | Действия | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для подписок на события. |
> | Действия | Microsoft.EventGrid/eventSubscriptions/read | Чтение подписки |
> | Действия | Microsoft.EventGrid/eventSubscriptions/write | Создание или обновление подписки |
> | Действия | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для разделов. |
> | Действия | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для разделов. |
> | Действия | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для разделов. |
> | Действия | Microsoft.EventGrid/extensionTopics/read | Чтение extensionTopic. |
> | Действия | Microsoft.EventGrid/locations/eventSubscriptions/read | Создание списка подписок на события в регионе |
> | Действия | Microsoft.EventGrid/locations/operationResults/read | Чтение результата региональной операции |
> | Действия | Microsoft.EventGrid/locations/operationsStatus/read | Чтение состояния региональной операции |
> | Действия | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Создание списка подписок на события в регионе по типу темы |
> | Действия | Microsoft.EventGrid/operationResults/read | Чтение результата операции |
> | Действия | Microsoft.EventGrid/operations/read | Вывод списка операций EventGrid. |
> | Действия | Microsoft.EventGrid/operationsStatus/read | Чтение состояния операции |
> | Действия | Microsoft.EventGrid/register/action | Регистрирует подписку для поставщика ресурсов EventGrid. |
> | Действия | Microsoft.EventGrid/topics/delete | Удаление раздела |
> | Действия | Microsoft.EventGrid/topics/listKeys/action | Получение списка ключей для раздела |
> | Действия | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для разделов. |
> | Действия | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для разделов. |
> | Действия | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для разделов. |
> | Действия | Microsoft.EventGrid/topics/read | Чтение раздела. |
> | Действия | Microsoft.EventGrid/topics/regenerateKey/action | Повторное создание ключа для раздела |
> | Действия | Microsoft.EventGrid/topics/write | Создание или обновление раздела. |
> | Действия | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Создание списка подписок на глобальные события по типу темы |
> | Действия | Microsoft.EventGrid/topictypes/eventtypes/read | Чтение типов eventtype, поддерживаемых topictype |
> | Действия | Microsoft.EventGrid/topictypes/read | Чтение типа раздела (topictype) |
> | Действия | Microsoft.EventGrid/unregister/action | Отмена регистрации подписки для поставщика ресурсов EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. EventHub/Аваилаблеклустеррегионс/чтение | Операция чтения для вывода списка доступных предварительно подготовленных кластеров в регионе Azure. |
> | Действия | Microsoft.EventHub/checkNameAvailability/action | Проверяет доступность пространства имен в заданной подписке. |
> | Действия | Microsoft.EventHub/checkNamespaceAvailability/action | Проверяет доступность пространства имен в заданной подписке. Этот API не рекомендуется. Вместо него используйте CheckNameAvailability. |
> | Действия | Microsoft. EventHub/Clusters/Delete | Удаляет существующий ресурс кластера. |
> | Действия | Microsoft. EventHub/кластеры/пространства имен/чтение | Список идентификаторов ARM для пространств имен в кластере. |
> | Действия | Microsoft. EventHub/Clusters/оператионресултс/Read | Получение состояния асинхронной операции кластера. |
> | Действия | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Возвращает список описаний ресурсов метрик кластера. |
> | Действия | Microsoft.EventHub/clusters/read | Получает описание кластерного ресурса. |
> | Действия | Microsoft.EventHub/clusters/write | Создает или изменяет существующий ресурс кластера. |
> | Действия | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Удаление правил виртуальной сети в поставщике ресурсов концентратора событий для указанной виртуальной сети |
> | Действия | Microsoft.EventHub/namespaces/authorizationRules/action | Обновление правила авторизации для пространства имен. Это нерекомендуемый API. Чтобы обновить правило авторизации для пространства имен, используйте вызов PUT. Эта операция не поддерживается для API версии 2017-04-01. |
> | Действия | Microsoft.EventHub/namespaces/authorizationRules/delete | Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию.  |
> | Действия | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Возвращает строку подключения к пространству имен. |
> | Действия | Microsoft.EventHub/namespaces/authorizationRules/read | Возвращает список описаний правил авторизации пространства имен. |
> | Действия | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действия | Microsoft.EventHub/namespaces/authorizationRules/write | Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации. |
> | Действия | Microsoft.EventHub/namespaces/Delete | Удаляет ресурс пространства имен. |
> | Действия | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Получение ключей правил авторизации основного пространства имен для аварийного восстановления. |
> | Действия | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Получение правил авторизации основного пространства имен для аварийного восстановления. |
> | Действия | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Отключение аварийного восстановления и остановка репликации изменений из основного пространства имен в дополнительное. |
> | Действия | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Проверка доступности псевдонима пространства имен в заданной подписке. |
> | Действия | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Удаление конфигурации аварийного восстановления, связанной с пространством имен. Эта операция может быть вызвана только через основное пространство имен. |
> | Действия | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Вызов отработки отказа географического аварийного восстановления и перенастройка псевдонима пространства имен для указания на дополнительное пространство. |
> | Действия | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Получение конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действия | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Создание или обновление конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действия | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Операция обновления концентратора событий. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT. |
> | Действия | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Операция удаления правил авторизации концентратора событий. |
> | Действия | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Возвращает строку подключения к концентратору событий. |
> | Действия | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Возвращает список правил авторизации концентратора событий. |
> | Действия | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действия | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Создает правила авторизации концентратора событий и обновляет его свойства. Можно обновить права доступа к правилам авторизации. |
> | Действия | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Операция удаления ресурса группы потребителей. |
> | Действия | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Возвращает список описаний ресурсов группы потребителей. |
> | Действия | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Создает или обновляет свойства группы потребителей. |
> | Действия | Microsoft.EventHub/namespaces/eventhubs/Delete | Операция удаления ресурса концентратора событий. |
> | Действия | Microsoft.EventHub/namespaces/eventhubs/read | Возвращает список описаний ресурсов концентратора событий. |
> | Действия | Microsoft.EventHub/namespaces/eventhubs/write | Создает или обновляет свойства концентратора событий. |
> | Действия | Microsoft.EventHub/namespaces/ipFilterRules/delete | Удаление ресурса фильтра IP-адресов |
> | Действия | Microsoft.EventHub/namespaces/ipFilterRules/read | Получение ресурса фильтра IP-адресов |
> | Действия | Microsoft.EventHub/namespaces/ipFilterRules/write | Создание ресурса фильтра IP-адресов |
> | DataAction | Microsoft. EventHub/пространства имен/сообщения/получение/действие | Получение сообщений |
> | DataAction | Microsoft. EventHub/пространства имен/сообщения/Отправка/действие | Отправка сообщений |
> | Действия | Microsoft.EventHub/namespaces/messagingPlan/read | Получение плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действия | Microsoft.EventHub/namespaces/messagingPlan/write | Обновление плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действия | Microsoft. EventHub/пространства имен, нетворкрулесет/удаление | Удаление ресурса правила виртуальной сети |
> | Действия | Microsoft. EventHub/пространства имен/нетворкрулесет/чтение | Получает ресурс Нетворкрулесет |
> | Действия | Microsoft. EventHub/пространства имен/нетворкрулесет/запись | Создание ресурса правила виртуальной сети |
> | Действия | Microsoft. EventHub/пространства имен, нетворкрулесетс/удаление | Удаление ресурса правила виртуальной сети |
> | Действия | Microsoft. EventHub/пространства имен/нетворкрулесетс/чтение | Получает ресурс Нетворкрулесет |
> | Действия | Microsoft. EventHub/пространства имен/нетворкрулесетс/запись | Создание ресурса правила виртуальной сети |
> | Действия | Microsoft.EventHub/namespaces/operationresults/read | Получение состояния операции пространства имен. |
> | Действия | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает список описаний параметров диагностики пространства имен. |
> | Действия | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Возвращает список описаний параметров диагностики пространства имен. |
> | Действия | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Возвращает список описаний ресурсов журналов пространства имен. |
> | Действия | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Возвращает список описаний ресурсов метрик пространства имен. |
> | Действия | Microsoft.EventHub/namespaces/read | Возвращает список описаний ресурсов пространства имен. |
> | Действия | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Удаляет пространство имен ACS. |
> | Действия | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Удаление ресурса правила виртуальной сети |
> | Действия | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Получение ресурса правила виртуальной сети |
> | Действия | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Создание ресурса правила виртуальной сети |
> | Действия | Microsoft.EventHub/namespaces/write | Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен. |
> | Действия | Microsoft.EventHub/operations/read | Получение операций. |
> | Действия | Microsoft.EventHub/register/action | Регистрирует подписку для поставщика ресурсов концентратора событий и позволяет создавать ресурсы концентратора событий. |
> | Действия | Microsoft.EventHub/sku/read | Получение списка описаний ресурсов номеров SKU. |
> | Действия | Microsoft.EventHub/sku/regions/read | Получение списка описаний ресурсов регионов для номеров SKU. |
> | Действия | Microsoft.EventHub/unregister/action | Регистрация поставщика ресурсов концентратора событий. |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Features/features/read | Возвращает функции подписки. |
> | Действия | Microsoft.Features/operations/read | Получает список операций. |
> | Действия | Microsoft.Features/providers/features/read | Возвращает функцию подписки в заданном поставщике ресурсов. |
> | Действия | Microsoft.Features/providers/features/register/action | Регистрирует функцию для подписки в заданном поставщике ресурсов. |
> | Действия | Microsoft.Features/providers/features/unregister/action | Отмена регистрации функции для подписки в заданном поставщике ресурсов. |
> | Действия | Microsoft.Features/register/action | Регистрирует компонент подписки. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Гуестконфигуратион/Гуестконфигуратионассигнментс/Delete | Удаление назначения конфигурации гостя. |
> | Действия | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Получение назначения конфигурации гостевой системы. |
> | Действия | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Получение отчета о назначении конфигурации гостевой системы. |
> | Действия | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Создание назначения конфигурации гостевой системы. |
> | Действия | Microsoft. Гуестконфигуратион/операции/чтение | Возвращает операции для поставщика ресурсов Microsoft. Гуестконфигуратион. |
> | Действия | Microsoft. Гуестконфигуратион/Register/действие | Регистрирует подписку для поставщика ресурсов Microsoft. Гуестконфигуратион. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.HDInsight/clusters/applications/delete | Удаление приложения для кластера HDInsight |
> | Действия | Microsoft.HDInsight/clusters/applications/read | Получение приложения для кластера HDInsight |
> | Действия | Microsoft.HDInsight/clusters/applications/write | Создание приложения или его обновление для кластера HDInsight |
> | Действия | Microsoft.HDInsight/clusters/changerdpsetting/action | Изменяет параметры RDP для кластера HDInsight. |
> | Действия | Microsoft.HDInsight/clusters/configurations/action | Обновляет конфигурацию кластера HDInsight. |
> | Действия | Microsoft.HDInsight/clusters/configurations/action | Возвращает конфигурации кластеров HDInsight. |
> | Действия | Microsoft.HDInsight/clusters/configurations/read | Возвращает конфигурации кластеров HDInsight. |
> | Действия | Microsoft.HDInsight/clusters/delete | Удаляет кластер HDInsight. |
> | Действия | Microsoft. HDInsight/кластеры/расширения/удаление | Удаление расширения кластера для кластера HDInsight |
> | Действия | Microsoft. HDInsight/кластеры/расширения/чтение | Получение расширения кластера для кластера HDInsight |
> | Действия | Microsoft. HDInsight/кластеры/расширения/запись | Создание расширения кластера для кластера HDInsight |
> | Действия | Microsoft. HDInsight/Clusters/Жетгатевайсеттингс/действие | Получение параметров шлюза для кластера HDInsight |
> | Действия | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса кластера HDInsight. |
> | Действия | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для ресурса кластера HDInsight. |
> | Действия | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для кластера HDInsight. |
> | Действия | Microsoft.HDInsight/clusters/read | Возвращает сведения о кластере HDInsight. |
> | Действия | Microsoft.HDInsight/clusters/roles/resize/action | Изменяет размер кластера HDInsight. |
> | Действия | Microsoft. HDInsight/Clusters/Упдатегатевайсеттингс/действие | Обновление параметров шлюза для кластера HDInsight |
> | Действия | Microsoft.HDInsight/clusters/write | Создает или обновляет кластер HDInsight. |
> | Действия | Microsoft.HDInsight/locations/capabilities/read | Возвращает возможности подписки. |
> | Действия | Microsoft.HDInsight/locations/checkNameAvailability/read | Проверяет доступность имени. |
> | Действия | Microsoft. HDInsight, регистрация/действие | Регистрация поставщика ресурсов HDInsight для подписки |
> | Действия | Microsoft. HDInsight, отмена регистрации или действие | Отмена регистрации поставщика ресурсов HDInsight для подписки |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ImportExport/jobs/delete | Удаляет существующее задание. |
> | Действия | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Возвращает ключи BitLocker для указанного задания. |
> | Действия | Microsoft.ImportExport/jobs/read | Возвращает свойства для указанного задания или возвращает список заданий. |
> | Действия | Microsoft.ImportExport/jobs/write | Создает задание с указанными параметрами либо обновляет свойства или теги указанного задания. |
> | Действия | Microsoft.ImportExport/locations/read | Возвращает свойства для указанного расположения или возвращает список расположений. |
> | Действия | Microsoft. ImportExport/операции/чтение | Возвращает операции, поддерживаемые поставщиком ресурсов. |
> | Действия | Microsoft.ImportExport/register/action | Регистрирует подписку для поставщика ресурсов импорта и экспорта и позволяет создавать задания импорта и экспорта. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Insights/ActionGroups/Delete | Удаление группы действий. |
> | Действия | Microsoft.Insights/ActionGroups/Read | Чтение группы действий. |
> | Действия | Microsoft.Insights/ActionGroups/Write | Создание или изменение группы действий. |
> | Действия | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Активировано оповещение журнала действий. |
> | Действия | Microsoft.Insights/ActivityLogAlerts/Delete | Удаление оповещения для журнала действий. |
> | Действия | Microsoft.Insights/ActivityLogAlerts/Read | Чтение оповещения из журнала действий. |
> | Действия | Microsoft.Insights/ActivityLogAlerts/Write | Создание или изменение оповещения для журнала действий. |
> | Действия | Microsoft.Insights/AlertRules/Activated/Action | Активировано классическое оповещение метрики. |
> | Действия | Microsoft.Insights/AlertRules/Delete | Удаление классического оповещения метрики. |
> | Действия | Microsoft.Insights/AlertRules/Incidents/Read | Чтение инцидента классического оповещения метрики. |
> | Действия | Microsoft.Insights/AlertRules/Read | Чтение классического оповещения метрики. |
> | Действия | Microsoft.Insights/AlertRules/Resolved/Action | Классическое оповещение метрики разрешено. |
> | Действия | Microsoft.Insights/AlertRules/Throttled/Action | Правило классического оповещения метрики отрегулировано. |
> | Действия | Microsoft.Insights/AlertRules/Write | Создание или изменение классического оповещения метрики. |
> | Действия | Microsoft.Insights/AutoscaleSettings/Delete | Удаление параметра автомасштабирования. |
> | Действия | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Чтение параметра диагностики для ресурсов. |
> | Действия | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Создание или изменение параметра для диагностики ресурсов. |
> | Действия | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Считывает определения журналов. |
> | Действия | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Считывает определения метрик. |
> | Действия | Microsoft.Insights/AutoscaleSettings/Read | Чтение параметра автомасштабирования. |
> | Действия | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Автоматическое уменьшение масштаба запущено. |
> | Действия | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Автоматическое уменьшение масштаба завершено. |
> | Действия | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Автоматическое увеличение масштаба запущено. |
> | Действия | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Автоматическое увеличение масштаба завершено. |
> | Действия | Microsoft.Insights/AutoscaleSettings/Write | Создание или изменение параметра автомасштабирования. |
> | Действия | Microsoft. Insights/базовый/чтение | Чтение базового показателя метрики (Предварительная версия) |
> | Действия | Microsoft. Insights/Калкулатебаселине/чтение | Вычисление базового показателя для значений метрик (Предварительная версия) |
> | Действия | Microsoft.Insights/Components/AnalyticsItems/Delete | Удаление элемента аналитики Application Insights. |
> | Действия | Microsoft.Insights/Components/AnalyticsItems/Read | Чтение элемента аналитики Application Insights. |
> | Действия | Microsoft.Insights/Components/AnalyticsItems/Write | Запись элемента аналитики Application Insights. |
> | Действия | Microsoft.Insights/Components/AnalyticsTables/Action | Действие таблицы аналитики Application Insights. |
> | Действия | Microsoft.Insights/Components/AnalyticsTables/Delete | Удаление схемы таблицы аналитики Application Insights. |
> | Действия | Microsoft.Insights/Components/AnalyticsTables/Read | Чтение схемы таблицы аналитики Application Insights. |
> | Действия | Microsoft.Insights/Components/AnalyticsTables/Write | Запись схемы таблицы аналитики Application Insights. |
> | Действия | Microsoft.Insights/Components/Annotations/Delete | Удаление заметки Application Insights. |
> | Действия | Microsoft.Insights/Components/Annotations/Read | Чтение заметки Application Insights. |
> | Действия | Microsoft.Insights/Components/Annotations/Write | Запись заметки Application Insights. |
> | Действия | Microsoft.Insights/Components/Api/Read | Чтение API данных компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/ApiKeys/Action | Создание ключа API Application Insights. |
> | Действия | Microsoft.Insights/Components/ApiKeys/Delete | Удаление ключа API Application Insights. |
> | Действия | Microsoft.Insights/Components/ApiKeys/Read | Чтение ключа API Application Insights. |
> | Действия | Microsoft.Insights/Components/BillingPlanForComponent/Read | Чтение плана выставления счетов для компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Чтение текущих функций выставления счетов для компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Запись текущих функций выставления счетов для компонента Application Insights. |
> | Действия | Microsoft. Insights/Components/Даиликапреачед/действие | Достигнуто ежедневное ограничение для Application Insights компонента |
> | Действия | Microsoft. Insights/Components/Даиликапварнингсрешолдреачед/действие | Достигнут порог предупреждения ежедневного ограничения для Application Insights компонента |
> | Действия | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Чтение конфигурации интеграции управления жизненным циклом приложений по умолчанию для Application Insights. |
> | Действия | Microsoft.Insights/Components/Delete | Удаление конфигурации компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/Events/Read | Получение журналов Application Insights с использованием формата запроса OData. |
> | Действия | Microsoft.Insights/Components/ExportConfiguration/Action | Действие параметров экспорта Application Insights. |
> | Действия | Microsoft.Insights/Components/ExportConfiguration/Delete | Удаление параметров экспорта Application Insights. |
> | Действия | Microsoft.Insights/Components/ExportConfiguration/Read | Чтение параметров экспорта Application Insights. |
> | Действия | Microsoft.Insights/Components/ExportConfiguration/Write | Запись параметров экспорта Application Insights. |
> | Действия | Microsoft.Insights/Components/ExtendQueries/Read | Чтение результатов расширенного запроса компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/Favorites/Delete | Удаление избранного объекта Application Insights. |
> | Действия | Microsoft.Insights/Components/Favorites/Read | Чтение избранного объекта Application Insights. |
> | Действия | Microsoft.Insights/Components/Favorites/Write | Запись избранного объекта Application Insights. |
> | Действия | Microsoft.Insights/Components/FeatureCapabilities/Read | Чтение возможностей функций компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Чтение доступных функций выставления счетов для компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/GetToken/Read | Чтение токена компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/MetricDefinitions/Read | Чтение определений метрик компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/Metrics/Read | Чтение метрик компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/Move/Action | Перемещение компонента Application Insights в другую группу ресурсов или подписку. |
> | Действия | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Удаление персонального элемента аналитики Application Insights. |
> | Действия | Microsoft.Insights/Components/MyAnalyticsItems/Read | Чтение персонального элемента аналитики Application Insights. |
> | Действия | Microsoft.Insights/Components/MyAnalyticsItems/Write | Запись персонального элемента аналитики Application Insights. |
> | Действия | Microsoft.Insights/Components/MyFavorites/Read | Чтение персонального избранного объекта Application Insights. |
> | Действия | Microsoft.Insights/Components/Operations/Read | Получение состояния длительных операций в Application Insights. |
> | Действия | Microsoft.Insights/Components/PricingPlans/Read | Чтение плана ценообразования компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/PricingPlans/Write | Запись плана ценообразования компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Чтение конфигурации упреждающего обнаружения Application Insights. |
> | Действия | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Запись конфигурации упреждающего обнаружения Application Insights. |
> | Действия | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Считывает определения метрик. |
> | Действия | Microsoft.Insights/Components/Purge/Action | Очистка данных Application Insights. |
> | Действия | Microsoft.Insights/Components/Query/Read | Выполнение запросов к журналам Application Insights. |
> | Действия | Microsoft.Insights/Components/QuotaStatus/Read | Чтение состояния квоты компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/Read | Чтение конфигурации компонента Application Insights. |
> | Действия | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Чтение расположений веб-тестов Application Insights. |
> | Действия | Microsoft.Insights/Components/Webtests/Read | Чтение конфигурации веб-теста. |
> | Действия | Microsoft.Insights/Components/WorkItemConfigs/Delete | Удаление конфигурации интеграции управления жизненным циклом приложений для Application Insights. |
> | Действия | Microsoft.Insights/Components/WorkItemConfigs/Read | Чтение конфигурации интеграции управления жизненным циклом приложений для Application Insights. |
> | Действия | Microsoft.Insights/Components/WorkItemConfigs/Write | Запись конфигурации интеграции управления жизненным циклом приложений для Application Insights. |
> | Действия | Microsoft.Insights/Components/Write | Запись конфигурации компонента Application Insights. |
> | Действия | Microsoft.Insights/DiagnosticSettings/Delete | Удаление параметра для диагностики ресурсов. |
> | Действия | Microsoft.Insights/DiagnosticSettings/Read | Чтение параметра диагностики для ресурсов. |
> | Действия | Microsoft.Insights/DiagnosticSettings/Write | Создание или изменение параметра для диагностики ресурсов. |
> | Действия | Microsoft.Insights/EventCategories/Read | Чтение доступных категорий событий для журнала действий. |
> | Действия | Microsoft.Insights/eventtypes/digestevents/Read | Считывает дайджест типов событий управления. |
> | Действия | Microsoft.Insights/eventtypes/values/Read | Чтение событий из журнала действий. |
> | Действия | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Удаление параметра диагностики журнала сетевых потоков. |
> | Действия | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Чтение параметра диагностики журнала сетевых потоков. |
> | Действия | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Создание или изменение параметра диагностики журнала сетевых потоков. |
> | Действия | Microsoft.Insights/ListMigrationDate/Action | Возврат даты перевода подписки. |
> | Действия | Microsoft.Insights/ListMigrationDate/Read | Возврат даты перевода подписки. |
> | Действия | Microsoft.Insights/LogDefinitions/Read | Считывает определения журналов. |
> | Действия | Microsoft.Insights/LogProfiles/Delete | Удаление профиля журнала действий. |
> | Действия | Microsoft.Insights/LogProfiles/Read | Чтение профиля журнала действий. |
> | Действия | Microsoft.Insights/LogProfiles/Write | Создание или изменение профиля журнала действий. |
> | Действия | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Чтение данных из таблицы ADAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/ADReplicationResult/Read | Чтение данных из таблицы ADReplicationResult. |
> | Действия | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Чтение данных из таблицы ADSecurityAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/Alert/Read | Чтение данных из таблицы Alert. |
> | Действия | Microsoft.Insights/Logs/AlertHistory/Read | Чтение данных из таблицы AlertHistory. |
> | Действия | Microsoft.Insights/Logs/ApplicationInsights/Read | Чтение данных из таблицы ApplicationInsights. |
> | Действия | Microsoft.Insights/Logs/AzureActivity/Read | Чтение данных из таблицы AzureActivity. |
> | Действия | Microsoft.Insights/Logs/AzureMetrics/Read | Чтение данных из таблицы AzureMetrics. |
> | Действия | Microsoft.Insights/Logs/BoundPort/Read | Чтение данных из таблицы BoundPort. |
> | Действия | Microsoft.Insights/Logs/CommonSecurityLog/Read | Чтение данных из таблицы CommonSecurityLog. |
> | Действия | Microsoft.Insights/Logs/ComputerGroup/Read | Чтение данных из таблицы ComputerGroup. |
> | Действия | Microsoft.Insights/Logs/ConfigurationChange/Read | Чтение данных из таблицы ConfigurationChange. |
> | Действия | Microsoft.Insights/Logs/ConfigurationData/Read | Чтение данных из таблицы ConfigurationData. |
> | Действия | Microsoft.Insights/Logs/ContainerImageInventory/Read | Чтение данных из таблицы ContainerImageInventory. |
> | Действия | Microsoft.Insights/Logs/ContainerInventory/Read | Чтение данных из таблицы ContainerInventory. |
> | Действия | Microsoft.Insights/Logs/ContainerLog/Read | Чтение данных из таблицы ContainerLog. |
> | Действия | Microsoft.Insights/Logs/ContainerServiceLog/Read | Чтение данных из таблицы ContainerServiceLog. |
> | Действия | Microsoft.Insights/Logs/DeviceAppCrash/Read | Чтение данных из таблицы DeviceAppCrash. |
> | Действия | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Чтение данных из таблицы DeviceAppLaunch. |
> | Действия | Microsoft.Insights/Logs/DeviceCalendar/Read | Чтение данных из таблицы DeviceCalendar. |
> | Действия | Microsoft.Insights/Logs/DeviceCleanup/Read | Чтение данных из таблицы DeviceCleanup. |
> | Действия | Microsoft.Insights/Logs/DeviceConnectSession/Read | Чтение данных из таблицы DeviceConnectSession. |
> | Действия | Microsoft.Insights/Logs/DeviceEtw/Read | Чтение данных из таблицы DeviceEtw. |
> | Действия | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Чтение данных из таблицы DeviceHardwareHealth. |
> | Действия | Microsoft.Insights/Logs/DeviceHealth/Read | Чтение данных из таблицы DeviceHealth. |
> | Действия | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Чтение данных из таблицы DeviceHeartbeat. |
> | Действия | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Чтение данных из таблицы DeviceSkypeHeartbeat. |
> | Действия | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Чтение данных из таблицы DeviceSkypeSignIn. |
> | Действия | Microsoft.Insights/Logs/DeviceSleepState/Read | Чтение данных из таблицы DeviceSleepState. |
> | Действия | Microsoft.Insights/Logs/DHAppFailure/Read | Чтение данных из таблицы DHAppFailure. |
> | Действия | Microsoft.Insights/Logs/DHAppReliability/Read | Чтение данных из таблицы DHAppReliability. |
> | Действия | Microsoft.Insights/Logs/DHDriverReliability/Read | Чтение данных из таблицы DHDriverReliability. |
> | Действия | Microsoft.Insights/Logs/DHLogonFailures/Read | Чтение данных из таблицы DHLogonFailures. |
> | Действия | Microsoft.Insights/Logs/DHLogonMetrics/Read | Чтение данных из таблицы DHLogonMetrics. |
> | Действия | Microsoft.Insights/Logs/DHOSCrashData/Read | Чтение данных из таблицы DHOSCrashData. |
> | Действия | Microsoft.Insights/Logs/DHOSReliability/Read | Чтение данных из таблицы DHOSReliability. |
> | Действия | Microsoft.Insights/Logs/DHWipAppLearning/Read | Чтение данных из таблицы DHWipAppLearning. |
> | Действия | Microsoft.Insights/Logs/DnsEvents/Read | Чтение данных из таблицы DnsEvents. |
> | Действия | Microsoft.Insights/Logs/DnsInventory/Read | Чтение данных из таблицы DnsInventory. |
> | Действия | Microsoft.Insights/Logs/ETWEvent/Read | Чтение данных из таблицы ETWEvent. |
> | Действия | Microsoft.Insights/Logs/Event/Read | Чтение данных из таблицы Event. |
> | Действия | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Чтение данных из таблицы ExchangeAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Чтение данных из таблицы ExchangeOnlineAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/Heartbeat/Read | Чтение данных из таблицы Heartbeat. |
> | Действия | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Чтение данных из таблицы IISAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/InboundConnection/Read | Чтение данных из таблицы InboundConnection. |
> | Действия | Microsoft.Insights/Logs/KubeNodeInventory/Read | Чтение данных из таблицы KubeNodeInventory. |
> | Действия | Microsoft.Insights/Logs/KubePodInventory/Read | Чтение данных из таблицы KubePodInventory. |
> | Действия | Microsoft.Insights/Logs/LinuxAuditLog/Read | Чтение данных из таблицы LinuxAuditLog. |
> | Действия | Microsoft.Insights/Logs/MAApplication/Read | Чтение данных из таблицы MAApplication. |
> | Действия | Microsoft.Insights/Logs/MAApplicationHealth/Read | Чтение данных из таблицы MAApplicationHealth. |
> | Действия | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Считывает данные из таблицы MAApplicationHealthAlternativeVersions. |
> | Действия | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Считывает данные из таблицы MAApplicationHealthIssues. |
> | Действия | Microsoft.Insights/Logs/MAApplicationInstance/Read | Чтение данных из таблицы MAApplicationInstance. |
> | Действия | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Считывает данные из таблицы MAApplicationInstanceReadiness. |
> | Действия | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Чтение данных из таблицы MAApplicationReadiness. |
> | Действия | Microsoft.Insights/Logs/MADeploymentPlan/Read | Чтение данных из таблицы MADeploymentPlan. |
> | Действия | Microsoft.Insights/Logs/MADevice/Read | Чтение данных из таблицы MADevice. |
> | Действия | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Считывает данные из таблицы MADevicePnPHealth. |
> | Действия | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Считывает данные из таблицы MADevicePnPHealthAlternativeVersions. |
> | Действия | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Считывает данные из таблицы MADevicePnPHealthIssues. |
> | Действия | Microsoft.Insights/Logs/MADeviceReadiness/Read | Чтение данных из таблицы MADeviceReadiness. |
> | Действия | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Считывает данные из таблицы MADriverInstanceReadiness. |
> | Действия | Microsoft.Insights/Logs/MADriverReadiness/Read | Чтение данных из таблицы MADriverReadiness. |
> | Действия | Microsoft.Insights/Logs/MAOfficeAddin/Read | Чтение данных из таблицы MAOfficeAddin. |
> | Действия | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Чтение данных из таблицы MAOfficeAddinHealth. |
> | Действия | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Считывает данные из таблицы MAOfficeAddinHealthIssues. |
> | Действия | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Чтение данных из таблицы MAOfficeAddinInstance. |
> | Действия | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Считывает данные из таблицы MAOfficeAddinInstanceReadiness. |
> | Действия | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Чтение данных из таблицы MAOfficeAddinReadiness. |
> | Действия | Microsoft.Insights/Logs/MAOfficeApp/Read | Чтение данных из таблицы MAOfficeApp. |
> | Действия | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Чтение данных из таблицы MAOfficeAppHealth. |
> | Действия | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Чтение данных из таблицы MAOfficeAppInstance. |
> | Действия | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Чтение данных из таблицы MAOfficeAppReadiness. |
> | Действия | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Чтение данных из таблицы MAOfficeBuildInfo. |
> | Действия | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Чтение данных из таблицы MAOfficeCurrencyAssessment. |
> | Действия | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Чтение данных из таблицы MAOfficeCurrencyAssessmentDailyCounts. |
> | Действия | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Чтение данных из таблицы MAOfficeDeploymentStatus. |
> | Действия | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Считывает данные из таблицы MAOfficeMacroHealth. |
> | Действия | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Считывает данные из таблицы MAOfficeMacroHealthIssues. |
> | Действия | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Считывает данные из таблицы MAOfficeMacroIssueInstanceReadiness. |
> | Действия | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Чтение данных из таблицы MAOfficeMacroIssueReadiness. |
> | Действия | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Чтение данных из таблицы MAOfficeMacroSummary. |
> | Действия | Microsoft.Insights/Logs/MAOfficeSuite/Read | Чтение данных из таблицы MAOfficeSuite. |
> | Действия | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Чтение данных из таблицы MAOfficeSuiteInstance. |
> | Действия | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Чтение данных из таблицы MAProposedPilotDevices. |
> | Действия | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Чтение данных из таблицы MAWindowsBuildInfo. |
> | Действия | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Чтение данных из таблицы MAWindowsCurrencyAssessment. |
> | Действия | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Чтение данных из таблицы MAWindowsCurrencyAssessmentDailyCounts. |
> | Действия | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Чтение данных из таблицы MAWindowsDeploymentStatus. |
> | Действия | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Считывает данные из таблицы MAWindowsSysReqInstanceReadiness. |
> | Действия | Microsoft.Insights/Logs/NetworkMonitoring/Read | Чтение данных из таблицы NetworkMonitoring. |
> | Действия | Microsoft.Insights/Logs/OfficeActivity/Read | Чтение данных из таблицы OfficeActivity. |
> | Действия | Microsoft.Insights/Logs/Operation/Read | Чтение данных из таблицы Operation. |
> | Действия | Microsoft.Insights/Logs/OutboundConnection/Read | Чтение данных из таблицы OutboundConnection. |
> | Действия | Microsoft.Insights/Logs/Perf/Read | Чтение данных из таблицы Perf. |
> | Действия | Microsoft.Insights/Logs/ProtectionStatus/Read | Чтение данных из таблицы ProtectionStatus. |
> | Действия | Microsoft.Insights/Logs/Read | Чтение данных из всех журналов. |
> | Действия | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Чтение данных из таблицы ReservedAzureCommonFields. |
> | Действия | Microsoft.Insights/Logs/ReservedCommonFields/Read | Чтение данных из таблицы ReservedCommonFields. |
> | Действия | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Чтение данных из таблицы SCCMAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Чтение данных из таблицы SCOMAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/SecurityAlert/Read | Чтение данных из таблицы SecurityAlert. |
> | Действия | Microsoft.Insights/Logs/SecurityBaseline/Read | Чтение данных из таблицы SecurityBaseline. |
> | Действия | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Чтение данных из таблицы SecurityBaselineSummary. |
> | Действия | Microsoft.Insights/Logs/SecurityDetection/Read | Чтение данных из таблицы SecurityDetection. |
> | Действия | Microsoft.Insights/Logs/SecurityEvent/Read | Чтение данных из таблицы SecurityEvent. |
> | Действия | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Чтение данных из таблицы ServiceFabricOperationalEvent. |
> | Действия | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Чтение данных из таблицы ServiceFabricReliableActorEvent. |
> | Действия | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Чтение данных из таблицы ServiceFabricReliableServiceEvent. |
> | Действия | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Чтение данных из таблицы SfBAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Чтение данных из таблицы SfBOnlineAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Чтение данных из таблицы SharePointOnlineAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Чтение данных из таблицы SPAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Чтение данных из таблицы SQLAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Чтение данных из таблицы SQLQueryPerformance. |
> | Действия | Microsoft.Insights/Logs/Syslog/Read | Чтение данных из таблицы Syslog. |
> | Действия | Microsoft.Insights/Logs/SysmonEvent/Read | Чтение данных из таблицы SysmonEvent. |
> | Действия | Microsoft. Insights/журналы/таблицы. пользовательский/чтение | Чтение данных из любого пользовательского журнала. |
> | Действия | Microsoft.Insights/Logs/UAApp/Read | Чтение данных из таблицы UAApp. |
> | Действия | Microsoft.Insights/Logs/UAComputer/Read | Чтение данных из таблицы UAComputer. |
> | Действия | Microsoft.Insights/Logs/UAComputerRank/Read | Чтение данных из таблицы UAComputerRank. |
> | Действия | Microsoft.Insights/Logs/UADriver/Read | Чтение данных из таблицы UADriver. |
> | Действия | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Чтение данных из таблицы UADriverProblemCodes. |
> | Действия | Microsoft.Insights/Logs/UAFeedback/Read | Чтение данных из таблицы UAFeedback. |
> | Действия | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Чтение данных из таблицы UAHardwareSecurity. |
> | Действия | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Чтение данных из таблицы UAIESiteDiscovery. |
> | Действия | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Чтение данных из таблицы UAOfficeAddIn. |
> | Действия | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Чтение данных из таблицы UAProposedActionPlan. |
> | Действия | Microsoft.Insights/Logs/UASysReqIssue/Read | Чтение данных из таблицы UASysReqIssue. |
> | Действия | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Чтение данных из таблицы UAUpgradedComputer. |
> | Действия | Microsoft.Insights/Logs/Update/Read | Чтение данных из таблицы Update. |
> | Действия | Microsoft.Insights/Logs/UpdateRunProgress/Read | Чтение данных из таблицы UpdateRunProgress. |
> | Действия | Microsoft.Insights/Logs/UpdateSummary/Read | Чтение данных из таблицы UpdateSummary. |
> | Действия | Microsoft.Insights/Logs/Usage/Read | Чтение данных из таблицы Usage. |
> | Действия | Microsoft.Insights/Logs/W3CIISLog/Read | Чтение данных из таблицы W3CIISLog. |
> | Действия | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Чтение данных из таблицы WaaSDeploymentStatus. |
> | Действия | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Чтение данных из таблицы WaaSInsiderStatus. |
> | Действия | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Чтение данных из таблицы WaaSUpdateStatus. |
> | Действия | Microsoft.Insights/Logs/WDAVStatus/Read | Чтение данных из таблицы WDAVStatus. |
> | Действия | Microsoft.Insights/Logs/WDAVThreat/Read | Чтение данных из таблицы WDAVThreat. |
> | Действия | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Чтение данных из таблицы WindowsClientAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/WindowsFirewall/Read | Чтение данных из таблицы WindowsFirewall. |
> | Действия | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Чтение данных из таблицы WindowsServerAssessmentRecommendation. |
> | Действия | Microsoft.Insights/Logs/WireData/Read | Чтение данных из таблицы WireData. |
> | Действия | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Чтение данных из таблицы WUDOAggregatedStatus. |
> | Действия | Microsoft.Insights/Logs/WUDOStatus/Read | Чтение данных из таблицы WUDOStatus. |
> | Действия | Microsoft.Insights/MetricAlerts/Delete | Удаление оповещения метрики. |
> | Действия | Microsoft.Insights/MetricAlerts/Read | Чтение оповещения метрики. |
> | Действия | Microsoft.Insights/MetricAlerts/Status/Read | Чтение состояния оповещения для метрики. |
> | Действия | Microsoft.Insights/MetricAlerts/Write | Создание или изменение оповещения метрики. |
> | Действия | Microsoft. Insights/Метрикбаселинес/чтение | Чтение базовых показателей метрик |
> | Действия | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Считывает определения метрик. |
> | Действия | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Считывает определения метрик. |
> | Действия | Microsoft.Insights/MetricDefinitions/Read | Считывает определения метрик. |
> | Действия | Microsoft. Insights/Метрикнамеспацес/чтение | Чтение пространств имен метрик |
> | Действия | Microsoft.Insights/Metrics/Action | Действие метрики |
> | Действия | Microsoft. Insights/метрики/Microsoft. Insights/Read | Считывает метрики. |
> | Действия | Microsoft.Insights/Metrics/providers/Metrics/Read | Считывает метрики. |
> | Действия | Microsoft.Insights/Metrics/Read | Считывает метрики. |
> | DataAction | Microsoft.Insights/Metrics/Write | Запись метрик. |
> | Действия | Microsoft.Insights/MigrateToNewpricingModel/Action | Перевод подписки на новую модель ценообразования. |
> | Действия | Microsoft.Insights/Operations/Read | Операции чтения |
> | Действия | Microsoft.Insights/Register/Action | Регистрирует поставщик Microsoft Insights. |
> | Действия | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Откат подписки к прежней версии модели ценообразования. |
> | Действия | Microsoft.Insights/ScheduledQueryRules/Delete | Удаление запланированного правила запроса. |
> | Действия | Microsoft.Insights/ScheduledQueryRules/Read | Считывание запланированного правила запроса. |
> | Действия | Microsoft.Insights/ScheduledQueryRules/Write | Запись запланированного правила запроса. |
> | Действия | Microsoft.Insights/Tenants/Register/Action | Инициализирует поставщик Microsoft Insights. |
> | Действия | Microsoft.Insights/Unregister/Action | Регистрирует поставщик Microsoft Insights. |
> | Действия | Microsoft.Insights/Webtests/Delete | Удаление конфигурации веб-теста. |
> | Действия | Microsoft.Insights/Webtests/GetToken/Read | Чтение токена веб-теста. |
> | Действия | Microsoft.Insights/Webtests/MetricDefinitions/Read | Чтение определений метрик веб-теста. |
> | Действия | Microsoft.Insights/Webtests/Metrics/Read | Чтение метрик веб-теста. |
> | Действия | Microsoft.Insights/Webtests/Read | Чтение конфигурации веб-теста. |
> | Действия | Microsoft.Insights/Webtests/Write | Запись конфигурации веб-теста. |
> | Действия | Microsoft. Insights/книги/удаление | Удаление книги |
> | Действия | Microsoft. Insights/книги/чтение | Чтение книги |
> | Действия | Microsoft. Insights/книги/запись | Создание или обновление книги |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Intune/diagnosticsettings/delete | Удаление параметра диагностики. |
> | Действия | Microsoft.Intune/diagnosticsettings/read | Чтение параметра диагностики. |
> | Действия | Microsoft.Intune/diagnosticsettings/write | Запись параметра диагностики. |
> | Действия | Microsoft.Intune/diagnosticsettingscategories/read | Чтение категорий параметра диагностики. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Иотцентрал/Апптемплатес/действие | Получает все доступные шаблоны приложений в Azure IoT Central |
> | Действия | Microsoft.IoTCentral/checkNameAvailability/action | Проверка доступности для имени приложения IoT Central |
> | Действия | Microsoft.IoTCentral/checkSubdomainAvailability/action | Проверка доступности для поддомена приложения IoT Central |
> | Действия | Microsoft.IoTCentral/IoTApps/delete | Удаление приложений IoT Central |
> | Действия | Microsoft.IoTCentral/IoTApps/read | Получение одного приложения IoT Central |
> | Действия | Microsoft.IoTCentral/IoTApps/write | Создание или обновлений приложений IoT Central |
> | Действия | Microsoft.IoTCentral/operations/read | Получение всех доступных операций в приложениях IoT Central |
> | Действия | Microsoft.IoTCentral/register/action | Регистрация подписки для поставщика ресурсов IoT Central Azure |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.IoTSpaces/Graph/delete | Удаляет ресурс Graph Microsoft.IoTSpaces |
> | Действия | Microsoft.IoTSpaces/Graph/read | Получает ресурсы Graph Microsoft.IoTSpaces |
> | Действия | Microsoft.IoTSpaces/Graph/write | Создает ресурс Graph Microsoft.IoTSpaces |
> | Действия | Microsoft.IoTSpaces/register/action | Регистрация подписки для поставщика ресурсов Graph Microsoft.IoTSpaces, чтобы обеспечить создание ресурсов. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.KeyVault/checkNameAvailability/read | Проверяет, является ли имя Key Vault допустимым и неиспользуемым. |
> | Действия | Microsoft.KeyVault/deletedVaults/read | Отображает свойства обратимо удаленных хранилищ Key Vault. |
> | Действия | Microsoft.KeyVault/hsmPools/delete | Удаление пула HSM. |
> | Действия | Microsoft.KeyVault/hsmPools/joinVault/action | Присоединение хранилища ключей к пулу HSM. |
> | Действия | Microsoft.KeyVault/hsmPools/read | Просмотр свойств пула HSM. |
> | Действия | Microsoft.KeyVault/hsmPools/write | Создание нового пула HSM или обновление свойств существующего пула HSM. |
> | Действия | Microsoft.KeyVault/locations/deletedVaults/purge/action | Очищает обратимо удаленное хранилище Key Vault. |
> | Действия | Microsoft.KeyVault/locations/deletedVaults/read | Отображает свойства обратимо удаленного Key Vault. |
> | Действия | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Уведомление Microsoft.KeyVault о том, что идет удаление виртуальной сети или подсети. |
> | Действия | Microsoft.KeyVault/locations/operationResults/read | Проверяет результат длительной операции. |
> | Действия | Microsoft.KeyVault/operations/read | Получение списка операций, доступных в поставщике ресурсов Microsoft.KeyVault. |
> | Действия | Microsoft.KeyVault/register/action | Регистрирует подписку. |
> | Действия | Microsoft.KeyVault/unregister/action | Отмена регистрации подписки. |
> | Действия | Microsoft.KeyVault/vaults/accessPolicies/write | Обновляет существующую политику доступа путем объединения, замены или добавления новой политики доступа в хранилище. |
> | Действия | Microsoft.KeyVault/vaults/delete | Удаляет Key Vault. |
> | Действия | Microsoft.KeyVault/vaults/deploy/action | Включает доступ к секретам в Key Vault при развертывании ресурсов Azure. |
> | Действия | Microsoft. KeyVault/хранилища/Евентгридфилтерс/удаление | Уведомляет Microsoft. KeyVault о том, что удаляется подписка EventGrid для Key Vault |
> | Действия | Microsoft. KeyVault/хранилища/Евентгридфилтерс/чтение | Уведомляет Microsoft. KeyVault о том, что просматривается подписка EventGrid для Key Vault |
> | Действия | Microsoft. KeyVault/Vault/Евентгридфилтерс/запись | Уведомляет Microsoft. KeyVault о том, что создается новая подписка EventGrid для Key Vault |
> | Действия | Microsoft.KeyVault/vaults/read | Отображает свойства Key Vault. |
> | Действия | Microsoft.KeyVault/vaults/secrets/read | Просмотр свойств секрета, но не его значения. |
> | Действия | Microsoft.KeyVault/vaults/secrets/write | Создайте новый секрет или обновите значение существующего секрета. |
> | Действия | Microsoft.KeyVault/vaults/write | Создает новое хранилище Key Vault или обновить свойства существующего. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Kusto/Clusters/активация/действие | Запускает кластер. |
> | Действия | Microsoft. Kusto/Clusters/Аттачеддатабасеконфигуратионс/Delete | Удаляет присоединенную конфигурацию базы данных Ресаурцекопи. |
> | Действия | Microsoft. Kusto/Clusters/Аттачеддатабасеконфигуратионс/Read | Считывает Ресаурцекопи конфигурации присоединенной базы данных. |
> | Действия | Microsoft. Kusto/Clusters/Аттачеддатабасеконфигуратионс/Write | Записывает Ресаурцекопи конфигурации присоединенной базы данных. |
> | Действия | Microsoft. Kusto/Clusters/Чеккнамеаваилабилити/действие | Проверяет доступность имени кластера. |
> | Действия | Microsoft. Kusto/Clusters/databases/АддпринЦипалс/Action | Добавляет участников базы данных. |
> | Действия | Microsoft. Kusto/Clusters/databases/Чеккнамеаваилабилити/Action | Проверяет доступность имени для заданного типа. |
> | Действия | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Удаляет подключения к данным Ресаурцекопи. |
> | Действия | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Считывает подключения к данным Ресаурцекопи. |
> | Действия | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Записывает подключения к данным Ресаурцекопи. |
> | Действия | Microsoft. Kusto/Clusters/databases/Датаконнектионвалидатион/Action | Проверяет подключение к данным базы данных. |
> | Действия | Microsoft.Kusto/Clusters/Databases/delete | Удаляет базу данных Ресаурцекопи. |
> | Действия | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Удаляет Ресаурцекопи подключений концентратора событий. |
> | Действия | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Считывает Ресаурцекопи подключений концентратора событий. |
> | Действия | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Записывает подключения к концентратору событий Ресаурцекопи. |
> | Действия | Microsoft. Kusto/Clusters/databases/Евенсубконнектионвалидатион/Action | Проверяет подключение концентратора событий базы данных. |
> | Действия | Microsoft. Kusto/Clusters/databases/ЛистпринЦипалс/Action | Выводит список участников базы данных. |
> | Действия | Microsoft.Kusto/Clusters/Databases/read | Считывает Ресаурцекопи базы данных. |
> | Действия | Microsoft. Kusto/Clusters/databases/РемовепринЦипалс/Action | Удаляет участников базы данных. |
> | Действия | Microsoft.Kusto/Clusters/Databases/write | Записывает Ресаурцекопи базы данных. |
> | Действия | Microsoft. Kusto/Clusters/Deactivate/действие | Останавливает кластер. |
> | Действия | Microsoft.Kusto/Clusters/delete | Удаляет кластер Ресаурцекопи. |
> | Действия | Microsoft.Kusto/Clusters/read | Считывает Ресаурцекопи кластера. |
> | Действия | Microsoft. Kusto/Clusters/SKU/чтение | Считывает номер SKU кластера Ресаурцекопи. |
> | Действия | Microsoft. Kusto/Clusters/запуск/действие | Запускает кластер. |
> | Действия | Microsoft. Kusto/Clusters/завершение/действие | Останавливает кластер. |
> | Действия | Microsoft.Kusto/Clusters/write | Записывает Ресаурцекопи кластера. |
> | Действия | Microsoft. Kusto/Детачфолловердатабасес/действие | Отсоединяет базы данных следа. |
> | Действия | Microsoft. Kusto/Листфолловердатабасес/действие | Выводит список баз данных следов. |
> | Действия | Microsoft. Kusto/Locations/Чеккнамеаваилабилити/Action | Проверяет доступность имени Ресаурцекопи. |
> | Действия | Microsoft.Kusto/locations/operationresults/read | Операции чтения Ресаурцекопис |
> | Действия | Microsoft.Kusto/Operations/read | Операции чтения Ресаурцекопис |
> | Действия | Microsoft. Kusto/Register/действие | Действие регистрации подписки |
> | Действия | Microsoft. Kusto/Register/действие | Регистрирует подписку в поставщике ресурсов Kusto. |
> | Действия | Microsoft. Kusto/SKU/чтение | Считывает номер SKU Ресаурцекопи. |
> | Действия | Microsoft. Kusto/отменить регистрацию или действие | Отменяет регистрацию подписки на поставщик ресурсов Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.LabServices/labAccounts/CreateLab/action | Создает лабораторию в учетной записи лаборатории. |
> | Действия | Microsoft.LabServices/labAccounts/delete | Удаление учетных записей лабораторий. |
> | Действия | Microsoft.LabServices/labAccounts/galleryImages/delete | Удаляет образы коллекции. |
> | Действия | Microsoft.LabServices/labAccounts/galleryImages/read | Считывает образы из коллекции. |
> | Действия | Microsoft.LabServices/labAccounts/galleryImages/write | Добавление или изменение образов из коллекции. |
> | Действия | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Позволяет получать сведения о доступности по регионам для каждой категории размеров, настроенной в учетной записи лаборатории. |
> | Действия | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Добавление пользователей в лабораторию |
> | Действия | Microsoft.LabServices/labAccounts/labs/delete | Удаляет лаборатории. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Удаление параметра среды. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Удаляет среды. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Считывает среды. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Сброс пароля пользователя в среде |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Запускает среду, запустив все ресурсы в ней. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Останавливает выполнение среды, остановив все ресурсы в ней. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Добавляет или изменяет среды. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Подготовка и отзыв необходимых ресурсов для параметра среды на основе текущего состояния параметра лаборатории или среды. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Чтение параметра среды. |
> | Действия | Microsoft. Лабсервицес/Лабаккаунтс/Labs/environmentSettings/Ресетпассворд/действие | Сбрасывает пароль на виртуальной машине шаблона. |
> | Действия | Microsoft. Лабсервицес/Лабаккаунтс/Labs/environmentSettings/Савеимаже/действие | Сохраняет образ текущего шаблона в общей коллекции в учетной записи лаборатории. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Удаляет расписания. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Считывает расписания. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Добавляет или изменяет расписания. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Запускает шаблон, запустив все ресурсы внутри него. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Останавливает шаблон путем остановки всех ресурсов внутри него. |
> | Действия | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Добавление или изменение параметра среды. |
> | Действия | Microsoft.LabServices/labAccounts/labs/read | Считывает лаборатории. |
> | Действия | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Отправка электронного письма со ссылкой для регистрации в лаборатории. |
> | Действия | Microsoft.LabServices/labAccounts/labs/users/delete | Удаление пользователей. |
> | Действия | Microsoft.LabServices/labAccounts/labs/users/read | Чтение пользователей. |
> | Действия | Microsoft.LabServices/labAccounts/labs/users/write | Добавление или изменение пользователей. |
> | Действия | Microsoft.LabServices/labAccounts/labs/write | Добавляет или изменяет лаборатории. |
> | Действия | Microsoft.LabServices/labAccounts/read | Чтение учетных записей лабораторий. |
> | Действия | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Удаление общих коллекций. |
> | Действия | Microsoft.LabServices/labAccounts/sharedGalleries/read | Чтение общих коллекций. |
> | Действия | Microsoft.LabServices/labAccounts/sharedGalleries/write | Добавление или изменение общих коллекций. |
> | Действия | Microsoft.LabServices/labAccounts/sharedImages/delete | Удаление общих образов. |
> | Действия | Microsoft.LabServices/labAccounts/sharedImages/read | Чтение общих образов. |
> | Действия | Microsoft.LabServices/labAccounts/sharedImages/write | Добавление или изменение общих образов. |
> | Действия | Microsoft.LabServices/labAccounts/write | Добавление или изменение учетных записей лабораторий. |
> | Действия | Microsoft.LabServices/locations/operations/read | Считывает операции. |
> | Действия | Microsoft.LabServices/register/action | Регистрирует подписку. |
> | Действия | Microsoft.LabServices/users/GetOperationBatchStatus/action | Получает состояние пакетной операции. |
> | Действия | Microsoft.LabServices/users/GetOperationStatus/action | Получение состояния длительной операции. |
> | Действия | Microsoft.LabServices/users/GetPersonalPreferences/action | Получение личных настроек для пользователя |
> | Действия | Microsoft. Лабсервицес/Users/Листалленвиронментс/Action | Список всех сред для пользователя |
> | Действия | Microsoft.LabServices/users/Register/action | Регистрация пользователя в управляемой лаборатории. |
> | Действия | Microsoft.LabServices/users/ResetPassword/action | Сброс пароля пользователя в среде |
> | Действия | Microsoft.LabServices/users/StartEnvironment/action | Запускает среду, запустив все ресурсы в ней. |
> | Действия | Microsoft.LabServices/users/StopEnvironment/action | Останавливает выполнение среды, остановив все ресурсы в ней. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Logic/integrationAccounts/agreements/delete | Удаление соглашения в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Получение URL-адреса обратного вызова для содержимого соглашения в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/agreements/read | Чтение соглашения в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/agreements/write | Создание или обновление соглашения в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/assemblies/delete | Удаление сборки в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Получение URL-адреса обратного вызова для содержимого сборки в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/assemblies/read | Чтение сборки в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/assemblies/write | Создание или обновление сборки в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Удаление конфигурации пакета в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Чтение конфигурации пакета в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Создание или обновление конфигурации пакета в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/certificates/delete | Удаление сертификата в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/certificates/read | Чтение сертификата в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/certificates/write | Создание или обновление сертификата в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/delete | Удаление учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/join/action | Присоединение к учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Получение URL-адреса обратного вызова для учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Получение ключей в хранилище ключей. |
> | Действия | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Регистрация событий отслеживания в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/maps/delete | Удаление сопоставления в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Получение URL-адреса обратного вызова для содержимого сопоставления в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/maps/read | Чтение сопоставления в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/maps/write | Создание или обновление сопоставления в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/partners/delete | Удаление партнера в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Получение URL-адреса обратного вызова для содержимого партнера в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/partners/read | Чтение партнера в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/partners/write | Создание или обновление партнера в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Чтение определений журналов для учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/read | Чтение учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Повторно создает секреты ключей доступа. |
> | Действия | Microsoft.Logic/integrationAccounts/schemas/delete | Удаление схемы в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Получение URL-адреса обратного вызова для содержимого схемы в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/schemas/read | Чтение схемы в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/schemas/write | Создание или обновление схемы в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/sessions/delete | Удаление сеанса в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/sessions/read | Чтение конфигурации пакета в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/sessions/write | Создание или обновление сеанса в учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationAccounts/write | Создание или обновление учетной записи интеграции. |
> | Действия | Microsoft.Logic/integrationServiceEnvironments/delete | Удаляет среду службы интеграции. |
> | Действия | Microsoft.Logic/integrationServiceEnvironments/join/action | Выполняет присоединение к среде службы интеграции. |
> | Действия | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Считывает операцию управляемого API для среды службы интеграции. |
> | Действия | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Считывает управляемый API для среды службы интеграции. |
> | Действия | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Считывание состояния операции среды службы интеграции. |
> | Действия | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Считывает определения метрик среды службы интеграции. |
> | Действия | Microsoft.Logic/integrationServiceEnvironments/read | Считывает среду службы интеграции. |
> | Действия | Microsoft.Logic/integrationServiceEnvironments/write | Создает или обновляет среду службы интеграции. |
> | Действия | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Получает рекомендуемые группы операций для рабочего процесса. |
> | Действия | Microsoft.Logic/locations/workflows/validate/action | Проверяет рабочий процесс. |
> | Действия | Microsoft.Logic/operations/read | Получение операции. |
> | Действия | Microsoft.Logic/register/action | Регистрация поставщика ресурсов Microsoft.Logic для заданной подписки. |
> | Действия | Microsoft.Logic/workflows/accessKeys/delete | Удаляет ключ доступа. |
> | Действия | Microsoft.Logic/workflows/accessKeys/list/action | Выводит список секретов ключей доступа. |
> | Действия | Microsoft.Logic/workflows/accessKeys/read | Считывает ключ доступа. |
> | Действия | Microsoft.Logic/workflows/accessKeys/regenerate/action | Повторно создает секреты ключей доступа. |
> | Действия | Microsoft.Logic/workflows/accessKeys/write | Создает или обновляет ключ доступа. |
> | Действия | Microsoft.Logic/workflows/delete | Удаляет рабочий процесс. |
> | Действия | Microsoft.Logic/workflows/disable/action | Отключает рабочий процесс. |
> | Действия | Microsoft.Logic/workflows/enable/action | Включает рабочий процесс. |
> | Действия | Microsoft.Logic/workflows/listCallbackUrl/action | Возвращает URL-адрес обратного вызова для рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/listSwagger/action | Возвращает определения Swagger рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/move/action | Назначает рабочему процессу, для которого задан идентификатор существующей подписки, группа ресурсов и (или) имя, другой идентификатор подписки, группу ресурсов и (или) имя. |
> | Действия | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Чтение параметров диагностики рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Чтение определений журналов рабочих процессов. |
> | Действия | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Чтение определений метрик рабочих процессов. |
> | Действия | Microsoft.Logic/workflows/read | Считывает рабочий процесс. |
> | Действия | Microsoft.Logic/workflows/regenerateAccessKey/action | Повторно создает секреты ключей доступа. |
> | Действия | Microsoft.Logic/workflows/run/action | Начинает выполнение рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Получение трассировок выражений для действия запуска рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/runs/actions/read | Считывает действие выполнения рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Получение трассировок выражений повторов для действия запуска рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/runs/actions/repetitions/read | Чтение повтора действия запуска рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Чтение журнала запросов, связанных с действиями повторного запуска рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Чтение журнала запросов действий, связанных с запуском рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Чтение повтора области для действия запуска рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/runs/cancel/action | Отменяет выполнение рабочего процесса. |
> | Действия | Microsoft. Logic/рабочие процессы/запуски и удаление | Удаляет выполнение рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/runs/operations/read | Считывает состояние операции выполнения рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/runs/read | Считывает данные о выполнении рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/suspend/action | Приостановка рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/triggers/histories/read | Считывает журналы триггера. |
> | Действия | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Повторяет отправку триггера рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Возвращает URL-адрес обратного вызова для триггера. |
> | Действия | Microsoft.Logic/workflows/triggers/read | Считывает триггер. |
> | Действия | Microsoft.Logic/workflows/triggers/reset/action | Сброс триггера. |
> | Действия | Microsoft.Logic/workflows/triggers/run/action | Выполняет триггер. |
> | Действия | Microsoft.Logic/workflows/triggers/setState/action | Настройка состояния триггера. |
> | Действия | Microsoft.Logic/workflows/validate/action | Проверяет рабочий процесс. |
> | Действия | Microsoft.Logic/workflows/versions/read | Считывает версию рабочего процесса. |
> | Действия | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Возвращает URL-адрес обратного вызова для триггера. |
> | Действия | Microsoft.Logic/workflows/write | Создает или обновляет рабочий процесс. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Перемещает связь плана предложения машинного обучения. |
> | Действия | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Считывает связь плана предложения машинного обучения. |
> | Действия | Microsoft.MachineLearning/commitmentPlans/delete | Удаляет план предложения машинного обучения. |
> | Действия | Microsoft.MachineLearning/commitmentPlans/join/action | Присоединяет план предложения машинного обучения. |
> | Действия | Microsoft.MachineLearning/commitmentPlans/read | Считывает план предложения машинного обучения. |
> | Действия | Microsoft.MachineLearning/commitmentPlans/write | Создает или обновляет план предложения машинного обучения. |
> | Действия | Microsoft.MachineLearning/locations/operationresults/read | Получение результата операции машинного обучения. |
> | Действия | Microsoft.MachineLearning/locations/operationsstatus/read | Получение состояния текущей операции машинного обучения. |
> | Действия | Microsoft.MachineLearning/operations/read | Получение операций машинного обучения. |
> | Действия | Microsoft.MachineLearning/register/action | Регистрирует подписку для поставщика ресурсов веб-службы машинного обучения и позволяет создавать веб-службы. |
> | Действия | Microsoft.MachineLearning/skus/read | Получение номеров SKU плана предложения машинного обучения. |
> | Действия | Microsoft.MachineLearning/webServices/action | Создает свойства региональной веб-службы для поддерживаемых регионов. |
> | Действия | Microsoft.MachineLearning/webServices/delete | Удаляет веб-службу машинного обучения. |
> | Действия | Microsoft.MachineLearning/webServices/listkeys/read | Получает ключи к веб-службе машинного обучения. |
> | Действия | Microsoft.MachineLearning/webServices/read | Считывает веб-службу машинного обучения. |
> | Действия | Microsoft.MachineLearning/webServices/write | Создает или обновляет веб-службу машинного обучения. |
> | Действия | Microsoft.MachineLearning/Workspaces/delete | Удаляет рабочую область машинного обучения. |
> | Действия | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Выводит список ключей для рабочей области машинного обучения. |
> | Действия | Microsoft.MachineLearning/Workspaces/read | Считывает рабочую область машинного обучения. |
> | Действия | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Повторно синхронизирует ключи учетной записи хранения, настроенной для рабочей области машинного обучения. |
> | Действия | Microsoft.MachineLearning/Workspaces/write | Создает или обновляет рабочую область машинного обучения. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Мачинелеарнингсервицес/Locations/компутеоператионсстатус/Read | Получает состояние определенной вычислительной операции. |
> | Действия | Microsoft.MachineLearningServices/locations/usages/read | Отчет об использовании для вычислительных ресурсов Машинного обучения Azure в подписке. |
> | Действия | Microsoft.MachineLearningServices/locations/vmsizes/read | Возвращает поддерживаемые размеры виртуальных машин. |
> | Действия | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Получает состояние определенной операции рабочей области. |
> | Действия | Microsoft.MachineLearningServices/register/action | Регистрирует подписку для поставщика ресурсов служб машинного обучения. |
> | Действия | Microsoft.MachineLearningServices/workspaces/computes/delete | Удаляет вычислительные ресурсы в рабочих областях служб машинного обучения. |
> | Действия | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Выводит список секретов для вычислительных ресурсов в рабочей области служб машинного обучения. |
> | Действия | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Выводит список узлов для вычислительного ресурса в рабочей области Служб машинного обучения. |
> | Действия | Microsoft.MachineLearningServices/workspaces/computes/read | Получает вычислительные ресурсы в рабочих областях служб машинного обучения. |
> | Действия | Microsoft.MachineLearningServices/workspaces/computes/write | Создает или обновляет вычислительные ресурсы в рабочих областях служб машинного обучения. |
> | Действия | Microsoft.MachineLearningServices/workspaces/delete | Удаляет рабочие области служб машинного обучения. |
> | DataAction | Microsoft. Мачинелеарнингсервицес/рабочие области/эксперименты/удаление | Удаляет эксперименты в рабочих областях Службы машинного обучения |
> | DataAction | Microsoft. Мачинелеарнингсервицес/рабочие области/эксперименты/чтение | Получение экспериментов в Службы машинного обучения рабочих областях |
> | DataAction | Microsoft. Мачинелеарнингсервицес/рабочие области/эксперименты/запись | Создает или обновляет эксперименты в Службы машинного обучения рабочих областях |
> | Действия | Microsoft.MachineLearningServices/workspaces/listKeys/action | Выводит список секретов для рабочей области служб машинного обучения. |
> | Действия | Microsoft.MachineLearningServices/workspaces/read | Получает рабочие области служб машинного обучения. |
> | Действия | Microsoft.MachineLearningServices/workspaces/write | Создает или обновляет рабочие области служб машинного обучения. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. ManagedIdentity/удостоверения/чтение | Возвращает существующее удостоверение, назначенное системой |
> | Действия | Microsoft.ManagedIdentity/register/action | Регистрирует подписку для поставщика ресурсов управляемых удостоверений. |
> | Действия | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Действие RBAC для назначения существующего пользовательского удостоверения для ресурса. |
> | Действия | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Удаление существующего пользовательского удостоверения. |
> | Действия | Microsoft.ManagedIdentity/userAssignedIdentities/read | Получение существующего пользовательского удостоверения. |
> | Действия | Microsoft.ManagedIdentity/userAssignedIdentities/write | Создание существующего пользовательского удостоверения или обновление связанных с ним тегов. |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. ManagedServices/Маркетплацерегистратиондефинитионс/Read | Извлекает список определений регистрации управляемых служб. |
> | Действия | Microsoft. ManagedServices/операции/чтение | Возвращает список операций управляемых служб. |
> | Действия | Microsoft. ManagedServices/Оператионстатусес/Read | Считывает состояние операции ресурса. |
> | Действия | Microsoft. ManagedServices/Register/действие | Регистрация в управляемых службах. |
> | Действия | Microsoft. ManagedServices/Регистратионассигнментс/Delete | Удаляет назначение регистрации управляемых служб. |
> | Действия | Microsoft. ManagedServices/Регистратионассигнментс/Read | Возвращает список назначений регистрации управляемых служб. |
> | Действия | Microsoft. ManagedServices/Регистратионассигнментс/запись | Добавление или изменение назначения регистрации управляемых служб. |
> | Действия | Microsoft. ManagedServices/Регистратиондефинитионс/Delete | Удаляет определение регистрации управляемых служб. |
> | Действия | Microsoft. ManagedServices/Регистратиондефинитионс/Read | Извлекает список определений регистрации управляемых служб. |
> | Действия | Microsoft. ManagedServices/Регистратиондефинитионс/запись | Добавление или изменение определения регистрации управляемых служб. |
> | Действия | Microsoft. ManagedServices/отменить регистрацию или действие | Отмените регистрацию в управляемых службах. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Management/checkNameAvailability/action | Проверка того, является ли имя указанной группы управления допустимым и уникальным. |
> | Действия | Microsoft.Management/getEntities/action | Вывод списка всех сущностей (групп управления, подписок и т. д.) для прошедшего проверку подлинности пользователя. |
> | Действия | Microsoft.Management/managementGroups/delete | Удаление группы управления. |
> | Действия | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | Действия | Microsoft.Management/managementGroups/subscriptions/delete | Отмена связывания подписки с группой управления. |
> | Действия | Microsoft.Management/managementGroups/subscriptions/write | Связывание существующей подписки с группой управления. |
> | Действия | Microsoft.Management/managementGroups/write | Создание или обновление группы управления. |
> | Действия | Microsoft.Management/register/action | Регистрация указанной подписки в Microsoft.Management. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Предоставляет доступ на чтение данных учетных записей карт. |
> | Действия | Microsoft.Maps/accounts/delete | Удаление учетной записи Maps. |
> | Действия | Microsoft.Maps/accounts/eventGridFilters/delete | Удаление фильтра сетки событий |
> | Действия | Microsoft.Maps/accounts/eventGridFilters/read | Получение фильтра сетки событий |
> | Действия | Microsoft.Maps/accounts/eventGridFilters/write | Создание или изменение фильтра сетки событий |
> | Действия | Microsoft.Maps/accounts/listKeys/action | Вывод списка ключей учетной записи Maps. |
> | Действия | Microsoft.Maps/accounts/read | Получение учетной записи Maps. |
> | Действия | Microsoft.Maps/accounts/regenerateKey/action | Создание нового первичного или вторичного ключа учетной записи Maps. |
> | Действия | Microsoft.Maps/accounts/write | Создание или обновление учетной записи Maps. |
> | Действия | Microsoft. Maps/Operations/Read | Чтение операций поставщика |
> | Действия | Microsoft.Maps/register/action | Регистрация поставщика. |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Возвращает соглашение. |
> | Действия | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Принимает подписанное соглашение. |
> | Действия | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Импортирует образ в запись контроля доступа конечного пользователя. |
> | Действия | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Возвращает файл конфигурации. |
> | Действия | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Сохраняет файл конфигурации. |
> | Действия | Microsoft.Marketplace/register/action | Регистрирует поставщик ресурсов Microsoft.Marketplace в подписке. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.MarketplaceApps/ClassicDevServices/delete | Выполнение операции DELETE в ресурсе классической службы разработки. |
> | Действия | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Получение ключей управления для ресурса классической службы разработки. |
> | Действия | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Получение URL-адреса единого входа для классической службы разработки. |
> | Действия | Microsoft.MarketplaceApps/ClassicDevServices/read | Выполнение операции GET в классической службе разработки. |
> | Действия | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Создание ключей управления для ресурса классической службы разработки. |
> | Действия | Microsoft.MarketplaceApps/Operations/read | Чтение операций для всех типов ресурсов. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Отменяет соглашение для заданного элемента Marketplace. |
> | Действия | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Возвращает соглашение для заданного элемента Marketplace. |
> | Действия | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Подписывает соглашение для заданного элемента Marketplace. |
> | Действия | Microsoft.MarketplaceOrdering/agreements/read | Возвращение всех соглашений в указанной подписке. |
> | Действия | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Получение соглашения для заданного элемента виртуальной машины Marketplace. |
> | Действия | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Подписывание соглашения или его отмена для заданного элемента виртуальной машины Marketplace. |
> | Действия | Microsoft. Маркетплацеордеринг/операции/чтение | Вывод списка всех возможных операций в API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Media/checknameavailability/action | Проверка доступности имени учетной записи служб мультимедиа. |
> | Действия | Microsoft.Media/locations/checkNameAvailability/action | Проверка доступности имени учетной записи служб мультимедиа. |
> | Действия | Microsoft.Media/mediaservices/accountfilters/delete | Удаление любого фильтра учетных записей. |
> | Действия | Microsoft.Media/mediaservices/accountfilters/read | Чтение любого фильтра учетных записей. |
> | Действия | Microsoft.Media/mediaservices/accountfilters/write | Создание или обновление фильтра учетных записей. |
> | Действия | Microsoft.Media/mediaservices/assets/assetfilters/delete | Удаление любого фильтра ресурсов. |
> | Действия | Microsoft.Media/mediaservices/assets/assetfilters/read | Чтение любого фильтра ресурсов. |
> | Действия | Microsoft.Media/mediaservices/assets/assetfilters/write | Создание или обновление фильтра ресурсов. |
> | Действия | Microsoft.Media/mediaservices/assets/delete | Удаление ресурса. |
> | Действия | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Получение ключа шифрования ресурса. |
> | Действия | Microsoft.Media/mediaservices/assets/listContainerSas/action | Вывод списка подписанных URL-адресов контейнера ресурсов. |
> | Действия | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Вывод списка указателей потоковой передачи для ресурса. |
> | Действия | Microsoft.Media/mediaservices/assets/read | Чтение ресурсов. |
> | Действия | Microsoft.Media/mediaservices/assets/write | Создание или обновление ресурсов. |
> | Действия | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Удаление политики ключей содержимого. |
> | Действия | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Получение свойств политики с секретами. |
> | Действия | Microsoft.Media/mediaservices/contentKeyPolicies/read | Чтение политики ключей содержимого. |
> | Действия | Microsoft.Media/mediaservices/contentKeyPolicies/write | Создание или обновление политики ключей содержимого. |
> | Действия | Microsoft.Media/mediaservices/delete | Удаление учетной записи служб мультимедиа. |
> | Действия | Microsoft.Media/mediaservices/eventGridFilters/delete | Удаление фильтра сетки событий. |
> | Действия | Microsoft.Media/mediaservices/eventGridFilters/read | Чтение фильтра сетки событий. |
> | Действия | Microsoft.Media/mediaservices/eventGridFilters/write | Создание или обновление фильтра сетки событий. |
> | Действия | Microsoft.Media/mediaservices/liveEventOperations/read | Чтение операции динамического события. |
> | Действия | Microsoft.Media/mediaservices/liveEvents/delete | Удаление динамического события. |
> | Действия | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Удаление выходных данных динамического события. |
> | Действия | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Чтение выходных данных динамического события. |
> | Действия | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Создание или обновление выходных данных динамического события. |
> | Действия | Microsoft.Media/mediaservices/liveEvents/read | Чтение динамического события. |
> | Действия | Microsoft.Media/mediaservices/liveEvents/reset/action | Сброс операции динамического события. |
> | Действия | Microsoft.Media/mediaservices/liveEvents/start/action | Запуск операции динамического события. |
> | Действия | Microsoft.Media/mediaservices/liveEvents/stop/action | Остановка выполнения операции динамического события. |
> | Действия | Microsoft.Media/mediaservices/liveEvents/write | Создание или обновление динамического события. |
> | Действия | Microsoft.Media/mediaservices/liveOutputOperations/read | Чтение операции с выходными данными динамического события. |
> | Действия | Microsoft.Media/mediaservices/read | Удаление учетной записи служб мультимедиа. |
> | Действия | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Чтение операции конечной точки потоковой передачи. |
> | Действия | Microsoft.Media/mediaservices/streamingEndpoints/delete | Удаление конечной точки потоковой передачи. |
> | Действия | Microsoft.Media/mediaservices/streamingEndpoints/read | Чтение конечной точки потоковой передачи. |
> | Действия | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Масштабирование операции конечной точки потоковой передачи. |
> | Действия | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Запуск операции конечной точки потоковой передачи. |
> | Действия | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Остановка выполнения операции конечной точки потоковой передачи. |
> | Действия | Microsoft.Media/mediaservices/streamingEndpoints/write | Создание или обновление конечной точки потоковой передачи. |
> | Действия | Microsoft.Media/mediaservices/streamingLocators/delete | Удаление потокового указателя. |
> | Действия | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Вывод списка ключей содержимого. |
> | Действия | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Вывод списка путей. |
> | Действия | Microsoft.Media/mediaservices/streamingLocators/read | Считывание потокового указателя. |
> | Действия | Microsoft.Media/mediaservices/streamingLocators/write | Создание или обновление потокового указателя. |
> | Действия | Microsoft.Media/mediaservices/streamingPolicies/delete | Удаление политики потоковой передачи. |
> | Действия | Microsoft.Media/mediaservices/streamingPolicies/read | Чтение политики потоковой передачи. |
> | Действия | Microsoft.Media/mediaservices/streamingPolicies/write | Создание или обновление политики потоковой передачи. |
> | Действия | Microsoft.Media/mediaservices/syncStorageKeys/action | Синхронизация ключей хранилища для присоединенной учетной записи хранения Azure. |
> | Действия | Microsoft.Media/mediaservices/transforms/delete | Удаление преобразования. |
> | Действия | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Отменяет задание. |
> | Действия | Microsoft.Media/mediaservices/transforms/jobs/delete | Удаление задания. |
> | Действия | Microsoft.Media/mediaservices/transforms/jobs/read | Считывание задания. |
> | Действия | Microsoft.Media/mediaservices/transforms/jobs/write | Создание или обновление задания. |
> | Действия | Microsoft.Media/mediaservices/transforms/read | Чтение преобразования. |
> | Действия | Microsoft.Media/mediaservices/transforms/write | Создание или обновление преобразования. |
> | Действия | Microsoft.Media/mediaservices/write | Создание или обновление учетной записи служб мультимедиа. |
> | Действия | Microsoft.Media/operations/read | Возвращает доступные операции. |
> | Действия | Microsoft.Media/register/action | Регистрация подписки для поставщика ресурсов служб мультимедиа и разрешение создания учетных записей служб мультимедиа. |
> | Действия | Microsoft.Media/unregister/action | Отмена регистрации подписки для поставщика ресурсов Media Services. |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Migrate/ассессментпрожектс/Ассессментоптионс/чтение | Получает параметры оценки, которые можно найти в указанном расположении |
> | Действия | Microsoft. Migrate/ассессментпрожектс/оценки/чтение | Выводит список оценок в пределах проекта. |
> | Действия | Microsoft. Migrate/ассессментпрожектс/удалить | Удаляет проект оценки. |
> | Действия | Microsoft. Migrate/ассессментпрожектс/группы/оценки/ассесседмачинес/чтение | Получение свойств компьютера, прошедшего оценку |
> | Действия | Microsoft. Migrate/ассессментпрожектс/группы/оценки/удаление | Удаляет оценку |
> | Действия | Microsoft. Migrate/ассессментпрожектс/группы/оценки/довнлоадурл/действие | Скачивает отчет оценки по URL-адресу. |
> | Действия | Microsoft. Migrate/ассессментпрожектс/группы/оценки/чтение | Получает свойства оценки. |
> | Действия | Microsoft. Migrate/ассессментпрожектс/группы/оценки/запись | Создает новую оценку или обновляет существующую |
> | Действия | Microsoft. Migrate/ассессментпрожектс/группы/удалить | Удаляет группу |
> | Действия | Microsoft. Migrate/ассессментпрожектс/группы/чтение | Получение свойств группы |
> | Действия | Microsoft. Migrate/ассессментпрожектс/группы/Упдатемачинес/действие | Обновление группы путем добавления или удаления компьютеров |
> | Действия | Microsoft. Migrate/ассессментпрожектс/группы/запись | Создает новую группу или обновляет существующую |
> | Действия | Microsoft. Migrate/ассессментпрожектс/хипервколлекторс/удаление | Удаляет сборщик HyperV. |
> | Действия | Microsoft. Migrate/ассессментпрожектс/хипервколлекторс/чтение | Возвращает свойства сборщика HyperV. |
> | Действия | Microsoft. Migrate/ассессментпрожектс/хипервколлекторс/запись | Создает новый сборщик HyperV или обновляет существующий сборщик HyperV. |
> | Действия | Microsoft. Migrate/ассессментпрожектс/Machines/Read | Получает свойства компьютера |
> | Действия | Microsoft. Migrate/ассессментпрожектс/чтение | Возвращает свойства проекта оценки. |
> | Действия | Microsoft. Migrate/ассессментпрожектс/вмвареколлекторс/удаление | Удаляет сборщик VMware. |
> | Действия | Microsoft. Migrate/ассессментпрожектс/вмвареколлекторс/чтение | Возвращает свойства сборщика VMware. |
> | Действия | Microsoft. Migrate/ассессментпрожектс/вмвареколлекторс/запись | Создает новый сборщик VMware или обновляет существующий сборщик VMware. |
> | Действия | Microsoft. Migrate/ассессментпрожектс/запись | Создает новый проект оценки или обновляет существующий проект оценки. |
> | Действия | Microsoft.Migrate/locations/assessmentOptions/read | Получает параметры оценки, которые можно найти в указанном расположении |
> | Действия | Microsoft.Migrate/locations/checknameavailability/action | Проверка доступности имени ресурса для заданной подписки в заданном расположении |
> | Действия | Microsoft. Migrate/мигратепрожектс/Датабасеинстанцес/чтение | Возвращает свойства экземпляра базы данных. |
> | Действия | Microsoft. Migrate/мигратепрожектс/databases/Read | Возвращает свойства базы данных. |
> | Действия | Microsoft. Migrate/мигратепрожектс/удалить | Удаляет проект миграции. |
> | Действия | Microsoft. Migrate/мигратепрожектс/Machines/Read | Получает свойства компьютера |
> | Действия | Microsoft. Migrate/мигратепрожектс/Мигративентс/удаление | Удаляет событие миграции. |
> | Действия | Microsoft. Migrate/мигратепрожектс/Мигративентс/чтение | Возвращает свойства событий миграции. |
> | Действия | Microsoft.Migrate/migrateprojects/read | Возвращает свойства проекта "миграция" |
> | Действия | Microsoft. Migrate/мигратепрожектс/Рефрешсуммари/действие | Обновляет сводку проекта по миграции |
> | Действия | Microsoft. Migrate/мигратепрожектс/Регистертул/действие | Регистрация средства в проекте миграции |
> | Действия | Microsoft. Migrate/мигратепрожектс/Solutions/Клеанупдата/Action | Очистка данных решения "миграция проекта" |
> | Действия | Microsoft. Migrate/мигратепрожектс/Solutions/Delete | Удаляет решение "миграция проекта" |
> | Действия | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Получает конфигурацию решения проекта миграции. |
> | Действия | Microsoft.Migrate/migrateprojects/solutions/read | Возвращает свойства решения "миграция проекта" |
> | Действия | Microsoft. Migrate/мигратепрожектс/решения/запись | Создает новое решение миграции проектов или обновляет существующее решение по переносу проекта |
> | Действия | Microsoft. Migrate/мигратепрожектс/запись | Создает новый проект миграции или обновляет существующий. |
> | Действия | Microsoft.Migrate/Operations/read | Список операций, доступных в поставщике ресурсов Microsoft.Migrate |
> | Действия | Microsoft.Migrate/projects/assessments/read | Выводит список оценок в пределах проекта. |
> | Действия | Microsoft.Migrate/projects/delete | Удаляет проект |
> | Действия | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Получение свойств компьютера, прошедшего оценку |
> | Действия | Microsoft.Migrate/projects/groups/assessments/delete | Удаляет оценку |
> | Действия | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Скачивает отчет оценки по URL-адресу. |
> | Действия | Microsoft.Migrate/projects/groups/assessments/read | Получает свойства оценки. |
> | Действия | Microsoft.Migrate/projects/groups/assessments/write | Создает новую оценку или обновляет существующую |
> | Действия | Microsoft.Migrate/projects/groups/delete | Удаляет группу |
> | Действия | Microsoft.Migrate/projects/groups/read | Получение свойств группы |
> | Действия | Microsoft.Migrate/projects/groups/write | Создает новую группу или обновляет существующую |
> | Действия | Microsoft.Migrate/projects/keys/action | Получает общие ключи проекта |
> | Действия | Microsoft.Migrate/projects/machines/read | Получает свойства компьютера |
> | Действия | Microsoft.Migrate/projects/read | Получает свойства проекта |
> | Действия | Microsoft.Migrate/projects/write | Создает новый проект или обновляет существующий |
> | Действия | Microsoft.Migrate/register/action | Регистрирует подписки в поставщике ресурсов Microsoft.Migrate |

## <a name="microsoftmixedreality"></a>Microsoft. Микседреалити

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Микседреалити/Register/действие | Регистрирует подписку для поставщика ресурсов Mixed Reality. |
> | DataAction | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/создание/действие | Создание пространственных привязок |
> | DataAction | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/Delete | Удаление пространственных привязок |
> | DataAction | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/обнаружение/чтение | Обнаружение ближайших пространственных привязок |
> | DataAction | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/свойства/чтение | Получение свойств пространственных привязок |
> | Действия | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/providers/Microsoft. Insights/diagnosticSettings/Read | Получает параметр диагностики для Microsoft. Микседреалити/Спатиаланчорсаккаунтс |
> | Действия | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/providers/Microsoft. Insights/diagnosticSettings/запись | Создает или обновляет параметр диагностики для Microsoft. Микседреалити/Спатиаланчорсаккаунтс |
> | Действия | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/providers/Microsoft. Insights/metricDefinitions/Read | Получение доступных метрик для Microsoft. Микседреалити/Спатиаланчорсаккаунтс |
> | DataAction | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запрос/чтение | Определение пространственных привязок |
> | DataAction | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/субмитдиаг/Read | Отправка диагностических данных для повышения качества службы пространственных привязок Azure |
> | DataAction | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запись | Обновить свойства пространственных привязок |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. NetApp/Locations/чеккфилепасаваилабилити/Action | Проверьте, доступен ли путь к файлу |
> | Действия | Microsoft. NetApp/Locations/чеккнамеаваилабилити/Action | Проверьте, доступно ли имя ресурса |
> | Действия | Microsoft.NetApp/locations/operationresults/read | Чтение ресурса результата операции. |
> | Действия | Microsoft.NetApp/locations/read | Чтение ресурса проверки доступности. |
> | Действия | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Удаление пула ресурсов. |
> | Действия | Microsoft.NetApp/netAppAccounts/capacityPools/read | Чтение пула ресурсов. |
> | Действия | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Удаление ресурса тома. |
> | Действия | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Чтение ресурса цели подключения. |
> | Действия | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Чтение ресурса тома. |
> | Действия | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Удаление ресурса моментального снимка. |
> | Действия | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Чтение ресурса моментального снимка. |
> | Действия | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Запись ресурса моментального снимка. |
> | Действия | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Запись ресурса тома. |
> | Действия | Microsoft.NetApp/netAppAccounts/capacityPools/write | Запись ресурса пула. |
> | Действия | Microsoft.NetApp/netAppAccounts/delete | Удаляет ресурс учетной записи. |
> | Действия | Microsoft.NetApp/netAppAccounts/read | Чтение ресурса учетной записи. |
> | Действия | Microsoft.NetApp/netAppAccounts/write | Запись ресурса учетной записи. |
> | Действия | Microsoft.NetApp/Operations/read | Чтение ресурсов операций. |
> | Действия | Microsoft. NetApp/Register/действие | Регистрирует подписку с помощью поставщика ресурсов Microsoft. NetApp. |
> | Действия | Microsoft. NetApp/отменить регистрацию или действие | Отмена регистрации подписки с помощью поставщика ресурсов Microsoft. NetApp. |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Получение доступных заголовков запроса шлюза приложений. |
> | Действия | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Получение доступного заголовка ответа шлюза приложений. |
> | Действия | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Получение доступных переменных сервера шлюза приложений. |
> | Действия | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Предопределенная политика SSL шлюза приложений. |
> | Действия | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Доступные параметры SSL шлюза приложений. |
> | Действия | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Возвращает доступные наборы правил WAF шлюза приложений. |
> | Действия | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Присоединяет внутренний пул адресов шлюза приложений. Не оповещать. |
> | Действия | Microsoft.Network/applicationGateways/backendhealth/action | Возвращает сведения о работоспособности серверной части шлюза приложений. |
> | Действия | Microsoft.Network/applicationGateways/delete | Удаляет шлюз приложений. |
> | Действия | Microsoft. Network/Аппликатионгатевайс/Жетбаккендхеалсондеманд/действие | Возвращает сведения о работоспособности серверной части шлюза приложений по запросу для указанного параметра HTTP и внутреннего пула. |
> | Действия | Microsoft.Network/applicationGateways/read | Возвращает шлюз приложений. |
> | Действия | Microsoft.Network/applicationGateways/start/action | Запускает шлюз приложений. |
> | Действия | Microsoft.Network/applicationGateways/stop/action | Останавливает шлюз приложений. |
> | Действия | Microsoft.Network/applicationGateways/write | Создает новый шлюз приложений или обновляет существующий. |
> | Действия | Microsoft. Network/АппликатионгатевайвебаппликатионфиреваллполиЦиес/удаление | Удаляет политику WAF шлюза приложений. |
> | Действия | Microsoft. Network/АппликатионгатевайвебаппликатионфиреваллполиЦиес/чтение | Возвращает политику WAF шлюза приложений. |
> | Действия | Microsoft. Network/АппликатионгатевайвебаппликатионфиреваллполиЦиес/запись | Создает политику WAF для шлюза приложений или обновляет политику WAF шлюза приложений. |
> | Действия | Microsoft.Network/applicationSecurityGroups/delete | Удаление группы безопасности приложений. |
> | Действия | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Присоединение конфигурации IP-адреса к группам безопасности приложений. Не оповещать. |
> | Действия | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Присоединение правила безопасности к группам безопасности приложений. Не оповещать. |
> | Действия | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Вывод списка конфигураций IP-адресов в ApplicationSecurityGroup. |
> | Действия | Microsoft.Network/applicationSecurityGroups/read | Получение идентификатора группы безопасности приложений. |
> | Действия | Microsoft.Network/applicationSecurityGroups/write | Создание группы безопасности приложений или обновление существующей группы. |
> | Действия | Microsoft.Network/azureFirewallFqdnTags/read | Получает теги полного доменного имени службы "Брандмауэр Azure" |
> | Действия | Microsoft.Network/azurefirewalls/delete | Удаляет службу "Брандмауэр Azure". |
> | Действия | Microsoft.Network/azurefirewalls/read | Получает службу "Брандмауэр Azure". |
> | Действия | Microsoft.Network/azurefirewalls/write | Создает или обновляет службу "Брандмауэр Azure". |
> | Действия | Microsoft.Network/bastionHosts/delete | Удаление узла-бастиона |
> | Действия | Microsoft.Network/bastionHosts/read | Получение узла-бастиона |
> | Действия | Microsoft.Network/bastionHosts/write | Создание или обновление узла-бастиона |
> | Действия | Microsoft.Network/bgpServiceCommunities/read | Возвращает сообщества службы BGP. |
> | Действия | Microsoft.Network/checkFrontDoorNameAvailability/action | Проверка доступности для имени Front Door. |
> | Действия | Microsoft.Network/checkTrafficManagerNameAvailability/action | Проверяет доступность относительного DNS-имени диспетчера трафика. |
> | Действия | Microsoft.Network/connections/delete | Удаляет подключение шлюза виртуальной сети. |
> | Действия | Microsoft.Network/connections/read | Возвращает подключение шлюза виртуальной сети. |
> | Действия | Microsoft.Network/connections/revoke/action | Помечает подключение ExpressRoute как отмененное. |
> | Действия | Microsoft.Network/connections/sharedkey/action | Получение общего ключа для подключения шлюза виртуальной сети. |
> | Действия | Microsoft.Network/connections/sharedKey/read | Возвращает общий ключ для подключения шлюза виртуальной сети. |
> | Действия | Microsoft.Network/connections/sharedKey/write | Создает новый общий ключ для подключения шлюза виртуальной сети или обновляет существующий. |
> | Действия | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Получение конфигурации VPN-устройства для подключения шлюза виртуальной сети. |
> | Действия | Microsoft.Network/connections/write | Создает новое подключение шлюза виртуальной сети или обновляет существующее. |
> | Действия | Microsoft.Network/ddosCustomPolicies/delete | Удаление политики, настроенной с учетом DDoS |
> | Действия | Microsoft.Network/ddosCustomPolicies/read | Получение определения политики, настроенной с учетом DDoS |
> | Действия | Microsoft.Network/ddosCustomPolicies/write | Создание политики, настроенной с учетом DDoS, или обновление такой существующей политики |
> | Действия | Microsoft.Network/ddosProtectionPlans/delete | Удаление плана защиты от атак DDoS. |
> | Действия | Microsoft.Network/ddosProtectionPlans/join/action | Присоединяет план защиты от атак DDoS. Не оповещать. |
> | Действия | Microsoft.Network/ddosProtectionPlans/read | Получение плана защиты от атак DDoS. |
> | Действия | Microsoft.Network/ddosProtectionPlans/write | Создание или обновление плана защиты от атак DDoS.  |
> | Действия | Microsoft.Network/dnsoperationresults/read | Возвращает результаты операции DNS. |
> | Действия | Microsoft.Network/dnsoperationstatuses/read | Возвращает состояние операции DNS.  |
> | Действия | Microsoft.Network/dnszones/A/delete | Удаляет из зоны DNS набор записей типа A с заданным именем. |
> | Действия | Microsoft.Network/dnszones/A/read | Возвращает набор записей типа A в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft.Network/dnszones/A/write | Создает или обновляет набора записей типа A в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft.Network/dnszones/AAAA/delete | Удаляет из зоны DNS набор записей типа AAAA с заданным именем. |
> | Действия | Microsoft.Network/dnszones/AAAA/read | Возвращает набор записей типа AAAA в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft.Network/dnszones/AAAA/write | Создает или обновляет набора записей типа AAAA в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft.Network/dnszones/all/read | Возвращает набор записей DNS разного типа. |
> | Действия | Microsoft.Network/dnszones/CAA/delete | Удаление из зоны DNS набора записей типа CAA с заданным именем. |
> | Действия | Microsoft.Network/dnszones/CAA/read | Получение набора записей типа CAA в формате JSON. Набор записей содержит срок жизни, теги и ETag. |
> | Действия | Microsoft.Network/dnszones/CAA/write | Создание или обновление набора записей типа CAA в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft.Network/dnszones/CNAME/delete | Удаляет из зоны DNS набор записей типа CNAME с заданным именем. |
> | Действия | Microsoft.Network/dnszones/CNAME/read | Возвращает набор записей типа CNAME в формате JSON. Набор записей содержит срок жизни, теги и ETag. |
> | Действия | Microsoft.Network/dnszones/CNAME/write | Создает или обновляет набора записей типа CNAME в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft.Network/dnszones/delete | Удаляет зону DNS в формате JSON. К свойствам зоны относятся tags, etag, numberOfRecordSets и maxNumberOfRecordSets. |
> | Действия | Microsoft.Network/dnszones/MX/delete | Удаляет из зоны DNS набор записей типа MX с заданным именем. |
> | Действия | Microsoft.Network/dnszones/MX/read | Возвращает набор записей типа MX в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft.Network/dnszones/MX/write | Создает или обновляет набора записей типа MX в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft.Network/dnszones/NS/delete | Удаляет набор записей DNS типа NS. |
> | Действия | Microsoft.Network/dnszones/NS/read | Возвращает набор записей DNS типа NS. |
> | Действия | Microsoft.Network/dnszones/NS/write | Создает или обновляет набор записей DNS типа NS. |
> | Действия | Microsoft.Network/dnszones/PTR/delete | Удаляет из зоны DNS набор записей типа PTR с заданным именем. |
> | Действия | Microsoft.Network/dnszones/PTR/read | Возвращает набор записей типа PTR в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft.Network/dnszones/PTR/write | Создает или обновляет набора записей типа PTR в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft.Network/dnszones/read | Возвращает зону DNS в формате JSON. К свойствам зоны относятся tags, etag, numberOfRecordSets и maxNumberOfRecordSets. Обратите внимание, что эта команда не извлекает наборы записей, содержащиеся в зоне. |
> | Действия | Microsoft.Network/dnszones/recordsets/read | Возвращает набор записей DNS разного типа. |
> | Действия | Microsoft.Network/dnszones/SOA/read | Возвращает набор записей DNS типа SOA. |
> | Действия | Microsoft.Network/dnszones/SOA/write | Создает или обновляет набор записей DNS типа SOA. |
> | Действия | Microsoft.Network/dnszones/SRV/delete | Удаляет из зоны DNS набор записей типа SRV с заданным именем. |
> | Действия | Microsoft.Network/dnszones/SRV/read | Возвращает набор записей типа SRV в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft.Network/dnszones/SRV/write | Создает или обновляет набор записей типа SRV. |
> | Действия | Microsoft.Network/dnszones/TXT/delete | Удаляет из зоны DNS набор записей типа TXT с заданным именем. |
> | Действия | Microsoft.Network/dnszones/TXT/read | Возвращает набор записей типа TXT в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft.Network/dnszones/TXT/write | Создает или обновляет набора записей типа TXT в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft.Network/dnszones/write | Создает или обновляет зону DNS в группе ресурсов.  Используется для обновления тегов в ресурсе зоны DNS. Обратите внимание, что эту команду невозможно использовать для создания или обновления наборов записей в зоне. |
> | Действия | Microsoft.Network/expressRouteCircuits/authorizations/delete | Удаляет данные авторизации канала ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/authorizations/read | Возвращает данные авторизации канала ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/authorizations/write | Создает новые данные авторизации канала ExpressRoute или обновляет существующие. |
> | Действия | Microsoft.Network/expressRouteCircuits/delete | Удаляет канал ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/join/action | Присоединяет канал Express Route. Не оповещать. |
> | Действия | Microsoft. Network/ЕкспрессраутеЦиркуитс/пиринг/Арптаблес/Read | Возвращает таблицу ARP для пиринга канала ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Удаление подключения канала ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Получение подключения канала ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Создание нового ресурса подключения канала ExpressRoute или обновление существующего. |
> | Действия | Microsoft.Network/expressRouteCircuits/peerings/delete | Удаляет пиринг канала ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Получает пиринговое подключение канала ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/peerings/read | Возвращает пиринг канала ExpressRoute. |
> | Действия | Microsoft. Network/ЕкспрессраутеЦиркуитс/пиринг/Раутетаблес/Read | Возвращает таблицу маршрутов для пиринга канала ExpressRoute. |
> | Действия | Microsoft. Network/ЕкспрессраутеЦиркуитс/пиринг/Раутетаблессуммари/Read | Возвращает сводку по таблице маршрутов для пиринга канала ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Возвращает статистику пиринга канала ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/peerings/write | Создает новый пиринг канала ExpressRoute или обновляет существующий. |
> | Действия | Microsoft.Network/expressRouteCircuits/read | Возвращает канал ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/stats/read | Возвращает статистику канала ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCircuits/write | Создает новый канал ExpressRoute или обновляет существующий. |
> | Действия | Microsoft.Network/expressRouteCrossConnections/join/action | Присоединяет перекрестное подключение Express Route. Не оповещать. |
> | Действия | Microsoft. Network/Експрессраутекроссконнектионс/пиринг/Арптаблес/Read | Получение таблицы ARP перекрестного пирингового подключения ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Удаление перекрестного пирингового подключения ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCrossConnections/peerings/read | Получение перекрестного пирингового подключения ExpressRoute. |
> | Действия | Microsoft. Network/Експрессраутекроссконнектионс/пиринг/Раутетаблес/Read | Получение таблицы маршрутов перекрестного пирингового подключения ExpressRoute. |
> | Действия | Microsoft. Network/Експрессраутекроссконнектионс/пиринг/Раутетаблесуммари/Read | Получение сводки таблицы маршрутов перекрестного пирингового подключения ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteCrossConnections/peerings/write | Создание перекрестного пирингового подключения ExpressRoute или обновление существующего. |
> | Действия | Microsoft.Network/expressRouteCrossConnections/read | Получение перекрестного подключения ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Удаляет подключение Express Route. |
> | Действия | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Получает подключение Express Route. |
> | Действия | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Создает подключение Express Route или обновляет имеющееся подключение. |
> | Действия | Microsoft.Network/expressRouteGateways/join/action | Присоединяется к шлюзу Express Route. Не оповещать. |
> | Действия | Microsoft.Network/expressRouteGateways/read | Получает шлюз Express Route. |
> | Действия | Microsoft.Network/expressRoutePorts/delete | Удаляет ExpressRoutePorts |
> | Действия | Microsoft.Network/expressRoutePorts/join/action | Соединяет порты Express Route. Не оповещать. |
> | Действия | Microsoft.Network/expressRoutePorts/links/read | Получает канал ExpressRoute. |
> | Действия | Microsoft.Network/expressRoutePorts/read | Получает ExpressRoutePorts |
> | Действия | Microsoft.Network/expressRoutePorts/write | Создает или обновляет ExpressRoutePorts |
> | Действия | Microsoft.Network/expressRoutePortsLocations/read | Получает порты расположений ExpressRoute. |
> | Действия | Microsoft.Network/expressRouteServiceProviders/read | Возвращает поставщики службы ExpressRoute. |
> | Действия | Microsoft. Network/ФиреваллполиЦиес/удаление | Удаляет политику брандмауэра. |
> | Действия | Microsoft. Network/ФиреваллполиЦиес/соединение/действие | Присоединяет политику брандмауэра. Не оповещать. |
> | Действия | Microsoft. Network/ФиреваллполиЦиес/чтение | Возвращает политику брандмауэра. |
> | Действия | Microsoft. Network/ФиреваллполиЦиес/Рулеграупс/удаление | Удаляет группу правил политики брандмауэра. |
> | Действия | Microsoft. Network/ФиреваллполиЦиес/Рулеграупс/чтение | Возвращает группу правил политики брандмауэра. |
> | Действия | Microsoft. Network/ФиреваллполиЦиес/Рулеграупс/запись | Создает группу правил политики брандмауэра или обновляет существующую группу правил политики брандмауэра. |
> | Действия | Microsoft. Network/ФиреваллполиЦиес/запись | Создание политики брандмауэра или обновление существующей политики брандмауэра. |
> | Действия | Microsoft.Network/frontDoors/backendPools/delete | Удаление внутреннего пула |
> | Действия | Microsoft.Network/frontDoors/backendPools/read | Получение внутреннего пула |
> | Действия | Microsoft.Network/frontDoors/backendPools/write | Создание или обновление внутреннего пула. |
> | Действия | Microsoft.Network/frontDoors/delete | Удаление Front Door |
> | Действия | Microsoft.Network/frontDoors/frontendEndpoints/delete | Удаление интерфейсной конечной точки |
> | Действия | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Отключение HTTPS для интерфейсной конечной точки. |
> | Действия | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Отключение протокола HTTPS для конечной точки интерфейса |
> | Действия | Microsoft.Network/frontDoors/frontendEndpoints/read | Получение интерфейсной конечной точки |
> | Действия | Microsoft.Network/frontDoors/frontendEndpoints/write | Создание или обновление интерфейсной конечной точки |
> | Действия | Microsoft.Network/frontDoors/healthProbeSettings/delete | Удаление параметров проверки работоспособности |
> | Действия | Microsoft.Network/frontDoors/healthProbeSettings/read | Получение параметров проверки работоспособности |
> | Действия | Microsoft.Network/frontDoors/healthProbeSettings/write | Создание или обновление параметров проверки работоспособности |
> | Действия | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Создание или обновление параметров балансировки нагрузки |
> | Действия | Microsoft.Network/frontDoors/loadBalancingSettings/read | Получение параметров балансировки нагрузки |
> | Действия | Microsoft.Network/frontDoors/loadBalancingSettings/write | Создание или обновление параметров балансировки нагрузки |
> | Действия | Microsoft.Network/frontDoors/purge/action | Очистка кэшированного содержимого из Front Door |
> | Действия | Microsoft.Network/frontDoors/read | Получение Front Door |
> | Действия | Microsoft.Network/frontDoors/routingRules/delete | Создание правила маршрутизации |
> | Действия | Microsoft.Network/frontDoors/routingRules/read | Получение правила маршрутизации |
> | Действия | Microsoft.Network/frontDoors/routingRules/write | Создание или обновление правила маршрутизации |
> | Действия | Microsoft.Network/frontDoors/validateCustomDomain/action | Проверка конечной точки интерфейса для Front Door |
> | Действия | Microsoft.Network/frontDoors/write | Создание или обновление Front Door |
> | Действия | Microsoft. Network/Фронтдурвебаппликатионфиреваллманажедрулесетс/чтение | Получает наборы управляемых правил брандмауэра веб-приложения |
> | Действия | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Удаление политики брандмауэра веб-приложения |
> | Действия | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Получение политики брандмауэра веб-приложения |
> | Действия | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Создание или обновление политики брандмауэра веб-приложения |
> | Действия | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Присоединяет внутренний пул адресов подсистемы балансировки нагрузки. Не оповещать. |
> | Действия | Microsoft.Network/loadBalancers/backendAddressPools/read | Возвращает определение внутреннего пула адресов подсистемы балансировки нагрузки. |
> | Действия | Microsoft.Network/loadBalancers/delete | Удаляет подсистему балансировки нагрузки. |
> | Действия | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Объединяет внешнюю IP-конфигурацию Load Balancer. Не оповещать. |
> | Действия | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Возвращает определение внешней IP-конфигурации подсистемы балансировки нагрузки. |
> | Действия | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Присоединяется к пулу входящих подключений NAT подсистемы балансировки нагрузки. Не оповещать. |
> | Действия | Microsoft.Network/loadBalancers/inboundNatPools/read | Возвращает определение пула входящих подключений NAT подсистемы балансировки нагрузки. |
> | Действия | Microsoft.Network/loadBalancers/inboundNatRules/delete | Удаляет правило NAT для входящего трафика подсистемы балансировки нагрузки. |
> | Действия | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединяет правило NAT для входящего трафика подсистемы балансировки нагрузки. Не оповещать. |
> | Действия | Microsoft.Network/loadBalancers/inboundNatRules/read | Возвращает определение правила NAT для входящего трафика подсистемы балансировки нагрузки. |
> | Действия | Microsoft.Network/loadBalancers/inboundNatRules/write | Создает новое правило NAT для входящего трафика подсистемы балансировки нагрузки или обновляет существующее. |
> | Действия | Microsoft.Network/loadBalancers/loadBalancingRules/read | Возвращает определение правила подсистемы балансировки нагрузки. |
> | Действия | Microsoft.Network/loadBalancers/networkInterfaces/read | Возвращает ссылки на все сетевые интерфейсы в подсистеме балансировки нагрузки. |
> | Действия | Microsoft.Network/loadBalancers/outboundRules/read | Получает определения исходящего правила подсистемы балансировки нагрузки |
> | Действия | Microsoft.Network/loadBalancers/probes/join/action | Позволяет использовать зонды подсистемы балансировки нагрузки. Например, при наличии этого разрешения свойство healthProbe в масштабируемом наборе виртуальных машин может ссылаться на конкретный зонд. Не оповещать. |
> | Действия | Microsoft.Network/loadBalancers/probes/read | Возвращает пробу подсистемы балансировки нагрузки. |
> | Действия | Microsoft.Network/loadBalancers/read | Возвращает определение подсистемы балансировки нагрузки. |
> | Действия | Microsoft.Network/loadBalancers/virtualMachines/read | Возвращает ссылки на все виртуальные машины в подсистеме балансировки нагрузки. |
> | Действия | Microsoft.Network/loadBalancers/write | Создает новую подсистему балансировки нагрузки или обновляет существующую. |
> | Действия | Microsoft.Network/localnetworkgateways/delete | Удаляет шлюз локальной сети. |
> | Действия | Microsoft.Network/localnetworkgateways/read | Возвращает шлюз локальной сети. |
> | Действия | Microsoft.Network/localnetworkgateways/write | Создает новый шлюз локальной сети или обновляет существующий. |
> | Действия | Microsoft. Network/Locations/Аутоаппроведпривателинксервицес/Read | Получает автоматические утвержденные службы частной связи |
> | Действия | Microsoft.Network/locations/availableDelegations/read | Получает доступные делегирования. |
> | Действия | Microsoft. Network/Locations/Аваилаблеприватиндпоинттипес/Read | Получение доступных ресурсов частной конечной точки |
> | Действия | Microsoft. Network/Locations/Аваилаблесервицеалиасес/Read | Получает доступные псевдонимы служб |
> | Действия | Microsoft.Network/locations/bareMetalTenants/action | Выделяет или проверяет клиент исходного состояния. |
> | Действия | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Проверяет поддержку ускоренной сети. |
> | Действия | Microsoft.Network/locations/checkDnsNameAvailability/read | Проверяет, доступна ли метка DNS в указанном расположении. |
> | Действия | Microsoft. Network/Locations/Чеккпривателинксервицевисибилити/Action | Проверяет видимость службы закрытых ссылок |
> | Действия | Microsoft.Network/locations/operationResults/read | Возвращает результат асинхронной операции POST или DELETE. |
> | Действия | Microsoft.Network/locations/operations/read | Возвращает ресурс операции, представляющий состояние асинхронной операции. |
> | Действия | Microsoft. Network/Locations/Сервицетагс/Read | Получить Теги службы |
> | Действия | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Возвращает поддерживаемые размеры виртуальных машин. |
> | Действия | Microsoft.Network/locations/usages/read | Возвращает метрики использования ресурсов. |
> | Действия | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Получение списка доступных служб конечных точек виртуальной сети. |
> | Действия | Microsoft.Network/networkIntentPolicies/delete | Удаляет политику намерений сети |
> | Действия | Microsoft.Network/networkIntentPolicies/read | Получает описание политики намерений сети |
> | Действия | Microsoft.Network/networkIntentPolicies/write | Создает политику намерения сети или обновляет существующую |
> | Действия | Microsoft.Network/networkInterfaces/delete | Удаляет сетевой интерфейс. |
> | Действия | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Возвращает группы безопасности сети, настроенные для сетевого интерфейса виртуальной машины. |
> | Действия | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Возвращает таблицу маршрутов, настроенную для сетевого интерфейса виртуальной машины. |
> | Действия | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Объединяет IP-конфигурацию сетевого интерфейса. Не оповещать. |
> | Действия | Microsoft.Network/networkInterfaces/ipconfigurations/read | Возвращает определение IP-конфигурации сетевого интерфейса.  |
> | Действия | Microsoft.Network/networkInterfaces/join/action | Присоединяет виртуальную машину к сетевому интерфейсу. Не оповещать. |
> | Действия | Microsoft.Network/networkInterfaces/loadBalancers/read | Возвращает все подсистемы балансировки нагрузки, в которых используется сетевой интерфейс. |
> | Действия | Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
> | Действия | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Удаляет конфигурацию TAP сетевого интерфейса. |
> | Действия | Microsoft.Network/networkInterfaces/tapConfigurations/read | Получает конфигурацию TAP сетевого интерфейса. |
> | Действия | Microsoft.Network/networkInterfaces/tapConfigurations/write | Создает конфигурацию TAP сетевого интерфейса или обновляет имеющуюся конфигурацию TAP сетевого интерфейса. |
> | Действия | Microsoft.Network/networkInterfaces/write | Создает новый сетевой интерфейс или обновляет существующий.  |
> | Действия | Microsoft.Network/networkProfiles/delete | Удаляет сетевой профиль |
> | Действия | Microsoft.Network/networkProfiles/read | Получает сетевой профиль |
> | Действия | Microsoft.Network/networkProfiles/removeContainers/action | Удаляет контейнеры |
> | Действия | Microsoft.Network/networkProfiles/setContainers/action | Задает контейнеры |
> | Действия | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Задает интерфейсы сетевых контейнеров |
> | Действия | Microsoft.Network/networkProfiles/write | Создает или обновляет сетевой профиль |
> | Действия | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Возвращает определение правила безопасности по умолчанию. |
> | Действия | Microsoft.Network/networkSecurityGroups/delete | Удаляет группу безопасности сети. |
> | Действия | Microsoft.Network/networkSecurityGroups/join/action | Присоединяет группу безопасности сети. Не оповещать. |
> | Действия | Microsoft.Network/networkSecurityGroups/read | Возвращает определение группы безопасности сети. |
> | Действия | Microsoft.Network/networkSecurityGroups/securityRules/delete | Удаляет правило безопасности. |
> | Действия | Microsoft.Network/networkSecurityGroups/securityRules/read | Возвращает определение правила безопасности. |
> | Действия | Microsoft.Network/networkSecurityGroups/securityRules/write | Создает новое правило безопасности или обновляет существующее. |
> | Действия | Microsoft.Network/networkSecurityGroups/write | Создает новую группу безопасности сети или обновляет существующую. |
> | Действия | Microsoft.Network/networkWatchers/availableProvidersList/action | Получение всех доступных поставщиков услуг Интернета в заданном регионе Azure. |
> | Действия | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Получение оценки относительной задержки для поставщиков услуг Интернета из указанного расположения в регионы Azure. |
> | Действия | Microsoft.Network/networkWatchers/configureFlowLog/action | Настраивает ведение журнала потоков для целевого ресурса. |
> | Действия | Microsoft.Network/networkWatchers/connectionMonitors/delete | Удаление монитора подключения. |
> | Действия | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Запрос мониторинга подключения между указанными конечными точками. |
> | Действия | Microsoft.Network/networkWatchers/connectionMonitors/read | Получение сведений о мониторе подключения. |
> | Действия | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Запуск мониторинга подключения между указанными конечными точками. |
> | Действия | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Остановка или приостановка мониторинга подключения между указанными конечными точками. |
> | Действия | Microsoft.Network/networkWatchers/connectionMonitors/write | Создание монитора подключения. |
> | Действия | Microsoft.Network/networkWatchers/connectivityCheck/action | Проверка возможности прямого подключения TCP между виртуальной машиной и определенной конечной точкой, при этом используется другая виртуальная машина или произвольный удаленный сервер. |
> | Действия | Microsoft.Network/networkWatchers/delete | Удаляет Наблюдатель за сетями. |
> | Действия | Microsoft.Network/networkWatchers/ipFlowVerify/action | Проверяет, разрешена ли передача или прием пакета для определенного места назначения. |
> | Действия | Microsoft.Network/networkWatchers/lenses/delete | Удаление группы связанных элементов. |
> | Действия | Microsoft.Network/networkWatchers/lenses/query/action | Запрос мониторинга трафика в указанной конечной точке. |
> | Действия | Microsoft.Network/networkWatchers/lenses/read | Получение сведений о группе связанных элементов. |
> | Действия | Microsoft.Network/networkWatchers/lenses/start/action | Запуск мониторинга трафика в указанной конечной точке. |
> | Действия | Microsoft.Network/networkWatchers/lenses/stop/action | Остановка или приостановка мониторинга трафика в указанной конечной точке. |
> | Действия | Microsoft.Network/networkWatchers/lenses/write | Создание группы связанных элементов. |
> | Действия | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Диагностика конфигурации сети. |
> | Действия | Microsoft.Network/networkWatchers/nextHop/action | Для указанного целевого объекта и конечного IP-адреса возвращает тип следующего прыжка и его IP-адрес. |
> | Действия | Microsoft.Network/networkWatchers/packetCaptures/delete | Удаляет запись пакетов. |
> | Действия | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Возвращает сведения о свойствах и состоянии ресурса записи пакетов. |
> | Действия | Microsoft.Network/networkWatchers/packetCaptures/read | Возвращает определение записи пакетов. |
> | Действия | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Останавливает выполняемый сеанс записи пакетов. |
> | Действия | Microsoft.Network/networkWatchers/packetCaptures/write | Создает запись пакетов. |
> | Действия | Microsoft.Network/networkWatchers/pingMeshes/delete | Удаляет PingMesh. |
> | Действия | Microsoft.Network/networkWatchers/pingMeshes/read | Получает сведения о PingMesh. |
> | Действия | Microsoft.Network/networkWatchers/pingMeshes/start/action | Запускает PingMesh между указанными виртуальными машинами. |
> | Действия | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Останавливает PingMesh между указанными виртуальными машинами. |
> | Действия | Microsoft.Network/networkWatchers/pingMeshes/write | Создает PingMesh. |
> | Действия | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Пакетный запрос мониторинга подключения между указанными конечными точками |
> | Действия | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Возвращает состояние ведения журнала потоков для ресурса. |
> | Действия | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Возвращает результат устранения неполадок предыдущей или текущей операции по устранению неполадок. |
> | Действия | Microsoft.Network/networkWatchers/read | Возвращает определение Наблюдателя за сетями. |
> | Действия | Microsoft.Network/networkWatchers/securityGroupView/action | Отображает настроенные и действующие правила группы безопасности сети для виртуальной машины. |
> | Действия | Microsoft.Network/networkWatchers/topology/action | Возвращает представление уровня сети для ресурсов и их связей в группе ресурсов. |
> | Действия | Microsoft.Network/networkWatchers/troubleshoot/action | Начинает устранение неполадок сетевого ресурса в Azure. |
> | Действия | Microsoft.Network/networkWatchers/write | Создает новый Наблюдатель за сетями или обновляет существующий. |
> | Действия | Microsoft.Network/operations/read | Возвращает доступные операции. |
> | Действия | Microsoft.Network/p2sVpnGateways/delete | Удаляет P2SVpnGateway. |
> | Действия | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Создание профиля Vpn для P2SVpnGateway |
> | Действия | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Получает данные о работоспособности VPN-подключения типа "точка — сеть" для P2SVpnGateway. |
> | Действия | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/действие | Получение сведений о работоспособности VPN-подключения P2S для P2SVpnGateway |
> | Действия | Microsoft.Network/p2sVpnGateways/read | Возвращает P2SVpnGateway. |
> | Действия | Microsoft.Network/p2sVpnGateways/write | Получает P2SVpnGateway. |
> | Действия | Microsoft. Network/Приватеднсоператионресултс/чтение | Возвращает результаты операции Частная зона DNS |
> | Действия | Microsoft. Network/Приватеднсоператионстатусес/чтение | Возвращает состояние операции Частная зона DNS |
> | Действия | Microsoft. Network/Приватеднсзонес/A/удаление | Удаление набора записей с заданным именем и типа "A" из Частная зона DNS зоны. |
> | Действия | Microsoft. Network/Приватеднсзонес/а/Read | Получение набора записей типа A в Частная зона DNSной зоне в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft. Network/Приватеднсзонес/A/запись | Создание или обновление набора записей типа A в Частная зона DNSной зоне. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft. Network/Приватеднсзонес, AAAA/удалить | Удаление набора записей с заданным именем и типа "AAAA" из зоны Частная зона DNS. |
> | Действия | Microsoft. Network/Приватеднсзонес/AAAA/чтение | Получите набор записей типа AAAA в Частная зона DNSной зоне в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft. Network/Приватеднсзонес, AAAA/запись | Создание или обновление набора записей типа AAAA в зоне Частная зона DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft. Network/Приватеднсзонес/все/чтение | Возвращает Частная зона DNS наборов записей между типами |
> | Действия | Microsoft. Network/Приватеднсзонес, CNAME/Delete | Удаление набора записей с заданным именем и типа "CNAME" из зоны Частная зона DNS. |
> | Действия | Microsoft. Network/Приватеднсзонес/запись CNAME/Read | Получите набор записей типа CNAME в Частная зона DNSной зоне в формате JSON. |
> | Действия | Microsoft. Network/Приватеднсзонес/CNAME/запись | Создание или обновление набора записей типа "CNAME" в зоне Частная зона DNS. |
> | Действия | Microsoft. Network/Приватеднсзонес/удаление | Удаление зоны Частная зона DNS. |
> | Действия | Microsoft. Network/Приватеднсзонес, MX/Delete | Удаление набора записей с заданным именем и типа MX из зоны Частная зона DNS. |
> | Действия | Microsoft. Network/Приватеднсзонес, MX/Read | Получите набор записей типа MX в зоне Частная зона DNS в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft. Network/Приватеднсзонес, MX/Write | Создание или обновление набора записей типа MX в зоне Частная зона DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft. Network/Приватеднсзонес, PTR/Delete | Удалить набор записей с заданным именем и типом "PTR" из зоны Частная зона DNS. |
> | Действия | Microsoft. Network/Приватеднсзонес/PTR/чтение | Получите набор записей типа PTR в Частная зона DNSной зоне в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft. Network/Приватеднсзонес/PTR/запись | Создание или обновление набора записей типа PTR в зоне Частная зона DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft. Network/Приватеднсзонес/чтение | Получение свойств зоны Частная зона DNS в формате JSON. Обратите внимание, что эта команда не извлекает виртуальные сети, к которым привязана Частная зона DNSная зона, или наборы записей, содержащиеся в зоне. |
> | Действия | Microsoft. Network/Приватеднсзонес/наборы записей/чтение | Возвращает Частная зона DNS наборов записей между типами |
> | Действия | Microsoft. Network/Приватеднсзонес/SOA/чтение | Получите набор записей типа SOA в зоне Частная зона DNS в формате JSON. |
> | Действия | Microsoft. Network/Приватеднсзонес/SOA/запись | Обновление набора записей типа "SOA" в зоне Частная зона DNS. |
> | Действия | Microsoft. Network/Приватеднсзонес, SRV/Delete | Удалить набор записей с заданным именем и типом "SRV" из зоны Частная зона DNS. |
> | Действия | Microsoft. Network/Приватеднсзонес/SRV/чтение | Получите набор записей типа SRV в Частная зона DNSной зоне в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft. Network/Приватеднсзонес/SRV/запись | Создание или обновление набора записей типа SRV в зоне Частная зона DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft. Network/Приватеднсзонес/TXT/удаление | Удаление набора записей с заданным именем и типа "TXT" из зоны Частная зона DNS. |
> | Действия | Microsoft. Network/Приватеднсзонес/TXT/чтение | Получите набор записей типа TXT в зоне Частная зона DNS в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действия | Microsoft. Network/Приватеднсзонес/TXT/запись | Создание или обновление набора записей типа "TXT" в зоне Частная зона DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действия | Microsoft. Network/Приватеднсзонес/Виртуалнетворклинкс/удаление | Удаление ссылки на зону Частная зона DNS в виртуальную сеть. |
> | Действия | Microsoft. Network/Приватеднсзонес/Виртуалнетворклинкс/чтение | Ссылка на зону Частная зона DNS в свойствах виртуальной сети в формате JSON. |
> | Действия | Microsoft. Network/Приватеднсзонес/Виртуалнетворклинкс/запись | Создайте или обновите ссылку на зону Частная зона DNS к виртуальной сети. |
> | Действия | Microsoft. Network/Приватеднсзонес/запись | Создание или обновление зоны Частная зона DNS в группе ресурсов. Обратите внимание, что эта команда не может использоваться для создания или обновления связей виртуальных сетей или наборов записей в зоне. |
> | Действия | Microsoft. Network/Приватиндпоинтредиректмапс/чтение | Получает закрытую конечную точку Редиректмап |
> | Действия | Microsoft. Network/Приватиндпоинтредиректмапс/запись | Создает закрытую конечную точку Редиректмап или обновляет существующую закрытую конечную точку Редиректмап |
> | Действия | Microsoft. Network/Приватиндпоинтс/удаление | Удаляет ресурс частной конечной точки. |
> | Действия | Microsoft. Network/Приватиндпоинтс/чтение | Получает ресурс частной конечной точки. |
> | Действия | Microsoft. Network/Приватиндпоинтс/запись | Создает новую частную конечную точку или обновляет существующую закрытую конечную точку. |
> | Действия | Microsoft.Network/privateLinkServices/delete | Удаляет ресурс службы частной связи. |
> | Действия | Microsoft. Network/Привателинксервицес/Приватиндпоинтконнектионс/удаление | Удаляет подключение частной конечной точки. |
> | Действия | Microsoft. Network/Привателинксервицес/Приватиндпоинтконнектионс/чтение | Возвращает определение подключения к частной конечной точке. |
> | Действия | Microsoft. Network/Привателинксервицес/Приватиндпоинтконнектионс/запись | Создает новое подключение к частной конечной точке или обновляет существующее подключение к частной конечной точке. |
> | Действия | Microsoft.Network/privateLinkServices/read | Получает ресурс службы частной связи. |
> | Действия | Microsoft.Network/privateLinkServices/write | Создает новую службу частной связи или обновляет существующую. |
> | Действия | Microsoft.Network/publicIPAddresses/delete | Удаляет общедоступный IP-адрес. |
> | Действия | Microsoft.Network/publicIPAddresses/join/action | Присоединяет общедоступный IP-адрес. Не оповещать. |
> | Действия | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Действия | Microsoft.Network/publicIPAddresses/write | Создает новый общедоступный IP-адрес или обновляет существующий.  |
> | Действия | Microsoft.Network/publicIPPrefixes/delete | Удаляет префикс общедоступного IP-адреса. |
> | Действия | Microsoft.Network/publicIPPrefixes/join/action | Присоединяется к ПублиЦиппрефикс. Не оповещать. |
> | Действия | Microsoft.Network/publicIPPrefixes/read | Возвращает определение префикса общедоступного IP-адреса. |
> | Действия | Microsoft.Network/publicIPPrefixes/write | Создает новый префикс общедоступного IP-адреса или обновляет существующий. |
> | Действия | Microsoft.Network/register/action | Регистрирует подписку. |
> | Действия | Microsoft.Network/routeFilters/delete | Удаляет определение фильтра маршрутов. |
> | Действия | Microsoft.Network/routeFilters/join/action | Присоединяет фильтр маршрутов. Не оповещать. |
> | Действия | Microsoft.Network/routeFilters/read | Возвращает определение фильтра маршрутов. |
> | Действия | Microsoft.Network/routeFilters/routeFilterRules/delete | Удаляет определение правила фильтра маршрутов. |
> | Действия | Microsoft.Network/routeFilters/routeFilterRules/read | Возвращает определение правила фильтра маршрутов. |
> | Действия | Microsoft.Network/routeFilters/routeFilterRules/write | Создает новое правило фильтра маршрутов или обновляет существующее. |
> | Действия | Microsoft.Network/routeFilters/write | Создает новый фильтр маршрутов или обновляет существующий. |
> | Действия | Microsoft.Network/routeTables/delete | Удаляет определение таблицы маршрутов. |
> | Действия | Microsoft.Network/routeTables/join/action | Присоединяет таблицу маршрутов. Не оповещать. |
> | Действия | Microsoft.Network/routeTables/read | Возвращает определение таблицы маршрутов. |
> | Действия | Microsoft.Network/routeTables/routes/delete | Удаляет определение маршрута. |
> | Действия | Microsoft.Network/routeTables/routes/read | Возвращает определение маршрута. |
> | Действия | Microsoft.Network/routeTables/routes/write | Создает новый маршрут или обновляет существующий. |
> | Действия | Microsoft.Network/routeTables/write | Создает новую таблицу маршрутов или обновляет существующую. |
> | Действия | Microsoft.Network/securegateways/delete | Удаление защищенного шлюза. |
> | Действия | Microsoft.Network/securegateways/read | Получение защищенного шлюза. |
> | Действия | Microsoft.Network/securegateways/write | Создание или обновление защищенного шлюза. |
> | Действия | Microsoft.Network/serviceEndpointPolicies/delete | Удаление политики конечной точки службы. |
> | Действия | Microsoft.Network/serviceEndpointPolicies/join/action | Присоединяет политику конечной точки службы. Не оповещать. |
> | Действия | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Присоединяет подсеть к политикам конечной точки службы. Не оповещать. |
> | Действия | Microsoft.Network/serviceEndpointPolicies/read | Получение описания политики конечной точки службы. |
> | Действия | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Удаление определения политики конечной точки службы. |
> | Действия | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Получение описания определения политики конечной точки службы. |
> | Действия | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Создание определения политики конечной точки службы или обновление существующего. |
> | Действия | Microsoft.Network/serviceEndpointPolicies/write | Создание политики конечной точки службы или обновление существующей. |
> | Действия | Microsoft.Network/trafficManagerGeographicHierarchies/read | Возвращает географическую иерархию диспетчера трафика, содержащую регионы, которые могут быть использованы для метода географической маршрутизации трафика. |
> | Действия | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Удаление конечной точки Azure из существующего профиля диспетчера трафика. Диспетчер трафика остановит маршрутизацию трафика в удаленную конечную точку Azure. |
> | Действия | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Получение конечной точки Azure, которая относится к профилю диспетчера трафика, а также свойств этой конечной точки. |
> | Действия | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Добавление новой конечной точки Azure в существующий профиль диспетчера трафика или обновление свойств существующей конечной точки Azure в этом профиле. |
> | Действия | Microsoft.Network/trafficManagerProfiles/delete | Удаляет профиль диспетчера трафика. Будут утрачены все параметры, связанные с профилем диспетчера трафика, и профиль больше не сможет использоваться для маршрутизации трафика. |
> | Действия | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Удаление внешней конечной точки из существующего профиля диспетчера трафика. Диспетчер трафика остановит маршрутизацию трафика в удаленную внешнюю конечную точку. |
> | Действия | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Получение внешней конечной точки, которая относится к профилю диспетчера трафика, а также свойств этой конечной точки. |
> | Действия | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Добавление новой внешней конечной точки в существующий профиль диспетчера трафика или обновление свойств существующей внешней конечной точки в этом профиле. |
> | Действия | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Получение тепловой карты диспетчера трафика для данного профиля диспетчера трафика, которая содержит количество запросов и данные задержек по исходному IP-адресу и расположению. |
> | Действия | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Удаление вложенной конечной точки из существующего профиля диспетчера трафика. Диспетчер трафика остановит маршрутизацию трафика в удаленную вложенную конечную точку. |
> | Действия | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Получение вложенной конечной точки, которая относится к профилю диспетчера трафика, а также свойств этой конечной точки. |
> | Действия | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Добавление новой вложенной конечной точки в существующий профиль диспетчера трафика или обновление свойств существующей вложенной конечной точки в этом профиле. |
> | Действия | Microsoft.Network/trafficManagerProfiles/read | Возвращает конфигурацию профиля диспетчера трафика. Он содержит параметры DNS, параметры маршрутизации трафика, параметры отслеживания конечных точек, а также список конечных точек, маршрутизируемых данным профилем диспетчера трафика. |
> | Действия | Microsoft.Network/trafficManagerProfiles/write | Создает профиль диспетчера трафика или изменяет конфигурацию существующего профиля диспетчера трафика.<br>Сюда входит включение или отключение профиля и изменение параметров DNS, параметров маршрутизации трафика или параметров мониторинга конечных точек.<br>Конечные точки, маршрутизируемые с помощью профиля диспетчера трафика, можно добавлять, удалять, включать и отключать. |
> | Действия | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Удаление ключа уровня подписки, используемого для сбора метрик пользователя в реальном времени. |
> | Действия | Microsoft.Network/trafficManagerUserMetricsKeys/read | Получение ключа уровня подписки, используемого для сбора метрик пользователя в реальном времени. |
> | Действия | Microsoft.Network/trafficManagerUserMetricsKeys/write | Создание нового ключа уровня подписки, используемого для сбора метрик пользователя в реальном времени. |
> | Действия | Microsoft.Network/unregister/action | Отменяет регистрацию подписки. |
> | Действия | Microsoft.Network/virtualHubs/delete | Удаление виртуального концентратора. |
> | Действия | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Удаление подключения к виртуальной сети концентратора. |
> | Действия | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Получение подключения к виртуальной сети концентратора. |
> | Действия | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Создание или обновление подключения к виртуальной сети концентратора. |
> | Действия | Microsoft.Network/virtualHubs/read | Получение виртуального концентратора. |
> | Действия | Microsoft. Network/Виртуалхубс/Раутетаблес/удаление | Удаление VirtualHubRouteTableV2 |
> | Действия | Microsoft. Network/Виртуалхубс/Раутетаблес/чтение | Получение VirtualHubRouteTableV2 |
> | Действия | Microsoft. Network/Виртуалхубс/Раутетаблес/запись | Создание или обновление VirtualHubRouteTableV2 |
> | Действия | Microsoft.Network/virtualHubs/write | Создание или обновление виртуального концентратора. |
> | Действия | microsoft.network/virtualnetworkgateways/connections/read | Получение подключения к шлюзу виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworkGateways/delete | Удаление шлюза виртуальной сети. |
> | Действия | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Создание пакета VPN-клиента для шлюза виртуальной сети. |
> | Действия | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Создание пакета VPN-профиля для шлюза виртуальной сети. |
> | Действия | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Получение объявленных маршрутов шлюза виртуальной сети. |
> | Действия | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Получение состояния кэширующего узла BGP шлюза виртуальной сети. |
> | Действия | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Получение обнаруженных маршрутов шлюза виртуальной сети. |
> | Действия | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Получение работоспособности подключения VPN-клиента для шлюза виртуальной сети. |
> | Действия | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Получение параметров IPsec VPN-клиента для клиента P2S шлюза виртуальной сети. |
> | Действия | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Получение URL-адреса предварительно созданного пакета профиля VPN-клиента. |
> | Действия | Microsoft.Network/virtualNetworkGateways/read | Получение шлюза виртуальной сети. |
> | Действия | microsoft.network/virtualnetworkgateways/reset/action | Сброс шлюза виртуальной сети. |
> | Действия | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Сброс общего ключа Vpnclient клиента P2S VirtualNetworkGateway. |
> | Действия | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Настройка параметров IPsec VPN-клиента для клиента P2S шлюза виртуальной сети. |
> | Действия | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Получение списка поддерживаемых VPN-устройств. |
> | Действия | Microsoft.Network/virtualNetworkGateways/write | Создание или обновление шлюза виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworks/BastionHosts/action | Получение ссылок узла-бастиона в виртуальной сети. |
> | Действия | Microsoft. Network/virtualNetworks/Бастионхостс/значение по умолчанию/действие | Получение ссылок узла-бастиона в виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Проверяет, доступен ли IP-адрес в указанной виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworks/delete | Удаляет виртуальную сеть. |
> | Действия | Microsoft. Network/virtualNetworks/соединение/действие | Присоединяет виртуальную сеть. Не оповещать. |
> | Действия | Microsoft.Network/virtualNetworks/peer/action | Создает пиринг между виртуальными сетями. |
> | Действия | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Действия | Microsoft. Network/virtualNetworks/подсети/КонтекстуалсервицеендпоинтполиЦиес/Delete | Удаление политики конечной точки зависимой службы |
> | Действия | Microsoft. Network/virtualNetworks/подсети/КонтекстуалсервицеендпоинтполиЦиес/чтение | Получает контекстные политики конечной точки службы |
> | Действия | Microsoft. Network/virtualNetworks/подсети/КонтекстуалсервицеендпоинтполиЦиес/запись | Создание контекстной политики конечной точки службы или обновление существующей политики конечной точки зависимой службы |
> | Действия | Microsoft.Network/virtualNetworks/subnets/delete | Удаляет подсеть виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworks/subnets/join/action | Присоединяет виртуальную сеть. Не оповещать. |
> | Действия | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. Не оповещать. |
> | Действия | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Подготавливает подсеть, применяя необходимые политики сети. |
> | Действия | Microsoft.Network/virtualNetworks/subnets/read | Возвращает определение подсети виртуальной сети. |
> | Действия | Microsoft. Network/virtualNetworks/подсети/УнпрепаренетворкполиЦиес/действие | Отмена подготовки подсети путем удаления примененных политик сети |
> | Действия | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Возвращает ссылки на все виртуальные машины в подсети виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworks/subnets/write | Создает новую подсеть пиринг виртуальной сети или обновляет существующую. |
> | Действия | Microsoft.Network/virtualNetworks/usages/read | Получение сведения об использовании IP-адресов для каждой подсети виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworks/virtualMachines/read | Возвращает ссылки на все виртуальные машины в виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Удаляет пиринг виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Возвращает определение пиринга виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Создает новый пиринг виртуальной сети или обновляет существующий. |
> | Действия | Microsoft.Network/virtualNetworks/write | Создает новую виртуальную сеть или обновляет существующую. |
> | Действия | Microsoft.Network/virtualNetworkTaps/delete | Удаление устройства перехватчика трафика (TAP) виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworkTaps/join/action | Присоединяется к отводам виртуальной сети. Не оповещать. |
> | Действия | Microsoft.Network/virtualNetworkTaps/read | Получение TAP виртуальной сети. |
> | Действия | Microsoft.Network/virtualNetworkTaps/write | Создание или обновление TAP виртуальной сети. |
> | Действия | Microsoft. Network/Виртуалраутерс/удаление | Удаляет Виртуалраутер |
> | Действия | Microsoft. Network/Виртуалраутерс/соединение/действие | Присоединяется к Виртуалраутер. Не оповещать. |
> | Действия | Microsoft. Network/Виртуалраутерс/чтение | Получает Виртуалраутер |
> | Действия | Microsoft. Network/Виртуалраутерс/Виртуалраутерпирингс/удаление | Удаляет Виртуалраутерпиринг |
> | Действия | Microsoft. Network/Виртуалраутерс/Виртуалраутерпирингс/чтение | Получает Виртуалраутерпиринг |
> | Действия | Microsoft. Network/Виртуалраутерс/Виртуалраутерпирингс/запись | Создает Виртуалраутерпиринг или обновляет существующий Виртуалраутерпиринг |
> | Действия | Microsoft. Network/Виртуалраутерс/запись | Создает Виртуалраутер или обновляет существующий Виртуалраутер |
> | Действия | Microsoft.Network/virtualWans/delete | Удаление виртуальной глобальной сети. |
> | Действия | Microsoft. Network/виртуалванс/Женератевпнпрофиле/действие | Создать Виртуалванвпнсерверконфигуратион Впнпрофиле |
> | Действия | Microsoft.Network/virtualWans/read | Получение виртуальной глобальной сети. |
> | Действия | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Получает поддерживаемых поставщиков безопасности виртуальной глобальной сети. |
> | Действия | Microsoft.Network/virtualWans/virtualHubs/read | Получение всех виртуальных концентраторов, ссылающихся на виртуальную глобальную сеть. |
> | Действия | Microsoft.Network/virtualwans/vpnconfiguration/action | Получение конфигурации VPN. |
> | Действия | Microsoft. Network/виртуалванс/Впнсерверконфигуратионс/действие | Получить Виртуалванвпнсерверконфигуратионс |
> | Действия | Microsoft.Network/virtualWans/vpnSites/read | Получение всех сетей VPN, ссылающихся на виртуальную глобальную сеть. |
> | Действия | Microsoft.Network/virtualWans/write | Создание или обновление виртуальной глобальной сети. |
> | Действия | Microsoft.Network/vpnGateways/delete | Удаляет VPN-шлюз. |
> | Действия | microsoft.network/vpngateways/listvpnconnectionshealth/action | Получает состояние работоспособности подключения для всех или подмножества подключений на шлюзе VpnGateway |
> | Действия | Microsoft.Network/vpnGateways/read | Получение шлюза VPN. |
> | Действия | microsoft.network/vpngateways/reset/action | Выполняет сброс VpnGateway |
> | Действия | microsoft.network/vpnGateways/vpnConnections/delete | Удаляет VpnConnection. |
> | Действия | microsoft.network/vpnGateways/vpnConnections/read | Получение VPN-подключения. |
> | Действия | Microsoft. Network/Впнгатевайс/Впнконнектионс/Впнлинкконнектионс/чтение | Возвращает подключение VPN-канала. |
> | Действия | microsoft.network/vpnGateways/vpnConnections/write | Установка VPN-подключения. |
> | Действия | Microsoft.Network/vpnGateways/write | Установка VPN-шлюза. |
> | Действия | Microsoft. Network/Впнсерверконфигуратионс/удаление | Удалить Впнсерверконфигуратион |
> | Действия | Microsoft. Network/Впнсерверконфигуратионс/чтение | Получить Впнсерверконфигуратион |
> | Действия | Microsoft. Network/Впнсерверконфигуратионс/запись | Создание или обновление Впнсерверконфигуратион |
> | Действия | Microsoft.Network/vpnsites/delete | Удаление ресурса сети VPN. |
> | Действия | Microsoft.Network/vpnsites/read | Получение ресурса сети VPN. |
> | Действия | Microsoft. Network/Впнситес/Впнсителинкс/чтение | Возвращает ссылку на VPN-сайт |
> | Действия | Microsoft.Network/vpnsites/write | Создание или обновление ресурса сети VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Проверяет, доступно ли имя заданного ресурса пространства имен в службе центра уведомлений. |
> | Действия | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Возвращает список описаний правил авторизации пространства имен. |
> | Действия | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию.  |
> | Действия | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Возвращает строку подключения к пространству имен. |
> | Действия | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Возвращает список описаний правил авторизации пространства имен. |
> | Действия | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Правило авторизации пространства имен повторно создает первичный или вторичный ключ. Укажите ключ, который должен быть создан повторно. |
> | Действия | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации. |
> | Действия | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Проверяет, доступно ли заданное имя центра уведомлений в пространстве имен. |
> | Действия | Microsoft.NotificationHubs/Namespaces/Delete | Удаляет ресурс пространства имен. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Возвращает список правил авторизации центра уведомлений. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Удаляет правила авторизации центра уведомлений. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Возвращает строку подключения к центру уведомлений. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Возвращает список правил авторизации центра уведомлений. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Правило авторизации центра уведомлений повторно создает первичный или вторичный ключ. Укажите ключ, который должен быть создан повторно. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Создает правила авторизации центра уведомлений и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Отправляет тестовое push-уведомление. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Удаляет ресурс центра уведомлений. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Возвращает список описаний ресурсов метрик пространства имен. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Возвращает все учетные данные PNS центра уведомлений. Сюда входят учетные данные WNS, MPNS, APNS, GCM и Baidu. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Возвращает список описаний ресурсов центра уведомлений. |
> | Действия | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Создает центр уведомлений и обновляет его свойства. К этим свойствам в основном относятся учетные данные PNS. Правила авторизации и срок жизни. |
> | Действия | Microsoft.NotificationHubs/Namespaces/read | Возвращает список описаний ресурсов пространства имен. |
> | Действия | Microsoft.NotificationHubs/Namespaces/write | Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен. |
> | Действия | Microsoft.NotificationHubs/operationResults/read | Возвращает результаты операции для поставщика концентраторов уведомлений |
> | Действия | Microsoft.NotificationHubs/operations/read | Возвращает список поддерживаемых операций для поставщика Центров уведомлений |
> | Действия | Microsoft.NotificationHubs/register/action | Регистрирует подписку для поставщика ресурсов центров уведомлений и позволяет создавать пространства имен и центры уведомлений. |
> | Действия | Microsoft.NotificationHubs/unregister/action | Отменяет регистрацию подписки для поставщика ресурсов центров уведомлений, позволяющей создавать пространства имен и центры уведомлений. |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.OffAzure/HyperVSites/clusters/read | Получает свойства кластера Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/clusters/write | Создает или обновляет кластер Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/delete | Удаляет сайт Hyper-V. |
> | Действия | Microsoft. Оффазуре/Хипервситес/хеалссуммари/Read | Получение сводки работоспособности для ресурса Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/hosts/read | Получает свойства узла Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/hosts/write | Создает или обновляет узел Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/jobs/read | Получает свойства заданий Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/machines/read | Получает свойства компьютеров Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Получает свойства состояния операции Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/read | Получает свойства сайта Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/refresh/action | Обновляет объекты в пределах сайта Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Получает свойства учетных записей запуска от имени Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/usage/read | Получает сведения об использовании сайта Hyper-V. |
> | Действия | Microsoft.OffAzure/HyperVSites/write | Создает или обновляет сайт Hyper-V. |
> | Действия | Microsoft.OffAzure/Operations/read | Чтение предоставленных операций. |
> | Действия | Microsoft.OffAzure/register/action | Регистрирует подписку в поставщике ресурсов Microsoft.OffAzure. |
> | Действия | Microsoft. Оффазуре/Серверситес/задания/чтение | Возвращает свойства заданий сервера. |
> | Действия | Microsoft. Оффазуре/Серверситес/Machines/Read | Возвращает свойства компьютеров сервера. |
> | Действия | Microsoft. Оффазуре/Серверситес/Machines/Write | Запись свойств серверных компьютеров |
> | Действия | Microsoft. Оффазуре/Серверситес/оператионсстатус/Read | Возвращает свойства состояния операции сервера. |
> | Действия | Microsoft. Оффазуре/Серверситес/Read | Возвращает свойства сайта сервера. |
> | Действия | Microsoft. Оффазуре/Серверситес/обновление/действие | Обновляет объекты на сайте сервера |
> | Действия | Microsoft. Оффазуре/Серверситес/рунасаккаунтс/Read | Возвращает свойства учетных записей запуска от имени сервера |
> | Действия | Microsoft. Оффазуре/Серверситес/использование/чтение | Возвращает сведения об использовании сайта сервера |
> | Действия | Microsoft. Оффазуре/Серверситес/запись | Создает или обновляет сайт сервера. |
> | Действия | Microsoft.OffAzure/VMwareSites/delete | Удаляет сайт VMware. |
> | Действия | Microsoft. Оффазуре/Вмвареситес/хеалссуммари/Read | Возвращает сводку по работоспособности для ресурса VMware. |
> | Действия | Microsoft.OffAzure/VMwareSites/jobs/read | Получает свойства заданий VMware. |
> | Действия | Microsoft.OffAzure/VMwareSites/machines/read | Получает свойства компьютеров VMware. |
> | Действия | Microsoft.OffAzure/VMwareSites/machines/start/action | Запускает компьютеры VMware. |
> | Действия | Microsoft.OffAzure/VMwareSites/machines/stop/action | Останавливает компьютеры VMware. |
> | Действия | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Получает свойства состояния операции VMware. |
> | Действия | Microsoft.OffAzure/VMwareSites/read | Получает свойства сайта VMware. |
> | Действия | Microsoft.OffAzure/VMwareSites/refresh/action | Обновляет объекты в пределах сайта VMware. |
> | Действия | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Получает свойства учетных записей запуска от имени VMware. |
> | Действия | Microsoft.OffAzure/VMwareSites/usage/read | Получает сведения об использовании сайта VMware. |
> | Действия | Microsoft.OffAzure/VMwareSites/vcenters/read | Получает свойства VMware vCenter. |
> | Действия | Microsoft.OffAzure/VMwareSites/vcenters/write | Создает или обновляет VMware vCenter. |
> | Действия | Microsoft.OffAzure/VMwareSites/write | Создает или обновляет сайт VMware. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.OperationalInsights/linkTargets/read | Выводит список существующих учетных записей, которые не связаны с подпиской Azure. Чтобы связать эту подписку Azure с рабочей областью, укажите идентификатор клиента, возвращаемый этой операцией, в свойстве идентификатора клиента в операции создания рабочей области. |
> | Действия | microsoft.operationalinsights/operations/read | Отображение списка всех доступных операций REST API OperationalInsights. |
> | Действия | Microsoft. operationalinsights/Register/действие | Рергистерс подписку. |
> | Действия | Microsoft.OperationalInsights/register/action | Регистрирует подписку для поставщика ресурсов. |
> | Действия | Microsoft. operationalinsights/отменить регистрацию или действие | Отменяет регистрацию подписки. |
> | Действия | Microsoft.OperationalInsights/workspaces/analytics/query/action | Поиск с помощью нового механизма. |
> | Действия | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Получение схемы поиска версии 2. |
> | Действия | Microsoft.OperationalInsights/workspaces/api/query/action | Поиск с помощью нового механизма. |
> | Действия | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Получение схемы поиска версии 2. |
> | Действия | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Удаляет область конфигурации. |
> | Действия | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Возвращает область конфигурации. |
> | Действия | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Задает область конфигурации. |
> | Действия | Microsoft.OperationalInsights/workspaces/datasources/delete | Удаляет источники данных в рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/datasources/read | Возвращает источники данных в рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/datasources/write | Создает или обновляет источники данных в рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/delete | Удаляет рабочую область. Если при создании рабочая область была связана с существующей рабочей областью, то эта связанная рабочая область не удаляется. |
> | Действия | Microsoft.OperationalInsights/workspaces/gateways/delete | Удаляет шлюз, настроенный для рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Создает сертификат регистрации для рабочей области. Этот сертификат используется для подключения Microsoft System Center Operations Manager к рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Отключает пакет аналитики для заданной рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Включает пакет аналитики для заданной рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Выводит список всех пакетов аналитики, которые являются видимыми для заданной рабочей области, а также сведения о том, включен или отключен пакет для этой рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Удаление связанной службы в заданной рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/linkedServices/read | Получение связанных служб в заданной рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/linkedServices/write | Создание или обновление связанных служб в заданной рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/listKeys/action | Получение списка ключей для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/listKeys/read | Получение списка ключей для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/managementGroups/read | Возвращает имена и метаданные для групп управления System Center Operations Manager, подключенных к этой рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Получение определений метрик в рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Удаление параметров уведомления пользователя для рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Получение параметров уведомления пользователя для рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Настройка параметров уведомления пользователя для рабочей области. |
> | Действия | Microsoft. operationalinsights/рабочие области/операции/чтение | Возвращает состояние операции рабочей области OperationalInsights. |
> | Действия | Microsoft.OperationalInsights/workspaces/purge/action | Удаление указанных данных из рабочей области. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Ааддомаинсервицесаккаунтлогон/чтение | Чтение данных из таблицы Ааддомаинсервицесаккаунтлогон |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Ааддомаинсервицесаккаунтманажемент/чтение | Чтение данных из таблицы Ааддомаинсервицесаккаунтманажемент |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Ааддомаинсервицесдиректорисервицеакцесс/чтение | Чтение данных из таблицы Ааддомаинсервицесдиректорисервицеакцесс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Ааддомаинсервицеслогонлогофф/чтение | Чтение данных из таблицы Ааддомаинсервицеслогонлогофф |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Ааддомаинсервицесполицичанже/чтение | Чтение данных из таблицы Ааддомаинсервицесполицичанже |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Ааддомаинсервицеспривилежеусе/чтение | Чтение данных из таблицы Ааддомаинсервицеспривилежеусе |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Ааддомаинсервицессистемсекурити/чтение | Чтение данных из таблицы Ааддомаинсервицессистемсекурити |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Чтение данных из таблицы ADAssessmentRecommendation. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Аддоназуребаккупалертс/чтение | Чтение данных из таблицы Аддоназуребаккупалертс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Аддоназуребаккупжобс/чтение | Чтение данных из таблицы Аддоназуребаккупжобс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Аддоназуребаккупполици/чтение | Чтение данных из таблицы Аддоназуребаккупполици |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Аддоназуребаккуппротектединстанце/чтение | Чтение данных из таблицы Аддоназуребаккуппротектединстанце |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Аддоназуребаккупстораже/чтение | Чтение данных из таблицы Аддоназуребаккупстораже |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Адфактивитирун/чтение | Чтение данных из таблицы Адфактивитирун |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Адфпипелинерун/чтение | Чтение данных из таблицы Адфпипелинерун |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Адфтригжеррун/чтение | Чтение данных из таблицы Адфтригжеррун |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Чтение данных из таблицы ADReplicationResult. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Чтение данных из таблицы ADSecurityAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/Alert/read | Чтение данных из таблицы Alert. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Чтение данных из таблицы AlertHistory. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Амлкомпутеклустеревент/чтение | Чтение данных из таблицы Амлкомпутеклустеревент |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Амлкомпутеклустернодивент/чтение | Чтение данных из таблицы Амлкомпутеклустернодивент |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Амлкомпутежобевент/чтение | Чтение данных из таблицы Амлкомпутежобевент |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Апиманажементгатевайлогс/чтение | Чтение данных из таблицы Апиманажементгатевайлогс |
> | Действия | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Чтение данных из таблицы AppCenterError |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Чтение данных из таблицы ApplicationInsights. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Аппплатформлогсфорспринг/чтение | Чтение данных из таблицы Аппплатформлогсфорспринг |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Аппсервицеенвиронментплатформлогс/чтение | Чтение данных из таблицы Аппсервицеенвиронментплатформлогс |
> | Действия | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Чтение данных из таблицы AuditLogs |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Аутоскаливалуатионслог/чтение | Чтение данных из таблицы Аутоскаливалуатионслог |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Аутоскалескалеактионслог/чтение | Чтение данных из таблицы Аутоскалескалеактионслог |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Авсклаудтраил/чтение | Чтение данных из таблицы Авсклаудтраил |
> | Действия | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Чтение данных из таблицы AzureActivity. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Азуреассессментрекоммендатион/чтение | Чтение данных из таблицы Азуреассессментрекоммендатион |
> | Действия | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Чтение данных из таблицы AzureMetrics. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Баиклустеревент/чтение | Чтение данных из таблицы Баиклустеревент |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Баиклустернодивент/чтение | Чтение данных из таблицы Баиклустернодивент |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Баижобевент/чтение | Чтение данных из таблицы Баижобевент |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Блоккчаинаппликатионлог/чтение | Чтение данных из таблицы Блоккчаинаппликатионлог |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Блоккчаинпроксилог/чтение | Чтение данных из таблицы Блоккчаинпроксилог |
> | Действия | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Чтение данных из таблицы BoundPort. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Чтение данных из таблицы CommonSecurityLog. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Чтение данных из таблицы ComputerGroup. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Чтение данных из таблицы ConfigurationChange. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Чтение данных из таблицы ConfigurationData. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Чтение данных из таблицы ContainerImageInventory. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Чтение данных из таблицы ContainerInventory. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Чтение данных из таблицы ContainerLog. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Контаинернодеинвентори/чтение | Чтение данных из таблицы Контаинернодеинвентори |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Контаинеррегистрилогиневентс/чтение | Чтение данных из таблицы Контаинеррегистрилогиневентс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Контаинеррегистрирепоситоревентс/чтение | Чтение данных из таблицы Контаинеррегистрирепоситоревентс |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Чтение данных из таблицы ContainerServiceLog. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Кореазуребаккуп/чтение | Чтение данных из таблицы Кореазуребаккуп |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Датабрикксаккаунтс/чтение | Чтение данных из таблицы Датабрикксаккаунтс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Датабрикксклустерс/чтение | Чтение данных из таблицы Датабрикксклустерс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Датабрикксдбфс/чтение | Чтение данных из таблицы Датабрикксдбфс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Датабрикксинстанцепулс/чтение | Чтение данных из таблицы Датабрикксинстанцепулс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Датабрикксжобс/чтение | Чтение данных из таблицы Датабрикксжобс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Датабриккснотебук/чтение | Чтение данных из таблицы Датабриккснотебук |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Датабриккссекретс/чтение | Чтение данных из таблицы Датабриккссекретс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Датабриккссклпермиссионс/чтение | Чтение данных из таблицы Датабриккссклпермиссионс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Датабриккссш/чтение | Чтение данных из таблицы Датабриккссш |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Датабриккстаблес/чтение | Чтение данных из таблицы Датабриккстаблес |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Датабрикксворкспаце/чтение | Чтение данных из таблицы Датабрикксворкспаце |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Чтение данных из таблицы DeviceAppCrash. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Чтение данных из таблицы DeviceAppLaunch. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Чтение данных из таблицы DeviceCalendar. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Чтение данных из таблицы DeviceCleanup. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Чтение данных из таблицы DeviceConnectSession. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Чтение данных из таблицы DeviceEtw. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Чтение данных из таблицы DeviceHardwareHealth. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Чтение данных из таблицы DeviceHealth. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Чтение данных из таблицы DeviceHeartbeat. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Чтение данных из таблицы DeviceSkypeHeartbeat. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Чтение данных из таблицы DeviceSkypeSignIn. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Чтение данных из таблицы DeviceSleepState. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Чтение данных из таблицы DHAppFailure. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Чтение данных из таблицы DHAppReliability. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Чтение данных из таблицы DHDriverReliability. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Чтение данных из таблицы DHLogonFailures. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Чтение данных из таблицы DHLogonMetrics. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Чтение данных из таблицы DHOSCrashData. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Чтение данных из таблицы DHOSReliability. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Чтение данных из таблицы DHWipAppLearning. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Чтение данных из таблицы DnsEvents. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Чтение данных из таблицы DnsInventory. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Чтение данных из таблицы ETWEvent. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/Event/read | Чтение данных из таблицы Event. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Чтение данных из таблицы ExchangeAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Чтение данных из таблицы ExchangeOnlineAssessmentRecommendation. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Фаилединжестион/чтение | Чтение данных из таблицы Фаилединжестион |
> | Действия | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Чтение данных из таблицы Heartbeat. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Хунтингбукмарк/чтение | Чтение данных из таблицы Хунтингбукмарк |
> | Действия | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Чтение данных из таблицы IISAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Чтение данных из таблицы InboundConnection. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Инсигхтсметрикс/чтение | Чтение данных из таблицы Инсигхтсметрикс |
> | Действия | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Чтение данных из таблицы IntuneAuditLogs. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Чтение данных из таблицы IntuneOperationalLogs. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Чтение данных из таблицы KubeEvents |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Кубехеалс/чтение | Чтение данных из таблицы Кубехеалс |
> | Действия | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Чтение данных из таблицы KubeNodeInventory. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Чтение данных из таблицы KubePodInventory. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Чтение данных из таблицы KubeServices |
> | Действия | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Чтение данных из таблицы LinuxAuditLog. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Чтение данных из таблицы MAApplication. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Чтение данных из таблицы MAApplicationHealth. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Считывает данные из таблицы MAApplicationHealthAlternativeVersions. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Считывает данные из таблицы MAApplicationHealthIssues. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Чтение данных из таблицы MAApplicationInstance. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Считывает данные из таблицы MAApplicationInstanceReadiness. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Чтение данных из таблицы MAApplicationReadiness. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Чтение данных из таблицы MADeploymentPlan. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Чтение данных из таблицы MADevice. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Считывание данных из таблицы MADeviceNotEnrolled |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Мадевиценрт/чтение | Чтение данных из таблицы Мадевиценрт |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Считывает данные из таблицы MADevicePnPHealth. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Считывает данные из таблицы MADevicePnPHealthAlternativeVersions. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Считывает данные из таблицы MADevicePnPHealthIssues. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Чтение данных из таблицы MADeviceReadiness. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Считывает данные из таблицы MADriverInstanceReadiness. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Чтение данных из таблицы MADriverReadiness. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Чтение данных из таблицы MAOfficeAddin. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Маоффицеаддинентитихеалс/чтение | Чтение данных из таблицы Маоффицеаддинентитихеалс |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Чтение данных из таблицы MAOfficeAddinHealth. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Маоффицеаддинхеалсевентнрт/чтение | Чтение данных из таблицы Маоффицеаддинхеалсевентнрт |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Считывает данные из таблицы MAOfficeAddinHealthIssues. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Чтение данных из таблицы MAOfficeAddinInstance. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Считывает данные из таблицы MAOfficeAddinInstanceReadiness. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Чтение данных из таблицы MAOfficeAddinReadiness. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Чтение данных из таблицы MAOfficeApp. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Маоффицеаппкрашеснрт/чтение | Чтение данных из таблицы Маоффицеаппкрашеснрт |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Чтение данных из таблицы MAOfficeAppHealth. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Чтение данных из таблицы MAOfficeAppInstance. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Маоффицеаппинстанцехеалс/чтение | Чтение данных из таблицы Маоффицеаппинстанцехеалс |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Чтение данных из таблицы MAOfficeAppReadiness. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Маоффицеаппсессионснрт/чтение | Чтение данных из таблицы Маоффицеаппсессионснрт |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Чтение данных из таблицы MAOfficeBuildInfo. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Чтение данных из таблицы MAOfficeCurrencyAssessment. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Чтение данных из таблицы MAOfficeCurrencyAssessmentDailyCounts. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Чтение данных из таблицы MAOfficeDeploymentStatus. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Маоффицедеплойментстатуснрт/чтение | Чтение данных из таблицы Маоффицедеплойментстатуснрт |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Маоффицемакроеррорнрт/чтение | Чтение данных из таблицы Маоффицемакроеррорнрт |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Маоффицемакроглобалхеалс/чтение | Чтение данных из таблицы Маоффицемакроглобалхеалс |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Считывает данные из таблицы MAOfficeMacroHealth. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Считывает данные из таблицы MAOfficeMacroHealthIssues. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Считывает данные из таблицы MAOfficeMacroIssueInstanceReadiness. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Чтение данных из таблицы MAOfficeMacroIssueReadiness. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Чтение данных из таблицы MAOfficeMacroSummary. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Чтение данных из таблицы MAOfficeSuite. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Чтение данных из таблицы MAOfficeSuiteInstance. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Чтение данных из таблицы MAProposedPilotDevices. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Чтение данных из таблицы MAWindowsBuildInfo. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Чтение данных из таблицы MAWindowsCurrencyAssessment. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Чтение данных из таблицы MAWindowsCurrencyAssessmentDailyCounts. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Чтение данных из таблицы MAWindowsDeploymentStatus. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Мавиндовсдеплойментстатуснрт/чтение | Чтение данных из таблицы Мавиндовсдеплойментстатуснрт |
> | Действия | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Считывает данные из таблицы MAWindowsSysReqInstanceReadiness. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Мкасшадовитрепортинг/чтение | Чтение данных из таблицы Мкасшадовитрепортинг |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Микрософтдаташарешарелог/чтение | Чтение данных из таблицы Микрософтдаташарешарелог |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Микрософсеалскареаписаудитлогс/чтение | Чтение данных из таблицы Микрософсеалскареаписаудитлогс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Микрософтинсигхтсазуреактивитилог/чтение | Чтение данных из таблицы Микрософтинсигхтсазуреактивитилог |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Микрософтвебаппликатионлог/чтение | Чтение данных из таблицы Микрософтвебаппликатионлог |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Микрософтвебфунктионексекутионлогс/чтение | Чтение данных из таблицы Микрософтвебфунктионексекутионлогс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Микрософтвебстдаутстдеррлог/чтение | Чтение данных из таблицы Микрософтвебстдаутстдеррлог |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/MicrosoftWebW3CLog/чтение | Чтение данных из таблицы MicrosoftWebW3CLog |
> | Действия | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Чтение данных из таблицы NetworkMonitoring. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Чтение данных из таблицы OfficeActivity. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/Operation/read | Чтение данных из таблицы Operation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Чтение данных из таблицы OutboundConnection. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/Perf/read | Чтение данных из таблицы Perf. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Чтение данных из таблицы ProtectionStatus. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/read | Выполнение запросов к данным в рабочей области |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Чтение данных из таблицы ReservedAzureCommonFields. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Чтение данных из таблицы ReservedCommonFields. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Чтение данных из таблицы SCCMAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Чтение данных из таблицы SCOMAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Чтение данных из таблицы SecurityAlert. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Чтение данных из таблицы SecurityBaseline. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Чтение данных из таблицы SecurityBaselineSummary. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Чтение данных из таблицы SecurityDetection. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Чтение данных из таблицы SecurityEvent. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Секуритиотравевент/чтение | Чтение данных из таблицы Секуритиотравевент |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Секуритирекоммендатион/чтение | Чтение данных из таблицы Секуритирекоммендатион |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Чтение данных из таблицы ServiceFabricOperationalEvent. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Чтение данных из таблицы ServiceFabricReliableActorEvent. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Чтение данных из таблицы ServiceFabricReliableServiceEvent. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Чтение данных из таблицы SfBAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Чтение данных из таблицы SfBOnlineAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Чтение данных из таблицы SharePointOnlineAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Чтение данных из таблицы SigninLogs |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Чтение данных из таблицы SPAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Чтение данных из таблицы SQLAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Чтение данных из таблицы SQLQueryPerformance. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Склсреатпротектионлогинаудитс/чтение | Чтение данных из таблицы Склсреатпротектионлогинаудитс |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Склвулнерабилитяссессментресулт/чтение | Чтение данных из таблицы Склвулнерабилитяссессментресулт |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Сукцеедединжестион/чтение | Чтение данных из таблицы Сукцеедединжестион |
> | Действия | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Чтение данных из таблицы Syslog. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Чтение данных из таблицы SysmonEvent. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Чтение данных из любого пользовательского журнала. |
> | Действия | Microsoft. OperationalInsights/рабочие области/запрос/Среатинтеллиженцеиндикатор/чтение | Чтение данных из таблицы Среатинтеллиженцеиндикатор |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Чтение данных из таблицы UAApp. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Чтение данных из таблицы UAComputer. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Чтение данных из таблицы UAComputerRank. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Чтение данных из таблицы UADriver. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Чтение данных из таблицы UADriverProblemCodes. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Чтение данных из таблицы UAFeedback. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Чтение данных из таблицы UAHardwareSecurity. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Чтение данных из таблицы UAIESiteDiscovery. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Чтение данных из таблицы UAOfficeAddIn. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Чтение данных из таблицы UAProposedActionPlan. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Чтение данных из таблицы UASysReqIssue. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Чтение данных из таблицы UAUpgradedComputer. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/Update/read | Чтение данных из таблицы Update. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Чтение данных из таблицы UpdateRunProgress. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Чтение данных из таблицы UpdateSummary. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/Usage/read | Чтение данных из таблицы Usage. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Считывает данные из таблицы VMBoundPort. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Считывает данные из таблицы VMConnection. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Чтение данных из таблицы W3CIISLog. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Чтение данных из таблицы WaaSDeploymentStatus. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Чтение данных из таблицы WaaSInsiderStatus. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Чтение данных из таблицы WaaSUpdateStatus. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Чтение данных из таблицы WDAVStatus. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Чтение данных из таблицы WDAVThreat. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Чтение данных из таблицы WindowsClientAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Чтение данных из таблицы WindowsEvent |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Чтение данных из таблицы WindowsFirewall. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Чтение данных из таблицы WindowsServerAssessmentRecommendation. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WireData/read | Чтение данных из таблицы WireData. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Чтение данных из таблицы WorkloadMonitoringPerf. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Чтение данных из таблицы WUDOAggregatedStatus. |
> | Действия | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Чтение данных из таблицы WUDOStatus. |
> | Действия | Microsoft.OperationalInsights/workspaces/read | Возвращает существующую рабочую область. |
> | Действия | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Повторно создает общий ключ указанной рабочей области |
> | Действия | microsoft.operationalinsights/workspaces/rules/read | Получение всех правил генерации оповещений. |
> | Действия | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Удаляет сохраненный поисковый запрос. |
> | Действия | Microsoft.OperationalInsights/workspaces/savedSearches/read | Возвращает сохраненный поисковый запрос. |
> | Действия | microsoft.operationalinsights/workspaces/savedsearches/results/read | Получение сохраненных результатов поиска. Не рекомендуется |
> | Действия | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Удаление запланированных действий поиска. |
> | Действия | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Получение запланированных действий поиска. |
> | Действия | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Создание или обновление запланированных действий поиска. |
> | Действия | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Удаление запланированного поиска. |
> | Действия | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Получение запланированного поиска. |
> | Действия | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Создание или обновление запланированного поиска. |
> | Действия | Microsoft.OperationalInsights/workspaces/savedSearches/write | Создает сохраненный поисковый запрос. |
> | Действия | Microsoft.OperationalInsights/workspaces/schema/read | Возвращает схему поиска для рабочей области.  Схема поиска содержит предоставленные поля и их типы. |
> | Действия | Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
> | Действия | microsoft.operationalinsights/workspaces/search/read | Получение результатов поиска. Не рекомендуется. |
> | Действия | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | Действия | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Удаляет конфигурацию хранилища. Это остановит чтение данных из учетной записи хранения, выполняемое Microsoft Operational Insights. |
> | Действия | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Возвращает конфигурацию хранилища. |
> | Действия | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Создает конфигурацию учетной записи хранения. Эти конфигурации используются для извлечения данных из расположения в существующей учетной записи хранения. |
> | Действия | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Получение журнала ошибок преобразования обновления поиска для рабочей области |
> | Действия | Microsoft.OperationalInsights/workspaces/usages/read | Возвращает данные об использовании для рабочей области, включая объем данных, считываемых рабочей областью. |
> | Действия | Microsoft.OperationalInsights/workspaces/write | Создает новую рабочую область или устанавливает связь с существующей рабочей областью с помощью указанного идентификатора клиента, полученного из нее. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.OperationsManagement/managementAssociations/delete | Удаление существующей связи управления. |
> | Действия | Microsoft.OperationsManagement/managementAssociations/read | Получение существующей связи управления. |
> | Действия | Microsoft.OperationsManagement/managementAssociations/write | Создание новой связи управления. |
> | Действия | Microsoft.OperationsManagement/managementConfigurations/delete | Удаление существующей конфигурации управления. |
> | Действия | Microsoft.OperationsManagement/managementConfigurations/read | Получение существующей конфигурации управления. |
> | Действия | Microsoft.OperationsManagement/managementConfigurations/write | Создание новой конфигурации управления. |
> | Действия | Microsoft.OperationsManagement/register/action | Регистрирует подписку для поставщика ресурсов. |
> | Действия | Microsoft.OperationsManagement/solutions/delete | Удаляет существующее решение OMS. |
> | Действия | Microsoft.OperationsManagement/solutions/read | Возвращает существующее решение OMS. |
> | Действия | Microsoft.OperationsManagement/solutions/write | Создает решение OMS. |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.PolicyInsights/asyncOperationResults/read | Получает результат асинхронной операции. |
> | DataAction | Microsoft. Полициинсигхтс/Чеккдатаполицикомплианце/действие | Проверьте состояние соответствия данного компонента политикам данных. |
> | Действия | Microsoft. Полициинсигхтс/операции/чтение | Получение поддерживаемых операций для пространства имен Microsoft. Полициинсигхтс |
> | DataAction | Microsoft. Полициинсигхтс/Полициевентс/Логдатаевентс/Action | Регистрация событий политики компонента ресурсов. |
> | Действия | Microsoft.PolicyInsights/policyEvents/queryResults/action | Запрашивает сведения о событиях политики. |
> | Действия | Microsoft.PolicyInsights/policyEvents/queryResults/read | Запрашивает сведения о событиях политики. |
> | Действия | Microsoft.PolicyInsights/policyStates/queryResults/action | Запрашивает сведения о состояниях политики. |
> | Действия | Microsoft.PolicyInsights/policyStates/queryResults/read | Запрашивает сведения о состояниях политики. |
> | Действия | Microsoft.PolicyInsights/policyStates/summarize/action | Запрашивает сводную информацию о последних состояниях политики. |
> | Действия | Microsoft.PolicyInsights/policyStates/summarize/read | Запрашивает сводную информацию о последних состояниях политики. |
> | Действия | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Активирует новую оценку соответствия для выбранной области. |
> | Действия | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Запрос сведений о ресурсах, необходимых для политик DeployIfNotExists. |
> | Действия | Microsoft.PolicyInsights/register/action | Регистрирует поставщик ресурсов Microsoft Policy Insights и включает на нем действия. |
> | Действия | Microsoft.PolicyInsights/remediations/cancel/action | Отмена выполняющихся исправлений политики Майкрософт. |
> | Действия | Microsoft.PolicyInsights/remediations/delete | Удаление исправлений политики. |
> | Действия | Microsoft.PolicyInsights/remediations/listDeployments/read | Перечисление развертываний, необходимых для исправления политики. |
> | Действия | Microsoft.PolicyInsights/remediations/read | Получение исправлений политики. |
> | Действия | Microsoft.PolicyInsights/remediations/write | Создание или обновление исправлений политик Майкрософт. |
> | Действия | Microsoft. Полициинсигхтс/отменить регистрацию или действие | Отменяет регистрацию поставщика ресурсов Microsoft Policy Insights. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Portal/consoles/delete | Удаляет Cloud Shell экземпляр. |
> | Действия | Microsoft. портал/консоли/чтение | Считывает экземпляр Cloud Shell. |
> | Действия | Microsoft.Portal/consoles/write | Создание или обновление экземпляра Cloud Shell. |
> | Действия | Microsoft.Portal/dashboards/delete | Удаление панели мониторинга из подписки. |
> | Действия | Microsoft.Portal/dashboards/read | Чтение панели мониторинга для подписки. |
> | Действия | Microsoft.Portal/dashboards/write | Добавление или изменение панели мониторинга для подписки. |
> | Действия | Microsoft.Portal/register/action | Регистрация на портале. |
> | Действия | Microsoft.Portal/usersettings/delete | Удаляет Cloud Shell параметры пользователя. |
> | Действия | Microsoft.Portal/usersettings/read | Считывает Cloud Shell параметры пользователя. |
> | Действия | Microsoft.Portal/usersettings/write | Создание или обновление Cloud Shell параметра пользователя. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.PowerBIDedicated/capacities/delete | Удаление выделенной емкости Power BI. |
> | Действия | Microsoft.PowerBIDedicated/capacities/read | Извлечение сведений об указанной выделенной емкости Power BI. |
> | Действия | Microsoft.PowerBIDedicated/capacities/resume/action | Возобновляет использование емкости. |
> | Действия | Microsoft.PowerBIDedicated/capacities/skus/read | Получение доступных сведений о номере SKU для емкости. |
> | Действия | Microsoft.PowerBIDedicated/capacities/suspend/action | Приостановка использования емкости. |
> | Действия | Microsoft.PowerBIDedicated/capacities/write | Создание или обновление указанной выделенной емкости Power BI. |
> | Действия | Microsoft. Повербидедикатед/Locations/Чеккнамеаваилабилити/Action | Проверка того, что имя выделенной емкости Power BI является допустимым и не используется. |
> | Действия | Microsoft.PowerBIDedicated/locations/operationresults/read | Получение сведений о результате указанной операции. |
> | Действия | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Извлечение сведений о состоянии указанной операции. |
> | Действия | Microsoft.PowerBIDedicated/operations/read | Извлечение сведений об операциях. |
> | Действия | Microsoft.PowerBIDedicated/register/action | Регистрация выделенного поставщика ресурсов Power BI. |
> | Действия | Microsoft.PowerBIDedicated/skus/read | Извлечение сведений о номерах SKU. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Действия | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocatedStamp является внутренней операцией, используемой службой. |
> | Действия | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Действия | Microsoft. RecoveryServices/Locations/Баккуппротектедитем/Write | Создает элемент, защищенный службой архивации. |
> | Действия | Microsoft. RecoveryServices/Locations/Баккуппротектедитемс/Read | Возвращает список всех защищенных элементов. |
> | Действия | Microsoft.RecoveryServices/Locations/backupStatus/action | Проверка состояния архивации хранилищ служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Проверка компонентов. |
> | Действия | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Проверка доступности имени ресурса использует API, позволяющий проверить, доступно ли имя ресурса. |
> | Действия | Microsoft.RecoveryServices/locations/operationStatus/read | Возвращает состояние операции для данной операции |
> | Действия | Microsoft.RecoveryServices/operations/read | Получение списка операций для поставщика ресурсов. |
> | Действия | Microsoft.RecoveryServices/register/action | Регистрация подписки для заданного поставщика ресурсов. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupconfig/read | Возвращает конфигурацию хранилища служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupconfig/write | Обновляет конфигурацию хранилища служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupEngines/read | Возвращение всех серверов управления архивацией, зарегистрированных в хранилище. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Удаление цели защиты для резервного копирования. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Получение цели защиты для резервного копирования. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Создание цели защиты для резервной копии. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Возвращает состояние операции. |
> | Действия | Microsoft. RecoveryServices/хранилища/Баккупфабрикс/Оператионсстатус/чтение | Возвращает состояние операции. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Получение всех защищаемых контейнеров. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Удаление зарегистрированного контейнера. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Запрос рабочих нагрузок в контейнере. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Получение всех элементов в контейнере. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Возвращает результат операции, выполненной с контейнером защиты. |
> | Действия | Microsoft. RecoveryServices/Vault/Баккупфабрикс/Протектионконтаинерс/Оператионсстатус/чтение | Возвращает состояние операции, выполненной с контейнером защиты. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Архивирует защищенный элемент. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Удаляет защищенный элемент. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Возвращает результат операции, выполненной с защищенными элементами. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Возвращает состояние операции, выполненной с защищенными элементами. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Возвращает сведения об объекте для защищенного элемента. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Подготавливает мгновенное восстановление для защищенного элемента. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Возвращает точки восстановления для защищенных элементов. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Восстанавливает точки восстановления для защищенных элементов. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Отменяет мгновенное восстановление для защищенного элемента. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Создает элемент, защищенный службой архивации. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Возвращает все зарегистрированные контейнеры. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Создание зарегистрированного контейнера. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Обновляет список контейнеров. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Отменяет задание. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Возвращает результат операции задания. |
> | Действия | Microsoft. RecoveryServices/хранилища/Баккупжобс/Оператионсстатус/чтение | Возвращает состояние операции задания. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupJobs/read | Возвращает все объекты заданий. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспортирует задания. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Возвращает результат операции архивации для хранилища служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupOperations/read | Возвращает состояние операции архивации для хранилища служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Удаляет политику защиты. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Возвращает результаты операции политики. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Возвращает состояние операции политики. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Создает политику защиты. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Возвращает список всех защищаемых элементов. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Возвращает список всех защищенных элементов. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Возвращает все контейнеры, принадлежащие подписке. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Вывод списка всех целей защиты для резервного копирования. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Возвращает данные ПИН-кода безопасности для хранилища служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Возвращает конфигурацию службы хранилища для хранилища служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Обновляет конфигурацию службы хранилища для хранилища служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Проверка операций для защищенного элемента |
> | Действия | Microsoft.RecoveryServices/Vaults/certificates/write | Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища. |
> | Действия | Microsoft.RecoveryServices/Vaults/delete | Операция удаления хранилища удаляет указанный ресурс Azure типа "хранилище". |
> | Действия | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действия | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действия | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действия | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Разрешение оповещения. |
> | Действия | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Возвращает конфигурацию уведомлений хранилища служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Настройка уведомлений по электронной почте в хранилище служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Действия | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Операцию отмены регистрации контейнера можно использовать для отмены регистрации контейнера. |
> | Действия | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
> | Действия | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
> | Действия | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Операцию регистрации контейнера в службе можно использовать для регистрации контейнера в службе восстановления. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Считывание параметров оповещений |
> | Действия | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Создание или обновление параметров оповещений |
> | Действия | Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывание события |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Проверяет согласованность структуры. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Удаление структуры |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Развертывает образ сервера обработки. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Перенос Fabric в AAD |
> | Действия | Microsoft. RecoveryServices/хранилища/Репликатионфабрикс/оператионресултс/чтение | Отслеживание результатов асинхронной операции в структурах ресурсов |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Считывание структуры |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Повторно задает связь шлюза. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Удаляет структуру. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Обновление сертификата для структуры. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Считывание логических сетей |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Считывание сети |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Удаление сетевых сопоставлений |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Считывание сетевых сопоставлений |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Создание или обновление сетевых сопоставлений |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Обнаруживает защищаемый элемент. |
> | Действия | Microsoft. RecoveryServices/Vault/Репликатионфабрикс/Репликатионпротектионконтаинерс/оператионресултс/чтение | Отследите результаты асинхронной операции с контейнерами защиты ресурсов. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Считывание контейнеров защиты |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Удаляет контейнер защиты. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Удаление любых элементов миграции. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Перенос элемента. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Чтение любых точек восстановления для миграции. |
> | Действия | Microsoft. RecoveryServices/Vault/Репликатионфабрикс/Репликатионпротектионконтаинерс/Репликатионмигратионитемс/оператионресултс/чтение | Отследите результаты асинхронной операции с элементами миграции ресурсов. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Чтение любых элементов миграции. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Тестирование миграции. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Очистка тестовой миграции. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Создание или обновление элементов миграции. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Считывание защищаемых элементов |
> | Действия | Microsoft. RecoveryServices/Vault/Репликатионфабрикс/Репликатионпротектионконтаинерс/Репликатионпротектедитемс/Адддискс/действие | Добавление дисков |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Применяет точку восстановления. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Удаление защищенных элементов |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Выполняет отработку отказа с фиксацией. |
> | Действия | Microsoft. RecoveryServices/Vault/Репликатионфабрикс/Репликатионпротектионконтаинерс/Репликатионпротектедитемс/оператионресултс/чтение | Отследите результаты асинхронной операции с защищенными элементами ресурсов. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Выполняет плановую отработку отказа. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Считывание защищенных элементов |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Считывание точек восстановления для репликации |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Удаляет защищенный элемент. |
> | Действия | Microsoft. RecoveryServices/Vault/Репликатионфабрикс/Репликатионпротектионконтаинерс/Репликатионпротектедитемс/Ремоведискс/действие | Удаление дисков |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Исправляет репликацию. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Повторно защищает защищенный элемент. |
> | Действия | Microsoft. RecoveryServices/Vault/Репликатионфабрикс/Репликатионпротектионконтаинерс/Репликатионпротектедитемс/Ресолвехеалсеррорс/действие |  |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Отправить отзыв |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Считывание целевых объемов вычислительных ресурсов |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Тестирование отработки отказа |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Тестирует очистку отработки отказа. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Переход на другой ресурс |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Обновляет службу Mobility Service. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Создание или обновление защищенных элементов |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Удаление сопоставлений контейнера защиты |
> | Действия | Microsoft. RecoveryServices/Vault/Репликатионфабрикс/Репликатионпротектионконтаинерс/Репликатионпротектионконтаинермаппингс/operationresults/чтение | Отследите результаты асинхронной операции с сопоставлениями контейнеров защиты ресурсов. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Считывание сопоставлений контейнера защиты |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Удаляет сопоставление контейнера защиты. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Создание или обновление сопоставлений контейнера защиты |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Переключает контейнер защиты. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Создание или обновление контейнеры защиты |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Удаление поставщиков службы восстановления |
> | Действия | Microsoft. RecoveryServices/Vault/Репликатионфабрикс/Репликатионрековерисервицеспровидерс/оператионресултс/чтение | Отследите результаты асинхронной операции с поставщиками служб восстановления ресурсов. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Считывание поставщиков службы восстановления |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Обновляет поставщик. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Удаляет поставщик служб восстановления. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Создание или обновление поставщиков служб восстановления |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Считывание классификации хранилища |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Удаление сопоставлений классификаций хранилища |
> | Действия | Microsoft. RecoveryServices/Vault/Репликатионфабрикс/Репликатионсторажеклассификатионс/Репликатионсторажеклассификатионмаппингс/operationresults/чтение | Отследите результаты асинхронной операции с сопоставлениями классификации хранилища ресурсов. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Считывание сопоставлений классификаций хранилища |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Создание или обновление сопоставлений классификаций хранилища |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Удаление vCenter |
> | Действия | Microsoft. RecoveryServices/Vault/Репликатионфабрикс/Репликатионвцентерс/оператионресултс/чтение | Отслеживание результатов асинхронной операции в ресурсах vCenters |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Чтение vCenter |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Создание или обновление vCenter |
> | Действия | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Создание или обновление Fabrics |
> | Действия | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Отменяет задание. |
> | Действия | Microsoft. RecoveryServices/хранилища/Репликатионжобс/оператионресултс/чтение | Отследите результаты асинхронной операции с заданиями ресурсов. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationJobs/read | Считывание задания |
> | Действия | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Перезапускает задание. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Возобновляет выполнение задания. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Чтение любых элементов миграции. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Считывание сетевых сопоставлений |
> | Действия | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Считывание сети |
> | Действия | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Удаление политики |
> | Действия | Microsoft. RecoveryServices/хранилища/РепликатионполиЦиес/оператионресултс/чтение | Отследите результаты асинхронной операции с политиками ресурсов. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Считывание политики |
> | Действия | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Создание или обновление политики |
> | Действия | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Считывание защищенных элементов |
> | Действия | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Считывание сопоставлений контейнера защиты |
> | Действия | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Считывание контейнеров защиты |
> | Действия | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Удаление планов восстановления |
> | Действия | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Выполняет отработку отказа с фиксацией для плана восстановления. |
> | Действия | Microsoft. RecoveryServices/хранилища/Репликатионрековерипланс/оператионресултс/чтение | Отследите результаты асинхронной операции с планами восстановления ресурсов. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Выполняет плановую отработку отказа для плана восстановления. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Считывание планов восстановления |
> | Действия | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Повторно защищает план восстановления. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Тестирует отработку отказа для плана восстановления. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Тестирует очистку отработки отказа для плана восстановления. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Выполняет отработку отказа для плана восстановления. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Создание или обновление планов восстановления |
> | Действия | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Считывание поставщиков службы восстановления |
> | Действия | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Считывание сопоставлений классификаций хранилища |
> | Действия | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Считывание классификации хранилища |
> | Действия | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Чтение любого элемента.  |
> | Действия | Microsoft.RecoveryServices/vaults/replicationUsages/read | Чтение данных об использовании хранилищ репликации |
> | Действия | Microsoft. RecoveryServices/хранилища/Репликатионваулсеалс/оператионресултс/чтение | Отследите результаты асинхронной операции с работоспособностью репликации хранилища ресурсов. |
> | Действия | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Считывание всех сведений о работоспособности репликации хранилища |
> | Действия | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Обновление сведений о работоспособности хранилища |
> | Действия | Microsoft. RecoveryServices/хранилища/Репликатионваултсеттингс/чтение | Чтение любого элемента.  |
> | Действия | Microsoft. RecoveryServices/Vault/Репликатионваултсеттингс/запись | Создание или обновление любого  |
> | Действия | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Чтение vCenter |
> | Действия | Microsoft.RecoveryServices/vaults/usages/read | Считывание данных об использовании хранилищ |
> | Действия | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Действия | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища. |
> | Действия | Microsoft.RecoveryServices/Vaults/write | Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище". |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Relay/checkNameAvailability/action | Проверяет доступность пространства имен в заданной подписке. |
> | Действия | Microsoft.Relay/checkNamespaceAvailability/action | Проверяет доступность пространства имен в заданной подписке. Этот API не рекомендуется. Вместо него используйте CheckNameAvailability. |
> | Действия | Microsoft.Relay/namespaces/authorizationRules/action | Обновление правила авторизации для пространства имен. Это нерекомендуемый API. Чтобы обновить правило авторизации для пространства имен, используйте вызов PUT. Эта операция не поддерживается для API версии 2017-04-01. |
> | Действия | Microsoft.Relay/namespaces/authorizationRules/delete | Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию.  |
> | Действия | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Возвращает строку подключения к пространству имен. |
> | Действия | Microsoft.Relay/namespaces/authorizationRules/read | Возвращает список описаний правил авторизации пространства имен. |
> | Действия | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действия | Microsoft.Relay/namespaces/authorizationRules/write | Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации. |
> | Действия | Microsoft.Relay/namespaces/Delete | Удаляет ресурс пространства имен. |
> | Действия | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Получение ключей правил авторизации основного пространства имен для аварийного восстановления. |
> | Действия | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Получение правил авторизации основного пространства имен для аварийного восстановления. |
> | Действия | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Отключение аварийного восстановления и остановка репликации изменений из основного пространства имен в дополнительное. |
> | Действия | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Проверка доступности псевдонима пространства имен в заданной подписке. |
> | Действия | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Удаление конфигурации аварийного восстановления, связанной с пространством имен. Эта операция может быть вызвана только через основное пространство имен. |
> | Действия | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Вызов отработки отказа географического аварийного восстановления и перенастройка псевдонима пространства имен для указания на дополнительное пространство. |
> | Действия | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Получение конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действия | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Создание или обновление конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действия | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Операция обновления гибридного подключения. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT. |
> | Действия | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Операция удаления правил авторизации гибридного подключения. |
> | Действия | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Возвращает строку подключения для гибридного подключения. |
> | Действия | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Получение списка правил авторизации гибридного подключения. |
> | Действия | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действия | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Создает правила авторизации гибридного подключения и обновляет его свойства. Можно обновить права доступа к правилам авторизации. |
> | Действия | Microsoft.Relay/namespaces/HybridConnections/Delete | Операция удаления ресурса гибридного подключения. |
> | Действия | Microsoft.Relay/namespaces/HybridConnections/read | Возвращает список описаний ресурсов гибридного подключения. |
> | Действия | Microsoft.Relay/namespaces/HybridConnections/write | Создает или обновляет свойства гибридного подключения. |
> | Действия | Microsoft.Relay/namespaces/messagingPlan/read | Получение плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действия | Microsoft.Relay/namespaces/messagingPlan/write | Обновление плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действия | Microsoft.Relay/namespaces/operationresults/read | Получение состояния операции пространства имен. |
> | Действия | Microsoft. Relay/пространства имен/поставщики/Microsoft. Insights/diagnosticSettings/чтение | Возвращает список описаний параметров диагностики пространства имен. |
> | Действия | Microsoft. Relay/пространства имен/поставщики/Microsoft. Insights/diagnosticSettings/запись | Возвращает список описаний параметров диагностики пространства имен. |
> | Действия | Microsoft. Relay/пространства имен/поставщики/Microsoft. Insights/Логдефинитионс/чтение | Возвращает список описаний ресурсов журналов пространства имен. |
> | Действия | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Возвращает список описаний ресурсов метрик пространства имен. |
> | Действия | Microsoft.Relay/namespaces/read | Возвращает список описаний ресурсов пространства имен. |
> | Действия | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Удаляет пространство имен ACS. |
> | Действия | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Операция обновления ретранслятора WCF. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT. |
> | Действия | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Операция удаления правил авторизации ретранслятора WCF. |
> | Действия | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Возвращает строку подключения к ретранслятору WCF. |
> | Действия | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Получение списка правил авторизации ретранслятора WCF. |
> | Действия | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действия | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Создает правила авторизации ретранслятора WCF и обновляет его свойства. Можно обновить права доступа к правилам авторизации. |
> | Действия | Microsoft.Relay/namespaces/WcfRelays/Delete | Операция удаления ресурса ретранслятора WCF. |
> | Действия | Microsoft.Relay/namespaces/WcfRelays/read | Возвращает список описаний ресурсов ретранслятора WCF. |
> | Действия | Microsoft.Relay/namespaces/WcfRelays/write | Создает или обновляет свойства ретранслятора WCF. |
> | Действия | Microsoft.Relay/namespaces/write | Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен. |
> | Действия | Microsoft.Relay/operations/read | Получение операций. |
> | Действия | Microsoft.Relay/register/action | Регистрирует подписку для поставщика ресурсов ретранслятора и позволяет создавать ресурсы ретранслятора. |
> | Действия | Microsoft.Relay/unregister/action | Регистрирует подписку для поставщика ресурсов ретранслятора и позволяет создавать ресурсы ретранслятора. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Возвращает состояние доступности для указанного ресурса. |
> | Действия | Microsoft.ResourceHealth/AvailabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Действия | Microsoft.ResourceHealth/events/read | Получение событий работоспособности служб для данной подписки. |
> | Действия | Microsoft.Resourcehealth/healthevent/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действия | Microsoft.Resourcehealth/healthevent/Activated/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действия | Microsoft.Resourcehealth/healthevent/InProgress/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действия | Microsoft.Resourcehealth/healthevent/Pending/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действия | Microsoft.Resourcehealth/healthevent/Resolved/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действия | Microsoft.Resourcehealth/healthevent/Updated/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действия | Microsoft.ResourceHealth/impactedResources/read | Получение затронутых ресурсов для данной подписки. |
> | Действия | Microsoft. Ресаурцехеалс/метаданные/чтение | Получение метаданных |
> | Действия | Microsoft.ResourceHealth/Operations/read | Получение операций, доступных для ResourceHealth (Майкрософт) |
> | Действия | Microsoft.ResourceHealth/register/action | Регистрация подписки на службу "Работоспособность ресурсов Microsoft". |
> | Действия | Microsoft.ResourceHealth/unregister/action | Отмена регистрации подписки для ResourceHealth (Майкрософт) |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Resources/Калкулатетемплатехаш/действие | Вычислите хэш предоставленного шаблона. |
> | Действия | Microsoft.Resources/checkPolicyCompliance/action | Проверка состояния соответствия политикам для указанного ресурса. |
> | Действия | Microsoft.Resources/checkResourceName/action | Проверяет допустимость имени ресурса. |
> | Действия | Microsoft.Resources/deployments/cancel/action | Отменяет развертывание. |
> | Действия | Microsoft.Resources/deployments/delete | Удаляет развертывание. |
> | Действия | Microsoft. Resources/deployments/Експорттемплате/Action | Экспорт шаблона для развертывания |
> | Действия | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Действия | Microsoft. Resources/deployments/оператионстатусес/Read | Возвращает состояния операций развертывания или выводит их список. |
> | Действия | Microsoft.Resources/deployments/read | Возвращает развернутые службы или выводит их список. |
> | Действия | Microsoft.Resources/deployments/validate/action | Проверяет развертывание. |
> | Действия | Microsoft. Resources/deployments/whatIf/Action | Прогнозирует изменения развертывания шаблона. |
> | Действия | Microsoft.Resources/deployments/write | Создает или обновляет развертывание. |
> | Действия | Microsoft.Resources/links/delete | Удаляет ссылку на ресурс. |
> | Действия | Microsoft.Resources/links/read | Возвращает ссылки на ресурсы или выводит их список. |
> | Действия | Microsoft.Resources/links/write | Создает или обновляет ссылку на ресурс. |
> | Действия | Microsoft.Resources/marketplace/purchase/action | Покупка ресурса на Marketplace. |
> | Действия | Microsoft.Resources/providers/read | Возвращает список поставщиков. |
> | Действия | Microsoft.Resources/resources/read | Возвращает список ресурсов на основе фильтров. |
> | Действия | Microsoft.Resources/subscriptions/locations/read | Возвращает список поддерживаемых расположений. |
> | Действия | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Действия | Microsoft.Resources/subscriptions/providers/read | Возвращает поставщики ресурсов или выводит их список. |
> | Действия | Microsoft.Resources/subscriptions/read | Возвращает список подписок. |
> | Действия | Microsoft.Resources/subscriptions/resourceGroups/delete | Удаляет группу ресурсов со всеми ресурсами. |
> | Действия | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Действия | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Возвращает состояния операций развертывания или выводит их список. |
> | Действия | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Возвращает развернутые службы или выводит их список. |
> | Действия | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Создает или обновляет развертывание. |
> | Действия | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Перемещает ресурсы из одной группы ресурсов в другую. |
> | Действия | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Действия | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Возвращает ресурсы группы ресурсов. |
> | Действия | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Проверяет перемещение ресурсов из одной группы ресурсов в другую. |
> | Действия | Microsoft.Resources/subscriptions/resourceGroups/write | Создает или обновляет группу ресурсов. |
> | Действия | Microsoft.Resources/subscriptions/resources/read | Возвращает ресурсы подписки. |
> | Действия | Microsoft.Resources/subscriptions/tagNames/delete | Удаляет тег подписки. |
> | Действия | Microsoft.Resources/subscriptions/tagNames/read | Возвращает теги подписки или выводит их список. |
> | Действия | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Удаляет значение тега подписки. |
> | Действия | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Возвращает значения тегов подписки или выводит их список. |
> | Действия | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Добавляет значение тега подписки. |
> | Действия | Microsoft.Resources/subscriptions/tagNames/write | Добавляет тег подписки. |
> | Действия | Microsoft. Resources/Tags/удаление | Удаляет все теги в ресурсе. |
> | Действия | Microsoft. Resources/Tags/чтение | Возвращает все теги для ресурса. |
> | Действия | Microsoft. Resources/Tags/запись | Обновляет Теги ресурса, заменяя или объединяя существующие теги с новым набором тегов или удаляя существующие теги. |
> | Действия | Microsoft.Resources/tenants/read | Возвращает список клиентов. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Scheduler/jobcollections/delete | Удаляет коллекцию заданий. |
> | Действия | Microsoft.Scheduler/jobcollections/disable/action | Отключает коллекцию заданий. |
> | Действия | Microsoft.Scheduler/jobcollections/enable/action | Включает коллекцию заданий. |
> | Действия | Microsoft.Scheduler/jobcollections/jobs/delete | Удаляет задание. |
> | Действия | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Создает определение приложения логики на основе задания планировщика. |
> | Действия | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Получает журнал заданий. |
> | Действия | Microsoft.Scheduler/jobcollections/jobs/read | Возвращает задание. |
> | Действия | Microsoft.Scheduler/jobcollections/jobs/run/action | Выполняет задание. |
> | Действия | Microsoft.Scheduler/jobcollections/jobs/write | Создает или обновляет задание. |
> | Действия | Microsoft.Scheduler/jobcollections/read | Возвращает коллекцию заданий. |
> | Действия | Microsoft.Scheduler/jobcollections/write | Создает или обновляет коллекцию заданий. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Search/checkNameAvailability/action | Проверяет доступность имени службы. |
> | Действия | Microsoft.Search/operations/read | Выводит список всех доступных операций поставщика Microsoft.Search. |
> | Действия | Microsoft.Search/register/action | Регистрирует подписку для поставщика ресурсов службы поиска и позволяет создавать службы поиска. |
> | Действия | Microsoft.Search/searchServices/createQueryKey/action | Создает ключ запроса. |
> | Действия | Microsoft.Search/searchServices/delete | Удаляет службу поиска. |
> | Действия | Microsoft.Search/searchServices/deleteQueryKey/delete | Удаляет ключ запроса. |
> | Действия | Microsoft.Search/searchServices/listAdminKeys/action | Считывает ключи администратора. |
> | Действия | Microsoft. Search/Сеарчсервицес/Листкуерикэйс/действие | Возвращает список ключей API запроса для заданной службы Когнитивный поиск Azure. |
> | Действия | Microsoft.Search/searchServices/listQueryKeys/read | Возвращает список ключей API запроса для заданной службы Когнитивный поиск Azure. |
> | Действия | Microsoft.Search/searchServices/read | Считывает службу поиска. |
> | Действия | Microsoft.Search/searchServices/regenerateAdminKey/action | Повторно создает ключ администратора. |
> | Действия | Microsoft.Search/searchServices/start/action | Запускает службу поиска. |
> | Действия | Microsoft.Search/searchServices/stop/action | Останавливает службу поиска. |
> | Действия | Microsoft.Search/searchServices/write | Создает или обновляет службу поиска. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Security/Адаптивенетворкхарденингс/принудительно/действие | Применяет заданные правила усиления защиты трафика, создавая соответствующие правила безопасности для указанных групп безопасности сети. |
> | Действия | Microsoft. Security/Адаптивенетворкхарденингс/Read | Получение адаптивных рекомендаций по усилению защиты сети для ресурса, защищенного Azure. |
> | Действия | Microsoft.Security/advancedThreatProtectionSettings/read | Получает параметры расширенной защиты от угроз для ресурса. |
> | Действия | Microsoft.Security/advancedThreatProtectionSettings/write | Обновляет параметры расширенной защиты от угроз для ресурса. |
> | Действия | Microsoft.Security/alerts/read | Получение всех доступных оповещений системы безопасности. |
> | Действия | Microsoft.Security/applicationWhitelistings/read | Возвращает список разрешений приложения. |
> | Действия | Microsoft.Security/applicationWhitelistings/write | Создает новый список разрешений приложения или обновляет существующий. |
> | Действия | Microsoft. Security/Ассессментметадата/Read | Получите доступ к метаданным оценки безопасности в подписке |
> | Действия | Microsoft. Security/Ассессментметадата/Write | Создание или обновление метаданных оценки безопасности |
> | Действия | Microsoft. Security/оценке/чтение | Получение оценок безопасности для подписки |
> | Действия | Microsoft. Security/оценки/запись | Создание или обновление оценок безопасности в подписке |
> | Действия | Microsoft.Security/complianceResults/read | Получение результатов соответствия для ресурса. |
> | Действия | Microsoft.Security/informationProtectionPolicies/read | Получение сведений о политиках защиты для ресурса |
> | Действия | Microsoft.Security/informationProtectionPolicies/write | Обновление политик защиты сведений для ресурса |
> | Действия | Microsoft.Security/locations/alerts/activate/action | Активация оповещения системы безопасности. |
> | Действия | Microsoft.Security/locations/alerts/dismiss/action | Отклонение оповещения системы безопасности. |
> | Действия | Microsoft.Security/locations/alerts/read | Получение всех доступных оповещений системы безопасности. |
> | Действия | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Удаляет политики JIT-доступа к сети. |
> | Действия | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Инициирует запрос политики JIT-доступа к сети. |
> | Действия | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Возвращает политики JIT-доступа к сети. |
> | Действия | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Создает новую политику JIT-доступа к сети или обновляет существующую. |
> | Действия | Microsoft.Security/locations/read | Получение расположения данных безопасности. |
> | Действия | Microsoft.Security/locations/tasks/activate/action | Активирует рекомендацию по безопасности. |
> | Действия | Microsoft.Security/locations/tasks/dismiss/action | Закрывает рекомендацию по безопасности. |
> | Действия | Microsoft.Security/locations/tasks/read | Возвращает все доступные рекомендации по безопасности. |
> | Действия | Microsoft.Security/locations/tasks/resolve/action | Разрешение рекомендации по безопасности. |
> | Действия | Microsoft.Security/locations/tasks/start/action | Запуск рекомендации по безопасности. |
> | Действия | Microsoft.Security/policies/read | Возвращает политику безопасности. |
> | Действия | Microsoft.Security/policies/write | Обновляет политику безопасности. |
> | Действия | Microsoft.Security/pricings/delete | Удаление параметров ценообразования для области. |
> | Действия | Microsoft.Security/pricings/read | Получение параметров ценообразования для области. |
> | Действия | Microsoft.Security/pricings/write | Обновление параметров ценообразования для области. |
> | Действия | Microsoft.Security/register/action | Регистрация подписки в центре безопасности Azure. |
> | Действия | Microsoft.Security/securityContacts/delete | Удаление контактного лица по вопросам безопасности. |
> | Действия | Microsoft.Security/securityContacts/read | Получение контактного лица по вопросам безопасности. |
> | Действия | Microsoft.Security/securityContacts/write | Обновление контактного лица по вопросам безопасности. |
> | Действия | Microsoft.Security/securitySolutions/delete | Удаляет решение безопасности. |
> | Действия | Microsoft.Security/securitySolutions/read | Возвращает решения безопасности. |
> | Действия | Microsoft.Security/securitySolutions/write | Создает новое решение безопасности или обновляет существующее. |
> | Действия | Microsoft.Security/securitySolutionsReferenceData/read | Возвращает эталонные данные решений безопасности. |
> | Действия | Microsoft.Security/securityStatuses/read | Возвращает состояния работоспособности ресурсов Azure. |
> | Действия | Microsoft.Security/securityStatusesSummaries/read | Получение сводок состояния безопасности для области. |
> | Действия | Microsoft.Security/settings/read | Получение параметров для области |
> | Действия | Microsoft.Security/settings/write | Обновление параметров для области |
> | Действия | Microsoft.Security/tasks/read | Возвращает все доступные рекомендации по безопасности. |
> | Действия | Microsoft.Security/unregister/action | Отменяет регистрацию подписки в центре безопасности Azure |
> | Действия | Microsoft.Security/webApplicationFirewalls/delete | Удаляет брандмауэр веб-приложения. |
> | Действия | Microsoft.Security/webApplicationFirewalls/read | Возвращает брандмауэры веб-приложения. |
> | Действия | Microsoft.Security/webApplicationFirewalls/write | Создает новый брандмауэр веб-приложения или обновляет существующий. |
> | Действия | Microsoft.Security/workspaceSettings/connect/action | Изменение параметров повторного подключения к рабочей области. |
> | Действия | Microsoft.Security/workspaceSettings/delete | Удаление параметров рабочей области. |
> | Действия | Microsoft.Security/workspaceSettings/read | Получение параметров рабочей области. |
> | Действия | Microsoft.Security/workspaceSettings/write | Обновление параметров рабочей области. |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.SecurityGraph/diagnosticsettings/delete | Удаление параметра диагностики. |
> | Действия | Microsoft.SecurityGraph/diagnosticsettings/read | Чтение параметра диагностики. |
> | Действия | Microsoft.SecurityGraph/diagnosticsettings/write | Запись параметра диагностики. |
> | Действия | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Чтение категорий параметра диагностики. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ServiceBus/checkNameAvailability/action | Проверяет доступность пространства имен в заданной подписке. |
> | Действия | Microsoft.ServiceBus/checkNamespaceAvailability/action | Проверяет доступность пространства имен в заданной подписке. Этот API не рекомендуется. Вместо него используйте CheckNameAvailability. |
> | Действия | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Удаление правил виртуальной сети в поставщике ресурсов служебной шины для указанной виртуальной сети |
> | Действия | Microsoft.ServiceBus/namespaces/authorizationRules/action | Обновление правила авторизации для пространства имен. Это нерекомендуемый API. Чтобы обновить правило авторизации для пространства имен, используйте вызов PUT. Эта операция не поддерживается для API версии 2017-04-01. |
> | Действия | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию.  |
> | Действия | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Возвращает строку подключения к пространству имен. |
> | Действия | Microsoft.ServiceBus/namespaces/authorizationRules/read | Возвращает список описаний правил авторизации пространства имен. |
> | Действия | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действия | Microsoft.ServiceBus/namespaces/authorizationRules/write | Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации. |
> | Действия | Microsoft.ServiceBus/namespaces/Delete | Удаляет ресурс пространства имен. |
> | Действия | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Получение ключей правил авторизации основного пространства имен для аварийного восстановления. |
> | Действия | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Получение правил авторизации основного пространства имен для аварийного восстановления. |
> | Действия | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Отключение аварийного восстановления и остановка репликации изменений из основного пространства имен в дополнительное. |
> | Действия | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Проверка доступности псевдонима пространства имен в заданной подписке. |
> | Действия | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Удаление конфигурации аварийного восстановления, связанной с пространством имен. Эта операция может быть вызвана только через основное пространство имен. |
> | Действия | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Вызов отработки отказа географического аварийного восстановления и перенастройка псевдонима пространства имен для указания на дополнительное пространство. |
> | Действия | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Получение конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действия | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Создание или обновление конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действия | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Удаление фильтра сетки событий, связанного с пространством имен. |
> | Действия | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Получение фильтра сетки событий, связанного с пространством имен. |
> | Действия | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Создание или обновление фильтра сетки событий, связанного с пространством имен. |
> | Действия | Microsoft.ServiceBus/namespaces/eventhubs/read | Возвращает список описаний ресурсов концентратора событий. |
> | Действия | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Удаление ресурса фильтра IP-адресов |
> | Действия | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Получение ресурса фильтра IP-адресов |
> | Действия | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Создание ресурса фильтра IP-адресов |
> | DataAction | Microsoft. ServiceBus/пространства имен/сообщения/получение/действие | Получение сообщений |
> | DataAction | Microsoft. ServiceBus/пространства имен/сообщения/Отправка/действие | Отправка сообщений |
> | Действия | Microsoft.ServiceBus/namespaces/messagingPlan/read | Получение плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действия | Microsoft.ServiceBus/namespaces/messagingPlan/write | Обновление плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действия | Microsoft.ServiceBus/namespaces/migrate/action | Операция переноса пространства имен. |
> | Действия | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Удаляет конфигурацию миграции. |
> | Действия | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Получает конфигурацию миграции, показывающую ее состояние, и ожидающие операции репликации |
> | Действия | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Отменяет миграцию из пространства имен категории "Стандартный" в пространство категории "Премиум" |
> | Действия | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Назначает DNS, связанные с пространством имен категории "Стандартный", пространству категории "Премиум". Эта операция завершает миграцию и останавливает синхронизацию ресурсов из одного пространства в другое |
> | Действия | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Создает или обновляет конфигурации миграции. Эта операция начнет синхронизацию ресурсов из пространства имен категории "Стандартный" в пространство категории "Премиум" |
> | Действия | Microsoft. ServiceBus/пространства имен/нетворкрулесет/Delete | Удаление ресурса правила виртуальной сети |
> | Действия | Microsoft. ServiceBus/пространства имен/нетворкрулесет/чтение | Получает ресурс Нетворкрулесет |
> | Действия | Microsoft. ServiceBus/пространства имен/нетворкрулесет/запись | Создание ресурса правила виртуальной сети |
> | Действия | Microsoft. ServiceBus/пространства имен/нетворкрулесетс/Delete | Удаление ресурса правила виртуальной сети |
> | Действия | Microsoft. ServiceBus/пространства имен/нетворкрулесетс/чтение | Получает ресурс Нетворкрулесет |
> | Действия | Microsoft. ServiceBus/пространства имен/нетворкрулесетс/запись | Создание ресурса правила виртуальной сети |
> | Действия | Microsoft.ServiceBus/namespaces/operationresults/read | Получение состояния операции пространства имен. |
> | Действия | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает список описаний параметров диагностики пространства имен. |
> | Действия | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Возвращает список описаний параметров диагностики пространства имен. |
> | Действия | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Возвращает список описаний ресурсов журналов пространства имен. |
> | Действия | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Возвращает список описаний ресурсов метрик пространства имен. |
> | Действия | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Операция обновления очереди. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT. |
> | Действия | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Операция удаления правил авторизации очереди. |
> | Действия | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Возвращает строку подключения к очереди. |
> | Действия | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Возвращает список правил авторизации очереди. |
> | Действия | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действия | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Создает правила авторизации очереди и обновляет ее свойства. Можно обновить права доступа к правилам авторизации. |
> | Действия | Microsoft.ServiceBus/namespaces/queues/Delete | Операция удаления ресурса очереди. |
> | Действия | Microsoft.ServiceBus/namespaces/queues/read | Возвращает список описаний ресурсов очереди. |
> | Действия | Microsoft.ServiceBus/namespaces/queues/write | Создает или обновляет свойства очереди. |
> | Действия | Microsoft.ServiceBus/namespaces/read | Возвращает список описаний ресурсов пространства имен. |
> | Действия | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Удаляет пространство имен ACS. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Операция обновления раздела. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Операция удаления правил авторизации раздела. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Возвращает строку подключения к разделу. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Возвращает список правил авторизации раздела. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Создает правила авторизации раздела и обновляет его свойства. Можно обновить права доступа к правилам авторизации. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/Delete | Операция удаления ресурса раздела. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/read | Возвращает список описаний ресурсов раздела. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Операция удаления ресурса подписки на раздел. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Возвращает список описаний ресурсов подписки на раздел. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Операция удаления ресурса правила. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Возвращает список описаний ресурсов правила. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Создает или обновляет свойства правила. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Создает или обновляет свойства подписки на раздел. |
> | Действия | Microsoft.ServiceBus/namespaces/topics/write | Создает или обновляет свойства раздела. |
> | Действия | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Удаление ресурса правила виртуальной сети |
> | Действия | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Получение ресурса правила виртуальной сети |
> | Действия | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Создание ресурса правила виртуальной сети |
> | Действия | Microsoft.ServiceBus/namespaces/write | Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен. |
> | Действия | Microsoft.ServiceBus/operations/read | Получение операций. |
> | Действия | Microsoft.ServiceBus/register/action | Регистрирует подписку для поставщика ресурсов служебной шины и позволяет создавать ресурсы служебной шины. |
> | Действия | Microsoft.ServiceBus/sku/read | Получение списка описаний ресурсов номеров SKU. |
> | Действия | Microsoft.ServiceBus/sku/regions/read | Получение списка описаний ресурсов регионов для номеров SKU. |
> | Действия | Microsoft.ServiceBus/unregister/action | Регистрирует подписку для поставщика ресурсов служебной шины и позволяет создавать ресурсы служебной шины. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.ServiceFabric/clusters/applications/delete | Удаление любого приложения. |
> | Действия | Microsoft.ServiceFabric/clusters/applications/read | Чтение любого приложения. |
> | Действия | Microsoft.ServiceFabric/clusters/applications/services/delete | Удаление любой службы. |
> | Действия | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Чтение любого раздела. |
> | Действия | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Чтение любой реплики. |
> | Действия | Microsoft.ServiceFabric/clusters/applications/services/read | Чтение любой службы. |
> | Действия | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Чтение состояния любой службы. |
> | Действия | Microsoft.ServiceFabric/clusters/applications/services/write | Создание или обновление любой службы. |
> | Действия | Microsoft.ServiceFabric/clusters/applications/write | Создание или обновление любого приложения. |
> | Действия | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Удаление любого типа приложения. |
> | Действия | Microsoft.ServiceFabric/clusters/applicationTypes/read | Чтение любого типа приложения. |
> | Действия | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Удаление любой версии типа приложения. |
> | Действия | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Чтение любой версии типа приложения. |
> | Действия | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Создание или обновление любой версии типа приложения. |
> | Действия | Microsoft.ServiceFabric/clusters/applicationTypes/write | Создание или обновление любого типа приложения. |
> | Действия | Microsoft.ServiceFabric/clusters/delete | Удаление любого кластера. |
> | Действия | Microsoft.ServiceFabric/clusters/nodes/read | Чтение любого узла. |
> | Действия | Microsoft.ServiceFabric/clusters/read | Чтение любого кластера. |
> | Действия | Microsoft.ServiceFabric/clusters/statuses/read | Чтение состояния любого кластера. |
> | Действия | Microsoft.ServiceFabric/clusters/write | Создание или обновление любого кластера. |
> | Действия | Microsoft.ServiceFabric/locations/clusterVersions/read | Чтение любой версии кластера. |
> | Действия | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Чтение любой версии кластера для конкретной среды. |
> | Действия | Microsoft.ServiceFabric/locations/operationresults/read | Чтение результатов операций. |
> | Действия | Microsoft.ServiceFabric/locations/operations/read | Чтение операций по расположению. |
> | Действия | Microsoft.ServiceFabric/operations/read | Чтение доступных операций. |
> | Действия | Microsoft.ServiceFabric/register/action | Регистрация любого действия. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.SignalRService/locations/checknameavailability/action | Проверяет, доступно ли имя для новой службы SignalR. |
> | Действия | Microsoft.SignalRService/locations/operationresults/signalr/read | Запрос состояния асинхронной операции |
> | Действия | Microsoft. Сигналрсервице/Locations/Оператионстатусес/идентификатор операции чтения | Запрос состояния асинхронной операции |
> | Действия | Microsoft.SignalRService/locations/usages/read | Получение сведений об использовании квот для службы Azure SignalR. |
> | Действия | Microsoft.SignalRService/operationresults/read | Запрос состояния асинхронной операции |
> | Действия | Microsoft. Сигналрсервице/операции/чтение | Перечислите операции для службы Azure SignalR. |
> | Действия | Microsoft. Сигналрсервице/значение operationstatus/Read | Запрос состояния асинхронной операции |
> | Действия | Microsoft.SignalRService/register/action | Регистрирует поставщика ресурсов Microsoft.SignalRService в подписке. |
> | Действия | Microsoft.SignalRService/SignalR/delete | Удаление всей службы SignalR. |
> | Действия | Microsoft. Сигналрсервице/SignalR/Евентгридфилтерс/Delete | Удаление фильтра сетки событий из SignalR. |
> | Действия | Microsoft. Сигналрсервице/SignalR/Евентгридфилтерс/Read | Возвращает свойства указанного фильтра сетки событий или список всех фильтров сетки событий для указанного SignalR. |
> | Действия | Microsoft. Сигналрсервице/SignalR/Евентгридфилтерс/запись | Создание или обновление фильтра сетки событий для SignalR с указанными параметрами. |
> | Действия | Microsoft.SignalRService/SignalR/listkeys/action | Просмотр значений ключей доступа SignalR на портале управления или с помощью API. |
> | Действия | Microsoft.SignalRService/SignalR/read | Просмотр параметров и конфигураций SignalR на портале управления или с помощью API. |
> | Действия | Microsoft.SignalRService/SignalR/regeneratekey/action | Изменение значений ключей доступа SignalR на портале управления или с помощью API. |
> | Действия | Microsoft.SignalRService/SignalR/restart/action | Перезапуск службы Azure SignalR на портале управления или с помощью API. Эта операция вызовет определенный период простоя. |
> | Действия | Microsoft.SignalRService/SignalR/write | Изменяет параметры и конфигурации SignalR на портале управления или с помощью API. |
> | Действия | Microsoft.SignalRService/unregister/action | Отменяет регистрацию поставщика ресурсов Microsoft.SignalRService в подписке. |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Solutions/Аппликатиондефинитионс/Аппликатионартифактс/Read | Перечисляет артефакты приложения для определения приложения. |
> | Действия | Microsoft.Solutions/applicationDefinitions/delete | Удаление определения приложения. |
> | Действия | Microsoft.Solutions/applicationDefinitions/read | Извлечение списка определений приложений. |
> | Действия | Microsoft.Solutions/applicationDefinitions/write | Добавление или изменение определения приложения. |
> | Действия | Microsoft. Solutions/Applications/Аппликатионартифактс/Read | Перечисляет артефакты приложения. |
> | Действия | Microsoft.Solutions/applications/delete | Удаление приложения. |
> | Действия | Microsoft.Solutions/applications/read | Извлечение списка приложений. |
> | Действия | Microsoft. Solutions/Applications/Рефрешпермиссионс/Action | Обновляет разрешения приложения. |
> | Действия | Microsoft. Solutions/Applications/Упдатеакцесс/Action | Обновляет доступ к приложениям. |
> | Действия | Microsoft.Solutions/applications/write | Создает приложение. |
> | Действия | Microsoft.Solutions/jitRequests/delete | Удаление запросов Jit |
> | Действия | Microsoft.Solutions/jitRequests/read | Получение списка запросов Jit |
> | Действия | Microsoft.Solutions/jitRequests/write | Создание запроса JIT |
> | Действия | Microsoft.Solutions/locations/operationStatuses/read | Считывает состояние операции ресурса. |
> | Действия | Microsoft.Solutions/register/action | Регистрация в решениях. |
> | Действия | Microsoft. Solutions/Unregister/действие | Отменяет регистрацию в решениях. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Sql/checkNameAvailability/action | Проверка того, доступно ли данное имя сервера для подготовки по всему миру для данной подписки. |
> | Действия | Microsoft.Sql/instancePools/delete | Удаление пула экземпляров. |
> | Действия | Microsoft.Sql/instancePools/read | Получение пула экземпляров. |
> | Действия | Microsoft. SQL/Инстанцепулс/использование/чтение | Получает сведения об использовании пула экземпляров |
> | Действия | Microsoft.Sql/instancePools/write | Создание или обновление пула экземпляров. |
> | Действия | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Извлечение результата операции настройки расширенной политики аудита больших двоичных объектов на сервере. |
> | Действия | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Извлекает результат операции настройки политики аудита больших двоичных объектов на сервере. |
> | Действия | Microsoft.Sql/locations/capabilities/read | Получение сведений о возможностях этой подписки в данном расположении. |
> | Действия | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Получение состояния операции базы данных. |
> | Действия | Microsoft.Sql/locations/databaseOperationResults/read | Получение состояния операции базы данных. |
> | Действия | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Возвращение выполняемых операций на удаленном сервере. |
> | Действия | Microsoft.Sql/locations/deletedServerOperationResults/read | Возвращение выполняемых операций на удаленном сервере. |
> | Действия | Microsoft.Sql/locations/deletedServers/read | Возвращение списка удаленных серверов или получение свойств для указанного удаленного сервера. |
> | Действия | Microsoft.Sql/locations/deletedServers/recover/action | Восстановление удаленного сервера. |
> | Действия | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Возвращение асинхронной операции Azure для эластичного пула. |
> | Действия | Microsoft.Sql/locations/elasticPoolOperationResults/read | Возвращение результата операции эластичного пула. |
> | Действия | Microsoft. SQL/Locations/Енкриптионпротекторазуреасинкоператион/Read | Возвращает выполняемые операции с предохранителем шифрования прозрачного шифрования данных |
> | Действия | Microsoft. SQL/Locations/Енкриптионпротектороператионресултс/Read | Возвращает выполняемые операции с предохранителем шифрования прозрачного шифрования данных |
> | Действия | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Извлечение результата операции настройки расширенной политики аудита больших двоичных объектов на сервере. |
> | Действия | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Извлечение результата операции настройки расширенной политики аудита больших двоичных объектов на сервере. |
> | Действия | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Получение состояния операции правила брандмауэра. |
> | Действия | Microsoft.Sql/locations/firewallRulesOperationResults/read | Получение состояния операции правила брандмауэра. |
> | Действия | Microsoft.Sql/locations/instanceFailoverGroups/delete | Удаление имеющейся группы отработки отказа экземпляра. |
> | Действия | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Выполнение плановой отработки отказа в имеющейся группе отработки отказа экземпляра. |
> | Действия | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Выполнение принудительной отработки отказа в имеющейся группе отработки отказа экземпляра. |
> | Действия | Microsoft.Sql/locations/instanceFailoverGroups/read | Возвращение списка групп отработки отказа экземпляра или свойств для указанной группы экземпляра. |
> | Действия | Microsoft.Sql/locations/instanceFailoverGroups/write | Создает группу отработки отказа экземпляра с указанными параметрами или обновляет свойства или теги для указанной группы отработки отказа. |
> | Действия | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Возвращает состояние операции пула экземпляров. |
> | Действия | Microsoft.Sql/locations/instancePoolOperationResults/read | Возвращает результат для операции с пулом экземпляров |
> | Действия | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Возвращение сведений об асинхронной операции Azure для определенной конечной точки интерфейса |
> | Действия | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Возвращение сведений об операции с профилем для определенной конечной точки интерфейса |
> | Действия | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Получает состояние операции агента задания. |
> | Действия | Microsoft.Sql/locations/jobAgentOperationResults/read | Получает результат операции агента задания. |
> | Действия | Microsoft.Sql/locations/longTermRetentionBackups/read | Вывод списка резервных копий на долгосрочном хранении для каждой базы данных на каждом сервере в определенном расположении. |
> | Действия | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Вывод списка резервных копий на долгосрочном хранении для каждой базы данных на сервере. |
> | Действия | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Удаление резервной копии на долгосрочном хранении. |
> | Действия | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Вывод списка резервных копий на долгосрочном хранении для базы данных. |
> | Действия | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Завершение операции восстановления управляемой базы данных. |
> | Действия | Microsoft. SQL/Locations/Манажединстанцеенкриптионпротекторазуреасинкоператион/Read | Получает выполняемые операции с прозрачным шифрованием шифрования управляемого шифрования данных |
> | Действия | Microsoft. SQL/Locations/Манажединстанцеенкриптионпротектороператионресултс/Read | Получает выполняемые операции с прозрачным шифрованием шифрования управляемого шифрования данных |
> | Действия | Microsoft. SQL/Locations/Манажединстанцекэйазуреасинкоператион/Read | Возвращает выполняемые операции с управляемыми ключами экземпляра шифрования прозрачных данных |
> | Действия | Microsoft. SQL/Locations/Манажединстанцекэйоператионресултс/Read | Возвращает выполняемые операции с управляемыми ключами экземпляра шифрования прозрачных данных |
> | Действия | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Возвращение выполняемых операций прозрачного шифрования данных в управляемой базе данных. |
> | Действия | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Возвращение выполняемых операций прозрачного шифрования данных в управляемой базе данных. |
> | Действия | Microsoft. SQL/Locations/Приватиндпоинтконнектионазуреасинкоператион/Read | Возвращает результат операции подключения к частной конечной точке |
> | Действия | Microsoft. SQL/Locations/Приватиндпоинтконнектионоператионресултс/Read | Возвращает результат операции подключения к частной конечной точке |
> | Действия | Microsoft. SQL/Locations/Приватиндпоинтконнектионпроксязуреасинкоператион/Read | Возвращает результат для операции прокси-сервера подключения частной конечной точки |
> | Действия | Microsoft. SQL/Locations/Приватиндпоинтконнектионпроксйоператионресултс/Read | Возвращает результат для операции прокси-сервера подключения частной конечной точки |
> | Действия | Microsoft.Sql/locations/read | Получение расположений, доступных для данной подписки. |
> | Действия | Microsoft. SQL/Locations/Серверкэйазуреасинкоператион/Read | Возвращает выполняемые операции с ключами сервера прозрачного шифрования данных |
> | Действия | Microsoft. SQL/Locations/Серверкэйоператионресултс/Read | Возвращает выполняемые операции с ключами сервера прозрачного шифрования данных |
> | Действия | Microsoft.Sql/locations/syncAgentOperationResults/read | Получение результата операции ресурса агента синхронизации. |
> | Действия | Microsoft.Sql/locations/syncDatabaseIds/read | Получение идентификаторов базы данных синхронизации для конкретного региона и подписки. |
> | Действия | Microsoft.Sql/locations/syncGroupOperationResults/read | Получение результата операции синхронизации группы. |
> | Действия | Microsoft.Sql/locations/syncMemberOperationResults/read | Получение результата операции синхронизации элемента. |
> | Действия | Microsoft.Sql/locations/usages/read | Получение коллекции метрик использования для этой подписки в расположении. |
> | Действия | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Возвращение сведений об асинхронной операции Azure для указанных правил виртуальной сети.  |
> | Действия | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Возвращение сведений о правилах операции для указанных правил виртуальной сети.  |
> | Действия | Microsoft.Sql/managedInstances/administrators/delete | Удаление существующего администратора управляемого экземпляра. |
> | Действия | Microsoft.Sql/managedInstances/administrators/read | Получение списка администраторов управляемого экземпляра. |
> | Действия | Microsoft.Sql/managedInstances/administrators/write | Создание или обновление администратора управляемого экземпляра с указанными параметрами. |
> | Действия | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Получает политику краткосрочного хранения для управляемой базы данных |
> | Действия | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Обновляет политику краткосрочного хранения для управляемой базы данных |
> | Действия | Microsoft. SQL/Манажединстанцес/базы данных/столбцы/чтение | Возврат списка столбцов для управляемой базы данных |
> | Действия | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Получение списка меток конфиденциальности для конкретной базы данных. |
> | Действия | Microsoft. SQL/Манажединстанцес/databases/Куррентсенситивитилабелс/Write | Метки чувствительности пакетного обновления |
> | Действия | Microsoft.Sql/managedInstances/databases/delete | Удаление существующей управляемой базы данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действия | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Получение доступных журналов для баз данных управляемых экземпляров |
> | Действия | Microsoft.Sql/managedInstances/databases/read | Получение существующей управляемой базы данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Получение списка меток конфиденциальности для конкретной базы данных. |
> | Действия | Microsoft. SQL/Манажединстанцес/databases/Рекоммендедсенситивитилабелс/Write | Рекомендуемые метки чувствительности к пакетному обновлению |
> | Действия | Microsoft. SQL/Манажединстанцес/databases/Ресторедетаилс/Read | Возвращает сведения о восстановлении управляемой базы данных, пока выполняется восстановление. |
> | Действия | Microsoft. SQL/Манажединстанцес/базы данных/схемы/чтение | Получение схемы управляемой базы данных. |
> | Действия | Microsoft. SQL/Манажединстанцес/базы данных, схемы, таблицы, столбцы и чтение | Получение столбца управляемой базы данных |
> | Действия | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Удаление метки конфиденциальности данного столбца. |
> | Действия | Microsoft. SQL/Манажединстанцес/базы данных/схемы/таблицы/столбцы/Сенситивитилабелс/отключение/действие | Отключить рекомендации по конфиденциальности для данного столбца |
> | Действия | Microsoft. SQL/Манажединстанцес/базы данных/схемы/таблицы/столбцы/Сенситивитилабелс/включение/действие | Включить рекомендации по конфиденциальности для данного столбца |
> | Действия | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Получение метки конфиденциальности данного столбца. |
> | Действия | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Создание или обновление метки конфиденциальности данного столбца. |
> | Действия | Microsoft. SQL/Манажединстанцес/базы данных/схемы/таблицы/чтение | Получение таблицы управляемой базы данных |
> | Действия | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Получение списка политик обнаружения угроз управляемой базы данных, настроенных для данного сервера |
> | Действия | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Изменение политики обнаружения угроз для заданной управляемой базы данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/securityEvents/read | Получение событий безопасности управляемой базы данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Получение списка меток конфиденциальности для конкретной базы данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Получение сведений о прозрачном шифровании данных в данной управляемой базе данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Изменение прозрачного шифрования данных в данной управляемой базе данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Удаление оценки уязвимости указанной базы данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Получение политик оценки уязвимости для конкретной базы данных |
> | Действия | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Удаление базовых показателей правила оценки уязвимости для указанной базы данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Получение базовых показателей правила оценки уязвимости для указанной базы данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Изменение базовых показателей правила оценки уязвимости для указанной базы данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Преобразование существующего результата проверки в удобочитаемый формат. Если он уже существует, ничего не происходит. |
> | Действия | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Выполняет поиск уязвимостей базы данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Возвращение списка записей сканирования оценки уязвимости базы данных или получение записи для указанного идентификатора сканирования. |
> | Действия | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Изменение оценки уязвимости указанной базы данных. |
> | Действия | Microsoft.Sql/managedInstances/databases/write | Создает новую базу данных или обновляет имеющуюся. |
> | Действия | Microsoft.Sql/managedInstances/delete | Удаление существующего управляемого экземпляра. |
> | Действия | Microsoft.Sql/managedInstances/encryptionProtector/read | Возвращение списка предохранителей шифрования сервера или получение свойств для указанного предохранителя. |
> | Действия | Microsoft. SQL/Манажединстанцес/Енкриптионпротектор/повторная проверка/действие | Обновление свойств указанного предохранителя шифрования сервера. |
> | Действия | Microsoft.Sql/managedInstances/encryptionProtector/write | Обновление свойств указанного предохранителя шифрования сервера. |
> | Действия | Microsoft.Sql/managedInstances/keys/delete | Удаляет имеющийся ключ Управляемого экземпляра SQL Azure. |
> | Действия | Microsoft.Sql/managedInstances/keys/read | Возвращает список ключей управляемых экземпляров или получает свойства указанного ключа управляемого экземпляра. |
> | Действия | Microsoft.Sql/managedInstances/keys/write | Создает ключ с указанными параметрами или обновляет свойства или теги указанного ключа управляемого экземпляра. |
> | Действия | Microsoft.Sql/managedInstances/metricDefinitions/read | Получение определений метрик управляемого экземпляра. |
> | Действия | Microsoft.Sql/managedInstances/metrics/read | Получение метрик управляемого экземпляра. |
> | Действия | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действия | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Получение доступных журналов для управляемых экземпляров |
> | Действия | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Возвращение типов метрик, доступных для управляемых экземпляров |
> | Действия | Microsoft.Sql/managedInstances/read | Возвращение списка управляемых экземпляров или получение свойств указанного управляемого экземпляра. |
> | Действия | Microsoft.Sql/managedInstances/recoverableDatabases/read | Возвращает список доступных для восстановления управляемых баз данных. |
> | Действия | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Получает политику краткосрочного хранения для удаленной управляемой базы данных |
> | Действия | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Обновляет политику краткосрочного хранения для удаленной управляемой базы данных |
> | Действия | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Возвращает список доступных для восстановления удаленных управляемых баз данных. |
> | Действия | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Получение списка политик обнаружения угроз для управляемого сервера, настроенных для данного сервера |
> | Действия | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Изменение политики обнаружения угроз на данном управляемом сервере. |
> | Действия | Microsoft.Sql/managedInstances/tdeCertificates/action | Создание или обновление сертификата TDE |
> | Действия | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Удаление оценки уязвимости для указанного управляемого экземпляра |
> | Действия | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Получение политик оценки уязвимости для конкретного управляемого экземпляра |
> | Действия | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Изменение оценки уязвимости для указанного управляемого экземпляра |
> | Действия | Microsoft.Sql/managedInstances/write | Создание управляемого экземпляра с указанными параметрами либо обновление свойств или тегов указанного управляемого экземпляра. |
> | Действия | Microsoft.Sql/operations/read | Получение доступных операций REST. |
> | Действия | Microsoft. SQL/Приватиндпоинтконнектионсаппровал/действие | Определяет, разрешено ли пользователю утверждать подключение частной конечной точки |
> | Действия | Microsoft.Sql/register/action | Регистрация подписки для поставщика ресурсов базы данных SQL Azure и разрешение создания баз данных Microsoft SQL. |
> | Действия | Microsoft.Sql/servers/administrators/delete | Удаляет указанный объект Azure Active Directory "Администратор" |
> | Действия | Microsoft.Sql/servers/administrators/read | Возвращает конкретный объект Azure Active Directory администратора |
> | Действия | Microsoft.Sql/servers/administrators/write | Добавляет или обновляет указанный объект Azure Active Directory администратора |
> | Действия | Microsoft.Sql/servers/advisors/read | Возвращает список доступных помощников для сервера. |
> | Действия | Microsoft.Sql/servers/advisors/recommendedActions/read | Возвращает список рекомендуемых действий указанного помощника для сервера. |
> | Действия | Microsoft.Sql/servers/advisors/recommendedActions/write | Применяет рекомендуемое действие к серверу. |
> | Действия | Microsoft.Sql/servers/advisors/write | Обновляет состояние автовыполнения Помощника на уровне сервера. |
> | Действия | Microsoft.Sql/servers/auditingPolicies/read | Извлекает сведения о политике аудита таблиц по умолчанию, настроенной на заданном сервере. |
> | Действия | Microsoft.Sql/servers/auditingPolicies/write | Изменяет политику аудита таблиц по умолчанию для заданного сервера. |
> | Действия | Microsoft.Sql/servers/auditingSettings/operationResults/read | Извлекает результат операции настройки политики аудита больших двоичных объектов на сервере. |
> | Действия | Microsoft.Sql/servers/auditingSettings/read | Извлекает сведения о политике аудита больших двоичных объектов, настроенной на заданном сервере. |
> | Действия | Microsoft.Sql/servers/auditingSettings/write | Изменяет политику аудита больших двоичных объектов для заданного сервера. |
> | Действия | Microsoft.Sql/servers/automaticTuning/read | Возвращение параметров автоматической настройки для сервера. |
> | Действия | Microsoft.Sql/servers/automaticTuning/write | Обновление параметров автоматической настройки для сервера и возвращение обновленных параметров. |
> | Действия | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Удаление существующего архивного хранилища резервных копий. |
> | Действия | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | С помощью этой операции можно получить хранилище резервных копий долгосрочного хранения. Возвращает сведения о хранилище, зарегистрированном на данном сервере. |
> | Действия | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | С помощью этой операции можно зарегистрировать на сервере хранилище резервных копий долгосрочного хранения. |
> | Действия | Microsoft.Sql/servers/communicationLinks/delete | Удаление существующего подключения сервера. |
> | Действия | Microsoft.Sql/servers/communicationLinks/read | Возвращение списка подключений для указанного сервера. |
> | Действия | Microsoft.Sql/servers/communicationLinks/write | Создание или обновление подключения сервера. |
> | Действия | Microsoft.Sql/servers/connectionPolicies/read | Возвращение списка политик подключений для указанного сервера. |
> | Действия | Microsoft.Sql/servers/connectionPolicies/write | Создание или обновление политики подключения сервера. |
> | Действия | Microsoft.Sql/servers/databases/advisors/read | Возвращает список доступных помощников для базы данных. |
> | Действия | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Возвращает список рекомендуемых действий указанного помощника для базы данных. |
> | Действия | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Применяет рекомендуемое действие к базе данных. |
> | Действия | Microsoft.Sql/servers/databases/advisors/write | Обновляет состояние автовыполнения Помощника на уровне базы данных. |
> | Действия | Microsoft.Sql/servers/databases/auditingPolicies/read | Извлекает сведения о политике аудита таблиц, настроенной для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/auditingPolicies/write | Изменяет политику аудита таблиц для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/auditingSettings/read | Извлекает сведения о политике аудита больших двоичных объектов, настроенной для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/auditingSettings/write | Изменяет политику аудита больших двоичных объектов для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/auditRecords/read | Извлекает записи аудита больших двоичных объектов для базы данных. |
> | Действия | Microsoft.Sql/servers/databases/automaticTuning/read | Возвращение параметров автоматической настройки базы данных. |
> | Действия | Microsoft.Sql/servers/databases/automaticTuning/write | Обновление параметров автоматической настройки базы данных и возвращение обновленных параметров. |
> | Действия | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Получение состояния операции базы данных. |
> | Действия | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Возвращение списка политик хранения резервных копий для указанной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Создание или обновление политики хранения резервных копий базы данных. |
> | Действия | Microsoft. SQL/Servers/databases/БаккупшорттермретентионполиЦиес/Read | Возвращает политику краткосрочного хранения для базы данных. |
> | Действия | Microsoft. SQL/Servers/databases/БаккупшорттермретентионполиЦиес/Write | Обновляет политику краткосрочного хранения для базы данных. |
> | Действия | Microsoft. SQL/Servers/databases/Columns/Read | Возврат списка столбцов для базы данных |
> | Действия | Microsoft.Sql/servers/databases/connectionPolicies/read | Извлекает сведения о политике подключений, настроенной для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/connectionPolicies/write | Изменяет политику подключений для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Получение списка меток конфиденциальности для конкретной базы данных. |
> | Действия | Microsoft. SQL/Servers/databases/Куррентсенситивитилабелс/Write | Метки чувствительности пакетного обновления |
> | Действия | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Получение списка политик маскировки данных базы данных. |
> | Действия | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Удаление правила политики маскировки данных для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Извлекает сведения о правиле политики маскирования данных, настроенной для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Изменяет правило политики маскирования данных для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Изменяет политику маскирования данных для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Возвращает сведения о шаге распределенного запроса к хранилищу данных для выбранного идентификатора шага. |
> | Действия | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Возвращает данные запроса на распределение к хранилищу данных для выбранного идентификатора запроса. |
> | Действия | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Получение действий пользователя в экземпляре хранилища данных SQL, включая выполняемые и приостановленные запросы. |
> | Действия | Microsoft.Sql/servers/databases/delete | Удаление имеющейся базы данных. |
> | Действия | Microsoft.Sql/servers/databases/export/action | Экспорт базы данных SQL Azure. |
> | Действия | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Извлечение сведений о расширенной политике аудита больших двоичных объектов, настроенной для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Изменение расширенной политики аудита больших двоичных объектов для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/extensions/read | Получение коллекции расширений для базы данных. |
> | Действия | Microsoft.Sql/servers/databases/extensions/write | Изменение расширения для конкретной базы данных. |
> | Действия | Microsoft. SQL/серверы/базы данных/отработка отказа/действие | Отработка отказа базы данных, инициированная клиентом. |
> | Действия | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Получение политик геоизбыточного резервного копирования для конкретной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Создание или обновление политики геоизбыточного резервного копирования базы данных. |
> | Действия | Microsoft.Sql/servers/databases/importExportOperationResults/read | Получение выполняемых операций импорта и экспорта. |
> | Действия | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Получает список доступных окон обслуживания для выбранной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/maintenanceWindows/read | Получает параметры окон обслуживания для выбранной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/maintenanceWindows/write | Задает параметры окон обслуживания для выбранной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/metricDefinitions/read | Возвращает типы метрик, доступных для баз данных. |
> | Действия | Microsoft.Sql/servers/databases/metrics/read | Получение метрик для баз данных. |
> | Действия | Microsoft.Sql/servers/databases/move/action | Изменение имени существующей базы данных. |
> | Действия | Microsoft.Sql/servers/databases/operationResults/read | Получение состояния операции базы данных. |
> | Действия | Microsoft.Sql/servers/databases/operations/cancel/action | Отмена приостановленной асинхронной операции базы данных SQL Azure, которая еще не завершена. |
> | Действия | Microsoft.Sql/servers/databases/operations/read | Возвращение списка операций, выполняемых в базе данных. |
> | Действия | Microsoft.Sql/servers/databases/pause/action | Приостановка работы базы данных хранилища данных SQL Azure. |
> | Действия | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действия | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Возвращает доступные журналы для баз данных. |
> | Действия | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Возвращает типы метрик, доступных для баз данных. |
> | Действия | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Возвращение коллекции текстов запросов, которые соответствуют заданным параметрам. |
> | Действия | Microsoft.Sql/servers/databases/queryStore/read | Возвращение текущих значений параметров хранилища запросов для базы данных. |
> | Действия | Microsoft.Sql/servers/databases/queryStore/write | Обновляет параметр хранилища запросов для базы данных. |
> | Действия | Microsoft.Sql/servers/databases/read | Возвращение списка баз данных или возвращение свойств указанной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Получение списка меток конфиденциальности для конкретной базы данных. |
> | Действия | Microsoft. SQL/Servers/databases/Рекоммендедсенситивитилабелс/Write | Рекомендуемые метки чувствительности к пакетному обновлению |
> | Действия | Microsoft.Sql/servers/databases/replicationLinks/delete | Принудительно удаляет отношение репликации. При этом возможна потеря данных. |
> | Действия | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Выполнение отработки отказа после синхронизации всех изменений из базы данных-источника, превращая заданную базу данных в базу данных-источник для отношения репликации, а удаленную базу данных-источник — в базу данных-приемник. |
> | Действия | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Выполнение немедленной отработки отказа с потенциальной потерей данных, превращая заданную базу данных в базу данных-источник для отношения репликации, а удаленную базу данных-источник — в базу данных-приемник. |
> | Действия | Microsoft.Sql/servers/databases/replicationLinks/read | Возвращает список связей репликации или получает свойства для указанных связей репликации. |
> | Действия | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Удаляет отношение репликации в принудительном порядке или после синхронизации с партнером. |
> | Действия | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Обновляет режим репликации для ссылки на синхронный или асинхронный режим. |
> | Действия | Microsoft.Sql/servers/databases/restorePoints/action | Создает точку восстановления. |
> | Действия | Microsoft.Sql/servers/databases/restorePoints/delete | Удаляет точку восстановления для базы данных. |
> | Действия | Microsoft.Sql/servers/databases/restorePoints/read | Возвращение списка точек восстановления для базы данных. |
> | Действия | Microsoft.Sql/servers/databases/resume/action | Возобновление работы базы данных хранилища данных SQL Azure. |
> | Действия | Microsoft.Sql/servers/databases/schemas/read | Получение схемы базы данных. |
> | Действия | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Получение столбца базы данных. |
> | Действия | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Удаление метки конфиденциальности данного столбца. |
> | Действия | Microsoft. SQL/серверы/базы данных, схемы, таблицы, столбцы, Сенситивитилабелс, отключение и действие | Отключить рекомендации по конфиденциальности для данного столбца |
> | Действия | Microsoft. SQL/серверы/базы данных/схемы/таблицы/столбцы/Сенситивитилабелс/включение/действие | Включить рекомендации по конфиденциальности для данного столбца |
> | Действия | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Получение метки конфиденциальности данного столбца. |
> | Действия | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Создание или обновление метки конфиденциальности данного столбца. |
> | Действия | Microsoft.Sql/servers/databases/schemas/tables/read | Получение таблицы базы данных. |
> | Действия | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Извлекает список рекомендаций по индексам для базы данных. |
> | Действия | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Применяет рекомендацию по индексу. |
> | Действия | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Получение списка политик обнаружения угроз базы данных, настроенных для данного сервера |
> | Действия | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Изменение политики обнаружения угроз для конкретной базы данных |
> | Действия | Microsoft.Sql/servers/databases/securityMetrics/read | Возвращение коллекции метрик безопасности базы данных. |
> | Действия | Microsoft.Sql/servers/databases/sensitivityLabels/read | Получение списка меток конфиденциальности для конкретной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Возвращает предложение по масштабированию базы данных на основе статистики выполнения запросов для повышения производительности или снижения затрат. |
> | Действия | Microsoft.Sql/servers/databases/skus/read | Возвращение коллекции доступных номеров SKU для базы данных. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Отмена синхронизации группы синхронизации. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/delete | Удаление существующей группы синхронизации. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Возвращение списка схем баз данных концентратора синхронизации. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/logs/read | Возвращение списка журналов группы синхронизации. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/read | Возвращение списка групп синхронизации или свойств для указанной группы. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Обновление схемы базы данных концентратора синхронизации. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Получение результата операции обновления схемы концентратора синхронизации. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Удаление существующего элемента синхронизации. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Возвращение списка элементов синхронизации или свойств для указанного элемента. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Обновление схемы элемента синхронизации. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Получение результата операции обновления схемы элемента синхронизации. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Возвращение списка схем баз данных элемента синхронизации. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Создание элемента синхронизации с указанными параметрами либо обновление свойств указанного элемента. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Активация синхронизации группы синхронизации. |
> | Действия | Microsoft.Sql/servers/databases/syncGroups/write | Создание группы синхронизации с указанными параметрами либо обновление свойств указанной группы. |
> | Действия | Microsoft.Sql/servers/databases/topQueries/queryText/action | Возвращает текст Transact-SQL для выбранного идентификатора запроса. |
> | Действия | Microsoft.Sql/servers/databases/topQueries/read | Возвращает статистические данные среды выполнения для выбранного запроса в указанный временной период. |
> | Действия | Microsoft.Sql/servers/databases/topQueries/statistics/read | Возвращает статистические данные среды выполнения для выбранного запроса в указанный временной период. |
> | Действия | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Возвращение выполняемых операций прозрачного шифрования данных. |
> | Действия | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Извлекает состояние и сведения о функции безопасности "прозрачное шифрование данных" для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Изменение состояния прозрачного шифрования данных. |
> | Действия | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Обновление работы базы данных хранилища данных SQL Azure. |
> | Действия | Microsoft.Sql/servers/databases/usages/read | Возвращение сведений об использовании Базы данных SQL Azure. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Удаление оценки уязвимости указанной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Получение политик оценки уязвимости для конкретной базы данных |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Удаление базовых показателей правила оценки уязвимости для указанной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Получение базовых показателей правила оценки уязвимости для указанной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Изменение базовых показателей правила оценки уязвимости для указанной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Преобразование существующего результата проверки в удобочитаемый формат. Если он уже существует, ничего не происходит. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Выполняет поиск уязвимостей базы данных. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Возвращение списка записей сканирования оценки уязвимости базы данных или получение записи для указанного идентификатора сканирования. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Изменение оценки уязвимости указанной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Выполняет поиск уязвимостей базы данных. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Получение результата выполнения сканирования оценки уязвимости базы данных. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Извлечение сведений об оценке уязвимости, настроенной для заданной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Изменение оценки уязвимости указанной базы данных. |
> | Действия | Microsoft.Sql/servers/databases/write | Создание базы данных с указанными параметрами либо обновление свойств или тегов для указанной базы данных. |
> | Действия | Microsoft.Sql/servers/delete | Удаление существующего сервера. |
> | Действия | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Удаление существующих конфигураций аварийного восстановления для указанного сервера. |
> | Действия | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Отработка отказа конфигурации аварийного восстановления. |
> | Действия | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Принудительная отработка отказа конфигурации аварийного восстановления. |
> | Действия | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Получение коллекции конфигураций аварийного восстановления, в которых содержится данный сервер. |
> | Действия | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Изменение конфигурации аварийного восстановления сервера. |
> | Действия | Microsoft.Sql/servers/elasticPoolEstimates/read | Возвращает список оценок эластичных пулов, уже созданных на этом сервере. |
> | Действия | Microsoft.Sql/servers/elasticPoolEstimates/write | Создает оценку эластичного пула для указанных баз данных. |
> | Действия | Microsoft.Sql/servers/elasticPools/advisors/read | Возвращает список доступных помощников для эластичного пула. |
> | Действия | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Возвращает список рекомендуемых действий указанного помощника для эластичного пула. |
> | Действия | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Применяет рекомендуемое действие к эластичному пулу. |
> | Действия | Microsoft.Sql/servers/elasticPools/advisors/write | Обновляет состояние автовыполнения Помощника на уровне эластичного пула. |
> | Действия | Microsoft.Sql/servers/elasticPools/databases/read | Получение списка баз данных в эластичном пуле. |
> | Действия | Microsoft.Sql/servers/elasticPools/delete | Удаление имеющегося эластичного пула. |
> | Действия | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Извлекает действия и сведения о заданном пуле эластичных баз данных. |
> | Действия | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Извлекает действия заданной базы данных, входящей в пул эластичных баз данных, а также сведения о ней. |
> | Действия | Microsoft. SQL/Servers/Еластикпулс/отработка отказа/действие | Клиент инициировал отработку отказа эластичного пула. |
> | Действия | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Возвращает типы метрик, доступных для пулов эластичных баз данных. |
> | Действия | Microsoft.Sql/servers/elasticPools/metrics/read | Возвращение метрик для пулов эластичных баз данных. |
> | Действия | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Отмена приостановленной асинхронной операции эластичного пула SQL Azure, которая еще не завершена. |
> | Действия | Microsoft.Sql/servers/elasticPools/operations/read | Возвращение списка операций, выполняемых в эластичном пуле. |
> | Действия | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действия | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действия | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Возвращает типы метрик, доступных для пулов эластичных баз данных. |
> | Действия | Microsoft.Sql/servers/elasticPools/read | Извлечение сведений о пуле эластичных баз данных для заданного сервера. |
> | Действия | Microsoft.Sql/servers/elasticPools/skus/read | Возвращение коллекции доступных номеров SKU для пула эластичных баз данных. |
> | Действия | Microsoft.Sql/servers/elasticPools/write | Создание эластичного пула или изменение свойств существующего. |
> | Действия | Microsoft.Sql/servers/encryptionProtector/read | Возвращение списка предохранителей шифрования сервера или получение свойств для указанного предохранителя. |
> | Действия | Microsoft. SQL/Servers/Енкриптионпротектор, повторная проверка или действие | Обновление свойств указанного предохранителя шифрования сервера. |
> | Действия | Microsoft.Sql/servers/encryptionProtector/write | Обновление свойств указанного предохранителя шифрования сервера. |
> | Действия | Microsoft.Sql/servers/extendedAuditingSettings/read | Извлечение сведений о расширенной политике аудита больших двоичных объектов, настроенной на заданном сервере. |
> | Действия | Microsoft.Sql/servers/extendedAuditingSettings/write | Изменение расширенного аудита больших двоичных объектов для заданного сервера. |
> | Действия | Microsoft.Sql/servers/failoverGroups/delete | Удаление существующей группы отработки отказа. |
> | Действия | Microsoft.Sql/servers/failoverGroups/failover/action | Выполнение плановой отработки отказа в существующей группе отработки отказа. |
> | Действия | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Выполнение принудительной отработки отказа в существующей группе отработки отказа. |
> | Действия | Microsoft.Sql/servers/failoverGroups/read | Возвращение списка групп отработки отказа или свойств для указанной группы. |
> | Действия | Microsoft.Sql/servers/failoverGroups/write | Создание группы отработки отказа с указанными параметрами либо обновление свойств или тегов указанной группы. |
> | Действия | Microsoft.Sql/servers/firewallRules/delete | Удаление существующего правила брандмауэра сервера. |
> | Действия | Microsoft.Sql/servers/firewallRules/read | Возвращение списка правил брандмауэра сервера или получение свойства для указанного правила. |
> | Действия | Microsoft.Sql/servers/firewallRules/write | Создание правила брандмауэра сервера с указанными параметрами, обновление свойств указанного правила или перезапись всех существующих правил новыми правилами брандмауэра сервера. |
> | Действия | Microsoft.Sql/servers/import/action | Создает базу данных на сервере и развертывает схему и данные из пакета DACPAC. |
> | Действия | Microsoft.Sql/servers/importExportOperationResults/read | Получение выполняемых операций импорта и экспорта. |
> | Действия | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Удаляет профиль определенной конечной точки интерфейса |
> | Действия | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Возвращает свойства для профиля указанной конечной точки интерфейса |
> | Действия | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Создает профиль конечной точки интерфейса с указанными параметрами либо обновляет свойства или теги указанной конечной точки интерфейса |
> | Действия | Microsoft.Sql/servers/jobAgents/delete | Удаляет агент задания базы данных SQL Azure |
> | Действия | Microsoft.Sql/servers/jobAgents/read | Получает агент задания базы данных SQL Azure |
> | Действия | Microsoft.Sql/servers/jobAgents/write | Создает или изменяет агент задания базы данных SQL Azure |
> | Действия | Microsoft.Sql/servers/keys/delete | Удаление существующего ключа сервера. |
> | Действия | Microsoft.Sql/servers/keys/read | Возвращение списка ключей сервера или возвращение свойств указанного ключа сервера. |
> | Действия | Microsoft.Sql/servers/keys/write | Создание ключа с указанными параметрами либо обновление свойств или тегов указанного ключа сервера. |
> | Действия | Microsoft.Sql/servers/operationResults/read | Возвращение выполняемых операций сервера. |
> | Действия | Microsoft. SQL/Servers/Приватиндпоинтконнектионпроксиес/Delete | Удаляет существующий прокси-сервер подключения частной конечной точки. |
> | Действия | Microsoft. SQL/Servers/Приватиндпоинтконнектионпроксиес/Read | Возвращает список прокси-серверов подключения частной конечной точки или получает свойства для указанного прокси подключения к частной конечной точке. |
> | Действия | Microsoft. SQL/Servers/Приватиндпоинтконнектионпроксиес/проверка/действие | Проверяет подключение к частной конечной точке создать вызов с NRP стороны |
> | Действия | Microsoft. SQL/Servers/Приватиндпоинтконнектионпроксиес/Write | Создает прокси-сервер подключения частной конечной точки с указанными параметрами или обновляет свойства или теги для указанного прокси-сервера подключения частной конечной точки. |
> | Действия | Microsoft. SQL/Servers/Приватиндпоинтконнектионс/Delete | Удаляет существующее подключение к частной конечной точке |
> | Действия | Microsoft. SQL/Servers/Приватиндпоинтконнектионс/Read | Возвращает список подключений к частным конечным точкам или возвращает свойства для указанного подключения к частной конечной точке. |
> | Действия | Microsoft. SQL/Servers/Приватиндпоинтконнектионс/Write | Утверждает или отклоняет существующее подключение к частной конечной точке |
> | Действия | Microsoft. SQL/Servers/Приватиндпоинтконнектионсаппровал/действие | Определяет, разрешено ли пользователю утверждать подключение частной конечной точки |
> | Действия | Microsoft. SQL/Servers/Привателинкресаурцес/Read | Получение ресурсов частной ссылки для соответствующего сервера SQL Server |
> | Действия | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Возвращение типов метрик, доступных для серверов. |
> | Действия | Microsoft.Sql/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Действия | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Извлекает метрики для рекомендуемых пулов эластичных баз данных для заданного сервера. |
> | Действия | Microsoft.Sql/servers/recommendedElasticPools/read | Извлекает рекомендацию для пулов эластичных баз данных, позволяющую снизить затраты или повысить производительность, на основе журнала использования ресурсов. |
> | Действия | Microsoft.Sql/servers/recoverableDatabases/read | Эта операция используется для аварийного восстановления динамической базы данных. Она позволяет восстановить базу данных до последней известной корректной точки. Возвращение сведений о последней корректной резервной копии, но без восстановления базы данных. |
> | Действия | Microsoft.Sql/servers/replicationLinks/read | Возвращает список связей репликации или получает свойства для указанных связей репликации. |
> | Действия | Microsoft.Sql/servers/restorableDroppedDatabases/read | Получение списка баз данных, которые были удалены на заданном сервере, но остались в политике хранения. |
> | Действия | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Извлечение результатов операции записи политики обнаружения угроз на сервере. |
> | Действия | Microsoft.Sql/servers/securityAlertPolicies/read | Получение списка политик обнаружения угроз сервера, настроенных для данного сервера |
> | Действия | Microsoft.Sql/servers/securityAlertPolicies/write | Изменение политики обнаружения угроз для заданного сервера. |
> | Действия | Microsoft.Sql/servers/serviceObjectives/read | Извлекает список целей уровня обслуживания (также известных как уровни производительности), доступных на данном сервере. |
> | Действия | Microsoft.Sql/servers/syncAgents/delete | Удаление существующего агента синхронизации. |
> | Действия | Microsoft.Sql/servers/syncAgents/generateKey/action | Создание ключа регистрации агента синхронизации. |
> | Действия | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Получение списка баз данных, связанных с агентом синхронизации. |
> | Действия | Microsoft.Sql/servers/syncAgents/read | Возвращение списка агентов синхронизации или свойств для указанного агента. |
> | Действия | Microsoft.Sql/servers/syncAgents/write | Создание агента синхронизации с указанными параметрами либо обновление свойств указанного агента. |
> | Действия | Microsoft.Sql/servers/tdeCertificates/action | Создание или обновление сертификата TDE |
> | Действия | Microsoft.Sql/servers/usages/read | Возвращение квоты DTU сервера и текущего потребления DTU всеми базами данных на сервере |
> | Действия | Microsoft.Sql/servers/virtualNetworkRules/delete | Удаление существующего правила виртуальной сети. |
> | Действия | Microsoft.Sql/servers/virtualNetworkRules/read | Возвращение списка правил виртуальной сети или свойств указанного правила виртуальной сети. |
> | Действия | Microsoft.Sql/servers/virtualNetworkRules/write | Создание правила виртуальной сети с указанными параметрами либо обновление свойств или тегов указанного правила виртуальной сети. |
> | Действия | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Удаление оценки уязвимости указанного сервера |
> | Действия | Microsoft.Sql/servers/vulnerabilityAssessments/read | Получение политик оценки уязвимости для конкретного сервера |
> | Действия | Microsoft.Sql/servers/vulnerabilityAssessments/write | Изменение оценки уязвимости указанного сервера |
> | Действия | Microsoft.Sql/servers/write | Создание сервера с указанными параметрами либо обновление свойств или тегов указанного сервера. |
> | Действия | Microsoft.Sql/unregister/action | Отмена регистрации подписки для поставщика ресурсов базы данных SQL Azure с возможностью создания баз данных Microsoft SQL. |
> | Действия | Microsoft. SQL/Виртуалклустерс/Delete | Удаляет существующий виртуальный кластер. |
> | Действия | Microsoft.Sql/virtualClusters/read | Возвращение списка виртуальных кластеров или свойств указанного виртуального кластера. |
> | Действия | Microsoft.Sql/virtualClusters/write | Обновление тегов виртуального кластера. |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Storage/checknameavailability/read | Проверяет, является ли имя учетной записи допустимым и неиспользуемым. |
> | Действия | Microsoft. Storage/Locations/чеккнамеаваилабилити/Read | Проверяет, является ли имя учетной записи допустимым и неиспользуемым. |
> | Действия | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Уведомляет поставщик ресурсов Microsoft.Storage о том, что удаляется виртуальная сеть или подсеть. |
> | Действия | Microsoft.Storage/locations/usages/read | Возвращает предельное и текущее число используемых ресурсов для указанной подписки. |
> | Действия | Microsoft.Storage/operations/read | Опрашивает состояние асинхронной операции. |
> | Действия | Microsoft.Storage/register/action | Регистрирует подписку для поставщика ресурсов службы хранилища и позволяет создавать учетные записи хранения. |
> | Действия | Microsoft.Storage/skus/read | Выводит список номеров SKU, поддерживаемых Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Возвращение результата, полученного при добавлении содержимого большого двоичного объекта. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Возвращение результата, полученного при удалении большого двоичного объекта. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Возвращает результат, полученный при удалении автоматического моментального снимка |
> | DataAction | Microsoft. Storage/storageAccounts/Блобсервицес/Containers/blobs/Filter/действие | Возвращает список больших двоичных объектов в учетной записи с совпадающим фильтром тегов |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Возвращение большого двоичного объекта или списка таких объектов. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Возвращает результат выполнения команды для BLOB-объекта |
> | DataAction | Microsoft. Storage/storageAccounts/Блобсервицес/контейнеры/BLOB-объекты/Теги/чтение | Возвращает результат чтения тегов больших двоичных объектов |
> | DataAction | Microsoft. Storage/storageAccounts/Блобсервицес/Containers/blobs/Tags/запись | Возвращает результат записи тегов больших двоичных объектов |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Возвращение результата, полученного при записи большого двоичного объекта. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Снятие с контейнера больших двоичных объектов удержания по юридическим причинам. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Возвращение результата удаления контейнера. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Удаление политики неизменяемости контейнера больших двоичных объектов. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Расширение политики неизменяемости контейнера больших двоичных объектов. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Блокировка политики неизменяемости контейнера больших двоичных объектов. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Получение политики неизменяемости контейнера больших двоичных объектов. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Размещение политики неизменяемости контейнера больших двоичных объектов. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Возвращает результат аренды контейнера больших двоичных объектов. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/read | Возвращает контейнер. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/read | Возвращает список контейнеров. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Постановка контейнера больших двоичных объектов на удержание по юридическим причинам. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/write | Возвращает результат обновления контейнера больших двоичных объектов. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/containers/write | Возвращает результат размещения контейнера больших двоичных объектов. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Возвращает ключ делегирования пользователя для службы BLOB-объектов |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/read | Возвращение свойств или статистики службы BLOB-объектов. |
> | Действия | Microsoft.Storage/storageAccounts/blobServices/write | Возвращение результата настройки свойств службы BLOB-объектов. |
> | Действия | Microsoft.Storage/storageAccounts/delete | Удаляет существующую учетную запись хранения. |
> | Действия | Microsoft. Storage, storageAccounts/Енкриптионскопес/Read |  |
> | Действия | Microsoft. Storage/storageAccounts/Енкриптионскопес/запись |  |
> | Действия | Microsoft. Storage, storageAccounts, отработка отказа или действие | Клиент может управлять отработкой отказа в случае проблем с доступностью |
> | DataAction | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/Files/актассуперусер/Action | Получение привилегий администратора файлов |
> | DataAction | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/Files/Delete | Возвращает результат удаления файла или папки |
> | DataAction | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/Files/модифипермиссионс/Action | Возвращает результат изменения разрешения для файла или папки |
> | DataAction | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/чтение | Возвращает файл или папку или список файлов и папок |
> | DataAction | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/запись | Возвращает результат записи файла или создания папки |
> | Действия | Microsoft. Storage, storageAccounts/Филесервицес/Read |  |
> | Действия | Microsoft. Storage, storageAccounts/Филесервицес/Read | Получение свойств службы файлов |
> | Действия | Microsoft. Storage/storageAccounts/Филесервицес/shares/Delete |  |
> | Действия | Microsoft. Storage/storageAccounts/Филесервицес/shares/Read |  |
> | Действия | Microsoft. Storage/storageAccounts/Филесервицес/shares/Read |  |
> | Действия | Microsoft. Storage/storageAccounts/Филесервицес/shares/Write |  |
> | Действия | Microsoft. Storage/storageAccounts/Филесервицес/запись |  |
> | Действия | Microsoft.Storage/storageAccounts/listAccountSas/action | Возвращает маркер SAS для указанной учетной записи хранения. |
> | Действия | Microsoft.Storage/storageAccounts/listkeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Действия | Microsoft.Storage/storageAccounts/listServiceSas/action | Возвращение токена SAS службы для указанной учетной записи хранения. |
> | Действия | Microsoft. Storage, storageAccounts/МанажементполиЦиес/Delete | Удаление политик управления учетными записями хранения |
> | Действия | Microsoft. Storage, storageAccounts/МанажементполиЦиес/Read | Получение политик учетных записей управления хранилищами |
> | Действия | Microsoft. Storage/storageAccounts/МанажементполиЦиес/запись | Размещение политик управления учетными записями хранения |
> | Действия | Microsoft. Storage, storageAccounts/ОбжектрепликатионполиЦиес/Delete |  |
> | Действия | Microsoft. Storage, storageAccounts/ОбжектрепликатионполиЦиес/Read |  |
> | Действия | Microsoft. Storage/storageAccounts/ОбжектрепликатионполиЦиес/запись |  |
> | Действия | Microsoft. Storage, storageAccounts/Приватиндпоинтконнектионпроксиес/Delete | Удаление прокси-серверов подключения частной конечной точки |
> | Действия | Microsoft. Storage, storageAccounts/Приватиндпоинтконнектионпроксиес/Read | Получение прокси-сервера подключения к частной конечной точке |
> | Действия | Microsoft. Storage/storageAccounts/Приватиндпоинтконнектионпроксиес/запись | Размещение прокси-серверов подключения частной конечной точки |
> | Действия | Microsoft. Storage, storageAccounts/Приватиндпоинтконнектионс/Delete | Удаление подключения к частной конечной точке |
> | Действия | Microsoft. Storage, storageAccounts/Приватиндпоинтконнектионс/Read | Получение подключения к частной конечной точке |
> | Действия | Microsoft. Storage/storageAccounts/Приватиндпоинтконнектионс/запись | Размещение частного подключения конечной точки |
> | Действия | Microsoft. Storage/storageAccounts/Приватиндпоинтконнектионсаппровал/действие | Утверждение подключений к частным конечным точкам |
> | Действия | Microsoft. Storage, storageAccounts/Привателинкресаурцес/Read | Получение StorageAccount граупидс |
> | Действия | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Возвращение результата удаления очереди. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Возвращение результата, полученного при добавлении сообщения. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Возвращение результата, полученного при удалении сообщения. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Возвращение результата, полученного при обработке сообщения. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Возвращение сообщения. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Возвращение результата, полученного при записи сообщения. |
> | Действия | Microsoft.Storage/storageAccounts/queueServices/queues/read | Возвращение очереди или списка очередей. |
> | Действия | Microsoft.Storage/storageAccounts/queueServices/queues/write | Возвращение результата записи очереди. |
> | Действия | Microsoft.Storage/storageAccounts/queueServices/read | Получение свойств служба очередей |
> | Действия | Microsoft.Storage/storageAccounts/queueServices/read | Возвращение свойств или статистики службы очередей. |
> | Действия | Microsoft.Storage/storageAccounts/queueServices/write | Возвращение результата настройки свойств службы очередей. |
> | Действия | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Действия | Microsoft.Storage/storageAccounts/regeneratekey/action | Повторно создает ключи доступа для указанной учетной записи хранения. |
> | Действия | Microsoft. Storage/storageAccounts/Рестореблобранжес/действие | Восстановить диапазоны BLOB-объектов в состояние указанного времени |
> | Действия | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Отменяет все ключи делегирования пользователей для указанной учетной записи хранения. |
> | Действия | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Создает или обновляет параметры диагностики учетной записи хранения. |
> | Действия | Microsoft. Storage, storageAccounts/Таблесервицес/Read | Получение свойств службы таблиц |
> | Действия | Microsoft.Storage/storageAccounts/write | Создает новую учетную запись хранения с указанными параметрами, обновляет свойства или теги указанной существующей учетной записи хранения или добавляет в нее личный домен. |
> | Действия | Microsoft.Storage/usages/read | Возвращает предельное и текущее число используемых ресурсов для указанной подписки. |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | microsoft.storagesync/locations/checkNameAvailability/action | Проверяет, является ли имя службы синхронизации хранилища допустимым и неиспользуемым. |
> | Действия | microsoft.storagesync/locations/workflows/operations/read | Возвращает состояние асинхронной операции. |
> | Действия | Microsoft. storagesync/операции/чтение | Возвращает список поддерживаемых операций. |
> | Действия | Microsoft. storagesync/Register/действие | Регистрирует подписку для поставщика синхронизации хранилища. |
> | Действия | microsoft.storagesync/storageSyncServices/delete | Удаление служб синхронизации хранилища. |
> | Действия | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для служб синхронизации хранилища. |
> | Действия | microsoft.storagesync/storageSyncServices/read | Чтение служб синхронизации хранилища. |
> | Действия | microsoft.storagesync/storageSyncServices/registeredServers/delete | Удаление любого зарегистрированного сервера. |
> | Действия | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Получает доступные метрики для зарегистрированного сервера. |
> | Действия | microsoft.storagesync/storageSyncServices/registeredServers/read | Чтение зарегистрированного сервера. |
> | Действия | microsoft.storagesync/storageSyncServices/registeredServers/write | Создание или обновление любого зарегистрированного сервера. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Удаление конечных точек облака. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Получает состояние асинхронной операции резервного копирования или восстановления |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Это действие следует вызывать после резервного копирования. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Это действие следует вызывать после восстановления. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Это действие следует вызывать до резервного копирования. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Это действие следует вызывать до восстановления. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Чтение конечных точек облака. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Восстановление пакетов пульса. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Создание или обновление конечных точек облака. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/delete | Удаление любой группы синхронизации. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Получает доступные метрики для групп синхронизации. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/read | Чтение всех групп синхронизации. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Удаление любой конечной точки сервера. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Получает доступные метрики для конечных точек сервера. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Чтение конечных точек сервера. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Действие для отзыва файлов на сервере. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Создание или обновление конечных точек сервера. |
> | Действия | microsoft.storagesync/storageSyncServices/syncGroups/write | Создание или обновление групп синхронизации. |
> | Действия | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Возвращает состояние асинхронной операции. |
> | Действия | microsoft.storagesync/storageSyncServices/workflows/operations/read | Возвращает состояние асинхронной операции. |
> | Действия | microsoft.storagesync/storageSyncServices/workflows/read | Чтение рабочих процессов |
> | Действия | microsoft.storagesync/storageSyncServices/write | Создание или обновление служб синхронизации хранилища. |
> | Действия | Microsoft. storagesync/отменить регистрацию или действие | Отменяет регистрацию подписки для поставщика синхронизации хранилища. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.StorSimple/managers/accessControlRecords/delete | Удаляет записи контроля доступа. |
> | Действия | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/accessControlRecords/read | Выводит список записей контроля доступа или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/accessControlRecords/write | Создает или обновляет записи контроля доступа. |
> | Действия | Microsoft.StorSimple/managers/alerts/read | Выводит список оповещений или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/backups/read | Возвращает набор архивации или выводит его. |
> | Действия | Microsoft.StorSimple/managers/bandwidthSettings/delete | Удаляет существующие параметры пропускной способности (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Выводит результаты операции. |
> | Действия | Microsoft.StorSimple/managers/bandwidthSettings/read | Выводит список параметров пропускной способности (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/bandwidthSettings/write | Создает или обновляет параметры пропускной способности (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/certificates/write | Создает или обновляет сертификаты. |
> | Действия | Microsoft.StorSimple/Managers/certificates/write | Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища. |
> | Действия | Microsoft.StorSimple/managers/clearAlerts/action | Очищает все оповещения, связанные с диспетчером устройств. |
> | Действия | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Выводит список конфигураций, поддерживаемых облачным устройством. |
> | Действия | Microsoft.StorSimple/managers/configureDevice/action | Настраивает устройство. |
> | Действия | Microsoft.StorSimple/managers/delete | Удаляет диспетчеры устройств. |
> | Действия | Microsoft.StorSimple/Managers/delete | Операция удаления хранилища удаляет указанный ресурс Azure типа "хранилище". |
> | Действия | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/alertSettings/read | Возвращает параметры оповещения или выводит их список. |
> | Действия | Microsoft.StorSimple/managers/devices/alertSettings/write | Создает или обновляет параметры оповещения. |
> | Действия | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Разрешает смену ключей шифрования службы на устройствах. |
> | Действия | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Выполняет ручную архивацию всех томов, защищенных с помощью политики архивации, для создания их резервной копии по запросу. |
> | Действия | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Удаляет существующие политики архивации (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Выводит результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/backupPolicies/read | Выводит список политик архивации (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Удаляет существующие расписания. |
> | Действия | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Выводит результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Выводит список расписаний. |
> | Действия | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Создает или обновляет расписания. |
> | Действия | Microsoft.StorSimple/managers/devices/backupPolicies/write | Создает или обновляет политики архивации (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/devices/backups/delete | Удаляет набор архивации. |
> | Действия | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Клонирует общедоступный ресурс или том с помощью элемента архива. |
> | Действия | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/backups/read | Возвращает набор архивации или выводит его. |
> | Действия | Microsoft.StorSimple/managers/devices/backups/restore/action | Восстанавливает все тома из набора архивации. |
> | Действия | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Удаляет группы расписаний архивации. |
> | Действия | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Выводит список групп расписаний архивации или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Создает или обновляет группы расписаний архивации. |
> | Действия | Microsoft.StorSimple/managers/devices/chapSettings/delete | Удаляет параметры CHAP. |
> | Действия | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/chapSettings/read | Возвращает параметры CHAP или выводит их список. |
> | Действия | Microsoft.StorSimple/managers/devices/chapSettings/write | Создает или обновляет параметры CHAP. |
> | Действия | Microsoft.StorSimple/managers/devices/deactivate/action | Деактивирует устройство. |
> | Действия | Microsoft.StorSimple/managers/devices/delete | Удаляет устройства. |
> | Действия | Microsoft.StorSimple/managers/devices/disks/read | Выводит список дисков или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/download/action | Скачивание обновлений для устройства. |
> | Действия | Microsoft.StorSimple/managers/devices/failover/action | Выполняет отработку отказа устройства. |
> | Действия | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/failoverTargets/read | Выводит или возвращает цели отработки отказа для устройств. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Архивирует файловый сервер. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/delete | Удаляет файловые серверы. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Выводит список метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/read | Выводит список файловых серверов или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Удаляет общедоступные ресурсы. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Выводит список метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Выводит список общедоступных ресурсов или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Создает или обновляет общедоступные ресурсы. |
> | Действия | Microsoft.StorSimple/managers/devices/fileservers/write | Создает или обновляет файловые серверы. |
> | Действия | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Изменяет состояние питания контроллера для групп компонентов оборудования. |
> | Действия | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Выводит результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Выводит список групп компонентов оборудования. |
> | Действия | Microsoft.StorSimple/managers/devices/install/action | Устанавливает обновления на устройство. |
> | Действия | Microsoft.StorSimple/managers/devices/installUpdates/action | Устанавливает обновления на устройства (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Архивирует сервер iSCSI. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Удаляет серверы iSCSI. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Удаляет диски. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Выводит список метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Выводит список дисков или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Создает или обновляет диски. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Выводит список метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/read | Выводит список серверов iSCSI или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/iscsiservers/write | Создает или обновляет серверы iSCSI. |
> | Действия | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Отменяет выполнение задания. |
> | Действия | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Выводит результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/jobs/read | Выводит список заданий или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Выводит список групп отработки отказа для существующего устройства (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Выводит список целей отработки отказа для устройств (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/devices/metrics/read | Выводит список метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Подтверждает успешный перенос и фиксирует его. |
> | Действия | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Выводит состояние подтверждения переноса. |
> | Действия | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Извлекает состояние подтверждения переноса. |
> | Действия | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Извлекает состояние задания оценки переноса. |
> | Действия | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Извлекает состояние переноса. |
> | Действия | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Импортирует конфигурации источников для переноса. |
> | Действия | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Выводит оценку переноса. |
> | Действия | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Выводит состояние переноса. |
> | Действия | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Выводит результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Начинает перенос с использованием конфигураций источников. |
> | Действия | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Запускает задание для оценки длительности процесса переноса. |
> | Действия | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Выводит результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/networkSettings/read | Возвращает параметры сети или выводит их список. |
> | Действия | Microsoft.StorSimple/managers/devices/networkSettings/write | Создает или обновляет параметры сети. |
> | Действия | Microsoft.StorSimple/managers/devices/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Отображает открытый ключ шифрования для диспетчера устройств. |
> | Действия | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Публикация пакета поддержки для существующего устройства. Пакет поддержки StorSimple — это простой в использовании механизм, который собирает все соответствующие журналы, помогая службе технической поддержки Майкрософт устранять неполадки в работе устройства StorSimple. |
> | Действия | Microsoft.StorSimple/managers/devices/read | Выводит список устройств или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Выполняет поиск обновлений в устройстве. |
> | Действия | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/securitySettings/read | Выводит список параметров безопасности. |
> | Действия | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Синхронизирует сертификат удаленного управления для устройства. |
> | Действия | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Обновляет параметры безопасности. |
> | Действия | Microsoft.StorSimple/managers/devices/securitySettings/write | Создает или обновляет параметры безопасности. |
> | Действия | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Отправляет тестовое сообщение оповещения настроенным получателям электронной почты. |
> | Действия | Microsoft.StorSimple/managers/devices/shares/read | Выводит список общедоступных ресурсов или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Выводит результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/timeSettings/read | Возвращает параметры времени или выводит их список. |
> | Действия | Microsoft.StorSimple/managers/devices/timeSettings/write | Создает или обновляет параметры времени. |
> | Действия | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/updateSummary/read | Возвращает сводку обновлений или выводит их список. |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Удаляет существующие контейнеры томов (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Выводит список метрик. |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Выводит список определений метрик. |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Выводит результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/read | Выводит список контейнеров томов (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Удаляет существующие тома. |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Выводит список метрик. |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Выводит список определений метрик. |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Выводит результаты операции. |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Выводит список томов. |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Создает или обновляет тома. |
> | Действия | Microsoft.StorSimple/managers/devices/volumeContainers/write | Создает или обновляет контейнеры томов (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/devices/volumes/read | Выводит список томов. |
> | Действия | Microsoft.StorSimple/managers/devices/write | Создает или обновляет устройства. |
> | Действия | Microsoft.StorSimple/managers/encryptionSettings/read | Возвращает параметры шифрования или выводит их список. |
> | Действия | Microsoft.StorSimple/managers/extendedInformation/delete | Удаляет расширенные сведения о хранилище. |
> | Действия | Microsoft.StorSimple/Managers/extendedInformation/delete | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действия | Microsoft.StorSimple/managers/extendedInformation/read | Выводит или получает расширенные сведения о хранилище. |
> | Действия | Microsoft.StorSimple/Managers/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действия | Microsoft.StorSimple/managers/extendedInformation/write | Создает или обновляет расширенные сведения о хранилище. |
> | Действия | Microsoft.StorSimple/Managers/extendedInformation/write | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действия | Microsoft.StorSimple/managers/features/read | Выводит список функций. |
> | Действия | Microsoft.StorSimple/managers/fileservers/read | Выводит список файловых серверов или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/getEncryptionKey/action | Возвращает ключ шифрования для диспетчера устройств. |
> | Действия | Microsoft.StorSimple/managers/iscsiservers/read | Выводит список серверов iSCSI или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/jobs/read | Выводит список заданий или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/listActivationKey/action | Возвращает ключ активации диспетчера устройств StorSimple. |
> | Действия | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Выводит список открытых ключей шифрования для диспетчера устройств StorSimple. |
> | Действия | Microsoft.StorSimple/managers/metrics/read | Выводит список метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Переход с классической модели на модель Resource Manager. |
> | Действия | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Вывод списка конфигураций источника переноса (только для серии 8000). |
> | Действия | Microsoft.StorSimple/managers/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Создает облачное устройство. |
> | Действия | Microsoft.StorSimple/managers/read | Выводит список диспетчеров устройств или возвращает их. |
> | Действия | Microsoft.StorSimple/Managers/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Действия | Microsoft.StorSimple/managers/regenerateActivationKey/action | Повторное создание ключа активации для существующего диспетчера устройств StorSimple. |
> | Действия | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Удаляет данные учетной записи хранения. |
> | Действия | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/storageAccountCredentials/read | Выводит список данных учетной записи хранения или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/storageAccountCredentials/write | Создает или обновляет данные учетной записи хранения. |
> | Действия | Microsoft.StorSimple/managers/storageDomains/delete | Удаляет домены хранилища. |
> | Действия | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Выводит или возвращает результаты операции. |
> | Действия | Microsoft.StorSimple/managers/storageDomains/read | Выводит список доменов хранилища или возвращает их. |
> | Действия | Microsoft.StorSimple/managers/storageDomains/write | Создает или обновляет домены хранилища. |
> | Действия | Microsoft.StorSimple/managers/write | Создает или обновляет диспетчеры устройств. |
> | Действия | Microsoft.StorSimple/Managers/write | Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище". |
> | Действия | Microsoft.StorSimple/operations/read | Выводит или возвращает операции. |
> | Действия | Microsoft.StorSimple/register/action | Регистрирует поставщик Microsoft.StorSimple. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.StreamAnalytics/locations/quotas/Read | Чтение квоты подписки Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/operations/Read | Чтение операций Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/Register/action | Регистрация подписки в поставщике ресурсов Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/Delete | Удаляет задание Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Удаление функции задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Чтение результатов операции функции задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Чтение функции задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Получение определения по умолчанию для функции задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Тестирование функции задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Запись функции задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Удаляет входные данные задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Чтение результатов операции для получения входных данных задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Считывает входные данные задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Пример входных данных задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Тестирование входных данных задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Записывает входные данные задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Чтение определений метрик. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Чтение результатов операции задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Удаляет выходные данные задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Чтение результатов операции для получения выходных данных задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Считывает выходные данные задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Тестирование выходных данных задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Записывает выходные данные задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Считывает параметр диагностики. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Записывает параметр диагностики. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Возвращает доступные журналы для заданий потоковой передачи. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Возвращает доступные метрики для заданий потоковой передачи. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/Read | Считывает задание Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/Start/action | Запускает задание Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Останавливает задание Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Удаляет преобразование задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Считывает преобразование задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Записывает преобразование задания Stream Analytics. |
> | Действия | Microsoft.StreamAnalytics/streamingjobs/Write | Записывает задание Stream Analytics. |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft. Subscription/отмена/действие | Отменяет подписку |
> | Действия | Microsoft.Subscription/CreateSubscription/action | Создание подписки Azure |
> | Действия | Microsoft.Subscription/register/action | Регистрирует подписку в поставщике ресурсов Microsoft.Subscription. |
> | Действия | Microsoft. Subscription/Rename/Action | Переименовывает подписку |
> | Действия | Microsoft.Subscription/SubscriptionDefinitions/read | Получение определения подписки Azure в группе управления. |
> | Действия | Microsoft.Subscription/SubscriptionDefinitions/write | Создание определения подписки Azure. |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.Support/register/action | Выполняет регистрацию в поставщике ресурсов поддержки. |
> | Действия | Microsoft.Support/supportTickets/read | Возвращает подробные сведения о запросе в службу поддержки (включая состояние, серьезность, контактную информацию и сообщения) или возвращает список запросов в службу поддержки в подписках. |
> | Действия | Microsoft.Support/supportTickets/write | Создает или обновляет запрос в службу поддержки. Можно создавать запросы в службу поддержки по техническим проблемам, а также проблемам, связанным с выставлением счетов, квотами или управлением подписками. Вы можете изменить уровень серьезности, контактную информацию и сообщения для существующих запросов в службу поддержки. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Удаление политики доступа. |
> | Действия | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Получение свойств политики доступа. |
> | Действия | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Создание новой политики доступа для среды или обновление существующей политики. |
> | Действия | Microsoft.TimeSeriesInsights/environments/delete | Удаление среды. |
> | Действия | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Удаление источника события. |
> | Действия | Microsoft.TimeSeriesInsights/environments/eventsources/read | Получение свойств источника событий. |
> | Действия | Microsoft.TimeSeriesInsights/environments/eventsources/write | Создание нового источника событий для среды или обновление существующего. |
> | Действия | Microsoft.TimeSeriesInsights/environments/read | Получение свойств среды. |
> | Действия | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Удаление эталонного набора данных. |
> | Действия | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Получение свойств эталонного набора данных. |
> | Действия | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Создание нового эталонного набора данных для среды или обновление существующего. |
> | Действия | Microsoft.TimeSeriesInsights/environments/status/read | Получение состояния среды и связанных с ней операций, таких как получение входящего трафика. |
> | Действия | Microsoft.TimeSeriesInsights/environments/write | Создание новой среды или обновление существующей. |
> | Действия | Microsoft.TimeSeriesInsights/register/action | Регистрация подписки для поставщика ресурсов службы "Аналитика временных рядов" с возможностью создания сред данной службы. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.VisualStudio/Account/Delete | Удаление учетной записи. |
> | Действия | Microsoft.VisualStudio/Account/Extension/Read | Считывает учетную запись или расширение. |
> | Действия | Microsoft.VisualStudio/Account/Project/Read | Считывает учетную запись или проект. |
> | Действия | Microsoft.VisualStudio/Account/Project/Write | Настраивает учетную запись или проект. |
> | Действия | Microsoft.VisualStudio/Account/Read | Чтение учетной записи. |
> | Действия | Microsoft.VisualStudio/Account/Write | Настройка учетной записи. |
> | Действия | Microsoft.VisualStudio/Extension/Delete | Удаление расширения. |
> | Действия | Microsoft.VisualStudio/Extension/Read | Чтение расширения. |
> | Действия | Microsoft.VisualStudio/Extension/Write | Установка расширения. |
> | Действия | Microsoft.VisualStudio/Project/Delete | Удаление проекта. |
> | Действия | Microsoft.VisualStudio/Project/Read | Чтение проекта. |
> | Действия | Microsoft.VisualStudio/Project/Write | Настройка проекта. |
> | Действия | Microsoft.VisualStudio/Register/Action | Регистрирует подписку Azure у поставщика Microsoft.VisualStudio. |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | microsoft.web/apimanagementaccounts/apiacls/read | Получение списков ACL для учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Удаление списков ACL для API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/apiacls/read | Получение списков ACL для API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/apiacls/write | Обновление списков ACL для API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Получение списков ACL для подключения API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Подтверждение кода согласия на подключение для API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Удаление списков ACL для подключения API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Получение списков ACL для подключения API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Обновление списков ACL для подключения API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/connections/delete | Удаление подключений API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Получение ссылок на согласие для подключений API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Получение ключей подключения для подключений API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Получение списка секретов для подключений API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/connections/read | Получение подключений API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/connections/write | Обновление подключений API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/delete | Удаление API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Удаление локализованных определений API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Получение локализованных определений API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Обновление локализованных определений API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/read | Получение API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/apis/write | Обновление API-интерфейсов учетных записей управления API. |
> | Действия | microsoft.web/apimanagementaccounts/connectionacls/read | Получение списков ACL для подключения учетных записей управления API. |
> | Действия | microsoft.web/availablestacks/read | Возвращает доступные стеки. |
> | Действия | Microsoft.Web/certificates/Delete | Удаляет существующий сертификат. |
> | Действия | Microsoft.Web/certificates/Read | Возвращает список сертификатов. |
> | Действия | Microsoft.Web/certificates/Write | Добавляет новый сертификат или обновляет существующий. |
> | Действия | microsoft.web/checknameavailability/read | Проверяет, доступно ли имя ресурса. |
> | Действия | microsoft.web/classicmobileservices/read | Возвращает классические мобильные службы. |
> | Действия | Microsoft.Web/connectionGateways/Delete | Удаляет шлюз подключения. |
> | Действия | Microsoft.Web/connectionGateways/Join/Action | Присоединяет шлюз подключения. |
> | Действия | Microsoft.Web/connectionGateways/ListStatus/Action | Получение состояния шлюза подключения. |
> | Действия | Microsoft.Web/connectionGateways/Move/Action | Перемещение шлюза подключения. |
> | Действия | Microsoft.Web/connectionGateways/Read | Возвращает список шлюзов подключений. |
> | Действия | Microsoft.Web/connectionGateways/Write | Создает или обновляет шлюз для подключения. |
> | Действия | microsoft.web/connections/confirmconsentcode/action | Подтверждает код согласия на подключение. |
> | Действия | Microsoft.Web/connections/Delete | Удаляет подключение. |
> | Действия | Microsoft.Web/connections/Join/Action | Присоединяет подключение. |
> | Действия | microsoft.web/connections/listconsentlinks/action | Выводит список ссылок на согласие для подключений. |
> | Действия | Microsoft.Web/connections/Move/Action | Перемещение подключения. |
> | Действия | Microsoft.Web/connections/Read | Возвращает список подключений. |
> | Действия | Microsoft.Web/connections/Write | Создает или обновляет подключение. |
> | Действия | Microsoft.Web/customApis/Delete | Удаление пользовательского API. |
> | Действия | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Извлечение определения API из формата WSDL. |
> | Действия | Microsoft.Web/customApis/Join/Action | Присоединение пользовательского API. |
> | Действия | Microsoft.Web/customApis/listWsdlInterfaces/Action | Получение списка интерфейсов языка WSDL для пользовательского API. |
> | Действия | Microsoft.Web/customApis/Move/Action | Перемещение пользовательского API. |
> | Действия | Microsoft.Web/customApis/Read | Получение списка пользовательских API. |
> | Действия | Microsoft.Web/customApis/Write | Создание или обновление пользовательского API. |
> | Действия | Microsoft.Web/deletedSites/Read | Возвращение свойств удаленного веб-приложения. |
> | Действия | microsoft.web/deploymentlocations/read | Возвращает расположения развертываний. |
> | Действия | Microsoft.Web/geoRegions/Read | Возвращает список географических регионов. |
> | Действия | microsoft.web/hostingenvironments/capacities/read | Возвращает емкости сред внешнего размещения. |
> | Действия | Microsoft.Web/hostingEnvironments/Delete | Удаляет среду службы приложений. |
> | Действия | microsoft.web/hostingenvironments/detectors/read | Возвращение средств обнаружения для сред внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/diagnostics/read | Возвращает данные диагностики сред внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Получение конечных точек сети всех входящих зависимостей. |
> | Действия | Microsoft. Web/hostingEnvironments/Join/действие | Присоединяет Среда службы приложений |
> | Действия | microsoft.web/hostingenvironments/metricdefinitions/read | Возвращает определения метрик сред внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Возвращает определения метрик пулов множественных ролей в средах внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/multirolepools/metrics/read | Возвращает метрики пулов множественных ролей в средах внешнего размещения. |
> | Действия | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Возвращает свойства интерфейсного пула в среде службы приложений. |
> | Действия | microsoft.web/hostingenvironments/multirolepools/skus/read | Возвращает номера SKU пулов множественных ролей в средах внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/multirolepools/usages/read | Возвращает данные об использовании пулов множественных ролей в средах внешнего размещения. |
> | Действия | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Создает новый интерфейсный пул в среде службы приложений или обновляет существующий. |
> | Действия | microsoft.web/hostingenvironments/operations/read | Возвращает операции сред внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Получение конечных точек сети всех исходящих зависимостей. |
> | Действия | Microsoft. Web/hostingEnvironments/Приватиндпоинтконнектионсаппровал/действие | Утверждение подключений к частным конечным точкам |
> | Действия | Microsoft.Web/hostingEnvironments/Read | Возвращает свойства среды службы приложений. |
> | Действия | Microsoft.Web/hostingEnvironments/reboot/Action | Перезагружает все компьютеры в среде службы приложений. |
> | Действия | microsoft.web/hostingenvironments/resume/action | Возобновляет работу сред внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/serverfarms/read | Возвращает планы службы приложений в средах внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/sites/read | Возвращает веб-приложения в средах внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/suspend/action | Приостанавливает работу сред внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/usages/read | Возвращает данные об использовании сред внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Возвращает определения метрик рабочих пулов в средах внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/workerpools/metrics/read | Возвращает метрики рабочих пулов в средах внешнего размещения. |
> | Действия | Microsoft.Web/hostingEnvironments/workerPools/Read | Возвращает свойства рабочего пула в среде службы приложений. |
> | Действия | microsoft.web/hostingenvironments/workerpools/skus/read | Возвращает номера SKU рабочих пулов в средах внешнего размещения. |
> | Действия | microsoft.web/hostingenvironments/workerpools/usages/read | Возвращает данные об использовании рабочих пулов в средах внешнего размещения. |
> | Действия | Microsoft.Web/hostingEnvironments/workerPools/Write | Создает новый рабочий пул в среде службы приложений или обновляет существующий. |
> | Действия | Microsoft.Web/hostingEnvironments/Write | Создает новую среду службы приложений или обновляет существующую. |
> | Действия | microsoft.web/ishostingenvironmentnameavailable/read | Проверяет, доступно ли имя среды внешнего размещения. |
> | Действия | microsoft.web/ishostnameavailable/read | Проверяет, доступно ли имя узла. |
> | Действия | microsoft.web/isusernameavailable/read | Проверяет, доступно ли имя пользователя. |
> | Действия | Microsoft.Web/listSitesAssignedToHostName/Read | Возвращает имена сайтов, назначенные имени узла. |
> | Действия | microsoft.web/locations/apioperations/read | Возвращает операции API расположений. |
> | Действия | microsoft.web/locations/connectiongatewayinstallations/read | Возвращает данные об установленных шлюзах подключений в расположениях. |
> | Действия | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Уведомление об удалении виртуальной сети или подсети для расположений. |
> | Действия | microsoft.web/locations/extractapidefinitionfromwsdl/action | Извлечение определения API для расположений из синтаксиса WSDL. |
> | Действия | microsoft.web/locations/listwsdlinterfaces/action | Получение списка интерфейсов языка WSDL для расположений. |
> | Действия | microsoft.web/locations/managedapis/apioperations/read | Получение операций с управляемыми API для расположений. |
> | Действия | Microsoft.Web/locations/managedapis/Join/Action | Присоединение управляемого API. |
> | Действия | microsoft.web/locations/managedapis/read | Возвращает расположения управляемых интерфейсов API. |
> | Действия | Microsoft. Web/Locations/Оператионресултс/Read | Возвращает операции. |
> | Действия | Microsoft. Web/Locations/Operations/Read | Возвращает операции. |
> | Действия | microsoft.web/operations/read | Возвращает операции. |
> | Действия | microsoft.web/publishingusers/read | Возвращает публикующих пользователей. |
> | Действия | microsoft.web/publishingusers/write | Обновляет публикующих пользователей. |
> | Действия | Microsoft.Web/recommendations/Read | Возвращает список рекомендаций для подписок. |
> | Действия | microsoft.web/register/action | Регистрирует поставщик ресурсов Microsoft.Web для подписки. |
> | Действия | microsoft.web/resourcehealthmetadata/read | Получение метаданных работоспособности ресурса. |
> | Действия | microsoft.web/serverfarms/capabilities/read | Возвращает возможности планов службы приложений. |
> | Действия | Microsoft.Web/serverfarms/Delete | Удаление существующего плана службы приложений |
> | Действия | Microsoft. Web/serverfarms/Евентгридфилтерс/Delete | Удаление фильтра сетки событий в ферме серверов. |
> | Действия | Microsoft. Web/serverfarms/Евентгридфилтерс/Read | Получение фильтра сетки событий на ферме серверов. |
> | Действия | Microsoft. Web/serverfarms/Евентгридфилтерс/Write | Добавьте фильтр сетки событий в ферме серверов. |
> | Действия | microsoft.web/serverfarms/firstpartyapps/settings/delete | Удаляет параметры основных приложений в планах службы приложений. |
> | Действия | microsoft.web/serverfarms/firstpartyapps/settings/read | Возвращает параметры основных приложений в планах службы приложений. |
> | Действия | microsoft.web/serverfarms/firstpartyapps/settings/write | Обновляет параметры основных приложений в планах службы приложений. |
> | Действия | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Удаление ретрансляторов в пространствах имен гибридных подключений для плана службы приложений. |
> | Действия | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Получение ретрансляторов в пространствах имен гибридных подключений для плана службы приложений. |
> | Действия | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Возвращает веб-приложения ретрансляторов в пространствах имен гибридных подключений для плана службы приложений. |
> | Действия | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Возвращает ограничения плана гибридных подключений для плана службы приложений. |
> | Действия | microsoft.web/serverfarms/hybridconnectionrelays/read | Возвращает ретрансляторы гибридных подключений для планов службы приложений. |
> | Действия | microsoft.web/serverfarms/metricdefinitions/read | Возвращает определения метрик планов службы приложений. |
> | Действия | microsoft.web/serverfarms/metrics/read | Возвращает метрики планов службы приложений. |
> | Действия | microsoft.web/serverfarms/operationresults/read | Возвращает результаты операций планов службы приложений. |
> | Действия | Microsoft.Web/serverfarms/Read | Возвращает свойства плана службы приложений. |
> | Действия | Microsoft.Web/serverfarms/restartSites/Action | Перезапустите все веб-приложения в плане службы приложений. |
> | Действия | microsoft.web/serverfarms/sites/read | Возвращает веб-приложения в планах службы приложений. |
> | Действия | microsoft.web/serverfarms/skus/read | Возвращает номера SKU в планах службы приложений. |
> | Действия | microsoft.web/serverfarms/usages/read | Возвращает данные об использовании для планов службы приложений. |
> | Действия | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Обновляет шлюзы подключений виртуальной сети для планов службы приложений. |
> | Действия | microsoft.web/serverfarms/virtualnetworkconnections/read | Возвращает подключения виртуальной сети для планов службы приложений. |
> | Действия | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Удаляет маршруты подключений виртуальной сети для планов службы приложений. |
> | Действия | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Возвращает маршруты подключений виртуальной сети для планов службы приложений. |
> | Действия | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Обновляет маршруты подключений виртуальной сети для планов службы приложений. |
> | Действия | microsoft.web/serverfarms/workers/reboot/action | Перезапускает рабочие роли планов службы приложений. |
> | Действия | Microsoft.Web/serverfarms/Write | Создает новый план службы приложений или обновляет существующий. |
> | Действия | microsoft.web/sites/analyzecustomhostname/read | Анализирует пользовательское имя узла. |
> | Действия | Microsoft.Web/sites/applySlotConfig/Action | Применяет конфигурацию слота веб-приложения из целевого слота к текущему веб-приложению. |
> | Действия | Microsoft.Web/sites/backup/Action | Создает резервную копию веб-приложения. |
> | Действия | microsoft.web/sites/backup/read | Возвращает резервную копию веб-приложений. |
> | Действия | microsoft.web/sites/backup/write | Обновляет резервную копию веб-приложений. |
> | Действия | microsoft.web/sites/backups/action | Обнаружение имеющейся резервной копии приложения, которую можно восстановить из большого двоичного объекта в хранилище Azure. |
> | Действия | microsoft.web/sites/backups/delete | Удаление резервных копий веб-приложений. |
> | Действия | microsoft.web/sites/backups/list/action | Выводит список резервных копий веб-приложений. |
> | Действия | Microsoft.Web/sites/backups/Read | Возвращает свойства резервной копии веб-приложения. |
> | Действия | microsoft.web/sites/backups/restore/action | Восстанавливает резервные копии веб-приложений. |
> | Действия | microsoft.web/sites/backups/write | Обновление резервных копий веб-приложений. |
> | Действия | microsoft.web/sites/config/delete | Удаляет конфигурацию веб-приложений. |
> | Действия | Microsoft.Web/sites/config/list/Action | Отображает влияющие на безопасность параметры веб-приложения, такие как учетные данные для публикации, параметры приложения и строки подключения. |
> | Действия | Microsoft.Web/sites/config/Read | Возвращает параметры конфигурации веб-приложения. |
> | Действия | microsoft.web/sites/config/snapshots/read | Получение моментальных снимков конфигурации веб-приложений. |
> | Действия | Microsoft.Web/sites/config/Write | Обновляет параметры конфигурации веб-приложения. |
> | Действия | microsoft.web/sites/containerlogs/action | Получение сжатых журналов контейнеров для веб-приложений. |
> | Действия | microsoft.web/sites/containerlogs/download/action | Скачивание журналов контейнера веб-приложений. |
> | Действия | microsoft.web/sites/continuouswebjobs/delete | Удаляет непрерывные веб-задания веб-приложений. |
> | Действия | microsoft.web/sites/continuouswebjobs/read | Возвращает непрерывные веб-задания веб-приложений. |
> | Действия | microsoft.web/sites/continuouswebjobs/start/action | Запускает непрерывные веб-задания веб-приложений. |
> | Действия | microsoft.web/sites/continuouswebjobs/stop/action | Останавливает непрерывные веб-задания веб-приложений. |
> | Действия | Microsoft.Web/sites/Delete | Удаление существующего веб-приложения |
> | Действия | microsoft.web/sites/deployments/delete | Удаляет развертывания веб-приложений. |
> | Действия | microsoft.web/sites/deployments/log/read | Возвращает журнал развертываний веб-приложений. |
> | Действия | microsoft.web/sites/deployments/read | Возвращает развертывания веб-приложений. |
> | Действия | microsoft.web/sites/deployments/write | Обновляет развертывания веб-приложений. |
> | Действия | microsoft.web/sites/detectors/read | Получение средств обнаружения для веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/analyses/execute/Action | Запуск анализа данных диагностики веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/analyses/read | Получение анализа данных диагностики веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/aspnetcore/read | Получение данных диагностики веб-приложений для приложения ASP.NET Core. |
> | Действия | microsoft.web/sites/diagnostics/autoheal/read | Получение данных диагностики о функции Autoheal для веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/deployment/read | Получение данных диагностики о развертывании для веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/deployments/read | Получение данных диагностики о развертываниях для веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/detectors/execute/Action | Запуск средства обнаружения диагностики веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/detectors/read | Получение данных средства обнаружения диагностики веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Получение данных диагностики веб-приложений о неудачных запросах для каждого URI. |
> | Действия | microsoft.web/sites/diagnostics/frebanalysis/read | Возвращает данные диагностики об анализе FREB для веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/loganalyzer/read | Получение данных диагностики веб-приложений об анализаторе журналов. |
> | Действия | microsoft.web/sites/diagnostics/read | Получение категорий данных диагностики веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/runtimeavailability/read | Возвращает данные диагностики о доступности среды выполнения для веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/servicehealth/read | Возвращает данные диагностики о работоспособности служб для веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Получение данных диагностики веб-приложений об анализе ЦП для сайта. |
> | Действия | microsoft.web/sites/diagnostics/sitecrashes/read | Получение данных диагностики веб-приложений о сбоях сайта. |
> | Действия | microsoft.web/sites/diagnostics/sitelatency/read | Получение данных диагностики веб-приложений о задержках сайта. |
> | Действия | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Получение данных диагностики веб-приложений об анализе памяти для сайта. |
> | Действия | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Получение данных диагностики веб-приложений об обновлении параметра перезапуска сайта. |
> | Действия | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Получение данных диагностики веб-приложений об инициированном пользователем перезапуске сайта. |
> | Действия | microsoft.web/sites/diagnostics/siteswap/read | Получение данных диагностики веб-приложений о переключении сайта. |
> | Действия | microsoft.web/sites/diagnostics/threadcount/read | Получение данных диагностики веб-приложений о количестве потоков. |
> | Действия | microsoft.web/sites/diagnostics/workeravailability/read | Возвращает данные диагностики о доступности рабочих процессов для веб-приложений. |
> | Действия | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Возвращает данные диагностики перезапуска рабочих процессов для веб-приложений. |
> | Действия | microsoft.web/sites/domainownershipidentifiers/read | Возвращает идентификаторы владения доменом веб-приложений. |
> | Действия | microsoft.web/sites/domainownershipidentifiers/write | Обновляет идентификаторы владения доменом веб-приложений. |
> | Действия | Microsoft. Web/Sites/Евентгридфилтерс/Delete | Удалить фильтр сетки событий в веб-приложении. |
> | Действия | Microsoft. Web/Sites/Евентгридфилтерс/Read | Получение фильтра сетки событий в веб-приложении. |
> | Действия | Microsoft. Web/Sites/Евентгридфилтерс/Write | Разместить фильтр сетки событий в веб-приложении. |
> | Действия | Microsoft. Web/Sites/Extensions/Delete | Удаление расширений сайта веб-приложений. |
> | Действия | Microsoft. Web/Sites/Extensions/Read | Получение расширений сайта веб-приложений. |
> | Действия | Microsoft. Web/Sites/Extensions/Write | Обновление расширений сайта веб-приложений. |
> | Действия | microsoft.web/sites/functions/action | Выполняет функции веб-приложений. |
> | Действия | microsoft.web/sites/functions/delete | Удаляет функции веб-приложений. |
> | Действия | Microsoft. Web/Sites/functions/ключи/удалить | Удаление функциональных клавиш. |
> | Действия | Microsoft. Web/Sites/функции/ключи/запись | Обновление функциональных клавиш. |
> | Действия | Microsoft. Web/Sites/functions/listkeys/Action | Перечисление функциональных клавиш. |
> | Действия | microsoft.web/sites/functions/listsecrets/action | Перечисление секретов функций. |
> | Действия | microsoft.web/sites/functions/masterkey/read | Получение главного ключа функций веб-приложений. |
> | Действия | Microsoft. Web/Sites/функции/свойства/чтение | Чтение свойств функций веб-приложений. |
> | Действия | Microsoft. Web/Sites/функции/свойства/запись | Обновляет свойства функций веб-приложений. |
> | Действия | microsoft.web/sites/functions/read | Возвращает функции веб-приложений. |
> | Действия | microsoft.web/sites/functions/token/read | Получение токена функций веб-приложений. |
> | Действия | microsoft.web/sites/functions/write | Обновляет функции веб-приложений. |
> | Действия | Microsoft. Web/Sites/Host/функтионкэйс/Delete | Функции Delete размещают ключи функций. |
> | Действия | Microsoft. Web/Sites/Host/функтионкэйс/Write | Функции обновления размещают ключи функций. |
> | Действия | Microsoft. Web/Sites/Host/listkeys/действие | Вывод списка функций ключи узла. |
> | Действия | Microsoft. Web/Sites/Host/листсинкстатус/действие | Перечисление состояния триггеров функции синхронизации. |
> | Действия | Microsoft. Web/Sites/узел/свойства/чтение | Чтение свойств узла функций веб-приложений. |
> | Действия | Microsoft. Web/Sites/узел/Синхронизация/действие | Триггеры синхронизации функций. |
> | Действия | Microsoft. Web/Sites/Host/системкэйс/Delete | Удалить функции системные ключи узла. |
> | Действия | Microsoft. Web/Sites/Host/системкэйс/Write | Функции обновления размещают системные ключи. |
> | Действия | microsoft.web/sites/hostnamebindings/delete | Удаляет привязки имен узлов для веб-приложений. |
> | Действия | microsoft.web/sites/hostnamebindings/read | Возвращает привязки имен узлов для веб-приложений. |
> | Действия | microsoft.web/sites/hostnamebindings/write | Обновляет привязки имен узлов для веб-приложений. |
> | Действия | microsoft.web/sites/hostruntime/functions/keys/read | Получение ключей функций среды выполнения узла веб-приложений. |
> | Действия | Microsoft.Web/sites/hostruntime/host/_master/read | Получение главного ключа приложения-функции для операций администрирования. |
> | Действия | Microsoft.Web/sites/hostruntime/host/action | Выполнение действий среды выполнения приложения-функции, таких как синхронизация триггеров, добавление функций, вызов функций, их удаление и т.д. |
> | Действия | microsoft.web/sites/hostruntime/host/read | Получение узла для среды выполнения узла веб-приложений. |
> | Действия | microsoft.web/sites/hybridconnection/delete | Удаляет гибридное подключение для веб-приложений. |
> | Действия | microsoft.web/sites/hybridconnection/read | Возвращает гибридное подключение для веб-приложений. |
> | Действия | microsoft.web/sites/hybridconnection/write | Обновляет гибридное подключение для веб-приложений. |
> | Действия | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Удаление ретрансляторов в пространствах имен гибридных подключений для веб-приложений. |
> | Действия | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Получение списка ключей ретрансляторов в пространствах имен гибридных подключений для веб-приложений. |
> | Действия | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Получение ретрансляторов в пространствах имен гибридных подключений для веб-приложений. |
> | Действия | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Обновление ретрансляторов в пространствах имен гибридных подключений для веб-приложений. |
> | Действия | microsoft.web/sites/hybridconnectionrelays/read | Возвращает ретрансляторы гибридных подключений для веб-приложений. |
> | Действия | microsoft.web/sites/instances/deployments/delete | Удаление развертываний экземпляров веб-приложений. |
> | Действия | microsoft.web/sites/instances/deployments/read | Возвращает развертывания экземпляров веб-приложений. |
> | Действия | microsoft.web/sites/instances/extensions/log/read | Получение журнала расширений экземпляров веб-приложений. |
> | Действия | microsoft.web/sites/instances/extensions/processes/read | Получение процессов расширений экземпляров веб-приложений. |
> | Действия | microsoft.web/sites/instances/extensions/read | Получение расширений экземпляров веб-приложений. |
> | Действия | microsoft.web/sites/instances/processes/delete | Удаляет процессы экземпляров веб-приложений. |
> | Действия | microsoft.web/sites/instances/processes/modules/read | Получение модулей процессов экземпляров веб-приложений. |
> | Действия | microsoft.web/sites/instances/processes/read | Возвращает процессы экземпляров веб-приложений. |
> | Действия | microsoft.web/sites/instances/processes/threads/read | Возвращение потоков процессов экземпляров веб-приложений. |
> | Действия | microsoft.web/sites/instances/read | Возвращает экземпляры веб-приложений. |
> | Действия | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Вывод состояния триггера для функции синхронизации. |
> | Действия | microsoft.web/sites/metricdefinitions/read | Возвращает определения метрик веб-приложений. |
> | Действия | microsoft.web/sites/metrics/read | Возвращает метрики веб-приложений. |
> | Действия | microsoft.web/sites/metricsdefinitions/read | Получение определений метрик веб-приложений. |
> | Действия | microsoft.web/sites/migratemysql/action | Перенос веб-приложений MySQL. |
> | Действия | microsoft.web/sites/migratemysql/read | Получение сведений о переносе веб-приложений MySQL. |
> | Действия | microsoft.web/sites/networktrace/action | Трассирует сеть для веб-приложений. |
> | Действия | Microsoft. Web/Sites/нетворктрацес/оператионресултс/Read | Возвращает результаты операции трассировки сети для веб-приложений. |
> | Действия | microsoft.web/sites/newpassword/action | Задает новый пароль для веб-приложений. |
> | Действия | microsoft.web/sites/operationresults/read | Возвращает результаты операций веб-приложений. |
> | Действия | microsoft.web/sites/operations/read | Получение операций веб-приложений. |
> | Действия | microsoft.web/sites/perfcounters/read | Возвращает счетчики производительности веб-приложений. |
> | Действия | microsoft.web/sites/premieraddons/delete | Удаляет надстройки Premier для веб-приложений. |
> | Действия | microsoft.web/sites/premieraddons/read | Возвращает надстройки Premier для веб-приложений. |
> | Действия | microsoft.web/sites/premieraddons/write | Обновляет надстройки Premier для веб-приложений. |
> | Действия | microsoft.web/sites/privateaccess/read | Возвращение данных контроля доступа к частным сайтам и авторизованные виртуальные сети, у которых есть доступ к сайту. |
> | Действия | Microsoft. Web/Sites/Приватиндпоинтконнектионсаппровал/Action | Утверждение подключений к частным конечным точкам |
> | Действия | Microsoft. Web/Sites/Processes/modules/Read | Возвращает веб-приложения, обрабатывающие модули. |
> | Действия | microsoft.web/sites/processes/read | Получение процессов веб-приложений. |
> | Действия | Microsoft. Web/Sites/Processes/threads/Read | Возвращает веб-приложения, обрабатывающие потоки. |
> | Действия | microsoft.web/sites/publiccertificates/delete | Удаление открытых сертификатов веб-приложений. |
> | Действия | microsoft.web/sites/publiccertificates/read | Получение открытых сертификатов веб-приложений. |
> | Действия | microsoft.web/sites/publiccertificates/write | Обновление открытых сертификатов веб-приложений. |
> | Действия | Microsoft.Web/sites/publish/Action | Публикует веб-приложение. |
> | Действия | Microsoft.Web/sites/publishxml/Action | Возвращает XML-файл профиля публикации для веб-приложения. |
> | Действия | microsoft.web/sites/publishxml/read | Возвращает XML-файл профиля публикации для веб-приложений. |
> | Действия | Microsoft.Web/sites/Read | Возвращает свойства веб-приложения. |
> | Действия | microsoft.web/sites/recommendationhistory/read | Возвращает журнал рекомендаций для веб-приложения. |
> | Действия | microsoft.web/sites/recommendations/disable/action | Отключает рекомендации для веб-приложений. |
> | Действия | Microsoft.Web/sites/recommendations/Read | Возвращает список рекомендаций для веб-приложения. |
> | Действия | microsoft.web/sites/recover/action | Восстановление веб-приложений. |
> | Действия | Microsoft.Web/sites/resetSlotConfig/Action | Сбрасывает конфигурацию веб-приложения. |
> | Действия | microsoft.web/sites/resourcehealthmetadata/read | Получение метаданных работоспособности ресурсов веб-приложений. |
> | Действия | Microsoft.Web/sites/restart/Action | Перезапускает веб-приложение. |
> | Действия | microsoft.web/sites/restore/read | Возвращает данные о восстановлении веб-приложений. |
> | Действия | microsoft.web/sites/restore/write | Восстановление веб-приложений. |
> | Действия | microsoft.web/sites/restorefrombackupblob/action | Восстановление веб-приложения из большого двоичного объекта резервных копий. |
> | Действия | Microsoft. Web/Sites/ресторефромделетедапп/Action | Восстановление веб-приложений из удаленного приложения. |
> | Действия | microsoft.web/sites/restoresnapshot/action | Восстановление моментальных снимков веб-приложений. |
> | Действия | microsoft.web/sites/siteextensions/delete | Удаление расширений сайта веб-приложений. |
> | Действия | microsoft.web/sites/siteextensions/read | Получение расширений сайта веб-приложений. |
> | Действия | microsoft.web/sites/siteextensions/write | Обновление расширений сайта веб-приложений. |
> | Действия | microsoft.web/sites/slots/analyzecustomhostname/read | Возвращает результаты анализа пользовательского имени узла для слотов веб-приложений. |
> | Действия | Microsoft.Web/sites/slots/applySlotConfig/Action | Применяет конфигурацию слота веб-приложения из целевого слота к текущему слоту. |
> | Действия | Microsoft.Web/sites/slots/backup/Action | Создает резервную копию слота веб-приложения. |
> | Действия | microsoft.web/sites/slots/backup/read | Получение резервной копии слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/backup/write | Обновляет резервную копию слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/backups/action | Обнаружение резервных копий слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/backups/delete | Удаление резервной копии слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/backups/list/action | Выводит список резервных копий слотов веб-приложений. |
> | Действия | Microsoft.Web/sites/slots/backups/Read | Возвращает свойства резервной копии слотов веб-приложения. |
> | Действия | microsoft.web/sites/slots/backups/restore/action | Восстанавливает резервные копии слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/config/delete | Удаляет конфигурацию слотов веб-приложений. |
> | Действия | Microsoft.Web/sites/slots/config/list/Action | Отображает влияющие на безопасность параметры слота веб-приложения, такие как учетные данные для публикации, параметры приложения и строки подключения. |
> | Действия | Microsoft.Web/sites/slots/config/Read | Возвращает параметры конфигурации слота веб-приложения. |
> | Действия | Microsoft.Web/sites/slots/config/Write | Обновляет параметры конфигурации слота веб-приложения. |
> | Действия | microsoft.web/sites/slots/containerlogs/action | Получение сжатых журналов контейнеров для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/containerlogs/download/action | Скачивание журналов контейнера слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/continuouswebjobs/delete | Удаляет непрерывные веб-задания слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/continuouswebjobs/read | Возвращает непрерывные веб-задания слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/continuouswebjobs/start/action | Запускает непрерывные веб-задания слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/continuouswebjobs/stop/action | Останавливает непрерывные веб-задания слотов веб-приложений. |
> | Действия | Microsoft.Web/sites/slots/Delete | Удаляет существующий слот веб-приложения. |
> | Действия | microsoft.web/sites/slots/deployments/delete | Удаляет развертывания слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/deployments/log/read | Возвращает журнал развертываний слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/deployments/read | Возвращает развертывания слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/deployments/write | Обновляет развертывания слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/detectors/read | Возвращение средств обнаружения слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Запуск анализа данных диагностики слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/analyses/read | Получение анализа данных диагностики слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Получение данных диагностики слотов для веб-приложений для приложения ASP.NET Core. |
> | Действия | microsoft.web/sites/slots/diagnostics/autoheal/read | Получение данных диагностики слотов о функции Autoheal для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/deployment/read | Получение данных диагностики слотов о развертывании для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/deployments/read | Получение данных диагностики слотов о развертываниях для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Запуск средства обнаружения данных диагностики слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/detectors/read | Получение сведений средства обнаружения данных диагностики слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Возвращение данных диагностики об анализе FREB для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Получение данных диагностики слотов для веб-приложений об анализаторе журналов. |
> | Действия | microsoft.web/sites/slots/diagnostics/read | Получение данных диагностики слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Получение данных диагностики слотов о доступности среды выполнения для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/servicehealth/read | Получение данных диагностики слотов о работоспособности служб для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Получение данных диагностики слотов об анализе ЦП сайта для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Получение данных диагностики слотов о сбоях сайта для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/sitelatency/read | Получение данных диагностики слотов о задержке сайта для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Получение данных диагностики слотов об анализе памяти сайта для веб-приложений. |
> | Действия | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Получение данных диагностики слотов для веб-приложений об обновлении параметра перезапуска сайта. |
> | Действия | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Получение данных диагностики слотов для веб-приложений об инициированном пользователем перезапуске сайта. |
> | Действия | microsoft.web/sites/slots/diagnostics/siteswap/read | Получение данных диагностики слотов для веб-приложений о переключении сайта. |
> | Действия | microsoft.web/sites/slots/diagnostics/threadcount/read | Получение данных диагностики слотов для веб-приложений о количестве потоков. |
> | Действия | microsoft.web/sites/slots/diagnostics/workeravailability/read | Получение данных диагностики слотов для веб-приложений о доступности рабочих процессов. |
> | Действия | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Получение данных диагностики слотов для веб-приложений о перезапуске рабочих процессов. |
> | Действия | microsoft.web/sites/slots/domainownershipidentifiers/read | Получение идентификаторов владения доменом слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/functions/listsecrets/action | Вывод списка секретов для функций слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/functions/read | Возвращение функций слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/hostnamebindings/delete | Удаляет привязки имен узлов для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/hostnamebindings/read | Возвращает привязки имен узлов для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/hostnamebindings/write | Обновляет привязки имен узлов для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/hybridconnection/delete | Удаляет гибридное подключение для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/hybridconnection/read | Возвращает гибридное подключение для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/hybridconnection/write | Обновляет гибридное подключение для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Удаление ретрансляторов в пространствах имен гибридных подключений слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Обновление ретрансляторов в пространствах имен гибридных подключений слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/hybridconnectionrelays/read | Получение ретрансляторов гибридных подключений слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/instances/deployments/read | Возвращает развертывания экземпляры слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/instances/processes/delete | Удаление процессов экземпляров слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/instances/processes/read | Возвращает процессы экземпляры слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/instances/read | Возвращает экземпляры слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/metricdefinitions/read | Возвращает определения метрик слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/metrics/read | Возвращает метрики слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/migratemysql/read | Получение сведений о переносе слотов для веб-приложений MySQL. |
> | Действия | microsoft.web/sites/slots/networktrace/action | Трассировка сети слотов для веб-приложений. |
> | Действия | Microsoft. Web/Sites/Slots/нетворктрацес/оператионресултс/Read | Возвращает результаты операции трассировки сети для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/newpassword/action | Задает новый пароль для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/operationresults/read | Возвращает результаты операций слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/operations/read | Получение операций слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/perfcounters/read | Получение счетчиков производительности слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/phplogging/read | Возвращает сведения о ведении журнала PHP для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/premieraddons/delete | Удаляет надстройки Premier для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/premieraddons/read | Возвращает надстройки Premier для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/premieraddons/write | Обновляет надстройки Premier для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/processes/read | Получение процессов слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/publiccertificates/delete | Удаление открытых сертификатов слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/publiccertificates/read | Получение открытых сертификатов слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/publiccertificates/write | Создание или обновление открытых сертификатов слотов для веб-приложений. |
> | Действия | Microsoft.Web/sites/slots/publish/Action | Публикует слот веб-приложения. |
> | Действия | Microsoft.Web/sites/slots/publishxml/Action | Возвращает XML-файл профиля публикации для слота веб-приложения. |
> | Действия | Microsoft.Web/sites/slots/Read | Возвращает свойства слота развертывания веб-приложения. |
> | Действия | microsoft.web/sites/slots/recover/action | Восстановление слотов веб-приложений. |
> | Действия | Microsoft.Web/sites/slots/resetSlotConfig/Action | Сбрасывает конфигурацию слота веб-приложения. |
> | Действия | microsoft.web/sites/slots/resourcehealthmetadata/read | Получение метаданных работоспособности ресурса слотов для веб-приложений. |
> | Действия | Microsoft.Web/sites/slots/restart/Action | Перезапускает слот веб-приложения. |
> | Действия | microsoft.web/sites/slots/restore/read | Возвращает данные о восстановлении слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/restore/write | Восстановление слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/restorefrombackupblob/action | Восстановление слота веб-приложений из большого двоичного объекта резервных копий. |
> | Действия | Microsoft. Web/Sites/Slots/ресторефромделетедапп/Action | Восстановление слотов веб-приложений из удаленного приложения. |
> | Действия | microsoft.web/sites/slots/restoresnapshot/action | Восстановление моментальных снимков слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/siteextensions/delete | Удаление расширений сайта слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/siteextensions/read | Получение расширений сайта слотов для веб-приложений. |
> | Действия | microsoft.web/sites/slots/siteextensions/write | Обновление расширений сайта слотов для веб-приложений. |
> | Действия | Microsoft.Web/sites/slots/slotsdiffs/Action | Возвращает различия конфигураций веб-приложения и слотов. |
> | Действия | Microsoft.Web/sites/slots/slotsswap/Action | Переключает слоты развертывания веб-приложения. |
> | Действия | microsoft.web/sites/slots/snapshots/read | Получение моментальных снимков слотов для веб-приложений. |
> | Действия | Microsoft.Web/sites/slots/sourcecontrols/Delete | Удаляет параметры конфигурации системы управления версиями для слота веб-приложения. |
> | Действия | Microsoft.Web/sites/slots/sourcecontrols/Read | Возвращает параметры конфигурации системы управления версиями для слота веб-приложения. |
> | Действия | Microsoft.Web/sites/slots/sourcecontrols/Write | Обновляет параметры конфигурации системы управления версиями для слота веб-приложения. |
> | Действия | Microsoft.Web/sites/slots/start/Action | Запускает слот веб-приложения. |
> | Действия | Microsoft.Web/sites/slots/stop/Action | Останавливает слот веб-приложения. |
> | Действия | microsoft.web/sites/slots/sync/action | Синхронизирует слоты веб-приложений. |
> | Действия | microsoft.web/sites/slots/triggeredwebjobs/delete | Удаляет активированные веб-задания слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/triggeredwebjobs/read | Возвращает активированные веб-задания слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/triggeredwebjobs/run/action | Выполняет активированные веб-задания слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/usages/read | Возвращает данные об использовании слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/virtualnetworkconnections/delete | Удаляет подключения виртуальных сетей для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Обновляет шлюзы подключений виртуальных сетей для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/virtualnetworkconnections/read | Возвращает подключения виртуальных сетей для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/virtualnetworkconnections/write | Обновляет подключения виртуальных сетей для слотов веб-приложений. |
> | Действия | microsoft.web/sites/slots/webjobs/read | Возвращает веб-задания слотов веб-приложений. |
> | Действия | Microsoft.Web/sites/slots/Write | Создает новый слот веб-приложения или обновляет существующий. |
> | Действия | Microsoft.Web/sites/slotsdiffs/Action | Возвращает различия конфигураций веб-приложения и слотов. |
> | Действия | Microsoft.Web/sites/slotsswap/Action | Переключает слоты развертывания веб-приложения. |
> | Действия | microsoft.web/sites/snapshots/read | Возвращает моментальные снимки веб-приложений. |
> | Действия | Microsoft.Web/sites/sourcecontrols/Delete | Удаляет параметры конфигурации системы управления версиями веб-приложения. |
> | Действия | Microsoft.Web/sites/sourcecontrols/Read | Возвращает параметры конфигурации системы управления версиями веб-приложения. |
> | Действия | Microsoft.Web/sites/sourcecontrols/Write | Обновляет параметры конфигурации системы управления версиями веб-приложения. |
> | Действия | Microsoft.Web/sites/start/Action | Запускает веб-приложение. |
> | Действия | Microsoft.Web/sites/stop/Action | Останавливает веб-приложение. |
> | Действия | microsoft.web/sites/sync/action | Синхронизирует веб-приложения. |
> | Действия | microsoft.web/sites/syncfunctiontriggers/action | Триггеры синхронизации функций. |
> | Действия | microsoft.web/sites/triggeredwebjobs/delete | Удаляет активированные веб-задания веб-приложений. |
> | Действия | microsoft.web/sites/triggeredwebjobs/history/read | Получение истории активированных веб-заданий для веб-приложений. |
> | Действия | microsoft.web/sites/triggeredwebjobs/read | Возвращает активированные веб-задания веб-приложений. |
> | Действия | microsoft.web/sites/triggeredwebjobs/run/action | Выполняет активированные веб-задания веб-приложений. |
> | Действия | microsoft.web/sites/usages/read | Возвращает данные об использовании веб-приложений. |
> | Действия | microsoft.web/sites/virtualnetworkconnections/delete | Удаляет подключения виртуальных сетей веб-приложений. |
> | Действия | microsoft.web/sites/virtualnetworkconnections/gateways/read | Возвращает шлюзы подключений виртуальных сетей веб-приложений. |
> | Действия | microsoft.web/sites/virtualnetworkconnections/gateways/write | Обновляет шлюзы подключений виртуальных сетей веб-приложений. |
> | Действия | microsoft.web/sites/virtualnetworkconnections/read | Возвращает подключения виртуальных сетей веб-приложений. |
> | Действия | microsoft.web/sites/virtualnetworkconnections/write | Обновляет подключения виртуальных сетей веб-приложений. |
> | Действия | microsoft.web/sites/webjobs/read | Возвращает веб-задания веб-приложений. |
> | Действия | Microsoft.Web/sites/Write | Создает новое веб-приложение или обновляет существующее. |
> | Действия | microsoft.web/skus/read | Возвращает номера SKU. |
> | Действия | microsoft.web/sourcecontrols/read | Возвращает систему управления версиями. |
> | Действия | microsoft.web/sourcecontrols/write | Обновляет системы управления версиями. |
> | Действия | microsoft.web/unregister/action | Отменяет регистрацию поставщика ресурсов Microsoft.Web для подписки. |
> | Действия | microsoft.web/validate/action | Выполняет проверку. |
> | Действия | microsoft.web/verifyhostingenvironmentvnet/action | Проверка виртуальной сети среды размещения. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | Описание |
> | --- | --- | --- |
> | Действия | Microsoft.WorkloadMonitor/components/read | Возвращает компоненты ресурса. |
> | Действия | Microsoft.WorkloadMonitor/componentsSummary/read | Возвращает сводки компонентов. |
> | Действия | Microsoft.WorkloadMonitor/monitorInstances/read | Получает экземпляры мониторов ресурса. |
> | Действия | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Получает сводку экземпляров монитора. |
> | Действия | Microsoft.WorkloadMonitor/monitors/read | Получает мониторы ресурсов. |
> | Действия | Microsoft.WorkloadMonitor/monitors/write | Настойка монитора ресурса. |
> | Действия | Microsoft.WorkloadMonitor/notificationSettings/read | Получает параметры уведомлений для ресурса. |
> | Действия | Microsoft.WorkloadMonitor/notificationSettings/write | Настройка параметров уведомлений для ресурса. |
> | Действия | Microsoft.WorkloadMonitor/operations/read | Получает поддерживаемые операции. |

## <a name="next-steps"></a>Дальнейшие действия

- [Сопоставить поставщик ресурсов со службой](../azure-resource-manager/azure-services-resource-providers.md)
- [Пользовательские роли для ресурсов Azure](custom-roles.md)
- [Встроенные роли управления доступом на основе ролей в Azure](built-in-roles.md)
