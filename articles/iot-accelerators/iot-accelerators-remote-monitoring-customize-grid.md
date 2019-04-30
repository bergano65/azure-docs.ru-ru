---
title: Добавление сетки в пользовательский интерфейс решения для удаленного мониторинга в Azure | Документация Майкрософт
description: В этой статье показано, как добавить сетку на страницу в веб-интерфейс акселератора решения для удаленного мониторинга.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/04/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447103"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Добавление пользовательской сетки в веб-интерфейс акселератора решения для удаленного мониторинга

В этой статье показано, как добавить сетку на страницу в веб-интерфейс акселератора решения для удаленного мониторинга. Содержание статьи:

- Подготовка локальной среды разработки.
- Добавление новой сетки на страницу в пользовательском веб-интерфейсе.

Пример сетки в этой статье отображает данные из службы, добавление которой описано в статье [Add a custom service to the Remote Monitoring solution accelerator web UI](iot-accelerators-remote-monitoring-customize-service.md) (Добавление пользовательской службы в веб-интерфейс акселератора решения для удаленного мониторинга).

## <a name="prerequisites"></a>Технические условия

Чтобы выполнить действия, описанные в этом руководстве, необходимо установить следующее ПО на локальный компьютер разработки:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Перед началом работы

Прежде чем продолжить, необходимо выполнить шаги, указанные в следующих статьях:

- [Добавление пользовательской страницы в веб-интерфейс акселератора решения для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-page.md);
- [Добавление пользовательской службы в веб-интерфейс акселератора решений для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Добавление сетки

Чтобы добавить сетку в веб-интерфейс, необходимо добавить исходные файлы, которые служат для ее определения. Также необходимо внести изменения в некоторые имеющиеся файлы, чтобы новый компонент учитывался в веб-интерфейсе.

### <a name="add-the-new-files-that-define-the-grid"></a>Добавление новых файлов, которые определяют сетку

Чтобы приступить к работе, просмотрите файлы, которые определяют сетку, в папке **src/walkthrough/components/pages/pageWithGrid/exampleGrid**.

**exampleGrid.js**



**exampleGridConfig.js**



Скопируйте папку **src/walkthrough/components/pages/pageWithGrid/exampleGrid** в папку **src/components/pages/example**.

### <a name="add-the-grid-to-the-page"></a>Добавление сетки на страницу

Чтобы импортировать определения службы, измените **src/components/pages/example/basicPage.container.js**.

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Чтобы добавить сетку, измените **src/components/pages/example/basicPage.js**.

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Для обновления тестирования измените **src/components/pages/example/basicPage.test.js**.

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>Тестирование сетки

Если веб-интерфейс еще не запущен локально, запустите следующую команду в корне локальной копии репозитория:

```cmd/sh
npm start
```

Предыдущая команда запускает пользовательский интерфейс локально по адресу [http://localhost:3000/dashboard](http://localhost:3000/dashboard). Чтобы увидеть отображение данных службы в виде сетки, перейдите на страницу **Пример**.

## <a name="select-rows"></a>Выбор строк

Существует два варианта, с помощью которых пользователь может выбирать строки в сетке.

### <a name="hard-select-rows"></a>"Жесткий" выбор строк

Когда пользователю приходится работать с несколькими строками одновременно, используйте их флажки.

1. Включение "жесткого" выбора строк выполняется путем добавления **checkboxColumn** к **columnDefs**, предоставляемом сетке. **checkboxColumn** определяется в **/src/components/shared/pcsGrid/pcsGrid.js**.

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Чтобы получить доступ к выбранным элементам, воспользуйтесь ссылкой на API внутренней сетки.

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Когда для выбора строки будет использоваться "жесткий" вариант, на странице появятся контекстные кнопки.

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Если их нажать, то над элементами, выбранными с помощью "жесткого" метода начнут выполнятся действия.

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>"Мягкий" выбор строк

Когда пользователю приходится работать только с одной строкой, выполните настройку ссылки выбранной с помощью "мягкого" выбора для одного или нескольких столбцов в **columnDefs**.

1. В **exampleGridConfig.js** добавьте **SoftSelectLinkRenderer** в качестве **cellRendererFramework** для **columnDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Если щелкнуть ссылку, созданную при помощи "мягкого" варианта, будет запущено событие **onSoftSelectChange**. Для этой строки можно выполнить любые описанные действия, например, открытие всплывающего элемента с подробной информацией. Данный пример просто будет записан в консоль.

    ```js
    onSoftSelectChange = (rowId, rowEvent) => {
      const { onSoftSelectChange } = this.props;
      const obj = (this.gridApi.getDisplayedRowAtIndex(rowId) || {}).data;
      if (obj) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', obj);
        this.setState({ softSelectedObj: obj });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(obj, rowEvent);
      }
    }
    ```

## <a name="next-steps"></a>Дальнейшие действия

Из статьи вы узнали о ресурсах, которые могут помочь вам при настройке или добавлении станицы в веб-интерфейсе в акселераторе решения для удаленного мониторинга.

Вы определили сетку. Теперь необходимо ознакомится со статьей [Add a custom flyout to the Remote Monitoring solution accelerator web UI](iot-accelerators-remote-monitoring-customize-flyout.md) (Добавление пользовательского всплывающего элемента в веб-интерфейс акселератора решения для удаленного мониторинга).

Более подробные сведения об акселераторе решения для удаленного мониторинга см. в [этой статье](iot-accelerators-remote-monitoring-sample-walkthrough.md).
