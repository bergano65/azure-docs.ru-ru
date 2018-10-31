---
title: Добавление всплывающего элемента в пользовательский интерфейс решения для удаленного мониторинга в Azure | Документация Майкрософт
description: В этой статье показано, как добавить всплывающий элемент на страницу в веб-интерфейс акселератора решений для удаленного мониторинга.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167432"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Добавление пользовательского всплывающего элемента в веб-интерфейс акселератора решений для удаленного мониторинга

В этой статье показано, как добавить новый всплывающий элемент на страницу в веб-интерфейсе акселератора решений для удаленного мониторинга. Содержание статьи:

- Подготовка локальной среды разработки.
- Добавление нового всплывающего элемента на страницу в пользовательском веб-интерфейсе.

Пример всплывающего элемента в этой статье отображается на странице с сеткой, добавление которой описано в статье [Add a custom grid to the Remote Monitoring solution accelerator web UI](iot-accelerators-remote-monitoring-customize-grid.md) (Добавление пользовательской сетки в веб-интерфейс акселератора решения для удаленного мониторинга).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этом руководстве, необходимо установить следующее ПО на локальный компьютер разработки:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Перед началом работы

Прежде чем продолжить, необходимо выполнить шаги, указанные в следующих статьях:

- [Добавление пользовательской страницы в веб-интерфейс акселератора решений для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-page.md);
- [Добавление пользовательской службы в веб-интерфейс акселератора решений для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-service.md)
- [Добавление пользовательской сетки в веб-интерфейс акселератора решений для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Добавление всплывающего элемента

Чтобы добавить всплывающий элемент в веб-интерфейс, необходимо добавить исходные файлы, которые определяют этот элемент. Необходимо также изменить некоторые имеющиеся файлы, чтобы веб-интерфейс учитывал новый компонент.

### <a name="add-the-new-files-that-define-the-flyout"></a>Добавление новых файлов, которые определяют всплывающий элемент

Чтобы приступить к работе, просмотрите файлы, которые определяют всплывающий элемент, в папке **src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout**.

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Скопируйте папку **src/walkthrough/components/pages/pageWithFlyout/flyouts** в папку **src/components/pages/example**.

### <a name="add-the-flyout-to-the-page"></a>Добавление всплывающего элемента на страницу

Чтобы добавить всплывающий элемент, измените **src/components/pages/example/basicPage.js**.

Добавьте **Btn** к импорту из **components/shared** и добавьте импорт для **svgs** и **ExampleFlyoutContainer**.

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Добавьте определение **const** для **closedFlyoutState** и добавьте его в состояние в конструкторе.

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Добавьте следующие функции в класс **BasicPage**.

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Добавьте следующие определения**const** в функцию **render**.

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Добавьте кнопку, чтобы открыть всплывающий элемент в контекстном меню.

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Добавьте текст и контейнер всплывающего элемента к содержимому страницы.

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Тестирование всплывающего элемента

Если веб-интерфейс еще не запущен локально, запустите следующую команду в корне локальной копии репозитория:

```cmd/sh
npm start
```

Предыдущая команда запускает пользовательский интерфейс локально по адресу [http://localhost:3000/dashboard](http://localhost:3000/dashboard). Перейдите на страницу **Пример** и щелкните **Открыть всплывающий элемент**.

## <a name="next-steps"></a>Дополнительная информация

Из статьи вы узнали о ресурсах, которые могут помочь вам при настройке или добавлении станицы в веб-интерфейсе в акселераторе решений для удаленного мониторинга.

Вы определили всплывающий элемент на странице. Следующий шаг — [Добавление пользовательской панели на панель мониторинга в веб-интерфейсе акселератора решений для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-panel.md).

Более подробные сведения об акселераторе решений для удаленного мониторинга см. в [этой статье](iot-accelerators-remote-monitoring-sample-walkthrough.md).
