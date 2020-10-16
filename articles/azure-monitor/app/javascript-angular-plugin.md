---
title: Угловой подключаемый модуль для Application Insights SDK JavaScript
description: Как установить и использовать угловой подключаемый модуль для Application Insights SDK для JavaScript.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91844031"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Угловой подключаемый модуль для Application Insights SDK JavaScript

Угловой подключаемый модуль для пакета SDK для Application Insights JavaScript позволяет:

- Отслеживание изменений маршрутизатора
- Статистика использования угловых компонентов

> [!WARNING]
> Угловой подключаемый модуль не совместим с ECMAScript 3 (ES3).

## <a name="getting-started"></a>Начало работы

Установить пакет NPM:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Основное использование

Настройте экземпляр Application Insights в компоненте записи в приложении:

```js
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

Чтобы использовать `trackMetric` метод для трассировки использования компонентов в угловых компонентах, добавьте в `AngularPluginService` список поставщиков в файле в качестве поставщика `app.module.ts` .

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

Чтобы отвести отслеживание времени существования компонента, вызовите `trackMetric` `ngOnDestroy` метод этого компонента. Когда компонент уничтожается, он запускает `trackMetric` событие, которое отправляет время, неизменности пользователем на странице, и имя компонента.

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о пакете SDK для JavaScript см. в [документации по Application Insights JavaScript SDK](javascript.md)
- [Угловой подключаемый модуль на GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)