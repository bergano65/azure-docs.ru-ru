---
title: Включение Snapshot Debugger для приложений .NET в службе приложений Azure | Документация Майкрософт
description: Включение Snapshot Debugger для приложений .NET в службе приложений Azure
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f6eb6376075337edd7656e4bc83b5b7fddde479
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899892"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Включение Snapshot Debugger для приложений .NET в службе приложений Azure

Snapshot Debugger в настоящее время работает для приложений ASP.NET и ASP.NET Core, работающих в службе приложений Azure в планах служб Windows.

## <a id="installation"></a>Включить Snapshot Debugger
Чтобы включить Snapshot Debugger для приложения, следуйте приведенным ниже инструкциям. Если вы используете другой тип службы Azure, ниже приведены инструкции по включению Snapshot Debugger на других поддерживаемых платформах:
* [Oблачныe службы Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Службы Service Fabric Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Профилирование веб-приложений, работающих на виртуальной машине Azure или в масштабируемом наборе виртуальных машин, с помощью Application Insights Profiler](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Локальные виртуальные или физические компьютеры](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Если вы используете предварительную версию .NET Core, следуйте инструкциям по [включению snapshot Debugger для других сред](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) , прежде чем добавить пакет NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) в приложение. а затем выполните остальные инструкции ниже. 

Application Insights Snapshot Debugger предустановлена в составе среды выполнения служб приложений, но ее необходимо включить, чтобы получить моментальные снимки для приложения службы приложений. После развертывания приложения, даже если вы включили в исходный код пакет SDK для Application Insights, выполните следующие действия, чтобы включить отладчик моментальных снимков.

1. Перейдите к панели **служб приложений** на портале Azure.
2. Выберите **Параметры > Application Insights**.

   ![Включение Application Insights на портале служб приложений](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Следуйте инструкциям на панели, чтобы создать ресурс Application Insights или выбрать имеющийся ресурс для отслеживания приложения. Также убедитесь, что оба параметра для Snapshot Debugger **включены.**

   ![Добавление расширения сайта Application Insights][Enablement UI]

4. Snapshot Debugger теперь включена с помощью параметра приложения служб приложений.

    ![Параметр приложения для Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Отключить Snapshot Debugger

Выполните те же действия, что и для параметра **Enable snapshot Debugger**, но установите оба переключателя для snapshot Debugger в значение **Off**.
Рекомендуется включить Snapshot Debugger для всех приложений, чтобы упростить диагностику исключений приложений.

## <a name="next-steps"></a>Дальнейшие действия

- Создание трафика для приложения, которое может вызвать исключение. Затем подождите 10 – 15 минут, чтобы моментальные снимки отправлялись на экземпляр Application Insights.
- См. раздел [моментальные снимки](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) в портал Azure.
- Справку по устранению неполадок Snapshot Debugger см. в разделе [snapshot Debugger устранение неполадок](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

