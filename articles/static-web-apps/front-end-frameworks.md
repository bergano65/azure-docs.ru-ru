---
title: Настройка интерфейсных платформ с помощью предварительной версии статических веб-приложений Azure
description: Параметры для популярных интерфейсных платформ, необходимых для статических веб-приложений Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 4b1bc58b6b4a87cd6e5e09e83020a38261b8746f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905379"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Настройка интерфейсных платформ и библиотек с помощью предварительной версии статических веб-приложений Azure

Статические веб-приложения Azure требуют наличия соответствующих значений конфигурации в [файле конфигурации сборки](github-actions-workflow.md) для интерфейсной платформы или библиотеки.

## <a name="configuration"></a>Конфигурация

В следующей таблице перечислены параметры для ряда платформ и библиотек<sup>1</sup>.

Цель столбцов таблицы объясняется следующими элементами:

- **Расположение артефакта приложения**: содержит значение `app_artifact_location` , которое является [папкой для построенных версий файлов приложения](github-actions-workflow.md#build-and-deploy).

- **Настраиваемая команда сборки**: Если для платформы требуется команда, отличающаяся от `npm run build` или `npm run azure:build` , можно определить [пользовательскую команду сборки](github-actions-workflow.md#custom-build-commands).

| Инфраструктура | Расположение артефактов приложения | Настраиваемая команда сборки |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | н/д <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Угловой универсальный](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [аурелиа](https://aurelia.io/) | `dist` | Недоступно |
| [Backbone.js](https://backbonejs.org/) | `/` | Недоступно |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | Недоступно |
| [Ember](https://emberjs.com/) | `dist` | Недоступно |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [глиммер](https://glimmerjs.com/) | `dist` | Недоступно |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | Недоступно |
| [хиперапп](https://hyperapp.dev/) | `/` | Недоступно |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | Недоступно |
| [jQuery](https://jquery.com/) | `/` | Недоступно |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | Недоступно |
| [лителемент](https://lit-element.polymer-project.org/) | `dist` | Недоступно |
| [марко](https://markojs.com/) | `public` | Недоступно |
| [Метеор](https://www.meteor.com/) | `bundle` | Недоступно |
| [мисрил](https://mithril.js.org/) | `dist` | Недоступно |
| [Polymer](https://www.polymer-project.org/) | `build/default` | Недоступно |
| [преакт](https://preactjs.com/) | `build` | Недоступно |
| [React](https://reactjs.org/) | `build` | Недоступно |
| [Трафарета](https://stenciljs.com/) | `www` | Недоступно |
| [Svelte](https://svelte.dev/) | `public` | Недоступно |
| [Three.js](https://threejs.org/) | `/` | Недоступно |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | Недоступно |
| [Vue.js](https://vuejs.org/) | `dist` | Недоступно |

<sup>1</sup> приведенная выше таблица не является исчерпывающим списком платформ и библиотек, которые работают со статическими веб-приложениями Azure.

<sup>2</sup> неприменимо

## <a name="next-steps"></a>Дальнейшие шаги

- [Конфигурация сборки и рабочего процесса](github-actions-workflow.md)
