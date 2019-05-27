---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d804cb310a8638713cabf76c2f4192a0e4d0f43d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65914283"
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