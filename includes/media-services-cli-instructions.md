---
title: включение файла
description: включение файла
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/25/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b335cf996de41f4eea15af1899a0c6654c2f679f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104989"
---
## <a name="open-cli-shell"></a>Открытие оболочки CLI

Для выполнения команд CLI мы рекомендуем использовать [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). **Cloud Shell** — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. В Cloud Shell предварительно установлены и настроены общие инструменты Azure для использования с вашей учетной записью. Нажмите кнопку "Копировать", чтобы скопировать код. Вставьте его в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить код. Cloud Shell можно открыть разными способами:

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Вход

Чтобы приступить к использованию оболочки CLI (в облаке или локально), запустите `az login` для создания соединения с Azure.

Если CLI сможет запустить браузер по умолчанию, он откроет в браузере страницу входа. В противном случае вам потребуется открыть страницу браузера и выполнить инструкции в командной строке, чтобы ввести код авторизации после перехода по адресу https://aka.ms/devicelogin в браузере.

### <a name="specify-location-of-files"></a>Определение расположения файлов

Многие команды CLI Служб мультимедиа позволяют передавать параметр с именем файла. 

Если вы используете **Azure Cloud Shell**, отправьте файл в **Azure Cloud Shell**. Вы можете найти кнопку "Отправка и скачивание файлов" в верхней части окна оболочки. После этого сошлитесь на файл следующим образом: `@{FileName}.`. 

![Отправка файлов]

Если вы используете Azure CLI локально, укажите полный путь к файлу. Например, `@c:\tracks.json`.


[Передача файлов]: ./media/media-services-cli/upload-download-files.png
