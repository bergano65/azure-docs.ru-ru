---
title: Установка кластера Azure Service Fabric на платформе Linux в Windows | Документация Майкрософт
description: В этой статье описывается установка кластеров Service Fabric на платформе Linux на компьютерах разработки Windows. Это особенно удобно для кроссплатформенной разработки.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: e700250a6ebcdb82f99c1b460a510811d7ceb96c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719946"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Установка кластера Service Fabric на платформе Linux на компьютере разработчики Windows

В этом документе описывается настройка установка локального кластера Service Fabric на платформе Linux на компьютерах разработки Windows. Установка локального кластера Linux удобна для быстрого тестирования приложений, нацеленных на кластеры Linux, но разработанных на компьютере Windows.

## <a name="prerequisites"></a>Технические условия
Изначально кластеры Service Fabric на платформе Linux не поддерживают запуск в среде Windows. Чтобы запустить локальный кластер Service Fabric, предоставляется предварительно настроенный образ контейнера Docker. Перед началом работы вам потребуются:

* не менее 4 ГБ ОЗУ;
* последняя версия [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows);
* Docker должен работать в режиме Linux

>[!TIP]
> * Чтобы установить Docker в среде Windows, можно выполнить инструкции, приведенные в официальной [документации](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) Docker. 
> * По завершении проверьте правильность установки, выполнив инструкции, приведенные [здесь](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Создание локального контейнера и настройка Service Fabric
Чтобы настроить локальный контейнер Docker и запустить его в кластере Service Fabric, выполните следующие действия в PowerShell:


1. Обновите конфигурацию управляющей программы Docker на узле следующим образом и перезапустите управляющую программу Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Еще один способ обновления (предпочтительный): щелкните значок Docker > "Параметры" > "Управляющая программа" > "Дополнительно". Затем перезапустите управляющую программу Docker, чтобы изменения вступили в силу. 

2. В новом каталоге создайте файл с именем `Dockerfile` для создания образа Service Fabric.

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >Вы можете адаптировать этот файл для добавления программ или зависимостей в контейнер.
    >Например, добавленный элемент `RUN apt-get install nodejs -y` разрешает поддержку приложений `nodejs` в качестве гостевых исполняемых файлов.
    
    >[!TIP]
    > По умолчанию вы получите образ с последней версией Service Fabric. Сведения о конкретной версии см. на странице [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

3. Чтобы создать образ для многократного использования на основе файла `Dockerfile`, откройте терминал и с помощью команды `cd` перейдите в каталог, в котором хранится `Dockerfile`. Затем выполните следующий код:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Для выполнения этой операции потребуется некоторое время, но она выполняется только один раз.

4. Теперь при необходимости вы можете быстро запустить локальную копию Service Fabric, выполнив следующий код:

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Укажите имя экземпляра контейнера, которое будет более удобным для чтения. 
    >
    >Если приложение ожидает передачи данных через определенные порты, их необходимо указать с помощью дополнительных тегов `-p`. Например, если приложение ожидает передачи данных через порт 8080, добавьте следующий тег `-p`:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. Для запуска кластера потребуется некоторое время. Чтобы просмотреть состояние работоспособности кластера, можно перейти к панели мониторинга кластера [http://localhost:19080](http://localhost:19080) или просмотреть журналы с помощью следующей команды:

    ```powershell 
    docker logs sftestcluster
    ```

6. Выполнив шаг 5, вы можете перейти по адресу ``http://localhost:19080`` на компьютере Windows и просмотреть Service Fabric Explorer. На этом этапе можно подключиться к кластеру с помощью любого инструмента на компьютере разработки Windows и развернуть приложение, нацеленное на кластеры Service Fabric на платформе Linux. 

    > [!NOTE]
    > Подключаемый модуль Eclipse сейчас не поддерживается в Windows. 

7. Когда все будет готово, остановите работу контейнера и очистите его с помощью следующей команды:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Известные ограничения 
 
 Ниже перечислены известные ограничения для локального кластера, запущенного в контейнере для Mac: 
 
 * Служба DNS не работает и не поддерживается ([проблема № 132](https://github.com/Microsoft/service-fabric/issues/132))

## <a name="next-steps"></a>Дальнейшие действия
* Приступите к работе с [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse).
* Просмотрите другие [примеры Java](https://github.com/Azure-Samples/service-fabric-java-getting-started).


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
