---
title: REST API управления сеансами
description: Описывает управление сеансами
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 9eb1bb87792dbc61e7c85dbc20c136499f23f67c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530218"
---
# <a name="use-the-session-management-rest-api"></a>Использование REST API управления сеансами

Чтобы использовать функции удаленной подготовки к просмотру Azure, необходимо создать *сеанс*. Каждый сеанс соответствует серверу, выделенному в Azure, к которому может подключаться клиентское устройство. Когда устройство подключается, сервер отображает запрошенные данные и передает результат в виде видеопотока. Во время создания сеанса выбирается [Тип сервера](../reference/vm-sizes.md) , на котором будет выполняться, который определяет цены. Когда сеанс больше не нужен, он должен быть остановлен. Если этот параметр не был остановлен вручную, он будет автоматически закрыт по истечении срока *аренды* сеанса.

## <a name="rest-api-reference"></a>Справочник по REST API

Ссылку на REST API можно найти [здесь](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering) и [в определениях](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)Swagger.
Мы предоставляем скрипт PowerShell в [репозитории образцов arr](https://github.com/Azure/azure-remote-rendering) в папке *Scripts* с именем *RenderingSession.ps1*, который демонстрирует использование нашей службы. Скрипт и его конфигурация описаны здесь: [примеры сценариев PowerShell](../samples/powershell-example-scripts.md).
Мы также предоставляем пакеты SDK для [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java и Python.

> [!IMPORTANT]
> Задержка является важным фактором при использовании удаленной отрисовки. Для лучшего удобства создайте сеансы в ближайшем для вас регионе. [Тест задержки Azure](https://www.azurespeed.com/Azure/Latency) можно использовать, чтобы определить, какой регион является ближайшим к вам.

> [!IMPORTANT]
> Для подключения клиентского устройства к сеансу отрисовки требуется пакет SDK среды выполнения ARR. Эти пакеты SDK доступны в [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering?view=remoterendering) и [C++](https://docs.microsoft.com/cpp/api/remote-rendering/). Кроме подключения к службе, эти пакеты SDK можно использовать для запуска и завершения сеансов.

## <a name="next-steps"></a>Дальнейшие действия

* [Использование внешних API Azure для аутентификации](frontend-apis.md)
* [Примеры сценариев PowerShell](../samples/powershell-example-scripts.md)
