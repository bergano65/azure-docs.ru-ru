---
title: Использование окна Azure Cloud Shell | Документация Майкрософт
description: Общие сведения о том, как использовать окно Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 2511f2c8fb706e232cde9ee4c02c7f8114bd3a2b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60200706"
---
# <a name="using-the-azure-cloud-shell-window"></a>Использование окна Azure Cloud Shell

В этом документе содержатся сведения о том, как использовать окно Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Переключение между средами Bash и PowerShell

Для переключения между средами Bash и PowerShell используйте селектора сред на панели инструментов Cloud Shell.  
![Выберите среду](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Перезапуск Cloud Shell
Щелкните значок перезапуска на панели инструментов Cloud Shell, чтобы сбросить состояние компьютера.  
![Перезапуск Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Перезапуск Cloud Shell сбросит состояние машины, и все файлы, не сохраненные в общей папке Azure, будут утеряны.

## <a name="change-the-text-size"></a>Изменение размера текста
Щелкните значок "Параметры" в верхнем левом углу окна, а затем наведите указатель мыши на параметр "Размер текста" и выберите нужный размер текста. Выбранные параметры будут сохранены во всех сеансах.
![Размер текста](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Изменение шрифта
Щелкните значок "Параметры" в верхней левой части окна, а затем наведите указатель мыши на параметр «Шрифт» и выберите нужный шрифт.  Выбранные параметры будут сохранены во всех сеансах.
![шрифт](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Отправка и загрузка файлов
Щелкните значок отправки или скачивания файлов в верхней левой части окна, затем выберите загрузки или передачи.  
![Отправлять или скачивать файлы](media/using-the-shell-window/uploaddownload.png)
* Для передачи файлов, используйте всплывающем окне перейдите к файлу на локальном компьютере, выберите нужный файл и нажмите кнопку «Открыть».  Файл будет передан в `/home/user` каталога.
* Для загрузки файлов, введите полный путь в всплывающем окне и нажмите кнопку «Загрузить».  
> [!NOTE] 
> Файлы и пути к файлам чувствительны к регистру в Cloud Shell. Внимательно проверьте ваши регистра в путь к файлу.

## <a name="open-another-cloud-shell-window"></a>Открыть еще одно окно Cloud Shell
Cloud Shell обеспечивает нескольких параллельных сеансов на вкладках браузера, позволяя каждый сеанс может существовать как отдельный процесс.
При выходе из сеанса необходимо выйти из всех окон, так как каждый процесс выполняется независимо, несмотря на то что они выполняются на одной машине.  
Щелкните значок открыть новый сеанс в верхнем левом углу окна. Откроется новая вкладка другим сеансам, подключенным к существующий контейнер.
![Откройте новый сеанс](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Редактор Cloud Shell
* Ссылаться на [с помощью редактора Azure Cloud Shell](using-cloud-shell-editor.md) страницы.

## <a name="web-preview"></a>Веб-Просмотр
Щелкните значок предварительного просмотра web в верхнем левом углу окна, выберите «Настройка», укажите, чтобы открыть нужный порт.  Выберите либо «открыть порт» только открыть порт, или «открыть и просмотреть» для открытия порта и предварительного просмотра порт в новой вкладке.  
![Веб-Просмотр](media/using-the-shell-window/preview.png)  
<br>
![Настройка порта](media/using-the-shell-window/preview-configure.png)  
Щелкните значок предварительного просмотра web в верхнем левом углу окна, выберите «Preview порт...» для предварительного просмотра с открытым портом в новой вкладке. Щелкните значок предварительного просмотра web в верхнем левом углу окна, выберите «Закрыть порт...» Чтобы закрыть этот порт.  
![Предварительная версия или закрыть порт](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Свертывание и развертывание окна Cloud Shell
Щелкните значок "Свернуть" в верхнем правом углу окна, чтобы скрыть окно. Для его отображения повторно щелкните значок Cloud Shell.
Щелкните значок "Развернуть", чтобы установить максимальную высоту окна. Чтобы восстановить окно до предыдущего размера, щелкните "Восстановить".  
![Свести к минимуму или полностью развернуть окно](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Копирование и вставка
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Изменение размера окна Cloud Shell
Щелкните верхний край панели и растяните или сожмите окно Cloud Shell, чтобы изменить его размер.

## <a name="scrolling-text-display"></a>Прокрутка текста
Прокручивайте текст с помощью мыши или сенсорной панели.

## <a name="exit-command"></a>Команда выхода
Команда `exit` позволяет завершить активный сеанс. Это происходит по умолчанию после 20 минут простоя.

## <a name="next-steps"></a>Дальнейшие действия

[Краткое руководство по Bash в Cloud Shell](quickstart.md) <br>
[Краткое руководство по использованию PowerShell в Azure Cloud Shell (предварительная версия)](quickstart-powershell.md)