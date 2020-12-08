---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 6011bf90d5a97dcc027f8a9a0916c28226c5c354
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96584507"
---
## <a name="download-and-install"></a>Загрузите и установите

#### <a name="windows-install"></a>[Установка (Windows)](#tab/windowsinstall)

Вот как установить интерфейс командной строки службы "Речь" в Windows:

1. В Windows для вашей платформы необходим [распространяемый компонент Microsoft Visual C++ для Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads). При первой установке может потребоваться перезагрузка.
2. Скачайте и распакуйте [ZIP-архив](https://aka.ms/speech/spx-zips.zip) интерфейса командной строки службы "Речь".
3. Перейдите в каталог, в который вы извлекли `spx-zips`. Эта папка содержит программные файлы для интерфейса командной строки службы "Речь" на различных платформах. 
4. Извлеките файлы для своей платформы (`spx-net471` для .NET Framework 4.7 либо `spx-netcore-win-x64` для .NET Core 3.0 на ЦП с архитектурой x64). Помните, что вы будете запускать `spx` из этого каталога.

### <a name="run-the-speech-cli"></a>Запуск интерфейса командной строки службы "Речь"

1. Откройте командную строку или PowerShell, а затем перейдите в каталог, в который вы извлекли интерфейс командной строки службы "Речь".  
2. Введите `spx`, чтобы получить команды справки по интерфейсу командной строки службы "Речь".

> [!NOTE]
> При поиске команды PowerShell не проверяет локальный каталог. В PowerShell измените каталог на расположение `spx` и вызовите средство, введя `.\spx`.
> Если добавить этот каталог в путь, PowerShell и командная строка Windows обнаружат `spx` из любого каталога без префикса `.\`.

### <a name="font-limitations"></a>Ограничения для шрифтов

В Windows в интерфейсе командной строки службы "Речь" могут отображаться только шрифты, доступные для командной строки на локальном компьютере.
[Терминал Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) поддерживает все шрифты, создаваемые интерфейсом командной строки службы "Речь" в интерактивном режиме.

При выводе результатов в файл текстовый редактор, например Блокнот, или веб-браузер, например Microsoft Edge, будут также отображать все шрифты.

#### <a name="linux-install"></a>[Установка (Linux)](#tab/linuxinstall)

Вот как установить интерфейс командной строки службы "Речь" в Linux (ЦП с архитектурой x64):

1. Установите [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Скачайте и распакуйте [ZIP-архив](https://aka.ms/speech/spx-zips.zip) интерфейса командной строки службы "Речь".
3. Перейдите в извлеченный корневой каталог `spx-zips` и извлеките `spx-netcore-30-linux-x64` в новый каталог `~/spx`.
4. В окне терминала введите следующие команды.
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Введите `spx`, чтобы получить справку по интерфейсу командной строки службы "Речь".

#### <a name="docker-install-windows-linux-macos"></a>[Установка Docker (Windows, Linux, macOS)](#tab/dockerinstall)

Чтобы установить CLI службы "Речь" в контейнере Docker:

1. При необходимости <a href="https://www.docker.com/get-started" target="_blank">установите Docker Desktop<span class="docon docon-navigate-external x-hidden-focus"></span></a> для своей платформы.
2. В новой командной строке или в терминале введите следующую команду: .
   ```shell   
   docker pull msftspeech/spx
   ```
3. Введите эту команду. Вы должны увидеть справочную информацию по интерфейсу командной строки службы "Речь": .
   ```shell 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Подключение каталога в контейнере

Средство командной строки службы "Речь" сохраняет параметры конфигурации в виде файлов и загружает их при выполнении любой команды (за исключением команд справки).
При использовании интерфейса командной строки службы "Речь" в контейнере Docker необходимо подключить локальный каталог из контейнера, чтобы средство могло сохранять или находить параметры конфигурации, а также считывать или записывать любые файлы (например, звуковые файлы с записанной речью), необходимые для выполнения команды.

В Windows введите следующую команду, чтобы создать локальный каталог, который интерфейс командной строки службы "Речь" сможет использовать из контейнера:

`mkdir c:\spx-data`

В Linux или macOS введите следующую команду в терминале, чтобы создать каталог и просмотреть абсолютный путь к нему:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

При вызове интерфейса командной строки службы "Речь" используется абсолютный путь.

### <a name="run-speech-cli-in-the-container"></a>Запуск интерфейса командной строки службы "Речь" в контейнере

В этой документации показана команда `spx` интерфейса командной строки службы "Речь", используемая при установках, отличных от Docker.
При вызове команды `spx` в контейнере Docker необходимо подключить каталог в контейнере к файловой системе, в которой интерфейс командной строки службы "Речь" может хранить и находить значения конфигурации, а также считывать и записывать файлы.

Команды в Windows:

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

В Linux или macOS команды будут выглядеть как в примере ниже. Замените `ABSOLUTE_PATH` на абсолютный путь к подключенному каталогу. Этот путь был возвращен командой `pwd` в предыдущем разделе. 

Если выполнить эту команду перед настройкой ключа и региона, появится сообщение об ошибке, уведомляющее о необходимости настроить ключ и регион:
```shell   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

Чтобы использовать команду `spx`, установленную в контейнере, всегда вводите полную версию команды, показанную выше, а затем — параметры запроса.
Например, в Windows эта команда задает ключ:

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

> [!WARNING]
> При запуске CLI службы "Речь" в контейнере Docker нельзя использовать микрофон или динамик компьютера. Но можно выполнять чтение и сохранение звуковых файлов в локальном подключенном каталоге. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Создание конфигурации подписки

Чтобы начать работу с интерфейсом командной строки службы "Речь", необходимо ввести ключ подписки службы "Речь" и идентификатор региона. Чтобы получить эти учетные данные, выполните инструкции из раздела [Бесплатная пробная подписка на службу "Речь"](../overview.md#try-the-speech-service-for-free).
Указав ключ подписки и идентификатор региона (например, `eastus` и `westus`), выполните следующие команды.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Теперь проверку подлинности для подписки можно выполнять для будущих запросов SPX. Если необходимо удалить любое из этих сохраненных значений, выполните `spx config @region --clear` или `spx config @key --clear`.
