---
title: включить файл
description: включить файл
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772439"
---
### <a name="create-an-environment-variable"></a>Создание переменной среды

С помощью ключа среды выполнения и конечной точки среды выполнения создайте переменные среды для проверки подлинности и доступа.

* `LUIS_RUNTIME_KEY` — ключ ресурса среды выполнения для проверки подлинности запросов.
* `LUIS_RUNTIME_ENDPOINT` — конечная точка среды выполнения, связанная с ключом.
* `LUIS_APP_ID` — идентификатор общедоступного приложения LUIS IoT: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production` или `staging`

Если вы планируете использовать это краткое руководство для доступа к собственному приложению, необходимо выполнить дополнительные действия:
* Создание приложения и получение идентификатора приложения
* Назначение ключа среды выполнения приложению на портале LUIS
* Проверка URL-адреса в браузере, чтобы получить доступ к приложению

Используйте инструкции для своей операционной системы.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Добавив переменные среды, перезапустите окно консоли.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

После добавления переменных среды запустите `source ~/.bashrc` из окна консоли, чтобы применить изменения.

#### <a name="macos"></a>[macOS](#tab/unix)

Измените `.bash_profile` и добавьте переменную среды:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

После добавления переменных среды запустите `source .bash_profile` из окна консоли, чтобы применить изменения.

***
