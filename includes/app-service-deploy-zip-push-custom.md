---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132769"
---
## <a name="deployment-customization"></a>Настройка развертывания

При развертывании предполагается, что отправляемый ZIP-файл содержит готовое к запуску приложение. По умолчанию настройки не задаются. Чтобы включить те же процессы компиляции, что и при непрерывной интеграции, добавьте в параметры приложения следующее:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

При развертывании из отправленного ZIP-файла для этого параметра по умолчанию установлено значение **false**. Для развертываний с непрерывной интеграцией значение по умолчанию — **true**. Если установлено значение **true**, при развертывании используются связанные с ним параметры, которые вы задали. Эти параметры можно настроить как параметры приложения, или указать в файле конфигурации .deployment, который находится в корневом каталоге ZIP-файла. Дополнительные сведения см. в разделе о [параметрах, связанных с репозиторием и развертыванием,](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) справочника по развертыванию.