---
title: Настройка интерфейсных платформ с помощью предварительной версии статических веб-приложений Azure
description: Параметры для популярных интерфейсных платформ, необходимых для статических веб-приложений Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: cshoe
ms.openlocfilehash: 41f0c44ad65af5fdf2560da3f977a28f135af878
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976716"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Настройка интерфейсных платформ и библиотек с помощью предварительной версии статических веб-приложений Azure

Статические веб-приложения Azure требуют наличия соответствующих значений конфигурации в [файле конфигурации сборки](github-actions-workflow.md) для интерфейсной платформы или библиотеки.

## <a name="configuration"></a>Параметр Configuration

В следующей таблице перечислены параметры для ряда платформ и библиотек<sup>1</sup>.

Цель столбцов таблицы объясняется следующими элементами:

- **Расположение артефакта приложения**: содержит значение `app_artifact_location` , которое является [папкой для построенных версий файлов приложения](github-actions-workflow.md#build-and-deploy).

- **Настраиваемая команда сборки**: Если для платформы требуется команда, отличающаяся от `npm run build` или `npm run azure:build` , можно определить [пользовательскую команду сборки](github-actions-workflow.md#custom-build-commands).

| Инфраструктура | Расположение артефакта приложения | Настраиваемая команда сборки |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | н/д <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Угловой универсальный](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [аурелиа](https://aurelia.io/) | `dist` | н/д |
| [Backbone.js](https://backbonejs.org/) | `/` | н/д |
| [Ember](https://emberjs.com/) | `dist` | н/д |
| [флуттер](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [глиммер](https://glimmerjs.com/) | `dist` | н/д |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | н/д |
| [хиперапп](https://hyperapp.dev/) | `/` | н/д |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | н/д |
| [jQuery](https://jquery.com/) | `/` | н/д |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | н/д |
| [лителемент](https://lit-element.polymer-project.org/) | `dist` | н/д |
| [марко](https://markojs.com/) | `public` | н/д |
| [Meteor](https://www.meteor.com/) | `bundle` | н/д |
| [мисрил](https://mithril.js.org/) | `dist` | н/д |
| [Polymer](https://www.polymer-project.org/) | `build/default` | н/д |
| [преакт](https://preactjs.com/) | `build` | н/д |
| [React](https://reactjs.org/) | `build` | н/д |
| [Трафарета](https://stenciljs.com/) | `www` | н/д |
| [Svelte](https://svelte.dev/) | `public` | н/д |
| [Three.js](https://threejs.org/) | `/` | н/д |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | н/д |
| [Vue](http://vuejs.com/) | `dist` | н/д |

<sup>1</sup> приведенная выше таблица не является исчерпывающим списком платформ и библиотек, которые работают со статическими веб-приложениями Azure.

<sup>2</sup> неприменимо

## <a name="next-steps"></a>Дальнейшие шаги

- [Конфигурация сборки и рабочего процесса](github-actions-workflow.md)
