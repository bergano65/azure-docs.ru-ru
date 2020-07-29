---
title: Поддержка API в Статических веб-приложениях Azure благодаря Функциям Azure
description: Сведения о возможностях API, поддерживаемых Статическими веб-приложениями Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: f5f40a615bc5faab6265f42d0728403e2735aa0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791628"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Поддержка API в предварительной версии службы "Статические веб-приложения Azure" благодаря Функциям Azure

Служба "Статические веб-приложения Azure" предоставляет бессерверные конечные точки API с помощью [Функций Azure](../azure-functions/functions-overview.md). Используя решение "Функции Azure", интерфейсы API динамически масштабируются в соответствии со спросом. При этом они предоставляют следующие возможности.

- **Встроенная безопасность** с прямым доступом к данным пользователя о [проверке подлинности и авторизации на основе ролей](user-information.md).
- **Простая маршрутизация**, обеспечивающая безопасный доступ к маршруту _api_ для веб-приложения без необходимости использования пользовательских правил CORS.
- **Функции Azure** версии 3, совместимые с Node.js 12.
- **Триггеры HTTP** и выходные привязки.

## <a name="configuration"></a>Конфигурация

Конечные точки API доступны для веб-приложения через маршрут _api_. Пока этот маршрут является фиксированным, вы можете контролировать папку, в которой находится связанное приложение решения "Функции Azure". Это расположение можно изменить, [отредактировав YAML-файл рабочего процесса](github-actions-workflow.md#build-and-deploy), расположенного в папке _.github/workflows_ репозитория.

## <a name="constraints"></a>Ограничения

Служба "Статические веб-приложения Azure" предоставляет API через решение "Функции Azure". Возможности Функций Azure ориентированы на конкретный набор функций, позволяющих создать API для веб-приложения и обеспечить безопасное подключение веб-приложения к API. Эти возможности предоставляются с некоторыми ограничениями, в том числе следующими.

- Префикс маршрута API должен быть _api_.
- Приложение-функция API должно быть написано на языке JavaScript.
- Правила маршрутов для функций API поддерживают только [Перенаправление](routes.md#redirects) и [защиту маршрутов с помощью ролей](routes.md#securing-routes-with-roles).
- Триггеры и привязки ограничены [HTTP](../azure-functions/functions-bindings-http-webhook.md).
  - Использование всех остальных [триггеров и привязок Функций Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings), за исключением выходных привязок, ограничено.
- Журналы доступны только при добавлении [Application Insights](../azure-functions/functions-monitoring.md) в приложение решения "Функции".

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Добавление API](add-api.md)
