---
title: включить файл
description: включить файл
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945157"
---
## <a name="create-a-project-zip-file"></a>Создание ZIP-файла проекта

>[!NOTE]
> Если вы загрузили ZIP-файл, сначала извлеките файлы из него. Например, если вы загрузили ZIP-файл из GitHub, его нельзя развернуть "как есть". GitHub добавляет дополнительные вложенные каталоги, которые не работают со Службой приложений. 
>

В окне терминала на локальном компьютере перейдите к корневому каталогу проекта приложения. 

Этот каталог должен содержать файл записи веб-приложения, например _index.html_, _index.php_ и _app.js_. Кроме того, в нем могут содержаться файлы управления пакетами, например _project.json_, _composer.json_, _package.json_, _bower.json_ и _requirements.txt_.

Если вы не хотите, чтобы служба приложений выполняла автоматизацию развертывания, выполните все задачи сборки (например, `npm`, `bower`, `gulp`, `composer`и `pip`) и убедитесь, что у вас есть все файлы, необходимые для запуска приложения. Этот шаг является обязательным, если вы хотите [запустить пакет напрямую](../articles/app-service/deploy-run-package.md).

Создайте ZIP-архив всего содержимого проекта. При выполнении следующей команды в окне терминала используется инструмент по умолчанию:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

