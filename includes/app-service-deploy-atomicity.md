---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: b8e3d32f0e230673ecbc043597afe8e7b5f25e06
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742418"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Что происходит с моим приложением во время развертывания?

Все официально поддерживаемые методы развертывания объединяет одно: они вносят изменения в файлы в папке `/home/site/wwwroot` приложения. Именно эти файлы выполняются в рабочей среде. Поэтому развертывание может завершиться сбоем из-за заблокированных файлов или приложение в рабочей среде может вести себя непредсказуемо во время развертывания, так как не все файлы обновляются одновременно. Избежать этих проблем можно несколькими способами.

- Остановите приложение или включите для него автономный режим на время развертывания. Дополнительные сведения см. в разделе [Dealing with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment) (Устранение проблем из-за заблокированных файлов при развертывании).
- Выполняйте развертывание в [промежуточный слот](../articles/app-service/web-sites-staged-publishing.md) с включенным [автоматическим переключением](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap). 
- Используйте [запуск из пакета](https://github.com/Azure/app-service-announcements/issues/84) вместо этого.
