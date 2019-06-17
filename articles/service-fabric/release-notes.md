---
title: Освобождает Azure Service Fabric
description: Заметки о выпуске для новейших функций и усовершенствований в Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 5610c6d31732144086812bb02b65cfaffa067eae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063002"
---
# <a name="service-fabric-releases"></a>Выпуски Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Руководства по устранению неполадок</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">отслеживание вопросов</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">варианты поддержки</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">поддерживаемые версии</a>  
|  <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Примеры кода</a>

Дополнительные сведения о последних выпусках и обновлениях среды выполнения Service Fabric и пакеты SDK в этой статье.

## <a name="whats-new-in-service-fabric"></a>**Новые возможности в Service Fabric**

### <a name="service-fabric-65"></a>Service Fabric 6.5

В последнем выпуске Service Fabric включает в себя возможности поддержки, надежность и производительность, новые возможности, исправления ошибок и усовершенствования для упрощения управления жизненным циклом кластера и приложений.

> [!IMPORTANT]
> Service Fabric 6.5 является окончательной с Service Fabric, средства поддержки в Visual Studio 2015. Пользователям рекомендуется переместить [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) Забегая вперед.

Ниже приведен, новые возможности в Service Fabric 6.5.

- Service Fabric Explorer включает [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) для проверки приложения, загруженные в хранилище образов.

- [Исправление Оркестрации приложение Реализовано](service-fabric-patch-orchestration-application.md) версии [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) представлено множество улучшений автоматической диагностики. Клиенты POA рекомендуется переместить до этой версии.

- [По умолчанию включена служба EventStore](service-fabric-visualizing-your-cluster.md#event-store) кластеров Service Fabric 6.5, если не указан параметр.

- Добавлен [события жизненного цикла реплики](service-fabric-diagnostics-event-generation-operational.md#replica-events) служб с отслеживанием состояния.

- [Улучшенная видимость начальное состояние узла](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), включая предупреждения на уровне кластера, если начальный узел находится в неработоспособном состоянии (*вниз*, *удалено* или *Неизвестный*).

- [Средство Service Fabric приложение аварийного восстановления](https://github.com/Microsoft/Service-Fabric-AppDRTool) позволяет службам с отслеживанием состояния Service Fabric быстро восстановить возникновения сбоя в основном кластере. Данные из основного кластера постоянную синхронизацию на дополнительный резервный приложения с помощью периодического резервного копирования и восстановления.

- Поддержка для Visual Studio [публикации приложений .NET Core в кластеры под управлением Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) будет устанавливаться автоматически Service Fabric 6.5 (и более поздних версиях) при обновлении или создать новый кластер Linux в Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) устанавливается по умолчанию в кластерах OneBox MacOS или Linux.

Дополнительные сведения см. в разделе [заметки о выпуске Service Fabric 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

## <a name="previous-versions"></a>Предыдущие версии

### <a name="service-fabric-64-releases"></a>Выпуски Service Fabric 6.4

| Дата выпуска | Release | Подробнее |
|---|---|---|
| 30 ноября 2018 г. | [Azure Service Fabric 6.4 ](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 декабря 2018 г. | [В кластерах Azure Service Fabric 6.4 обновить выпуск для Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 февраля 2019 г. | [Azure Service Fabric 6.4 обновления выпуска](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 марта 2019 г. | [Azure Service Fabric 6.4 обновления выпуска](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 апреля 2019 г. | [Azure Service Fabric 6.4 обновления выпуска](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 мая 2019 г. | [Azure Service Fabric 6.4 обновления выпуска](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 мая 2019 г. | [Azure Service Fabric 6.4 обновления выпуска](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Заметки о выпуске](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
