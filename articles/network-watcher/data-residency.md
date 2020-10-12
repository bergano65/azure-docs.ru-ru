---
title: Местонахождение данных для наблюдателя за сетями Azure | Документация Майкрософт
description: Эта статья поможет вам понять, как местонахождение данные для службы наблюдателя за сетями Azure.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 9451b6636f2f87806e3d1e39fec4e9e4d4390485
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706837"
---
# <a name="data-residency-for-azure-network-watcher"></a>Местонахождение данных для наблюдателя за сетями Azure
За исключением службы монитора подключений (Предварительная версия), наблюдатель за сетями Azure не хранит данные клиента.


## <a name="connection-monitor-preview-data-residency"></a>Монитор подключения (Предварительная версия) местонахождение данных
В службе монитора подключений (Предварительная версия) хранятся данные клиентов. Эти данные автоматически сохраняются службой "наблюдатель за сетями" в одном регионе. Итак, монитор подключения (Предварительная версия) автоматически удовлетворяет требованиям местонахождение данных в регионе, включая требования, указанные в [центре управления безопасностью](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Размещение данных в Сингапуре

В Azure функция, позволяющая хранить данные клиентов в одном регионе, в настоящее время доступна только в регионе Юго-Восточной Азии (Сингапур) Азиатско-Тихоокеанский регион Geo. Для всех других регионов данные клиента хранятся в географическом виде. Дополнительные сведения см. в [центре управления безопасностью](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Дальнейшие шаги

* Ознакомьтесь с обзором [наблюдателя за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
