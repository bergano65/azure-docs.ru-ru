---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d804cb310a8638713cabf76c2f4192a0e4d0f43d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133212"
---
## <a name="create-a-project-zip-file"></a>Создание ZIP-файла проекта

Убедитесь, что вы по-прежнему находитесь в корневом каталоге примера проекта. Создайте ZIP-архив всего содержимого проекта. При выполнении следующей команды в окне терминала используется инструмент по умолчанию:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

В дальнейшем вы отправите этот ZIP-файл в Azure и развернете его в службе приложений.