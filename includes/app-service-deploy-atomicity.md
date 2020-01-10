---
title: включить файл
description: включить файл
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d9b0a7f08efcf9ea5892467b638e95d0404f35f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751780"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Что происходит с моим приложением во время развертывания?

Все официально поддерживаемые методы развертывания вносит изменения в файлы в папке `/home/site/wwwroot` приложения. Эти файлы используются для запуска приложения. Поэтому развертывание может завершиться ошибкой из-за заблокированных файлов. Приложение может также вести себя непредсказуемо во время развертывания, так как не все файлы обновляются одновременно. Это нежелательно для приложения, ориентированного на клиента. Избежать этих проблем можно несколькими способами.

- Остановите приложение или включите для него автономный режим на время развертывания. Дополнительные сведения см. в разделе Работа [с заблокированными файлами во время развертывания](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Выполняйте развертывание в [промежуточный слот](../articles/app-service/deploy-staging-slots.md) с включенным [автоматическим переключением](../articles/app-service/deploy-staging-slots.md#configure-auto-swap). 
- Используйте [Запуск из пакета](https://github.com/Azure/app-service-announcements/issues/84) вместо непрерывного развертывания.
