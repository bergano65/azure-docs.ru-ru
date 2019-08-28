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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 9662ebd26a263fa006c8fccf877fdc950e9014c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102961"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric для Visual Studio Code

[Расширение Reliable Services на платформе Service Fabric для VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) предоставляет инструменты, необходимые для создания, сборки и отладки приложений Service Fabric в операционных системах Windows, Linux и macOS.

В статье представлены общие сведения о требованиях и надстройках расширения, а также использовании различных команд, которые оно предоставляет. 

> [!IMPORTANT]
> Приложения Java Service Fabric могут разрабатываться на компьютерах Windows, но развернуть их можно только в кластерах Linux в Azure. Отладка приложений Java в ОС Windows не поддерживается.

## <a name="prerequisites"></a>Предварительные требования

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

## <a name="setup"></a>Установка

1. Откройте VS Code.
2. Щелкните значок "Расширения" на **панели действий** в левой части VS Code. Выполните поиск словосочетания "Service Fabric". Нажмите кнопку **Установить** для расширения Reliable Services на платформе Service Fabric.

## <a name="commands"></a>Команды
Расширение Reliable Services на платформе Service Fabric для VS Code предоставляет множество команд, которые должны помочь разработчикам создавать и развертывать проекты Service Fabric. Команды можно вызывать из **палитры команд**. Для этого нажмите `(Ctrl + Shift + p)`, введите имя команды в строке ввода и выберите нужную команду из списка подсказок. 

* Service Fabric: Создать приложение 
* Service Fabric: Публикация приложения 
* Service Fabric: Развертывание приложения 
* Service Fabric: Удалить приложение  
* Service Fabric: Сборка приложения 
* Service Fabric: Очистить приложение 

### <a name="service-fabric-create-application"></a>Service Fabric: Создать приложение

**Service Fabric: Команда создать** приложение создает новое приложение Service Fabric в текущей рабочей области. В зависимости от того, какие генераторы Yeoman установлены на компьютере разработки, возможно создание нескольких типов приложения Service Fabric, в том числе проектов Java, C#, а также контейнерных и гостевых проектов. 

1.  **Выберите Service Fabric: Добавить команду** службы
2.  Выберите тип нового приложения Service Fabric. 
3.  Введите имя приложения, которое необходимо создать.
3.  Выберите тип службы, которую необходимо добавить в приложение Service Fabric. 
4.  Следуйте инструкциям, чтобы задать имя службы. 
5.  Новое приложение Service Fabric появится в рабочей области.
6.  Откройте папку нового приложения, чтобы она стала корневой в рабочей области. Вы можете продолжить выполнение команд отсюда.

### <a name="service-fabric-add-service"></a>Service Fabric: Добавить службу
**Service Fabric: Команда добавить** службу добавляет новую службу в существующее приложение Service Fabric. Папка приложения, в которое добавляется служба, должна быть корневым каталогом рабочей области. 

1.  **Выберите Service Fabric: Добавление команды** службы.
2.  Выберите тип текущего приложения Service Fabric. 
3.  Выберите тип службы, которую необходимо добавить в приложение Service Fabric. 
4.  Следуйте инструкциям, чтобы задать имя службы. 
5.  Новая служба появится в каталоге проекта. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Публикация приложения
**Service Fabric: Команда публикации** приложения развертывает приложение Service Fabric в удаленном кластере. Целевой кластер может быть безопасным или незащищенным. Если параметры не заданы в Cloud.json, приложение развертывается в локальном кластере.

1.  При первой сборке приложения в каталоге проекта создается файл Cloud.json.
2.  Введите в файл Cloud.json значения для кластера, к которому планируется подключение.
3.  **Выберите Service Fabric: Команда публикации** приложения.
4.  Проверьте, установлено ли приложение. Для этого просмотрите целевой кластер с помощью Service Fabric Explorer. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Развертывание приложения (localhost)
**Service Fabric: Команда "** развернуть приложение" развертывает приложение Service Fabric в локальном кластере. Перед использованием команды убедитесь, что локальный кластер запущен. 

1. **Выберите Service Fabric: Команда развертывания** приложения
2. Просмотрите локальный кластер с Service Fabric Explorer (http:\//ЛОКАЛХОСТ: 19080/Explorer), чтобы убедиться, что приложение установлено. Это может занять некоторое время.
3. Можно также использовать **Service Fabric: Команда публикации** приложения без параметров, заданных в файле Cloud. JSON для развертывания в локальном кластере.

> [!NOTE]
> Развертывание приложений Java не поддерживается на компьютерах под управлением Windows.

### <a name="service-fabric-remove-application"></a>Service Fabric: Удалить приложение
**Service Fabric: Команда "** удалить приложение" удаляет Service Fabric приложение из кластера, в котором оно было ранее развернуто, с использованием расширения VS Code. 

1.  **Выберите Service Fabric: Команда удаления** приложения.
2.  Проверьте, удалено ли приложение. Для этого просмотрите кластер с помощью Service Fabric Explorer. Это может занять некоторое время.

### <a name="service-fabric-build-application"></a>Service Fabric: Сборка приложения
**Service Fabric: Команда сборки** приложения может создавать приложения Java или C# Service Fabric. 

1.  Перед выполнением команды убедитесь, что открыта корневая папка приложения. Команда определяет тип приложения (C# или Java) и компилирует его соответствующим образом.
2.  **Выберите Service Fabric: Build Application** (Service Fabric: создать приложение).
3.  Выходные данные процесса сборки пишутся во встроенный терминал.

### <a name="service-fabric-clean-application"></a>Service Fabric: Очистить приложение
**Service Fabric: Команда "** очистить приложение" удаляет все JAR-файлы и собственные библиотеки, созданные сборкой. Действует только для приложений Java. 

1.  Перед выполнением команды убедитесь, что открыта корневая папка приложения. 
2.  **Выберите Service Fabric: Команда очистки** приложения.
3.  Выходные данные процесса очистки пишутся во встроенный терминал.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения см. в разделе [Разработка и отладка приложений Service Fabric на C# с помощью VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Дополнительные сведения см. в статье [Develop Java Service Fabric applications with Visual Studio Code](./service-fabric-develop-java-applications-with-vs-code.md) (Разработка и отладка приложений Java Service Fabric в VS Code).
