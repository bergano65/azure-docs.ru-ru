---
title: Заметки о выпуске Центра безопасности Azure
description: Описание новых возможностей и изменений в Центре безопасности Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2020
ms.author: memildin
ms.openlocfilehash: 6de06e4ab9a302517a09b34de56c1b5535b245a9
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93357816"
---
# <a name="whats-new-in-azure-security-center"></a>Новые возможности в Центре безопасности Azure

Центр безопасности находится в состоянии активной разработки, и мы постоянно совершенствуем его. Чтобы вы оставались в курсе последних разработок, на этой странице представлены сведения о новых функциях, исправленных ошибках и устаревших функциях.

Эта страница регулярно обновляется, поэтому рекомендуем часто просматривать ее. 

Чтобы узнать о *запланированных* изменениях, см. статью [Важные изменения, которые скоро будут реализованы в Центре безопасности Azure](upcoming-changes.md). 

> [!TIP]
> Если вам понадобятся материалы, которым более шести месяцев, см. [Архив сведений о новых возможностях Центра безопасности Azure](release-notes-archive.md).


## <a name="november-2020"></a>Ноябрь 2020 г.

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Добавлены 29 рекомендаций для предварительной версии, чтобы повысить охват Тестов производительности системы безопасности Azure

Тест производительности системы безопасности Azure — это руководства с рекомендациями по обеспечению безопасности и соответствия требованиям для Azure, подготовленные корпорацией Майкрософт на основе распространенных платформ соответствия. См. сведения о [Тесте производительности системы безопасности Azure](../security/benchmarks/introduction.md).

Следующие 29 новых рекомендаций для предварительной версии добавлены в Центр безопасности, чтобы увеличить охват теста производительности.

Рекомендации предварительной версии не присваивают ресурсу состояние неработоспособного и не включаются в вычисления вашей оценки безопасности. По возможности их все равно нужно разрешить, так как по окончанию периода предварительной версии они повлияют на оценку. Дополнительные сведения о том, как реагировать на эти рекомендации, см. в статье [Рекомендации по исправлению ошибок в Центре безопасности Azure](security-center-remediate-recommendations.md).

| Управление безопасностью                     | Новые рекомендации                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Шифрование передаваемых данных              | – Для серверов баз данных PostgreSQL должно быть включено принудительное использование SSL-соединения<br>– Для серверов баз данных MySQL должно быть включено принудительное использование SSL-соединения<br>– Для вашего приложения API необходимо обновить TLS до последней версии<br>– Для вашего приложения-функции необходимо обновить TLS до последней версии<br>– Для вашего веб-приложения необходимо обновить TLS до последней версии<br>– В вашем приложении API необходимо принудительно использовать FTPS<br>– В вашем приложении-функции необходимо принудительно использовать FTPS<br>– В вашем веб-приложении необходимо принудительно использовать FTPS                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Управление доступом и разрешения        | – Веб-приложения должны запрашивать сертификат SSL для всех входящих запросов<br>– В приложении API должно использоваться управляемое удостоверение<br>– В приложении-функции должно использоваться управляемое удостоверение<br>– В веб-приложении должно использоваться управляемое удостоверение                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Ограничение несанкционированного доступа к сети | – Для серверов PostgreSQL необходимо включить частную конечную точку<br>– Для серверов MariaDB необходимо включить частную конечную точку<br>– Для серверов MySQL необходимо включить частную конечную точку                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Включение аудита и ведения журнала          | – В Службах приложений должны быть включены журналы диагностики                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Реализация рекомендаций по безопасности    | – Необходимо включить Azure Backup для виртуальных машин<br>– База данных Azure для MariaDB должна использовать геоизбыточное резервное копирование<br>– База данных Azure для MySQL должна использовать геоизбыточное резервное копирование<br>– База данных Azure для PostgreSQL должна использовать геоизбыточное резервное копирование<br>– Для вашего приложения API необходимо обновить PHP до последней версии<br>– Для вашего веб-приложения необходимо обновить PHP до последней версии<br>– Для вашего приложения API необходимо обновить Java до последней версии<br>– Для вашего приложения-функции необходимо обновить Java до последней версии<br>– Для вашего веб-приложения необходимо обновить Java до последней версии<br>– Для вашего приложения API необходимо обновить Python до последней версии<br>– Для вашего приложения-функции необходимо обновить Python до последней версии<br>– Для вашего веб-приложения необходимо обновить Python до последней версии<br>– Аудит для серверов SQL должен храниться не менее 90 дней |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Связанные ссылки

- [Общие сведения о тесте производительности системы безопасности Azure](../security/benchmarks/introduction.md).
- [Дополнительные сведения о приложениях API](../app-service/app-service-web-tutorial-rest-api.md).
- [Дополнительные сведения о приложениях-функциях Azure](../azure-functions/functions-overview.md).
- [Дополнительные сведения о веб-приложениях Azure](../app-service/overview.md).
- [Дополнительные сведения о Базе данных Azure для MariaDB](../mariadb/overview.md).
- [Дополнительные сведения о Базе данных Azure для MySQL](../mysql/overview.md).
- [Дополнительные сведения о Базе данных Azure для PostgreSQL](../postgresql/overview.md).




## <a name="october-2020"></a>Октябрь 2020 г.

- [Оценка уязвимостей для компьютеров в локальной среде и нескольких облаках (предварительная версия)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Добавлена рекомендация Брандмауэра Azure (предварительная версия)](#azure-firewall-recommendation-added-preview)
- [Рекомендация "В службах Kubernetes нужно определить разрешенные диапазоны IP-адресов" обновлена с помощью быстрого исправления](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [На панели мониторинга соответствия нормативным требованиям теперь есть параметр для удаления стандартов](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Таблица Microsoft.Security/securityStatuses удалена из Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Оценка уязвимостей для компьютеров в локальной среде и нескольких облаках (предварительная версия)

Средство оценки уязвимостей в [Azure Defender для серверов](defender-for-servers-introduction.md) (на основе Qualys) теперь проверяет серверы с поддержкой Azure Arc.

Когда вы включаете Azure Arc на компьютерах, не относящихся к Azure, Центр безопасности предлагает развернуть на них встроенный сканер уязвимостей — вручную и в большом масштабе.

Начиная с этого обновления, вы сможете применять возможности **Azure Defender для серверов** , чтобы объединить программы управления уязвимостями для всех ресурсов в Azure и других средах.

Основные возможности:

- мониторинг состояния подготовки средства оценки уязвимостей на компьютерах Azure Arc;
- подготовка к работе интегрированного агента оценки уязвимостей для незащищенных компьютеров Azure Arc под управлением ОС Windows и Linux (вручную и в большом масштабе);
- получение и анализ обнаруженных уязвимостей от развернутых агентов (вручную и в большом масштабе);
- объединенный интерфейс для виртуальных машин Azure и компьютеров Azure Arc.

См. дополнительные сведения о [развертывании интегрированного сканера уязвимостей на гибридных компьютерах](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

См. дополнительные сведения о [серверах с поддержкой Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/).


### <a name="azure-firewall-recommendation-added-preview"></a>Добавлена рекомендация Брандмауэра Azure (предварительная версия)

Добавлена новая рекомендация, которая предлагает защитить виртуальные сети с помощью Брандмауэра Azure.

Эта рекомендация — **Virtual networks should be protected by Azure Firewall** (Виртуальные сети должны быть защищены с помощью Брандмауэра Azure) — предлагает ограничить доступ к виртуальным сетям, чтобы избежать потенциальных угроз с помощью Брандмауэра Azure.

Дополнительные сведения о [брандмауэре Azure](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Рекомендация "В службах Kubernetes нужно определить разрешенные диапазоны IP-адресов" обновлена с помощью быстрого исправления

Для рекомендации **В службах Kubernetes нужно определить разрешенные диапазоны IP-адресов** выпущено быстрое исправление.

Дополнительные сведения об этой и всех других рекомендациях Центра безопасности см. в статье [Рекомендации по безопасности — справочное руководство](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Рекомендация &quot;В службах Kubernetes нужно определить разрешенные диапазоны IP-адресов&quot; с быстрым исправлением":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>На панели мониторинга соответствия нормативным требованиям теперь есть параметр для удаления стандартов

Панель мониторинга соответствия нормативным требованиям в Центре безопасности предоставляет аналитические сведения о состоянии соответствия на основе того, как вы используете определенные средства и соблюдаете определенные требования.

Панель мониторинга включает набор нормативных стандартов по умолчанию. Если какой-либо из предоставляемых стандартов не имеет отношения к вашей организации, теперь достаточно просто удалить его из пользовательского интерфейса для подписки. Стандарты можно удалять только на уровне *подписки*. На уровне области группы управления такая возможность не поддерживается.

Дополнительные сведения см. в разделе [Удаление стандарта с панели мониторинга](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Таблица Microsoft.Security/securityStatuses удалена из Azure Resource Graph (ARG)

Azure Resource Graph — это служба в Azure, которая обеспечивает эффективную оценку ресурсов с возможностью выполнения запросов к заданному набору подписок в большом масштабе, чтобы вы могли эффективно управлять своей средой. 

При работе с Центром безопасности Azure вы можете применять ARG с [языком запросов Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/), чтобы получить расширенный набор данных о состоянии безопасности. Пример:

- Инвентаризация ресурсов использует ARG.
- Мы описали простой запрос ARG для [идентификации учетных записей, для которых не включена Многофакторная идентификация (MFA)](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled).

В ARG существуют таблицы данных, которые вы можете использовать в запросах.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Обозреватель Azure Resource Graph и доступные таблицы":::

> [!TIP]
> В статье [Таблица Azure Resource Graph и сведения о типах ресурсов](../governance/resource-graph/reference/supported-tables-resources.md) из документации по ARG представлен список всех доступных таблиц.

Начиная с этого обновления, из этого списка удалена таблица **Microsoft.Security/securityStatuses**. API securityStatuses остается доступным.

В качестве замены можно использовать таблицу Microsoft.Security/Assessments.

Основное различие между таблицами Microsoft.Security/securityStatuses и Microsoft.Security/Assessments заключается в том, что первая отображала оценки в агрегированном виде, а вторая содержит отдельную запись для каждой оценки.

Например, таблица Microsoft.Security/securityStatuses вернула бы результат с массивом из двух экземпляров policyAssessments:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
А таблица Microsoft.Security/Assessments будет содержать отдельную запись для каждой оценки политики:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Пример преобразования существующего запроса ARG с использованием таблицы securityStatuses для использования таблицы Assessments:**

Запрос к таблице securityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Новый запрос к таблице Assessments:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Дополнительные сведения см. по следующим ссылкам:
- [Как создавать запросы с помощью обозревателя Azure Resource Graph](../governance/resource-graph/first-query-portal.md)
- [Язык запросов Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>Сентябрь 2020 г.

В сентябре добавлены следующие обновления:
- [Новый внешний вид Центра безопасности](#security-center-gets-a-new-look)
- [Выпуск Azure Defender](#azure-defender-released)
- [Выпуск общедоступной версии Azure Defender для Key Vault](#azure-defender-for-key-vault-is-generally-available)
- [Выпуск общедоступной версии Azure Defender для защиты хранилища файлов и ADLS 2-го поколения](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Выпуск общедоступной версии средств инвентаризации ресурсов](#asset-inventory-tools-are-now-generally-available)
- [Отключение определенного результата поиска уязвимостей при проверках реестров контейнеров и виртуальных машин](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Исключение ресурса из рекомендации](#exempt-a-resource-from-a-recommendation)
- [Соединители AWS и GCP в Центре безопасности охватывают несколько облаков](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Пакет рекомендаций по защите рабочих нагрузок Kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Результаты оценки уязвимостей теперь доступны в непрерывном экспорте](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Защита от ошибок в конфигурации безопасности благодаря принудительному применению рекомендаций при создании новых ресурсов](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Улучшения рекомендаций по группе безопасности сети](#network-security-group-recommendations-improved)
- [Объявлена устаревшей рекомендация по AKS предварительной версии "В службах Kubernetes должны быть определены политики безопасности объекта pod"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Улучшения уведомлений по электронной почте от Центра безопасности Azure](#email-notifications-from-azure-security-center-improved)
- [Оценка безопасности не включает рекомендации предварительной версии](#secure-score-doesnt-include-preview-recommendations)
- [В рекомендациях добавлены индикатор серьезности и интервал актуальности](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Новый внешний вид Центра безопасности

Мы выпустили обновленный пользовательский интерфейс для страниц портала Центра безопасности. Добавлены новые страницы, в том числе с обзорными сведениями, а также панели мониторинга для оценки безопасности, инвентаризации активов и Azure Defender.

Обновленная страница обзорных сведений содержит плитку для доступа к панелям мониторинга для оценки безопасности, инвентаризации ресурсов и Azure Defender. Также на ней есть плитка, связанная с панелью мониторинга соответствия нормативным требованиям.

См. сведения о [странице обзора](overview-page.md).


### <a name="azure-defender-released"></a>Выпуск Azure Defender

**Azure Defender**  — это облачная платформа защиты рабочих нагрузок (CWPP), интегрированная в Центр безопасности для расширенной интеллектуальной защиты рабочих нагрузок Azure и гибридных рабочих нагрузок. Это решение заменяет Центр безопасности ценовой категории "Стандартный". 

При включении Azure Defender в области **Цены и параметры** в Центре безопасности Azure все следующие планы Defender активируются одновременно и обеспечивают комплексную защиту для уровней вычислений, данных и служб в вашей среде:

- [Azure Defender для серверов](defender-for-servers-introduction.md)
- [Azure Defender для Службы приложений](defender-for-app-service-introduction.md)
- [Azure Defender для службы хранилища](defender-for-storage-introduction.md)
- [Azure Defender для SQL](defender-for-sql-introduction.md)
- [Azure Defender для Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender для Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender для реестров контейнеров](defender-for-container-registries-introduction.md)

Каждый из этих планов отдельно описан в документации по Центру безопасности.

Azure Defender предоставляет на специальной панели мониторинга оповещения системы безопасности и возможности Расширенной защиты от угроз для виртуальных машин, баз данных SQL, контейнеров, веб-приложений, сети и т. д.

См. сведения об [Azure Defender](azure-defender.md).

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Выпуск общедоступной версии Azure Defender для Key Vault

Azure Key Vault — это облачная служба, которая обеспечивает защиту ключей шифрования и секретов (например, сертификатов, строк подключения и паролей). 

**Azure Defender для Key Vault** предоставляет реализованные в Azure возможности Расширенной защиты от угроз для Azure Key Vault с дополнительным уровнем аналитики безопасности. Azure Defender для Key Vault также защищает многие другие ресурсы, которые зависят от учетных записей Key Vault.

Выпущена общедоступная версия дополнительного плана. Ранее эта возможность предоставлялась как предварительная версия Расширенной защиты от угроз для Azure Key Vault.

Кроме того, страницы Key Vault на портале Azure теперь содержат специальную страницу **Безопасность** для рекомендаций и оповещений **Центра безопасности**.

См. сведения об [Azure Defender для Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Выпуск общедоступной версии Azure Defender для защиты хранилища файлов и ADLS 2-го поколения 

**Azure Defender для службы хранилища** обнаруживает потенциально опасные действия в учетных записях хранения Azure. Данные можно защитить независимо от того, хранятся ли они в виде контейнеров больших двоичных объектов, общих папок или озер данных.

Поддержка платформ [Файлы Azure](../storage/files/storage-files-introduction.md) и [Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-introduction.md) стала общедоступной.

С 1 октября 2020 г. мы будем взимать плату за защиту ресурсов в этих службах.

См. сведения об [Azure Defender для службы хранилища](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Выпуск общедоступной версии средств инвентаризации ресурсов

Страница инвентаризации ресурсов в Центре безопасности Azure позволяет просматривать состояние безопасности всех ресурсов, подключенных к Центру безопасности.

Центр безопасности периодически анализирует состояние безопасности ресурсов Azure, чтобы выявить потенциальные уязвимости. Затем он предоставляет рекомендации по устранению этих уязвимостей.

Если для каких-то ресурсов есть необработанные рекомендации, они будут отображаться в данных инвентаризации.

См. сведения об [изучении ресурсов и управлении ими с помощью инвентаризации ресурсов и средств управления](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Отключение определенного результата поиска уязвимостей при проверках реестров контейнеров и виртуальных машин

Azure Defender включает средства сканирования для проверки образов в Реестре контейнеров Azure и на виртуальных машинах.

Если правила вашей организации требуют игнорировать обнаруженную проблему, а не исправлять ее, вы можете исключить ее из результатов поиска. Отключенные результаты не учитываются в оценке безопасности и не создают нежелательный шум.

Если результат поиска соответствует критерию, заданному в правилах отключения, он не будет отображаться в списке результатов.

Этот параметр доступен на страницах с подробными сведениями для следующих рекомендаций:

- **Должны быть устранены уязвимости в образах Реестра контейнеров Azure**
- **Должны быть устранены уязвимости на ваших виртуальных машинах**.

См. сведения об отключении определенных результатов поиска [для образов контейнеров](defender-for-container-registries-usage.md#disable-specific-findings-preview) и [виртуальных машин](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Исключение ресурса из рекомендации

Иногда случается так, что согласно определенной рекомендации ресурс получает статус неработоспособного (что снижает оценку безопасности), но вы считаете это неправильным. Например, ресурс мог был исправлен процессом, который не отслеживается Центром безопасности. Или ваша организация решила принять этот риск для определенного ресурса. 

В таких случаях вы можете создать правило исключения, чтобы в будущем этот ресурс не попадал в список неработоспособных ресурсов. Такие правила могут содержать документированные обоснования, как описано ниже.

См. сведения об [исключении ресурса из рекомендаций и оценки безопасности](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Соединители AWS и GCP в Центре безопасности охватывают несколько облаков

Так как облачные рабочие нагрузки часто распределяются между несколькими облачными платформами, облачные службы безопасности должны охватывать их все.

Центр безопасности Azure теперь защищает рабочие нагрузки, развернутые в Azure, Amazon Web Services (AWS) и Google Cloud Platform (GCP).

При подключении учетных записей AWS и GCP к Центру безопасности происходит интеграция AWS Security Hub, GCP Security Command Center и Центра безопасности Azure. 

См. сведения о подключении к Центру безопасности Azure [учетных записей AWS](quickstart-onboard-aws.md) и [учетных записей GCP](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Пакет рекомендаций по защите рабочих нагрузок Kubernetes

Чтобы по умолчанию поддерживать безопасность рабочих нагрузок Kubernetes, Центр безопасности предоставляет рекомендации по усилению защиты на уровне Kubernetes, включая возможности усиления безопасности с помощью средств управления допуском Kubernetes.

Когда вы установите надстройку Политики Azure для Kubernetes в кластере AKS, каждый запрос к серверу API Kubernetes будет отслеживаться и сопоставляться с заранее установленным набором рекомендаций, прежде чем он достигнет кластера. Вы можете настроить принудительное применение таких рекомендаций для всех будущих рабочих нагрузок.

Например, можно запретить создание привилегированных контейнеров, и тогда любой запрос такого типа будет блокироваться.

См. сведения о [рекомендациях по защите рабочих нагрузок с использованием средств управления доступом в Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Результаты оценки уязвимостей теперь доступны в непрерывном экспорте

Используйте непрерывный экспорт для потоковой передачи оповещений и рекомендации в реальном времени в Центры событий Azure, рабочие области Log Analytics и Azure Monitor. Отсюда эти данные можно интегрировать с системами SIEM (например, Azure Sentinel, Power BI, Azure Data Explorer и т. д.).

Встроенные средства оценки уязвимостей в Центре безопасности возвращают результаты поиска, связанные с ресурсами, в виде дочерних рекомендаций в родительской рекомендации, предлагая, например, исправить уязвимости на виртуальных машинах. 

Результаты проверки безопасности теперь можно экспортировать через механизм непрерывного экспорта, выбрав нужные рекомендации и включив параметр **Включить результаты проверки безопасности**.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Параметр &quot;Включить результаты проверки безопасности&quot; в конфигурации непрерывного экспорта" :::

Связанные страницы:

- [Интегрированное решение оценки уязвимостей виртуальных машин в Центре безопасности](deploy-vulnerability-assessment-vm.md)
- [Интегрированное решение оценки уязвимостей образов в Реестре контейнеров Azure в Центре безопасности](defender-for-container-registries-usage.md)
- [непрерывный экспорт.](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Защита от ошибок в конфигурации безопасности благодаря принудительному применению рекомендаций при создании новых ресурсов

Ошибки в конфигурации безопасности являются частой причиной инцидентов безопасности. Центр безопасности теперь помогает *предотвращать* такие ошибки для новых ресурсов в соответствии с определенными рекомендациями. 

Эта возможность помогает поддерживать безопасность рабочих нагрузок и стабилизировать оценку безопасности.

Применять конфигурацию безопасности в соответствии с определенной рекомендацией можно в двух режимах:

- параметр **Отклонить** Политики Azure предотвращает создание неработоспособных ресурсов;

- параметр **Применить** использует действие **DeployIfNotExist** Политики Azure для автоматического исправления несоответствующих ресурсов после создания.
 
Эта возможность доступна в верхней части страницы сведений о ресурсах, но не для всех рекомендаций системы безопасности.

См. сведения о [предотвращении ошибок конфигурации с помощью рекомендаций о применении и отклонении](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Улучшения рекомендаций по группе безопасности сети

Были улучшены следующие рекомендации по безопасности, связанные с группами безопасности сети, чтобы сократить количество некоторых ложноположительных результатов.

- Требуется ограничить доступ ко всем сетевым портам в связанной с виртуальной машиной группе безопасности сети
- Необходимо закрыть порты управления на виртуальных машинах.
- Виртуальные машины с выходом в Интернет должны быть защищены с помощью групп безопасности сети.
- Подсети должны быть связаны с группой безопасности сети.


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Объявлена устаревшей рекомендация по AKS предварительной версии "В службах Kubernetes должны быть определены политики безопасности объекта pod"

Рекомендация предварительной версии "В службах Kubernetes должны быть определены политики безопасности группы pod" теперь является устаревшей, как описано в документации по [Службе Azure Kubernetes](../aks/use-pod-security-policies.md).

Функция политики безопасности объекта pod (предварительная версия) объявлена устаревшей и станет недоступной после 15 октября 2020 г. Вместо нее будет использоваться Политика Azure для AKS.

После этой даты политику безопасности pod (предварительная версия) нужно будет отключить на всех затронутых существующих кластерах, чтобы сохранить возможность обновления кластеров в будущем и обеспечить поддержку Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Улучшения уведомлений по электронной почте от Центра безопасности Azure

Улучшены следующие аспекты отправки оповещений системы безопасности по электронной почте: 

- включена возможность отправлять по электронной почте уведомления о предупреждениях для всех уровней серьезности;
- включена возможность уведомлять пользователей с разными ролями RBAC в подписке;
- мы по умолчанию упреждающе уведомляем владельцев подписок об оповещениях высокого уровня серьезности (связанных с высокой вероятностью обнаружения брешей);
- мы удалили поле для номера телефона на странице настройки уведомлений по электронной почте

См. сведения о [настройке отправки по электронной почте уведомлений об оповещениях системы безопасности](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Оценка безопасности не включает рекомендации предварительной версии 

Центр безопасности постоянно оценивает ресурсы, подписки и организацию на предмет проблем безопасности. Затем он объединяет все результаты в одну оценку, чтобы вы могли понять текущее состояние системы безопасности: чем выше оценка, тем ниже выявленный уровень риска.

По мере обнаружения новых угроз в Центре безопасности становятся доступными новые рекомендации с советами по обеспечению безопасности. Чтобы вы могли предотвратить неожиданное изменение оценки безопасности и настроить период отсрочки для изучения рекомендаций, прежде чем они начнут влиять на оценки, рекомендации с отметкой **Предварительная версия** теперь не учитываются в оценке безопасности. При этом их по возможности все равно нужно разрешить, так как по окончанию периода предварительной версии они повлияют на оценку.

Кроме того, рекомендации **предварительной версии** не присваивают ресурсу состояние неработоспособного.

Пример рекомендации предварительной версии:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Рекомендация с отметкой предварительной версии":::

См. сведения об [оценке безопасности](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>В рекомендациях добавлены индикатор серьезности и интервал актуальности

На странице сведений для рекомендаций теперь отображаются индикатор актуальности рекомендации (где он применим) и уровень ее серьезности.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Страница рекомендаций, где отображаются актуальность и серьезность":::



## <a name="august-2020"></a>Август 2020 г.

В августе добавлены следующие обновления:

- [Инвентаризация ресурсов — новое мощное представление состояния безопасности для ресурсов](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Включена поддержка параметров безопасности Azure Active Directory по умолчанию (для многофакторной проверки подлинности)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Добавлены рекомендации для субъектов-служб](#service-principals-recommendation-added)
- [Оценка уязвимостей на виртуальных машинах — рекомендации и политики объединены](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Новые политики безопасности AKS добавлены в инициативу ASC_default — только для пользователей закрытой предварительной версии](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Инвентаризация ресурсов — новое мощное представление состояния безопасности для ресурсов

Инвентаризация ресурсов Центра безопасности (сейчас доступна в предварительной версии) позволяет просматривать уровень безопасности ресурсов, подключенных к Центру безопасности.

Центр безопасности периодически анализирует состояние безопасности ресурсов Azure, чтобы выявить потенциальные уязвимости. Затем он предоставляет рекомендации по устранению этих уязвимостей. Если для каких-то ресурсов есть необработанные рекомендации, они будут отображаться в данных инвентаризации.

Вы можете использовать это представление и фильтры в нем для изучения данных о состоянии безопасности и выполнения дополнительных действий с учетом полученных результатов.

См. сведения об [инвентаризации ресурсов](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Включена поддержка параметров безопасности Azure Active Directory по умолчанию (для многофакторной проверки подлинности)

В Центре безопасности включена полная поддержка [параметров безопасности по умолчанию](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) (предоставляется корпорацией Майкрософт бесплатно для защиты удостоверений).

Параметры безопасности по умолчанию — это предварительно настроенная конфигурация безопасности для защиты удостоверений организации от распространенных атак, связанных с удостоверениями. Параметры безопасности по умолчанию уже помогают защитить более 5 000 000 арендаторов во всем мире, а Центр безопасности — 50 000 арендаторов.

Центр безопасности теперь предоставляет рекомендации по безопасности, если он определяет подписку Azure без включенных параметров безопасности по умолчанию. Ранее Центр безопасности рекомендовал включать многофакторную проверку подлинности с использованием условного доступа в рамках лицензии Azure Active Directory (AD) уровня "Премиум". Теперь для клиентов, которые используют бесплатную версию Azure AD, мы рекомендуем включать параметры безопасности по умолчанию. 

Мы хотим, чтобы как можно больше клиентов настроили MFA для защиты облачных сред, снизив один из самых высоких рисков, которые влияют на [оценку безопасности](secure-score-security-controls.md).

См. сведения о [параметрах безопасности по умолчанию](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Добавлены рекомендации для субъектов-служб

Добавлена новая рекомендация, которая предлагает клиентам Центра безопасности, использующим сертификаты управления для управления подписками, переключиться на субъекты-службы.

Эта рекомендация ( **Для защиты подписок вместо сертификатов управления следует использовать субъекты-службы** ) информирует о том, что использование субъектов-служб или Azure Resource Manager позволяет более безопасно управлять подписками. 

См. сведения об [использовании объектов субъектов-служб и приложений в Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Оценка уязвимостей на виртуальных машинах — рекомендации и политики объединены

Центр безопасности проверяет, используют ли виртуальные машины решение для оценки уязвимостей. Если решение для оценки уязвимостей не найдено, Центр безопасности рекомендует упростить развертывание.

При обнаружении уязвимостей Центр безопасности предоставляет рекомендацию с обобщенными результатами поиска, чтобы вы могли при необходимости исследовать и устранить проблемы.

Чтобы обеспечить согласованный интерфейс для всех пользователей независимо от используемого типа средства проверки, мы объединили четыре рекомендации в следующие две:

|Единая рекомендация|Описание изменений|
|----|:----|
|**Необходимо включить решение для оценки уязвимостей на виртуальных машинах**|Заменяет следующие две рекомендации:<br> **•** "Активируйте встроенное решение оценки уязвимостей на виртуальных машинах (на платформе Qualys)" (устаревшая; отображается для уровня "Стандартный");<br> **•** "Необходимо установить решение "Оценка уязвимостей" на виртуальных машинах" (устаревшая; отображается для уровней "Стандартный" и "Бесплатный").|
|**Должны быть устранены уязвимости на ваших виртуальных машинах**.|Заменяет следующие две рекомендации:<br>**•** "Устраните уязвимости, обнаруженные на виртуальных машинах (на платформе Qualys)" (устаревшая);<br>**•** "Уязвимости должны быть устранены с помощью решения для оценки уязвимостей" (устаревшая).|
|||

Теперь вы будете использовать одну рекомендацию для развертывания расширения Центра безопасности для оценки уязвимостей или решения с использованием собственной лицензии (BYOL), предоставленного такими партнерами, как Qualys или Rapid7.

Кроме того, если Центр безопасности получает сведения об обнаружении уязвимостей, одна рекомендация проинформирует вас о результатах поиска независимо от используемого решения для оценки уязвимостей.

#### <a name="updating-dependencies"></a>Обновление зависимостей

Если у вас есть скрипты, запросы или автоматизации, которые ссылаются на упомянутые рекомендации, ключи или имена политик, обновите эти ссылки в соответствии со следующими таблицами.

##### <a name="before-august-2020"></a>До августа 2020 года

|Рекомендация|Область|
|----|:----|
|**Активируйте встроенное решение оценки уязвимостей на виртуальных машинах (на платформе Qualys)**<br>Раздел: 550e890b-e652-4d22-8274-60b3bdb24c63|Встроено|
|**Устранение уязвимостей, обнаруженных на виртуальных машинах (на платформе Qualys)**<br>Раздел: 1195afff-c881-495e-9bc5-1486211ae03f|Встроено|
|**Необходимо установить решение "Оценка уязвимостей" на виртуальных машинах**<br>Раздел: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Уязвимости должны быть устранены с помощью решения для оценки уязвимостей**<br>Раздел: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Политика|Область|
|----|:----|
|**На виртуальных машинах должна быть включена оценка уязвимостей**<br>Идентификатор политики: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Встроено|
|**Уязвимости должны быть устранены с помощью решения для оценки уязвимостей**<br>Идентификатор политики: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>После августа 2020 года

|Рекомендация|Область|
|----|:----|
|**Необходимо включить решение для оценки уязвимостей на виртуальных машинах**<br>Ключ: ffff0522-1e88-47fc-8382-2a80ba848f5d|Встроено, с использованием собственной лицензии|
|**Должны быть устранены уязвимости на ваших виртуальных машинах**.<br>Раздел: 1195afff-c881-495e-9bc5-1486211ae03f|Встроено, с использованием собственной лицензии|
||||

|Политика|Область|
|----|:----|
|[**На виртуальных машинах должна быть включена оценка уязвимостей**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Идентификатор политики: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Встроено, с использованием собственной лицензии|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Новые политики безопасности AKS добавлены в инициативу ASC_default — только для пользователей закрытой предварительной версии

Чтобы по умолчанию поддерживать безопасность рабочих нагрузок Kubernetes, Центр безопасности предоставляет политики и рекомендации по усилению защиты на уровне Kubernetes, включая возможности усиления безопасности с помощью средств управления допуском Kubernetes.

Ранняя стадия этого проекта предусматривает режим закрытой предварительной версии и добавление новых (по умолчанию отключенных) политик в инициативу ASC_default.

Вы можете спокойно игнорировать эти политики, так как они не влияют на вашу среду. Если вы захотите включить их, зарегистрируйтесь для использования предварительной версии на странице https://aka.ms/SecurityPrP и выберите один из следующих вариантов:

1. **Single Preview** (Одна предварительная версия) — использование только этой закрытой предварительной версии. Явным образом укажите ASC Continuous Scan (Непрерывная проверка ASC) в качестве нужной предварительной версии.
1. **Ongoing Program** (Текущая программа) — использование не только этой, но и всех последующих закрытых предварительных версий. Вам будет предложено заполнить профиль и подтвердить соглашение о конфиденциальности.


## <a name="july-2020"></a>Июль 2020 г.

В июле добавлены следующие обновления:
- [Оценка уязвимости виртуальных машин стала доступной для образов, не предназначенных для Marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Защита от угроз для службы хранилища Azure предоставляется для Файлов Azure и Azure Data Lake Storage 2-го поколения (предварительная версия)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Восемь новых рекомендаций для включения возможностей защиты от угроз](#eight-new-recommendations-to-enable-threat-protection-features)
- [Улучшения защиты контейнеров — ускоренная проверка и обновленная документация](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Адаптивные элементы управления приложениями включают новые рекомендации и позволяют использовать подстановочные знаки в правилах путей](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Шесть политик для расширенной безопасности данных SQL объявлены устаревшими](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Оценка уязвимости виртуальных машин стала доступной для образов, не предназначенных для Marketplace

Перед развертыванием решения для оценки уязвимостей Центр безопасности ранее выполнял проверку допустимости. Эта проверка подтверждала наличие SKU для Marketplace на целевой виртуальной машине. 

Начиная с этого обновления, такая проверка не выполняется, что позволяет теперь развертывать средства для оценки уязвимостей на пользовательских компьютерах Windows и Linux. Пользовательскими считаются те образы, на которых что-то изменено по сравнению с конфигурацией по умолчанию из Marketplace.

Но независимо от возможности развертывать интегрированное расширение для оценки уязвимостей (на платформе Qualys) на большом числе новых компьютеров, поддержка предоставляется только для тех ОС, которые перечислены в разделе [Развертывание интегрированного средства проверки уязвимостей на виртуальных машинах](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

См. сведения об [интегрированном средстве проверки уязвимостей для виртуальных машин (требуется наличие Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Сведения об использовании собственного решения для оценки уязвимостей (например, от Qualys или Rapid7) с собственной лицензией см. в статье [Интегрированное решение для оценки уязвимостей Azure Defender для виртуальных машин Azure](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Защита от угроз для службы хранилища Azure предоставляется для Файлов Azure и Azure Data Lake Storage 2-го поколения (предварительная версия)

Защита от угроз для службы хранилища обнаруживает потенциально опасные действия в учетных записях хранения Azure. Центр безопасности отображает оповещения об обнаруженных попытках получения доступа или эксплойтов в учетных записях хранения. 

Данные можно защитить независимо от того, хранятся ли они в виде контейнеров больших двоичных объектов, общих папок или озер данных.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Восемь новых рекомендаций для включения возможностей защиты от угроз

Добавлены восемь новых рекомендаций, которые позволяют легко включить возможности Центра безопасности Azure для защиты от уязвимостей в ресурсах следующих типов: виртуальные машины, планы службы приложений, серверы Базы данных SQL Azure, серверы SQL на отдельных компьютерах, учетные записи хранения в службе хранилища Azure, кластеры Службы Azure Kubernetes, реестры Реестра контейнеров Azure и хранилища Azure Key Vault.

Вот эти новые рекомендации:

- **На серверах Базы данных SQL Azure должна быть включена Расширенная защита данных**
- **Для серверов SQL на компьютерах должна быть включена Расширенная защита данных**
- **Для планов Службы приложений Azure должна быть включена Расширенная защита от угроз**
- **Для реестров в службе "Реестр контейнеров Azure" должна быть включена Расширенная защита от угроз**
- **Для хранилищ в службе Azure Key Vault должна быть включена Расширенная защита от угроз**
- **Для кластеров в Службе Azure Kubernetes должна быть включена Расширенная защита от угроз**
- **Для учетных записей службы хранилища Azure должна быть включена Расширенная защита от угроз**
- **На виртуальных машинах должна быть включена Расширенная защита от угроз**

Все эти новые рекомендации относятся к элементу управления безопасностью **Enable Azure Defender** (Включить Azure Defender).

Каждая из них содержит функцию быстрого исправления. 

> [!IMPORTANT]
> Применение любой из этих рекомендаций предусматривает плату за защиту соответствующих ресурсов. Плата будет начислена немедленно, если у вас есть необходимые ресурсы в текущей подписке. Если вы добавите их позже, плата будет начисляться с момента добавления.
> 
> Например, если в текущей подписке нет кластеров Azure Kubernetes, при включении защиты от угроз плата не будет взиматься. Если позже вы добавите кластер в эту подписку, к нему будет автоматически применена как защита, за что будет начислена плата.

См. сведения о каждой из этих [рекомендаций по обеспечению безопасности](recommendations-reference.md).

Дополнительные сведения см. в статье [Защита от угроз с помощью Центра безопасности Azure](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Улучшения защиты контейнеров — ускоренная проверка и обновленная документация

Мы рады сообщить о непрерывных улучшениях в области защиты контейнеров, в частности о заметном повышении производительности динамических проверок Центра безопасности, выполняемых для образов контейнеров, хранящихся в Реестре контейнеров Azure. Теперь проверки выполняются примерно за две минуты. Но в некоторых случаях может потребоваться до 15 минут.

Для повышения четкости и качества рекомендаций о возможностях Центра безопасности Azure в отношении защиты контейнеров мы также обновили страницы документации по обеспечению безопасности контейнеров. 

Сведения о возможностях Центра безопасности для защиты контейнеров см. в следующих статьях:

- [Защита контейнеров в Центре безопасности](container-security.md)
- [Интеграция с Реестром контейнеров Azure](defender-for-container-registries-introduction.md)
- [Интеграция со Службой Azure Kubernetes](defender-for-kubernetes-introduction.md)
- [Проверка реестров и защита узлов Docker](container-security.md)
- [Оповещения системы безопасности от функций защиты от угроз для кластеров службы Kubernetes Azure](alerts-reference.md#alerts-akscluster)
- [Оповещения системы безопасности от функций защиты от угроз для узлов службы Kubernetes Azure](alerts-reference.md#alerts-containerhost)
- [Рекомендации по безопасности для контейнеров](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Адаптивные элементы управления приложениями включают новые рекомендации и позволяют использовать подстановочные знаки в правилах путей

Адаптивные элементы управления приложениями были существенно обновлены:

* Новая рекомендация определяет потенциально допустимое поведение, которое ранее было запрещено. Новая рекомендация ( **Требуется обновление правил списка разрешений в политике адаптивных элементов управления приложениями** ) предлагает добавить к существующей политике новые правила, чтобы сократить число ложноположительных результатов в оповещениях о нарушении работы адаптивных элементов управления приложениями.

* Правила путей теперь позволяют использовать подстановочные знаки. Начиная с этого обновления, вы можете настраивать правила разрешенных путей с использованием подстановочных знаков. Поддерживаются два сценария работы:

    * Использование подстановочного знака в конце пути, чтобы разрешить все исполняемые файлы в этой папке и всех вложенных папках.

    * Использование подстановочного знака в середине пути, чтобы разрешить исполняемый файл с известным именем в папках с разными именами (например, в сценариях с личными папками пользователей с известным исполняемым файлом, автоматически создаваемыми именами папок и т. д.).


[Дополнительные сведения об адаптивных элементах управления приложениями](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Шесть политик для расширенной безопасности данных SQL объявлены устаревшими

Объявлены устаревшими следующие шесть политик, связанных с расширенной безопасностью данных для компьютеров SQL:

- Для параметра "Типы Расширенной защиты от угроз" в параметрах Расширенной защиты данных Управляемого экземпляра SQL необходимо установить значение "Все".
- Для параметра "Типы Расширенной защиты от угроз" в параметрах Расширенной защиты данных SQL Server необходимо установить значение "Все".
- Дополнительные параметры безопасности данных для управляемого экземпляра SQL должны содержать адрес электронной почты для получения оповещений системы безопасности
- Дополнительные параметры безопасности данных для сервера SQL должны содержать адрес электронной почты для получения оповещений системы безопасности
- Уведомления по электронной почте для администраторов и владельцев подписок следует включить в параметрах расширенной защиты данных для управляемого экземпляра SQL.
- На серверах SQL в параметрах расширенной защиты данных должны быть включены уведомления, отправляемые по электронной почте администраторам и владельцам подписок.

См. сведения о [встроенных политиках](./policy-reference.md).





## <a name="june-2020"></a>Июнь 2020 г.

В июне добавлены следующие обновления:
- [API оценки безопасности (предварительная версия)](#secure-score-api-preview)
- [Расширенная защита данных для компьютеров SQL (в Azure, других облаках и локальной среде) (предварительная версия)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Две новые рекомендации по развертыванию агента Log Analytics на компьютерах Azure Arc (предварительная версия)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Новые политики для создания непрерывного экспорта и конфигураций автоматизации рабочих процессов в большом масштабе](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Новая рекомендация по использованию групп безопасности сети для защиты виртуальных машин, не подключенных к Интернету](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Новые политики для включения защиты от угроз и расширенной защиты данных](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>API оценки безопасности (предварительная версия)

Теперь вы можете получить доступ к оценке через [API оценки безопасности](/rest/api/securitycenter/securescores/) (предварительная версия). Методы этого API позволяют гибко выполнять запросы к данным и создавать собственные механизмы создания отчетов об оценках безопасности за разные периоды. Например, API **оценки безопасности** позволяет получить оценку для конкретной подписки. Кроме того, через API **элементов управления оценкой безопасности** позволяет перечислить элементы управления безопасностью и текущие оценки для всех подписок.

Примеры внешних средств, которые можно реализовать с помощью API оценки безопасности, см. в [разделе оценок безопасности в сообществе GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

См. сведения об [оценке безопасности и элементах управления безопасностью в Центре безопасности Azure](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Расширенная защита данных для компьютеров SQL (в Azure, других облаках и локальной среде) (предварительная версия)

Расширенная защита данных в Центре безопасности Azure для компьютеров SQL теперь защищает экземпляры SQL Server, размещенные в Azure, других облачных средах и даже на локальных компьютерах. Это позволяет обеспечить в гибридной среде такую же защиту экземпляров SQL Server, как и в Azure.

Расширенная защита данных предоставляет оценку уязвимостей и расширенную защиту от угроз для компьютеров SQL в любом расположении.

Настройка включает два шага:

1. развертывание агента Log Analytics на компьютере с SQL Server для подключения к учетной записи Azure;

1. включение дополнительного пакета на странице цен и параметров Центра безопасности.

См. сведения о [расширенной защите данных для компьютеров SQL](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Две новые рекомендации по развертыванию агента Log Analytics на компьютерах Azure Arc (предварительная версия)

Добавлены две новые рекомендации, которые помогают развернуть [агент Log Analytics](../azure-monitor/platform/log-analytics-agent.md) на компьютерах Azure Arc и обеспечить их защиту с помощью Центра безопасности Azure:

- **На виртуальных машинах Windows с Azure Arc должен быть установлен агент Log Analytics (предварительная версия)** ;
- **На виртуальных машинах Linux с Azure Arc должен быть установлен агент Log Analytics (предварительная версия)** .

Эти новые рекомендации будут отображаться в тех же четырех элементах управления безопасностью, что и существующая связанная рекомендация ( **Необходимо установить агент мониторинга на ваших компьютерах** ): исправление конфигураций системы безопасности, применение адаптивного элемента управления приложениями, применение системных обновлений и включение защиты конечных точек.

Также в эти рекомендации включена возможность быстрого исправления, позволяющая ускорить процесс развертывания. 

Сведения об этих двух новых рекомендациях см. в таблице с [рекомендациями по вычислительной среде и приложениям](recommendations-reference.md#recs-computeapp).

Сведения о том, как Центр безопасности Azure использует агент, см. в разделе [Что такое агент Log Analytics?](faq-data-collection-agents.md#what-is-the-log-analytics-agent)

См. сведения о [расширениях для компьютеров Azure Arc](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Новые политики для создания непрерывного экспорта и конфигураций автоматизации рабочих процессов в большом масштабе

Автоматизация корпоративных процессов мониторинга и реагирования на инциденты может значительно ускорить процессы изучения и устранения инцидентов безопасности.

Чтобы развернуть конфигурации автоматизации в масштабе всей организации, примените следующие встроенные политики Azure DeployIfdNotExist для создания и настройки процедур [непрерывного экспорта](continuous-export.md) и [автоматизации рабочих процессов](workflow-automation.md).

Эти политики можно найти в Политике Azure:


|Цель  |Политика  |Идентификатор политики  |
|---------|---------|---------|
|Непрерывный экспорт в концентратор событий|[Развертывание экспортированных данных в концентраторе событий для оповещений и рекомендаций Центра безопасности Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Непрерывный экспорт в рабочую область Log Analytics|[Развертывание экспортированных данных в рабочей области Log Analytics для оповещений и рекомендаций Центра безопасности Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Автоматизация рабочих процессов для оповещений системы безопасности|[Развертывание средств автоматизации рабочих процессов для оповещений Центра безопасности Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Автоматизация рабочих процессов для рекомендаций системы безопасности|[Развертывание средств автоматизации рабочих процессов для рекомендаций Центра безопасности Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Начало работы с [шаблонами автоматизации рабочих процессов](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Дополнительные сведения об использовании этих двух политик экспорта см. в разделах [Настройка автоматизации рабочих процессов в большом масштабе с помощью представленных политик](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) и [Настройка непрерывного экспорта](continuous-export.md#set-up-a-continuous-export).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Новая рекомендация по использованию групп безопасности сети для защиты виртуальных машин, не подключенных к Интернету

Элемент управления безопасностью "Внедрение лучших методик обеспечения безопасности" теперь включает следующие новые рекомендации:

- **Виртуальные машины без выхода в Интернет должны быть защищены с помощью групп безопасности сети**

Существующая рекомендация ( **Виртуальные машины с выходом в Интернет должны быть защищены с помощью групп безопасности сети** ) ранее не учитывала разницу между виртуальными машинами, подключенными и не подключенными к Интернету. В обоих случаях создавалась рекомендация с высоким уровнем серьезности, если виртуальная машина не была назначена группе безопасности сети. Новая рекомендация исключает компьютеры, не подключенные к Интернету, чтобы сократить число ложноположительных результатов и избежать появления ненужных оповещений с высоким уровнем серьезности.

См. сведения в таблице с [рекомендациями по использованию сети](recommendations-reference.md#recs-network).




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Новые политики для включения защиты от угроз и расширенной защиты данных

Перечисленные ниже новые политики добавлены в инициативу ASC_default, чтобы включить защиту от угроз или расширенную защиту данных для соответствующих типов ресурсов.

Эти политики можно найти в Политике Azure:


| Политика                                                                                                                                                                                                                                                                | Идентификатор политики                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [На серверах Базы данных SQL Azure должна быть включена Расширенная защита данных](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Для серверов SQL на компьютерах должна быть включена Расширенная защита данных](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Для учетных записей службы хранилища Azure должна быть включена Расширенная защита от угроз](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Для хранилищ в службе Azure Key Vault должна быть включена Расширенная защита от угроз](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Для планов Службы приложений Azure должна быть включена Расширенная защита от угроз](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Для реестров в службе "Реестр контейнеров Azure" должна быть включена Расширенная защита от угроз](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Для кластеров в Службе Azure Kubernetes должна быть включена Расширенная защита от угроз](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [На виртуальных машинах должна быть включена Расширенная защита от угроз](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Дополнительные сведения см. в статье [Защита от угроз с помощью Центра безопасности Azure](azure-defender.md).