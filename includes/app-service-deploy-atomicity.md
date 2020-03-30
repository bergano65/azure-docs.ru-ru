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
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75945146"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Что происходит с моим приложением во время развертывания?

Все официально поддерживаемые методы развертывания вносят `/home/site/wwwroot` изменения в файлы в папке приложения. Эти файлы используются для запуска приложения. Таким образом, развертывание может вывести из строя из-за заблокированных файлов. Приложение также может вести себя непредсказуемо во время развертывания, потому что не все файлы обновляются одновременно. Это нежелательно для приложения, ориентированного на клиента. Избежать этих проблем можно несколькими способами.

- [Выполнить приложение из пакета «ЗИП» непосредственно](../articles/app-service/deploy-run-package.md) без его распаковки.
- Остановите приложение или включите для него автономный режим на время развертывания. Для получения дополнительной информации смотрите [сделку с заблокированными файлами во время развертывания.](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)
- Выполняйте развертывание в [промежуточный слот](../articles/app-service/deploy-staging-slots.md) с включенным [автоматическим переключением](../articles/app-service/deploy-staging-slots.md#configure-auto-swap). 
