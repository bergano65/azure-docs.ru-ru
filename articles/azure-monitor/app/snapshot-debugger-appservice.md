---
title: Включить отладчик моментальных снимков для приложений .NET в службе приложений Azure | Документация Майкрософт
description: Включить отладчик моментальных снимков для приложений .NET в службе приложений Azure
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: 7ff93dffe2dd82bdbba204b4235a297b337438f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60784102"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Включить отладчик моментальных снимков для приложений .NET в службе приложений Azure

Отладчик моментальных снимков в настоящее время работает для приложений ASP.NET и ASP.NET Core, запущенных в службе приложений Azure в планы обслуживания Windows.

## <a id="installation"></a> Включение Snapshot Debugger
Чтобы включить отладчик моментальных снимков для приложения, следуйте приведенным ниже инструкциям. Если вы используете другой тип службы Azure, ниже приведены инструкции по включению отладчик моментальных снимков на других поддерживаемых платформах.
* [Облачные службы Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Службы Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Профилирование веб-приложений, работающих на виртуальной машине Azure или в масштабируемом наборе виртуальных машин, с помощью Application Insights Profiler](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Локальных виртуальных или физических компьютеров](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Snapshot Debugger предварительно устанавливается как часть среды выполнения службы приложений, но вам необходимо включить его на моментальные снимки get для приложения службы приложений. После развертывания приложения, даже если включить пакет SDK Application Insights в исходном коде, выполните следующие действия, чтобы включить отладчик моментальных снимков.

1. Перейдите к панели **служб приложений** на портале Azure.
2. Выберите **Параметры > Application Insights**.

   ![Включение Application Insights на портале служб приложений](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Следуйте инструкциям на панели, чтобы создать ресурс Application Insights или выбрать имеющийся ресурс для отслеживания приложения. Также убедитесь, что оба переключателя для отладчика моментальных снимков, **на**.

   ![Добавление расширения сайта Application Insights][Enablement UI]

4. Отладчик моментальных снимков включен с помощью параметра приложения службы приложений.

    ![Параметр приложения для отладчика моментальных снимков][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Отключить отладчик моментальных снимков

Выполните те же действия, что и для **Включение Snapshot Debugger**, но переключиться оба переключателя для отладчика моментальных снимков для **Off**.
Мы рекомендуем использовать отладчик моментальных снимков, которые включены для всех приложений, для упрощения диагностики исключений приложений.

## <a name="next-steps"></a>Дальнейшие действия

* [Работа с Azure Application Insights в Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

