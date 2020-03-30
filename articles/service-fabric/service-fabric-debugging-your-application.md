---
title: Отчерепное приложение в Visual Studio
description: Повысьте надежность и производительность служб, разрабатывая их в Visual Studio и локальном кластере разработки.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: fff8a19d5643f7ce866c9eb9c57486340b6f8a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624134"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Отладка приложения Service Fabric с помощью Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio и CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse и Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Отладка локального приложения Service Fabric
Вы можете сэкономить время и деньги, развернув приложение Azure Service Fabric и выполнив его отладку в кластере для разработки, состоящем из локальных компьютеров. Visual Studio 2019 или 2015 может развернуть приложение в локальном кластере и автоматически подключить отладчика ко всем экземплярам приложения. Visual Studio должна быть запущена в качестве администратора для подключения отладчика.

1. Запустите кластер локальной разработки, выполнив действия, описанные в статье [Настройка среды разработки Service Fabric](service-fabric-get-started.md).
2. Нажмите клавишу **F5** или выберите в меню **Отладка** > **Начать отладку**.
   
    ![Запуск отладки приложения][startdebugging]
3. Задайте в коде точки останова и поэтапно выполните приложение, выбирая соответствующие команды в меню **Отладка** .
   
   > [!NOTE]
   > Visual Studio подключается ко всем экземплярам приложения. При пошаговом выполнении кода разные процессы могут одновременно достигать точек останова. Это приводит к возникновению параллельных сеансов. Чтобы этого не происходило, настройте отключение точек останова после их достижения, сделав их зависимыми от идентификатора потока, или используйте события диагностики.
   > 
   > 
4. Окно **События диагностики** открывается автоматически и позволяет просматривать данные о событиях диагностики в реальном времени.
   
    ![Просмотр событий диагностики в режиме реального времени][diagnosticevents]
5. Кроме того, вы можете открыть окно **События диагностики** вручную, используя обозреватель облака.  В разделе **Service Fabric** щелкните любой узел правой кнопкой мыши и выберите пункт **Показать потоковую передачу трассировок**.
   
    ![Окно событий диагностики][viewdiagnosticevents]
   
    Если вы хотите отфильтровать свои следы в определенной службе или приложении, включите потоков на этой конкретной службе или приложении.
6. События диагностики можно просматривать в автоматически созданном файле **ServiceEventSource.cs** и вызывать из кода приложения.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. В окне **События диагностики** поддерживаются фильтрация, приостановка и проверка событий в реальном времени.  Фильтр представляет собой простой поиск по строкам сообщений о событиях, включая их содержимое.
   
    ![Фильтрация, приостановка, возобновление и проверка событий в реальном времени][diagnosticeventsactions]
8. Отладка служб аналогична отладке приложений. Обычно вы устанавливаете Breakpoints через Visual Studio для легкой отладки. Несмотря на то, что надежные коллекции реплицируются на нескольких узлах, они все равно реализуют интерфейс IEnumerable. Эта реализация означает, что вы можете использовать просмотр результатов в Visual Studio во время отладки, чтобы увидеть, что вы хранили внутри. Для этого установите точку разрыва в любом месте кода.
   
    ![Запуск отладки приложения][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Запуск скрипта в рамках отладки
В некоторых сценариях может потребоваться запустить скрипт в рамках запуска сеанса отладки (например, при неиспользовании служб по умолчанию).

В Visual Studio можно добавить файл под названием **Start-Service.ps1** в папке **Скриптов** проекта Service Fabric Application (.sfproj). Этот скрипт будет вызываться после создания приложения в локальном кластере.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Отладка удаленного приложения Service Fabric
Если приложения Service Fabric работают в кластере Service Fabric в Azure, эти приложения можно удаленно отладить непосредственно из Visual Studio.

> [!NOTE]
> Компоненту требуется [пакет SDK 2.0 для Service Fabric](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) и [пакет SDK Azure для .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> Удаленная отладка предназначена для сценариев разработки и тестирования и не должна использоваться в производственной среде, поскольку влияет на запущенные приложения.

1. Перейдите к кластеру в **Cloud Explorer.** Нажмите правой кнопкой мыши и выберите **Отладку включить**
   
    ![Включение удаленной отладки][enableremotedebugging]
   
    Это действие даст старт процессу включения расширения удаленной отладки на кластерных узлах и необходимых конфигурациях сетей.
2. Щелкните узел кластера в **обозревателе облака** правой кнопкой мыши и выберите пункт **Подключить отладчик**.
   
    ![Подключить отладчик][attachdebugger]
3. В диалоговом окне **Подключение к процессу** выберите процесс, который нужно отладить, и нажмите кнопку **Подключить**.
   
    ![Выбор процесса][chooseprocess]
   
    Имя процесса, к которому нужно подключиться, совпадает с именем сборки для проекта службы.
   
    Отладчик подключится ко всем узлам, на которых выполняется процесс.
   
   * В случае отладки службы без состояния все экземпляры службы во всех узлах являются частью сеанса отладки.
   * Если вы отлажаете службу stateful, то только основная копия любого раздела будет активна и, следовательно, поймана отладчиком. Если во время сеанса отладки первичная реплика переносится, обработка этой реплики по-прежнему входит в сеанс отладки.
   * Чтобы поймать только соответствующие разделы или экземпляры данной службы, можно использовать условные точки разрыва, чтобы разбить только определенный раздел или экземпляр.
     
     ![Условная точка останова][conditionalbreakpoint]
     
     > [!NOTE]
     > Сейчас отладка кластера Service Fabric с несколькими экземплярами исполняемого имени одной и той же службы не поддерживается.
     > 
     > 
4. После того как отладка приложения будет завершена, расширение удаленной отладки можно отключить, щелкнув кластер в **обозревателе облака** правой кнопкой мыши и выбрав пункт **Отключить отладку**.
   
    ![Отключение удаленной отладки][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Потоковая передача трассировок из удаленного узла кластера
Вы также можете передавать следы непосредственно из удаленного кластерного узла в Visual Studio. Эта функция позволяет передавать события трассировки ETW, возникающие на узле кластера Service Fabric.

> [!NOTE]
> Компоненту требуется [пакет SDK 2.0 для Service Fabric](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) и [пакет SDK Azure для .NET 2.9](https://azure.microsoft.com/downloads/).
> Эта функция поддерживает только кластеры на платформе Azure.
> 
> 

<!-- -->
> [!WARNING]
> Потоковая передача трассировок предназначена для сценариев разработки и тестирования и не должна использоваться в производственной среде, поскольку влияет на запущенные приложения.
> В рабочем сценарии следует применять пересылку событий с помощью средств диагностики Azure.

1. Перейдите к кластеру в **Cloud Explorer.** Нажмите правой кнопкой мыши и выберите **Следы потоковой передачи enable**
   
    ![Включение трассировки удаленной потоковой передачи][enablestreamingtraces]
   
    Это действие даст старт процессу включения расширения потоковых следов на кластерных узлах, а также требуемых конфигураций сетей.
2. Разверните элемент **Узлы** в **обозревателе облака**, щелкните правой кнопкой мыши узел, потоковую передачу трассировок с которого вы хотите включить, и выберите пункт **Показать потоковую передачу трассировок**.
   
    ![Просмотр трассировки удаленной потоковой передачи][viewremotestreamingtraces]
   
    Повторите шаг 2 для всех узлов, трассировку которых вы хотите получать. Потоковая передача каждого узла будет отображаться в отдельном окне.
   
    Теперь вы можете также получать трассировки, которые выпускает Service Fabric и ваши собственные службы. Если вы хотите отфильтровать события по определенному приложению, просто введите название этого приложения в качестве фильтра.
   
    ![Просмотр трассировки потоковой передачи][viewingstreamingtraces]
3. После того как потоковая передача трассировок будет завершена, вы можете отключить удаленную потоковую передачу трассировок, щелкнув кластер в **обозревателе облака** правой кнопкой мыши и выбрав пункт **Отключить трассировки потоковой передачи**.
   
    ![Отключение трассировки удаленной потоковой передачи][disablestreamingtraces]

## <a name="next-steps"></a>Дальнейшие действия
* [Проверьте сервис Fabric.](service-fabric-testability-overview.md)
* [Управление приложениями Service Fabric в Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
