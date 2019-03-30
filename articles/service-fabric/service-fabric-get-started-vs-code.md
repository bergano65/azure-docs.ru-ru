---
title: Начало работы с Azure Service Fabric с VS Code | Документация Майкрософт
description: В этой статье содержатся общие сведения о создании приложений Service Fabric с помощью Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: a0405e6c699192839f72b290d0466ab5062ac584
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670141"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric для Visual Studio Code

[Расширение Reliable Services на платформе Service Fabric для VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) предоставляет инструменты, необходимые для создания, сборки и отладки приложений Service Fabric в операционных системах Windows, Linux и macOS.

В статье представлены общие сведения о требованиях и надстройках расширения, а также использовании различных команд, которые оно предоставляет. 

> [!IMPORTANT]
> Приложения Java Service Fabric могут разрабатываться на компьютерах Windows, но развернуть их можно только в кластерах Linux в Azure. Отладка приложений Java в ОС Windows не поддерживается.

## <a name="prerequisites"></a>Технические условия

Необходимо обязательно установить следующие компоненты во всех средах:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Пакет SDK для Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Генераторы Yeoman (установите соответствующие генераторы для своего приложения)

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Для разработки на Java необходимо обязательно установить следующие компоненты:

* [Пакет SDK для Java](https://aka.ms/azure-jdks) (версия 1.8)
* [Gradle](https://gradle.org/install/)
* [Отладчик для расширения Java в VS Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug). Он необходим для отладки служб Java. Отладка служб Java поддерживается только в ОС Linux. Для установки воспользуйтесь VS Code Marketplace или щелкните значок "Расширения" на **панели действий** в VS Code и выполните поиск расширения.

Для разработки .NET Core/C# необходимо установить следующие компоненты:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (версия 2.0.0 или более новая)
* [Расширение VS Code C# для Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). Необходимо для отладки служб C#. Для установки воспользуйтесь VS Code Marketplace или щелкните значок "Расширения" на **панели действий** в VS Code и выполните поиск расширения.

## <a name="setup"></a>Настройка

1. Откройте VS Code.
2. Щелкните значок "Расширения" на **панели действий** в левой части VS Code. Выполните поиск словосочетания "Service Fabric". Нажмите кнопку **Установить** для расширения Reliable Services на платформе Service Fabric.

## <a name="commands"></a>Команды
Расширение Reliable Services на платформе Service Fabric для VS Code предоставляет множество команд, которые должны помочь разработчикам создавать и развертывать проекты Service Fabric. Команды можно вызывать из **палитры команд**. Для этого нажмите `(Ctrl + Shift + p)`, введите имя команды в строке ввода и выберите нужную команду из списка подсказок. 

* Service Fabric: Создание приложения 
* Service Fabric: Публикация приложения 
* Service Fabric: Развертывание приложения 
* Service Fabric: Удалить приложение  
* Service Fabric: Создание приложения 
* Service Fabric: Очистка приложения 

### <a name="service-fabric-create-application"></a>Service Fabric: Создание приложения

**Service Fabric: Создание приложения** команда создает новое приложение Service Fabric в текущей рабочей области. В зависимости от того, какие генераторы Yeoman установлены на компьютере разработки, возможно создание нескольких типов приложения Service Fabric, в том числе проектов Java, C#, а также контейнерных и гостевых проектов. 

1.  Выберите **Service Fabric: Добавление службы** команды
2.  Выберите тип нового приложения Service Fabric. 
3.  Введите имя приложения, которое необходимо создать.
3.  Выберите тип службы, которую необходимо добавить в приложение Service Fabric. 
4.  Следуйте инструкциям, чтобы задать имя службы. 
5.  Новое приложение Service Fabric появится в рабочей области.
6.  Откройте папку нового приложения, чтобы она стала корневой в рабочей области. Вы можете продолжить выполнение команд отсюда.

### <a name="service-fabric-add-service"></a>Service Fabric: Добавить службу
**Service Fabric: Добавление службы** команда добавляет новую службу в существующее приложение Service Fabric. Папка приложения, в которое добавляется служба, должна быть корневым каталогом рабочей области. 

1.  Выберите **Service Fabric: Добавление службы** команды.
2.  Выберите тип текущего приложения Service Fabric. 
3.  Выберите тип службы, которую необходимо добавить в приложение Service Fabric. 
4.  Следуйте инструкциям, чтобы задать имя службы. 
5.  Новая служба появится в каталоге проекта. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Публикация приложения
**Service Fabric: Публикация приложения** команда развертывает приложение Service Fabric на удаленном кластере. Целевой кластер может быть безопасным или незащищенным. Если параметры не заданы в Cloud.json, приложение развертывается в локальном кластере.

1.  При первой сборке приложения в каталоге проекта создается файл Cloud.json.
2.  Введите в файл Cloud.json значения для кластера, к которому планируется подключение.
3.  Выберите **Service Fabric: Публикация приложения** команды.
4.  Проверьте, установлено ли приложение. Для этого просмотрите целевой кластер с помощью Service Fabric Explorer. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Развертывание приложения (Localhost)
**Service Fabric: Развертывание приложения** команда развертывает приложение Service Fabric в локальном кластере. Перед использованием команды убедитесь, что локальный кластер запущен. 

1. Выберите **Service Fabric: Развертывание приложения** команды
2. Просмотр локального кластера с помощью обозревателя Service Fabric (http:\//localhost:19080 / Explorer) для подтверждения того, что приложение было установлено. Это может занять некоторое время.
3. Можно также использовать **Service Fabric: Публикация приложения** команду без параметров, задать в файле Cloud.json для развертывания к локальному кластеру.

> [!NOTE]
> Развертывание приложений Java не поддерживается на компьютерах под управлением Windows.

### <a name="service-fabric-remove-application"></a>Service Fabric: Удалить приложение
**Service Fabric: Удалить приложение** команда удаляет приложение Service Fabric из кластера, что он был ранее развернут с помощью расширения VS Code. 

1.  Выберите **Service Fabric: Удалить приложение** команды.
2.  Проверьте, удалено ли приложение. Для этого просмотрите кластер с помощью Service Fabric Explorer. Это может занять некоторое время.

### <a name="service-fabric-build-application"></a>Service Fabric: Создание приложения
**Service Fabric: Удалить приложение** команды можно создать либо Java или C# приложений Service Fabric. 

1.  Перед выполнением команды убедитесь, что открыта корневая папка приложения. Команда определяет тип приложения (C# или Java) и компилирует его соответствующим образом.
2.  Выберите **Service Fabric: Build Application** (Service Fabric: создать приложение).
3.  Выходные данные процесса сборки пишутся во встроенный терминал.

### <a name="service-fabric-clean-application"></a>Service Fabric: Очистка приложения
**Service Fabric: Clean Application** команда удаляет все JAR-файлы и собственные библиотеки, которые были созданы в процессе построения. Действует только для приложений Java. 

1.  Перед выполнением команды убедитесь, что открыта корневая папка приложения. 
2.  Выберите **Service Fabric: Clean Application** команды.
3.  Выходные данные процесса очистки пишутся во встроенный терминал.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения см. в разделе [Разработка и отладка приложений Service Fabric на C# с помощью VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Дополнительные сведения см. в статье [Develop Java Service Fabric applications with Visual Studio Code](./service-fabric-develop-java-applications-with-vs-code.md) (Разработка и отладка приложений Java Service Fabric в VS Code).
