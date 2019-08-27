---
title: Выпуски Service Fabric Azure
description: Заметки о выпуске для новейших функций и улучшений в Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 984e7ed82ba81d8c40688efd0a0a06e095be7c06
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018985"
---
# <a name="service-fabric-releases"></a>Service Fabric выпуски

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Руководства</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">проблема отслеживание</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">поддержка Параметры</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">поддержка версия</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">пример кода</a>

В этой статье содержатся дополнительные сведения о последних выпусках и обновлениях для среды выполнения Service Fabric и пакетов SDK.

## <a name="whats-new-in-service-fabric"></a>Новые возможности Service Fabric

### <a name="service-fabric-65"></a>Service Fabric 6,5

Последний выпуск Service Fabric включает в себя поддержку, надежность и улучшения производительности, новые функции, исправления ошибок и усовершенствования для упрощения управления жизненным циклом кластера и приложений.

> [!IMPORTANT]
> Service Fabric 6,5 — это окончательный выпуск с поддержкой средств Service Fabric в Visual Studio 2015. Пользователям рекомендуется перейти на [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) в будущем.

Вот именно новые Service Fabric 6,5:

- Service Fabric Explorer включает [средство просмотра хранилище образов](service-fabric-visualizing-your-cluster.md#image-store-viewer) для проверки приложений, отправленных в хранилище образов.

- [Приложение для управления исправлениями (вех)](service-fabric-patch-orchestration-application.md) версии [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) включает в себя множество улучшений самодиагностики. Клиентам ВЕХ рекомендуется перейти на эту версию.

- [Служба евентсторе по умолчанию включена](service-fabric-visualizing-your-cluster.md#event-store) для кластеров Service Fabric 6,5, если вы не выбрали.

- Добавлены [события жизненного цикла реплики](service-fabric-diagnostics-event-generation-operational.md#replica-events) для служб с отслеживанием состояния.

- [Улучшенная видимость состояния узла начальных значений](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), включая предупреждения на уровне кластера, если начальный узел неработоспособен (*отключен*или неизвестен).

- [Service Fabric средство аварийного восстановления приложений](https://github.com/Microsoft/Service-Fabric-AppDRTool) позволяет Service Fabric службам с отслеживанием состояния быстро восстанавливаться при аварии основного кластера. Данные из основного кластера непрерывно синхронизируются в резервном приложении-получателе с помощью периодического резервного копирования и восстановления.

- Поддержка [публикации приложений .NET Core в кластерах под управлением Linux в](service-fabric-how-to-publish-linux-app-vs.md)Visual Studio.

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) будет автоматически устанавливаться для Service Fabric 6,5 (и более поздних версий) при обновлении или создании нового кластера Linux в Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) устанавливается по умолчанию в кластерах MacOS/Linux OneBox.

Дополнительные сведения см. в заметках о выпуске [Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Выпуски Service Fabric 6,5

| Дата выпуска | Выпуск | Дополнительная информация |
|---|---|---|
| 11 июня 2019 г. | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Заметки о выпуске](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 июля 2019 г. | [Обновление выпуска Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Заметки о выпуске](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 июля 2019 г. | [Обновление выпуска Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Заметки о выпуске](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 августа 2019 г. | [Обновление выпуска Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Заметки о выпуске](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |

## <a name="previous-versions"></a>Предыдущие версии

### <a name="service-fabric-64-releases"></a>Выпуски Service Fabric 6,4

| Дата выпуска | Выпуск | Дополнительная информация |
|---|---|---|
| 30 ноября 2018 г. | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 декабря 2018 г. | [Обновление выпуска Azure Service Fabric 6,4 для кластеров Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 февраля 2019 г. | [Обновление выпуска Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 марта 2019 г. | [Обновление выпуска Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 апреля 2019 г. | [Обновление выпуска Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 мая 2019 г. | [Обновление выпуска Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 мая 2019 г. | [Обновление выпуска Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
