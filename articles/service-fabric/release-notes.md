---
title: Выпуски Azure Service Fabric
description: Выпуск заметок для сервиса Azure Fabric. Включает информацию о последних функциях и улучшениях в Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 3e0f6c78b6e5dd066cbfbac6805bb3c42068e66a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729594"
---
# <a name="service-fabric-releases"></a>Сервис Ткань релизы

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Проблемы Руководство</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Вопрос отслеживания</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">параметры</a> 
| поддержки<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">Поддерживаемые версии</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">образцы кода</a>

В этой статье содержится более подробная информация о последних версиях и обновлениях в расписании выполнения Service Fabric и SDK.

## <a name="whats-new-in-service-fabric"></a>Что нового в сервисной ткани

### <a name="service-fabric-71"></a>СервисНая ткань 7.1
В связи с нынешним кризисом COVID-19, и с учетом проблем, с которыми сталкиваются наши клиенты, мы делаем 7.1 доступным, но не будем автоматически модернизировать кластеры, установленные для автоматического обновления. Мы приостанавливаем автоматическое обновление до дальнейшего уведомления, чтобы гарантировать, что клиенты могут применять обновления, когда они наиболее подходящие для них, чтобы избежать непредвиденных сбоев.

Обновление до 7.1 можно будет через [портал Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) или через [развертывание диспетчера ресурсов Azure.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#set-the-upgrade-mode-using-a-resource-manager-template)

Кластеры Service Fabric с включенным автоматическим обновлением начнут автоматически получать обновление 7.1 после возобновления стандартной процедуры развертывания. Мы предоставим еще одно объявление до начала стандартного развертывания на [сайте сообщества Service Fabric Tech.](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)
Мы также опубликовали обновления до конца даты поддержки для основных релизов, начиная с 6,5 до 7,1 [здесь](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-versions). 

## <a name="what-is-new-in-service-fabric-71"></a>Что такое новый в обслуживании Ткань 7.1?
Мы рады объявить о следующем выпуске Service Fabric. Этот релиз загружается с ключевыми функциями и улучшениями. Ниже выделены некоторые ключевые особенности:
## <a name="key-announcements"></a>Основные объявления
- **Общая доступность** [ **управляемых идентификаторов сервисной ткани для приложений Service Fabric**](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)
- [**Поддержка Ubuntu 1804**](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster)
 - [**Предварительный просмотр: Поддержка дисков Эфемеральной ОС VMSS:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-azure-deployment-preparation#use-ephemeral-os-disks-for-virtual-machine-scale-sets)эфемерные диски ОС создаются на локальной виртуальной машине и не сохраняются в удаленном хранилище Azure. Они рекомендуются для всех типов узлов Service Fabric (первичных и вторичных), потому что по сравнению с традиционными стойкими дисками ОС, эфемерные диски ОС:
      -  Снижение задержки чтения/записи на дискЕ ОС
      -  Включить более быстрые операции управления сбросить/переобразить узла
      -  Снижение общих затрат (диски бесплатны и не несут дополнительных затрат на хранение)
- Поддержка объявления [**сертификатов конечной точки службы приложений Service Fabric по данному общему имени.**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
- [**Поддержка зондов здоровья для контейнеризированных услуг**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage): Поддержка механизма зонда Liveness для контейнерных приложений. Liveness Probe поможет объявить о живости контейнерного приложения, и когда они не отвечают своевременно, это приведет к перезагрузке. 
- [**Поддержка пакетов Кодификазаторов**](https://docs.microsoft.com/azure/service-fabric/initializer-codepackages) для [контейнеров](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) и [исполняемых](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) приложений для гостей. Это позволяет выполнять пакеты кода (например, контейнеры) в указанном порядке для выполнения инициализации пакета услуг.
- **FabricObserver и ClusterObserver** — это приложения без состояния, которые захватывают телеметрию Service Fabric, связанные с различными аспектами кластера SF. Оба этих приложения готовы к развертыванию в производственных кластерах Windows для захвата богатой телеметрии с реализованной поддержкой ApplicationInsights, EventSource и LogAnalytics.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)- работает на всех узлах, генерирует события работоспособности, испускает телеметрию при достижения пороговых значений использования ресурсов, настроенных пользователем. Данный выпуск содержит несколько усовершенствований в области мониторинга, управления данными, сведений о событиях работы, структурированной телеметрии.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - работает на одном уделе, фиксирует телеметрию здоровья уровня кластера. В этом выпуске ClusterObserver также отслеживает состояние узла и испускает телеметрию, когда узла не работает/отключается/отключен в течение более длительного времени, чем указанный пользователем период времени.

### <a name="improve-application-life-cycle-experience"></a>Улучшение жизненного цикла приложения

- **[Предварительный просмотр:Запрос утечки](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-advanced#avoid-connection-drops-during-planned-downtime-of-stateless-services)**: Во время запланированного обслуживания службы, таких как обновление сервиса или деактивация узла, вы хотели бы позволить службам изящно слить соединения. Эта функция добавляет задержку экземпляра в конфигурацию обслуживания. Во время запланированных операций SF удалит адрес службы из обнаружения, а затем подождать эту продолжительность, прежде чем закрыть службу.
- **[Автоматическое обнаружение и балансировка подкластеров:](https://docs.microsoft.com/azure/service-fabric/cluster-resource-manager-subclustering )** Подкластерирование происходит, когда службы с различными ограничениями размещения имеют общую [метрику нагрузки.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-metrics) Если нагрузка на различные наборы узлов значительно отличается, менеджер кластерных ресурсов Service Fabric считает, что кластер несбалансирован, даже если он имеет наилучший возможный баланс из-за ограничений размещения. В результате он пытается сбалансировать кластер, потенциально вызывая ненужные движения службы (поскольку "дисбаланс" не может быть существенно улучшен). Начиная с этого выпуска, менеджер кластерных ресурсов теперь будет пытаться автоматически обнаруживать такого рода конфигурации и понимать, когда дисбаланс может быть исправлен через движение, и когда вместо этого он должен оставить вещи в покое, так как никаких существенных улучшений не может быть сделано.  
- [**Различные затраты перемещения для вторичных реплик:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)Мы ввели новую стоимость перемещения VeryHigh, которая обеспечивает дополнительную гибкость в некоторых сценариях, чтобы определить, следует ли использовать отдельную стоимость перемещения для вторичных реплик.
- Механизм [**зонда Liveness**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage ) для контейнерных приложений. Liveness Probe поможет объявить о живости контейнерного приложения, и когда они не отвечают своевременно, это приведет к перезагрузке.
- [**Запуск до завершения / один раз для услуг**](https://docs.microsoft.com/azure/service-fabric/run-to-completion)**

### <a name="image-store-improvements"></a>Улучшения магазина изображений
 - Служба Fabric 7.1 использует **пользовательский транспорт для защиты передачи файлов между узлами по умолчанию.** Зависимость от доли файла SMB удаляется из версии 7.1. Защищенные доли файлов SMB по-прежнему существуют на узлах, содержащих реплику службы Image Store для выбора клиента, чтобы отказаться от дефолта и для обновления и понижения до старой версии.
       
 ### <a name="reliable-collections-improvements"></a>Улучшения надежных коллекций

- [**В памяти только поддержка государственных служб с использованием надежных коллекций:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-work-with-reliable-collections#volatile-reliable-collections)Летучие надежные коллекции позволяет данные, которые будут сохраняться на диске для прочности против крупномасштабных отключений, могут быть использованы для рабочих нагрузок, как реплицированный кэш, например, где случайные потери данных можно допустить. Основываясь на [ограничениях и ограничениях неустойчивых надежных коллекций,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines#volatile-reliable-collections)мы рекомендуем это для рабочих нагрузок, которые не нуждаются в настойчивости, для служб, которые обрабатывают редкие случаи потери Кворума.
- [**Предварительный просмотр: Service Fabric Backup Explorer:**](https://github.com/microsoft/service-fabric-backup-explorer)Для облегчения управления резервными копированиями надежных коллекций для сервисных приложений Fabric Stateful, Service Fabric Backup Explorer позволяет пользователям
    - Аудит и обзор содержимого надежных коллекций,
    - Обновление текущего состояния в согласованное представление
    - Создание резервного копирования текущего снимка надежных коллекций
    - Исправление повреждения данных
                 
### <a name="service-fabric-71-releases"></a>Сервис Ткань 7.1 релизы
| Дата выпуска | Release | Дополнительные сведения |
|---|---|---|
| 20 апреля 2020 года | [Лазурная служба Ткань 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Заметки о выпуске](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|


### <a name="service-fabric-70"></a>СервисНая ткань 7.0

Служба Azure Fabric 7.0 теперь доступна! Обновление до 7.0 можно будет через портал Azure или через развертывание диспетчера ресурсов Azure. Из-за отзывов клиентов о выпусках в праздничный период мы не будем автоматически обновлять кластеры, установленные для автоматического обновления до января.
В январе мы возобновим стандартную процедуру развертывания, и кластеры с включенным автоматическим обновлением начнут автоматически получать обновление 7.0. Мы предоставим еще одно объявление до начала развертывания.
Мы также обновим запланированные даты релиза, чтобы указать, что мы принимаем эту политику во внимание. Ищите здесь обновления на наших [будущих графиков релизов](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Это последний релиз Service Fabric и загружается с ключевыми функциями и улучшениями.

### <a name="key-announcements"></a>Основные объявления
 - [**KeyVaultReference поддержка секретов приложений (Preview)**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Приложения Service Fabric, которые позволили [управляемым идентификаторам,](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) теперь могут напрямую ссылаться на секретный URL-адрес Key Vault в качестве переменной среды, параметра приложения или учетных данных репозитория контейнеров. Service Fabric автоматически разрешит секрет, используя управляемую личность приложения. 
     
- **Улучшенная безопасность обновления для служб без состояния**: Чтобы гарантировать доступность во время обновления приложения, мы ввели новые конфигурации, чтобы определить [минимальное количество экземпляров для](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) услуг без состояния, которые будут считаться доступными. Ранее это значение составляло 1 для всех служб и не было изменяемым. С помощью этой новой проверки безопасности на обслуживание вы можете убедиться, что ваши службы сохранят минимальное количество экземпляров во время обновления приложений, обновления кластеров и другого обслуживания, которое зависит от проверки работоспособности и безопасности Service Fabric.
  
- [**Ограничения ресурсов для служб пользователей**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): Пользователи могут настроить ограничения ресурсов для служб пользователей на узлах для предотвращения таких сценариев, как исчерпание ресурсов служб системы Service Fabric. 
  
- [**Очень высокая стоимость перемещения обслуживания**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) для типа реплики. Реплики с очень высокой стоимостью перемещения будут перемещены только в том случае, если в кластере есть нарушение ограничения, которое не может быть исправлено каким-либо другим способом. Пожалуйста, ознакомьтесь с документами для получения дополнительной информации о том, когда использование "Очень высокая" стоимость перемещения является разумным и для дополнительных соображений.
  
-  **Дополнительные проверки безопасности кластера**: В этом выпуске мы ввели настраиваемую проверку безопасности кворума кворумов семян. Это позволяет настроить количество узлов семян, которые должны быть доступны во время циклов жизни и управления кластерными сценариями. Операции, которые будут принимать кластер ниже настроенного значения, блокируются. Сегодня значение по умолчанию всегда является кворумом узла семян, например, если у вас есть 7 узла семян, операция, которая приведет вас ниже 5 узла семян, будет заблокирована по умолчанию. С помощью этого изменения можно сделать минимальное безопасное значение 6, что позволит только одному узлам семян быть вниз за один раз.
   
- Добавлена поддержка [**для управления службой резервного копирования и восстановления в Service Fabric Explorer.**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster) Это делает возможными следующие действия непосредственно из SFX: открытие резервного копирования и восстановление службы, создание политики резервного копирования, включение автоматического резервного копирования, прием резервных upups adhoc, запуск операций восстановления и просмотр существующих резервных upups.

- Объявление о наличии [**НадежногоCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): Этот инструмент помогает проверить, что типы, используемые в надежных коллекциях, совместимы вперед и назад, совместимы во время обновления подвижного приложения. Это помогает предотвратить сбои обновления или потери данных и порчи данных из-за отсутствующих или несовместимых типов.

- [**Включить стабильные чтения на вторичных репликах**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1):Стабильные чтения ограничат вторичные реплики возвращающимися значениями, которые были унесены кворумом.

Кроме того, этот релиз содержит другие новые функции, исправления ошибок и поддержку, надежность и повышение производительности. Для получения полного списка изменений, пожалуйста, обратитесь к [примечаниям к выпуску](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Сервис Ткань 7.0 релизы

| Дата выпуска | Release | Дополнительные сведения |
|---|---|---|
| 18 ноября 2019 г. | [Лазурная служба Ткань 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Заметки о выпуске](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 января 2020 г. | [Обновление сервиса Azure Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Заметки о выпуске](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 февраля 2020 г. | [Обновление сервиса Azure Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Заметки о выпуске](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 марта 2020 г. | [Обновление сервиса Azure Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Заметки о выпуске](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>СервисНая ткань 6.5

Этот выпуск включает в себя поддержку, надежность и повышение производительности, новые функции, исправления ошибок и усовершенствования для облегчения управления жизненным циклом кластеров и приложений.

> [!IMPORTANT]
> Service Fabric 6.5 — это окончательный релиз с поддержкой инструментов Service Fabric в Visual Studio 2015. Клиентам рекомендуется перейти к [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) в будущем.

Вот что нового в Сервисной ткани 6.5:

- Сервис Fabric Explorer включает в себя [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) для проверки приложений, которые вы загрузили в хранилище изображений.

- [Патч-приложение (POA)](service-fabric-patch-orchestration-application.md) версия [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) включает в себя множество самодиагностических улучшений. Клиентам POA рекомендуется перейти к этой версии.

- [Служба EventStore включена по умолчанию](service-fabric-visualizing-your-cluster.md#event-store) для кластеров Service Fabric 6.5, если вы не отказались.

- Добавлены [события жизненного цикла реплики](service-fabric-diagnostics-event-generation-operational.md#replica-events) для служб состояния.

- [Улучшенная видимость состояния семенного узла,](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)включая предупреждения на уровне кластера, если семенной узор неработоспособен *(Down,* *Removed* или *Unknown).*

- [Service Fabric Application Disaster Recovery Tool](https://github.com/Microsoft/Service-Fabric-AppDRTool) позволяет службам service Fabric быстро восстанавливаться при возникновении проблемы основного кластера. Данные из первичного кластера постоянно синхронизируются на вторичном резервном приложении с помощью периодического резервного копирования и восстановления.

- Поддержка визуальной студии для [публикации приложений .NET Core для кластеров на основе Linux.](service-fabric-how-to-publish-linux-app-vs.md)

- [Служба обслуживания Azure Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) будет автоматически установлена для Service Fabric 6.5 (и более поздних версий) при обновлении или создании нового кластера Linux в Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) устанавливается по умолчанию на кластеры MacOS/Linux OneBox.

Для получения более [подробной](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)информации см.

### <a name="service-fabric-65-releases"></a>Сервис Ткань 6.5 релизы

| Дата выпуска | Release | Дополнительные сведения |
|---|---|---|
| 11 июня 2019 г. | [Лазурная служба Ткань 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Заметки о выпуске](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 июля 2019 г. | [Обновление сервиса Azure Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Заметки о выпуске](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 июля 2019 г. | [Обновление сервиса Azure Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Заметки о выпуске](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Авг 23, 2019 | [Обновление сервиса Azure Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Заметки о выпуске](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 октября 2019 г. | [Обновление сервиса Azure Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | (Заметки о выпуске) (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>Предыдущие версии

### <a name="service-fabric-64-releases"></a>Сервис Ткань 6.4 релизы

| Дата выпуска | Release | Дополнительные сведения |
|---|---|---|
| 30 ноября 2018 года | [Лазурная служба Ткань 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 декабря 2018 г. | [Обновление обновления службы Azure Fabric 6.4 для кластеров Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 февраля 2019 г. | [Обновление сервиса Azure Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 марта 2019 г. | [Обновление сервиса Azure Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 апреля 2019 года | [Обновление сервиса Azure Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 мая 2019 г. | [Обновление сервиса Azure Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 мая 2019 года | [Обновление сервиса Azure Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
