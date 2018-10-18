---
title: LogDownloader — пользовательская служба принятия решений
titlesuffix: Azure Cognitive Services
description: Загрузка файлов журнала, создаваемых Пользовательской службой принятия решений Azure.
services: cognitive-services
author: marco-rossi29
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 8c5ab0e297690f1fbdb41a2627dd63c3ea522d1b
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366818"
---
# <a name="logdownloader"></a>LogDownloader

Вы можете загружать файлы журналов, которые создаются Пользовательской службой принятия решений, и создавать файлы *.gz*, используемые службой Экспериментирования.

## <a name="prerequisites"></a>Предварительные требования

- Python 3: установлен и указан в переменной path. Рекомендуется использовать 64-разрядную версию для обработки больших файлов.
- Репозиторий *Microsoft/mwt-ds*: [клонируйте репозиторий](https://github.com/Microsoft/mwt-ds).
- Пакет *azure-storage-blob*: для получения подробных сведений об установке см. [библиотеку службы хранилища Microsoft Azure для Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Введите строку подключения хранилища Azure в файле *mwt-ds/DataScience/ds.config*: следуйте шаблону *my_app_id: my_connectionString*. Можно указать несколько `app_id`. Если при запуске `LogDownloader.py` входные данные `app_id` не найдены в `ds.config`, `LogDownloader.py` использует строку подключения `$Default`.

## <a name="usage"></a>Использование

Перейдите к `mwt-ds/DataScience` и запустите `LogDownloader.py` с соответствующими аргументами, как показано в следующем коде.

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Параметры

| Входные данные | ОПИСАНИЕ | значение по умолчанию |
| --- | --- | --- |
| `-h`, `--help` | Отображение справочного сообщения и выход. | |
| `-a APP_ID`, `--app_id APP_ID` | Идентификатор приложения (имя контейнера больших двоичных объектов хранилища Azure). | Обязательно |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Базовый каталог для загрузки данных (создается вложенная папка).  | Обязательно |
| `-s START_DATE`, `--start_date START_DATE` | Дата начала загрузки (включительно) в формате *ГГГГ-ММ-ДД*. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Дата окончания загрузки (включительно) в формате *ГГГГ-ММ-ДД*. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Используемый режим перезаписи. | |
| | `0`: никогда не перезаписывать; спрашивать у пользователя, используются ли сейчас большие двоичные объекты. | значение по умолчанию | |
| | `1`: спрашивать у пользователя о дальнейших действиях, если файлы разного размера или если большие двоичные объекты используются в настоящий момент. | |
| | `2`: всегда перезаписывать; загружать используемые сейчас большие двоичные объекты. | |
| | `3`: никогда не перезаписывать и не спрашивая добавлять, если размер большой; загружать используемые сейчас большие двоичные объекты. | |
| | `4`: никогда не перезаписывать и не спрашивая добавлять, если размер большой; пропускать используемые сейчас большие двоичные объекты. | |
| `--dry_run` | Печать сведений о больших двоичных объектах, которые были бы загружены, без их загрузки. | `False` |
| `--create_gzip` | Создание файла *gzip* для Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Интервал времени в секундах для определения, используется ли сейчас файл. | `3600` с (`1` ч) |
| `--verbose` | Печать подробных сведений. | `False` |
| `-v VERSION`, `--version VERSION` | Версия средства загрузки журнала, которая будет использоваться. | |
| | `1`: для необработанных журналов (только для обеспечения обратной совместимости). | Не рекомендуется |
| | `2`: для обработанных журналов. | значение по умолчанию |

### <a name="examples"></a>Примеры

Для пробной загрузки всех данных в контейнере больших двоичных объектов хранилища Azure используйте следующий код:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Чтобы загрузить только журналы, созданные после 1 января 2018 г. в режиме `overwrite_mode=4`, используйте следующий код:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Чтобы создать файл *gzip*, включающий все загруженные файлы, используйте следующий код:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```