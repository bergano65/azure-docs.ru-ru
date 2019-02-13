---
title: включение файла
description: включение файла
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 06651b06ae84934c16e9f1ac9f604abda8b65615
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55648621"
---
## <a name="open-cli-shell"></a>Открытие оболочки CLI

Для выполнения команд CLI мы рекомендуем использовать [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). **Cloud Shell** — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. В Cloud Shell предварительно установлены и настроены общие инструменты Azure для использования с вашей учетной записью. Она предоставляет гибкие возможности при выборе оболочки, соответствующей вашим методам работы. Пользователи Linux могут использовать Bash, а пользователи Windows — PowerShell.

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Вход

Чтобы приступить к использованию оболочки CLI (в облаке или локально), запустите `az login` для создания соединения с Azure.

Если CLI сможет запустить браузер по умолчанию, он откроет в браузере страницу входа. В противном случае вам потребуется открыть страницу браузера и выполнить инструкции в командной строке, чтобы ввести код авторизации после перехода по адресу https://aka.ms/devicelogin в браузере.

### <a name="specify-location-of-files"></a>Определение расположения файлов

Многие команды CLI Служб мультимедиа позволяют передавать параметр с именем файла. Если вы используете **Cloud Shell**, вы можете отправить файл в clouddrive (с помощью Bash или PowerShell). 

![Отправка файлов]

Независимо от того, используете ли вы локальный интерфейс командной строки или **Cloud Shell**, вам необходимо указать путь к файлу в соответствии с операционной системой или Cloud Shell (Bash или PowerShell), которую вы используете. Ниже приведено несколько примеров.

Относительный путь к файлу (все ОС)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Абсолютный путь к файлу в операционных системах Linux или Mac и Windows

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`


[Передача файлов]: ./media/media-services-cli/upload-download-files.png
