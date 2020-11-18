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
ms.openlocfilehash: a6be3a7fd19b43bd1b18af05d0dbfaf5053fb181
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682967"
---
# <a name="data-residency-for-azure-network-watcher"></a>Местонахождение данных для наблюдателя за сетями Azure
За исключением службы монитора подключений (Предварительная версия), наблюдатель за сетями Azure не хранит данные клиента.


## <a name="connection-monitor-preview-data-residency"></a>Монитор подключения (Предварительная версия) местонахождение данных
В службе монитора подключений (Предварительная версия) хранятся данные клиентов. Эти данные автоматически сохраняются службой "наблюдатель за сетями" в одном регионе. Итак, монитор подключения (Предварительная версия) автоматически удовлетворяет требованиям местонахождение данных в регионе, включая требования, указанные в [центре управления безопасностью](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Местонахождение данных
В Azure функция, позволяющая хранить данные клиентов в одном регионе, в настоящее время доступна только в регионе Юго-Восточной Азии (Сингапур) в регионе Азиатско-Тихоокеанский регион Geo and Южная Бразилия (Сан-Паулу State) географического региона Бразилии. Для всех других регионов данные клиента хранятся в геообъектах. Дополнительные сведения см. в [центре управления безопасностью](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с обзором [наблюдателя за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
