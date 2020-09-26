---
title: Добавление панели в пользовательский интерфейс решения для удаленного мониторинга в Azure | Документация Майкрософт
description: В этой статье показано, как добавить новую панель на панель мониторинга в веб-интерфейсе акселератора решения для удаленного мониторинга.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 1dcca8409022ba4cf1f988b7c777e3a1fa511060
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318370"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Добавление пользовательской панели на панель мониторинга в веб-интерфейсе акселератора решения для удаленного мониторинга

В этой статье показано, как добавить новую панель на страницу панели мониторинга в веб-интерфейсе акселератора решения для удаленного мониторинга. Содержание статьи:

- Подготовка локальной среды разработки.
- Добавление новой панели на страницу панели мониторинга в пользовательском веб-интерфейсе.

Пример панели в этой статье отображается на имеющейся странице панели мониторинга.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этом руководстве, необходимо установить следующее ПО на локальный компьютер разработки:

- [Git](https://git-scm.com/downloads);
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Перед началом работы

Прежде чем продолжить, следует выполнить действия, описанные в статье [Добавление пользовательской страницы в веб-интерфейс акселератора решения для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-page.md).

## <a name="add-a-panel"></a>Добавление панели

Чтобы добавить панель в веб-интерфейс, необходимо добавить исходные файлы, которые определяют панель, а затем изменить панель мониторинга, чтобы на ней отображалась эта панель.

### <a name="add-the-new-files-that-define-the-panel"></a>Добавление новых файлов, которые определяют панель

Чтобы приступить к работе, просмотрите файлы, которые определяют панель, в папке **src/walkthrough/components/pages/dashboard/panels/examplePanel**, включая следующие:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Скопируйте папку **src/walkthrough/components/pages/dashboard/panels/examplePanel** в папку **src/components/pages/dashboard/panels**.

Добавьте в файл **src/walkthrough/components/pages/dashboard/panels/index.js** следующий экспорт:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Добавление панели на панель мониторинга

Чтобы добавить панель, измените файл **src/components/pages/dashboard/dashboard.js**.

Добавьте пример панели в список операций импорта из панелей:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Добавьте следующее определение ячейки в сетку содержимого страницы:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Тестирование всплывающего элемента

Если веб-интерфейс еще не запущен локально, запустите следующую команду в корне локальной копии репозитория:

```cmd/sh
npm start
```

Предыдущая команда запускает пользовательский интерфейс локально по адресу `http://localhost:3000/dashboard`. Чтобы просмотреть новую панель, перейдите на страницу **панели мониторинга**.

## <a name="next-steps"></a>Дальнейшие действия

Из статьи вы узнали о ресурсах, которые могут помочь вам при настройке или добавлении панелей мониторинга в пользовательском веб-интерфейсе в акселераторе решения для удаленного мониторинга.

Дополнительные сведения об ускорителе решений для удаленного мониторинга см. в разделе [архитектура удаленного мониторинга](iot-accelerators-remote-monitoring-sample-walkthrough.md).
