---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: abfb4f6ba9452581811db1f462089cbafc771266
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544257"
---
## <a name="prerequisites"></a>Предварительные требования

Единственным предварительным требованием является наличие подписки службы "Речь Azure". Если у вас ее нет, см. руководство по [созданию новой подписки](../get-started.md#new-resource).

## <a name="download-and-install"></a>Загрузите и установите

#### <a name="windows-install"></a>[Установка (Windows)](#tab/windowsinstall)

Вот как установить интерфейс командной строки службы "Речь" в Windows:

1. Установите [.NET Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) или [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Скачайте и распакуйте [ZIP-архив](https://aka.ms/speech/spx-zips.zip) интерфейса командной строки службы "Речь".
3. Перейдите в извлеченный корневой каталог `spx-zips` и извлеките необходимый подкаталог (`spx-net471` для .NET Framework 4.7 или `spx-netcore-win-x64` для .NET Core 3.0 на ЦП с архитектурой x64).

В командной строке измените каталог на это расположение, а затем введите `spx`, чтобы открыть справку по интерфейсу командной строки службы "Речь".

> [!NOTE]
> В Windows в интерфейсе командной строки службы "Речь" могут отображаться только шрифты, доступные для командной строки на локальном компьютере.
> [Терминал Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) поддерживает все шрифты, создаваемые интерфейсом командной строки службы "Речь" в интерактивном режиме.
> При выводе результатов в файл текстовый редактор, например Блокнот, или веб-браузер, например Microsoft Edge, будут также отображать все шрифты.

> [!NOTE]
> При поиске команды PowerShell не проверяет локальный каталог. В PowerShell измените каталог на расположение `spx` и вызовите средство, введя `.\spx`.
> Если добавить этот каталог в путь, PowerShell и командная строка Windows обнаружат `spx` из любого каталога без префикса `.\`.

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

***

## <a name="create-subscription-config"></a>Создание конфигурации подписки

Чтобы начать работу с интерфейсом командной строки службы "Речь", сначала необходимо ввести ключ подписки службы "Речь" и сведения о регионе. Идентификатор региона можно узнать в разделе о [поддержке регионов](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk). Указав ключ подписки и идентификатор региона (например, `eastus` и `westus`), выполните следующие команды.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Теперь проверку подлинности для подписки можно выполнять для будущих запросов SPX. Если необходимо удалить любое из этих сохраненных значений, выполните `spx config @region --clear` или `spx config @key --clear`.
