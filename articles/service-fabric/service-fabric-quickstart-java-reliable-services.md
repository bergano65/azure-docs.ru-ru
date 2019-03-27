---
title: Создание приложения Java в Service Fabric в Azure | Документы Майкрософт
description: В рамках этого краткого руководства вы создадите приложение Java для Azure, используя пример приложения надежных служб Service Fabric.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: ddd56b8479678b288424dd896baadea6a41a2aef
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008140"
---
# <a name="quickstart-deploy-a-java-reliable-services-application-to-service-fabric"></a>Краткое руководство. Развертывание приложения надежных служб Java в Service Fabric

Azure Service Fabric — это платформа распределенных систем для развертывания микрослужб и контейнеров и управления ими.

В этом кратком руководстве показано, как развернуть первое приложение Java в Service Fabric с использованием интегрированной среды разработки Eclipse на компьютере разработчика Linux. После завершения этого руководства вы получите приложение для голосования с клиентской частью в виде веб-приложения Java, которое сохраняет результаты голосования во внутренней службе с отслеживанием состояния в кластере.

![Снимок экрана приложения](./media/service-fabric-quickstart-java/votingapp.png)

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:

* Использование Eclipse в качестве средства для приложений Java Service Fabric.
* Развертывание приложения в локальном кластере.
* Масштабирование приложения на несколько узлов

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее:

1. [Установите пакет SDK и интерфейс командной строки Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods).
2. [установите Git](https://git-scm.com/);
3. [Установите Eclipse](https://www.eclipse.org/downloads/).
4. [Настройте среду Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development), следуя дополнительным шагам по установке подключаемого модуля Eclipse.

## <a name="download-the-sample"></a>Скачивание примера приложения

В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Локальный запуск приложения

1. Запустите локальный кластер, выполнив следующую команду:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Запуск локального кластера занимает некоторое время. Чтобы убедиться, что кластер является рабочим, откройте Service Fabric Explorer по адресу **http://localhost:19080**. Наличие пяти работоспособных узлов означает, что локальный кластер запущен и работает.

    ![Работоспособное состояние локального кластера](./media/service-fabric-quickstart-java/localclusterup.png)

2. Откройте Eclipse.
3. Щелкните "Файл" -> "Импорт" -> "Gradle", выберите существующий проект Gradle и следуйте указаниям мастера.
4. Щелкните каталог и выберите каталог `Voting` из папки `service-fabric-java-quickstart`, клонированной из GitHub. Нажмите кнопку Готово.

    ![Диалоговое окно Eclipse "Import" (Импорт)](./media/service-fabric-quickstart-java/eclipseimport.png)

5. Теперь у вас имеется проект `Voting` в обозревателе пакетов для Eclipse.
6. Щелкните правой кнопкой мыши проект и выберите **Publish Application...** (Опубликовать приложение...) в раскрывающемся списке **Service Fabric**. Выберите **PublishProfiles/Local.json** в качестве целевого профиля и нажмите кнопку Publish (Опубликовать).

    ![Диалоговое окно публикации локально](./media/service-fabric-quickstart-java/localjson.png)

7. Откройте любой веб-браузер и перейдите к приложению по адресу `http://localhost:8080`.

    ![Локальная клиентская часть приложения](./media/service-fabric-quickstart-java/runninglocally.png)

Теперь можно добавить варианты для выбора в голосовании и начать прием голосов. Приложение запускается и хранит все данные в кластере Service Fabric без необходимости использования отдельной базы данных.

## <a name="scale-applications-and-services-in-a-cluster"></a>Масштабирование приложений и служб в кластере

Службы могут легко масштабироваться в кластере с учетом изменения нагрузки на службы. Масштабирование службы осуществляется путем изменения числа экземпляров, запущенных в кластере. Существует множество способов масштабирования служб. Например, можно использовать скрипты или команды интерфейса командной строки Service Fabric (sfctl). В следующих шагах используется Service Fabric Explorer.

Средство Service Fabric Explorer работает во всех кластерах Service Fabric. Чтобы его открыть, укажите адрес кластера и порт управления HTTP (19080) для кластера в адресной строке браузера, например `http://localhost:19080`.

Для масштабирования службы веб-интерфейса сделайте следующее:

1. Откройте Service Fabric Explorer в своем кластере (например, по ссылке `https://localhost:19080`).
2. Щелкните многоточие рядом с узлом **fabric:/Voting/VotingWeb** в дереве и выберите **Масштабировать службу**.

    ![Масштабирование службы в Service Fabric Explorer](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    Теперь вы можете изменить количество экземпляров службы веб-интерфейса.

3. Измените количество на **2** и нажмите кнопку **Масштабировать службу**.
4. Щелкните узел **fabric:/Voting/VotingWeb** в дереве и разверните узел раздела (он отображается в виде идентификатора GUID).

    ![Масштабирование службы в Service Fabric Explorer завершено](./media/service-fabric-quickstart-java/servicescaled.png)

    Теперь вы видите, что у службы есть два экземпляра, а с помощью дерева вы можете определить узлы, на которых запущены эти экземпляры.

С помощью этой простой задачи управления вы удвоили количество ресурсов для обработки пользовательской нагрузки для службы веб-интерфейса. Важно понимать, что для надежной работы службы не требуется запускать несколько экземпляров службы. При сбое в работе службы Service Fabric запускает новый экземпляр службы в кластере.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие действия:

* Использование Eclipse в качестве средства для приложений Java Service Fabric.
* Развертывание приложений Java в локальном кластере.
* Масштабирование приложения на несколько узлов

Дополнительные сведения о работе с приложениями Java в Service Fabric см. в руководстве по приложениям Java.

> [!div class="nextstepaction"]
> [Развертывание приложения Java](./service-fabric-tutorial-create-java-app.md)
