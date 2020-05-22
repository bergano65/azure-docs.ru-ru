---
title: Поддержка API в Статических веб-приложениях Azure благодаря Функциям Azure
description: Сведения о возможностях API, поддерживаемых Статическими веб-приложениями Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 7f86ce9158b5b07b036c785c2973e8a5883ed686
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594273"
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
- Триггеры и привязки ограничены [HTTP](../azure-functions/functions-bindings-http-webhook.md).
  - Использование всех остальных [триггеров и привязок Функций Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings), за исключением выходных привязок, ограничено.
- Журналы доступны только при добавлении [Application Insights](../azure-functions/functions-monitoring.md) в приложение решения "Функции".

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Добавление API](add-api.md)
