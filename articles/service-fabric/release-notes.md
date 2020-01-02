---
title: Выпуски Service Fabric Azure
description: Заметки о выпуске для новейших функций и улучшений в Service Fabric.
author: athinanthny
manager: gamonroy
ms.author: atsenthi
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 496a5babe58be4354ffb10a331d35abc8a51b04d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186520"
---
# <a name="service-fabric-releases"></a>Service Fabric выпуски

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">руководства по устранению неполадок</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Отслеживание проблем</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">варианты поддержки</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">поддерживаемых версий</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">примеров кода</a>

В этой статье содержатся дополнительные сведения о последних выпусках и обновлениях для среды выполнения Service Fabric и пакетов SDK.

## <a name="whats-new-in-service-fabric"></a>Новые возможности Service Fabric

### <a name="service-fabric-70"></a>Service Fabric 7,0

Azure Service Fabric 7,0 теперь доступна! Вы сможете выполнить обновление до 7,0 с помощью портал Azure или с помощью Azure Resource Managerного развертывания. Из-за отзывов клиентов о выпусках по истечении праздничного периода мы не будем начинать автоматическое обновление кластеров для получения автоматических обновлений до января.
В январе мы возбудем возобновить стандартную процедуру развертывания, и кластеры с включенным автоматическим обновлением начнут автоматически принимать обновление 7,0. Перед началом развертывания мы предложим другое объявление.
Мы также будем обновлять наши запланированные даты выпуска, чтобы указать, что мы рассматриваем эту политику. Здесь можно найти обновления для будущих [расписаний выпуска](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Это последний выпуск Service Fabric и он загружается с основными функциями и улучшениями.

### <a name="key-announcements"></a>Основные объявления
 - [**Поддержка кэйваултреференце для секретов приложений (Предварительная версия)** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Service Fabric приложения с включенными [управляемыми удостоверениями](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) теперь могут напрямую ссылаться на Key Vault секретный URL-адрес в качестве переменной среды, параметра приложения или учетных данных репозитория контейнеров. Service Fabric будет автоматически разрешать секрет с помощью управляемого удостоверения приложения. 
     
- **Улучшенная безопасность обновления для служб без отслеживания состояния**. чтобы обеспечить доступность во время обновления приложения, мы предоставили новые конфигурации для определения [минимального количества экземпляров для служб без отслеживания состояния](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) , которые будут считаться доступными. Ранее это значение было равно 1 для всех служб и не было изменено. Благодаря этой новой проверке безопасности для каждой службы можно гарантировать, что службы будут хранить минимальное количество экземпляров во время обновления приложения, обновления кластера и прочее обслуживание, основанное на проверках работоспособности Service Fabric и безопасности.
  
- [**Ограничения ресурсов для пользовательских служб**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services). пользователи могут настроить ограничения ресурсов для пользовательских служб на узле, чтобы предотвратить такие сценарии, как исчерпание ресурсов Service Fabric системных служб. 
  
- [**Очень высокая стоимость перемещения служб**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) для типа реплики. Реплики с очень высокими затратами на перемещение будут перемещены только в случае нарушения ограничения в кластере, которое не может быть исправлено каким-либо другим способом. Дополнительные сведения об использовании "очень высоких" затрат на перемещение см. в документах.
  
-  **Дополнительные проверки безопасности кластера**. в этом выпуске мы предоставили настраиваемую проверку безопасности кворума узла начального значения. Это позволяет настроить количество узлов начальных значений, которые должны быть доступны во время жизненного цикла кластера и сценариев управления. Операции, которые принимают кластер под заданным значением, блокируются. Сегодня значение по умолчанию всегда является кворумом узлов начальных значений. Например, если у вас 7 начальных узлов, то операция, которая помогла бы перейти на 5 начальных узлов, будет заблокирована по умолчанию. С этим изменением можно было бы сделать минимальное опасное значение 6, которое позволяло только одному узлу начальных значений одновременно.
   
- Добавлена поддержка [**управления службой резервного копирования и восстановления в Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Это делает возможными следующие действия непосредственно в SFX: обнаружение службы резервного копирования и восстановления, создание политики резервного копирования, включение автоматического резервного копирования, получение нерегламентированных резервных копий, активация операций восстановления и Просмотр существующих резервных копий.

- Объявление о доступности [**релиаблеколлектионсмиссингтипестул**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): это средство помогает проверить, что типы, используемые в надежных коллекциях, поддерживают прямую и обратную совместимость во время последовательного обновления приложения. Это помогает предотвратить сбои обновления, потерю данных и повреждение данных из-за отсутствия или несовместимости типов.

- [**Включить стабильные операции чтения на вторичных репликах. при**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)стабильном чтении вторичные реплики будут ограничены возвратом значений, которые были подтверждено) кворума.

Кроме того, этот выпуск содержит другие новые функции, исправления ошибок, а также улучшения поддержки, надежности и производительности. Полный список изменений см. в [заметках о выпуске](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

| Дата выпуска | Release | Подробнее |
|---|---|---|
| 18 ноября 2019 г. | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Заметки о выпуске](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|


### <a name="service-fabric-65"></a>Service Fabric 6,5

Этот выпуск включает в себя поддержку, надежность и улучшения производительности, новые функции, исправления ошибок и усовершенствования для упрощения управления жизненным циклом кластера и приложений.

> [!IMPORTANT]
> Service Fabric 6,5 — это окончательный выпуск с поддержкой средств Service Fabric в Visual Studio 2015. Пользователям рекомендуется перейти на [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) в будущем.

Вот именно новые Service Fabric 6,5:

- Service Fabric Explorer включает [средство просмотра хранилище образов](service-fabric-visualizing-your-cluster.md#image-store-viewer) для проверки приложений, отправленных в хранилище образов.

- [Приложение для управления исправлениями (вех)](service-fabric-patch-orchestration-application.md) версии [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) включает в себя множество улучшений самодиагностики. Клиентам ВЕХ рекомендуется перейти на эту версию.

- [Служба евентсторе по умолчанию включена](service-fabric-visualizing-your-cluster.md#event-store) для кластеров Service Fabric 6,5, если вы не выбрали.

- Добавлены [события жизненного цикла реплики](service-fabric-diagnostics-event-generation-operational.md#replica-events) для служб с отслеживанием состояния.

- [Улучшенная видимость состояния узла начальных значений](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), включая предупреждения на уровне кластера, если начальный узел неработоспособен(отключен *или* *неизвестен*).

- [Service Fabric средство аварийного восстановления приложений](https://github.com/Microsoft/Service-Fabric-AppDRTool) позволяет Service Fabric службам с отслеживанием состояния быстро восстанавливаться при аварии основного кластера. Данные из основного кластера непрерывно синхронизируются в резервном приложении-получателе с помощью периодического резервного копирования и восстановления.

- Поддержка [публикации приложений .NET Core в кластерах под управлением Linux в](service-fabric-how-to-publish-linux-app-vs.md)Visual Studio.

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) будет автоматически устанавливаться для Service Fabric 6,5 (и более поздних версий) при обновлении или создании нового кластера Linux в Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) устанавливается по умолчанию в кластерах MacOS/Linux OneBox.

Дополнительные сведения см. в [заметках о Выпуске Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Выпуски Service Fabric 6,5

| Дата выпуска | Release | Подробнее |
|---|---|---|
| 11 июня 2019 г. | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Заметки о выпуске](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 июля 2019 г. | [Обновление выпуска Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Заметки о выпуске](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 июля 2019 г. | [Обновление выпуска Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Заметки о выпуске](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 августа 2019 г. | [Обновление выпуска Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Заметки о выпуске](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 октября 2019 г. | [Обновление выпуска Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Заметки о выпуске](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Предыдущие версии

### <a name="service-fabric-64-releases"></a>Выпуски Service Fabric 6,4

| Дата выпуска | Release | Подробнее |
|---|---|---|
| 30 ноября 2018 г. | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 декабря 2018 г. | [Обновление выпуска Azure Service Fabric 6,4 для кластеров Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 февраля 2019 г. | [Обновление выпуска Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 марта 2019 г. | [Обновление выпуска Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 апреля 2019 г. | [Обновление выпуска Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 мая 2019 г. | [Обновление выпуска Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 мая 2019 г. | [Обновление выпуска Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
