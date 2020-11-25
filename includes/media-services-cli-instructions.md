---
title: включить файл
description: включить файл
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 1327ac49920af353d6adbbe592ab4189417e60e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996915"
---
## <a name="use-cli-shell"></a>Использование оболочки CLI

Для выполнения команд CLI мы рекомендуем использовать [Azure Cloud Shell](../articles/cloud-shell/overview.md?view=azure-cli-latest). **Cloud Shell** — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. В Cloud Shell предварительно установлены и настроены общие инструменты Azure для использования с вашей учетной записью. Она предоставляет гибкие возможности при выборе оболочки, соответствующей вашим методам работы. Пользователи Linux могут использовать Bash, а пользователи Windows — PowerShell.

Установить CLI также можно локально. Инструкции для своей платформы см. в статье [Установка Azure CLII](/cli/azure/install-azure-cli).

### <a name="sign-in"></a>Вход

Чтобы использовать локальный экземпляр CLI, необходимо выполнить вход в Azure. Этот шаг не является обязательным для Azure Cloud Shell. Выполните вход с помощью команды `az login`.

Если CLI сможет запустить браузер по умолчанию, он откроет в браузере страницу входа. В противном случае вам потребуется открыть страницу браузера и выполнить инструкции в командной строке, чтобы ввести код авторизации после перехода по адресу https://aka.ms/devicelogin в браузере.

### <a name="specify-location-of-files"></a>Определение расположения файлов

Многие команды CLI Служб мультимедиа позволяют передавать параметр с именем файла. Если вы используете **Cloud Shell**, вы можете отправить файл в clouddrive (с помощью Bash или PowerShell). 

![Upload files]

Независимо от того, используете ли вы локальный интерфейс командной строки или **Cloud Shell**, вам необходимо указать путь к файлу в соответствии с операционной системой или Cloud Shell (Bash или PowerShell), которую вы используете. Ниже приведено несколько примеров.

Относительный путь к файлу (все ОС)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Абсолютный путь к файлу в операционных системах Linux или Mac и Windows

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Если для команды нужно указать путь к файлу, используйте `{file}`. Например, `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Если с помощью команды будет загружаться указанный файл, используйте `@{file}`. Например, `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Передача файлов]: ./media/media-services-cli/upload-download-files.png