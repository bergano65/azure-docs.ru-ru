---
title: Варианты настройки CLI службы "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как создавать файлы конфигурации и управлять ими для использования с CLI службы Azure "Речь".
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: aa8551e49c8ef16984783c4e9735c987174b180a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540248"
---
# <a name="speech-cli-configuration-options"></a>Варианты настройки CLI службы Речи

Поведение CLI службы "Речь" определяется параметрами в файлах конфигурации, на которые можно ссылаться с помощью символа `@`. CLI службы "Речь" сохраняет новый параметр в новом подкаталоге `./spx/data`, который создается в текущей рабочей папке для CLI службы "Речь". При поиске значения конфигурации CLI службы "Речь" выполняет поиск в текущем рабочем каталоге, затем в хранилище данных по пути `./spx/data`, а затем в других хранилищах данных, в том числе в итоговом хранилище данных только для чтения в двоичном каталоге `spx`. 

При работе с кратким руководством по CLI службы "Речь" вы использовали хранилище данных для сохранения значений `@key` и `@region`. Поэтому вам не нужно указывать их с каждой командой `spx`. Учитывайте, что файлы конфигурации можно также использовать для хранения собственных параметров конфигурации или даже для передачи URL-адресов либо другого динамического содержимого, созданного во время выполнения.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Создание файлов конфигурации в хранилище данных и управление ими

В этом разделе показано, как использовать файл конфигурации в локальном хранилище данных для хранения и получения параметров команды с помощью `spx config`, а также сохранения выходных данных из CLI службы "Речь", применяя параметр `--output`.

В следующем примере очищается файл конфигурации `@my.defaults`, в файле добавляются пары "ключ-значение" для **key** и **region**, а также используется конфигурация при вызове `spx recognize`.

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Динамическое содержимое также можно записать в файл конфигурации. Например, следующая команда создает пользовательскую модель речи и сохраняет URL-адрес новой модели в файле конфигурации. Следующая команда ожидает, пока модель по этому URL-адресу не будет готова к использованию, прежде чем вернуться.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

В следующем примере два URL-адреса записываются в файл конфигурации `@my.datasets.txt`. В этом сценарии `--output` может включать дополнительное ключевое слово **add**, чтобы создать файл конфигурации или добавить к имеющемуся.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Для получения дополнительных сведений о файлах хранилища данных, включая использование файлов конфигурации по умолчанию (`@spx.default`, `@default.config` и `@*.default.config` для параметров по умолчанию конкретных команд), введите следующую команду:

```console
spx help advanced setup
```

## <a name="next-steps"></a>Следующие шаги 

* [Пакетные операции с использованием CLI службы "Речь"](./spx-batch-operations.md)