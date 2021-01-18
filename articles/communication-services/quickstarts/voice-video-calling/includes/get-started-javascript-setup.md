---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: mikben
ms.openlocfilehash: 90bc9def9c1a78799f4931b58ad0a661602985e3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013198"
---
## <a name="setting-up"></a>Настройка

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

Откройте терминал или командное окно, создайте каталог для своего приложения и перейдите к нему.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Воспользуйтесь командой `npm init -y`, чтобы создать файл **package.json** с параметрами по умолчанию.

```console
npm init -y
```

### <a name="install-the-package"></a>Установка пакета

Используйте команду `npm install`, чтобы установить клиентскую библиотеку Служб коммуникации для реализации вызовов на JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Для работы с этим кратким руководством рекомендуется использовать следующие версии этого пакета:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

Параметр `--save` указывает библиотеку как зависимость в файле пакета **package.json**.

### <a name="set-up-the-app-framework"></a>Настройка платформы приложения

В этом кратком руководстве для объединения ресурсов приложения используется webpack. Выполните следующую команду, чтобы установить пакеты npm webpack, webpack-cli и webpack-dev-server, а также указать их в качестве зависимостей разработки в файле **package.json**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Создайте файл **index.html** в корневом каталоге проекта. Мы будем использовать этот файл для настройки базового макета, с помощью которого пользователь сможет выполнить вызов.
