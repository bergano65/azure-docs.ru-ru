---
title: Устранение неполадок с Azure Application Insights Snapshot Debugger | Документация Майкрософт
description: В этой статье представлены шаги и сведения для разработчиков, которые возникают затруднения при включении или использовании Application Insights Snapshot Debugger устранения неполадок.
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: bf19d4f5ce60411413c21fce12f9fe9d2f391bf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783983"
---
# <a id="troubleshooting"></a> Устранение неполадок, включение Application Insights Snapshot Debugger или Просмотр моментальных снимков
Если включен отладчик моментальных снимков Application Insights для приложения, но не отображаются моментальные снимки для исключений, эти инструкции можно использовать для устранения неполадок. Может существовать много разных причин, почему моментальные снимки не создаются. Можно выполнить проверку работоспособности моментальных снимков для идентификации некоторые из возможных причин.

## <a name="use-the-snapshot-health-check"></a>Использование проверки работоспособности моментальных снимков
Из-за некоторых распространенных проблем окно "Открыть моментальный снимок отладки" может не отображаться. Например, из-за использования устаревшего сборщика моментальных снимков, достижения ежедневного лимита отправки или длительного времени передачи моментального снимка. Для устранения распространенных неполадок можно использовать проверку работоспособности моментальных снимков.

В области исключения в представлении сквозной трассировки есть ссылка, которая позволяет перейти к окну проверки работоспособности моментальных снимков.

![Введите данные для проверки работоспособности моментальных снимков](./media/snapshot-debugger/enter-snapshot-health-check.png)

Интерактивный интерфейс, подобный чату, ищет распространенные проблемы и приводит инструкции по их устранению.

![Проверка работоспособности](./media/snapshot-debugger/healthcheck.png)

Если это не устранит проблему, воспользуйтесь следующими шагами по устранению неполадок вручную.

## <a name="verify-the-instrumentation-key"></a>Проверка ключа инструментирования

Убедитесь, что в опубликованном приложении используется правильный ключ инструментирования. Как правило, ключ инструментирования считывается из файла ApplicationInsights.config. Убедитесь, что его значение такое же, что и у ключа инструментирования для ресурса Application Insights, который отображается на портале.

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Обновление пакета NuGet до последней версии

Если был включен отладчик моментальных снимков через [области на портале Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), а затем приложения должны работать последнюю версию пакета NuGet. Если был включен отладчик моментальных снимков, включив [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) пакет NuGet, диспетчер пакетов NuGet с помощью Visual Studio чтобы убедиться, что вы используете последнюю версию Microsoft.ApplicationInsights.SnapshotCollector. Заметки о выпуске можно найти на странице https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Проверьте журналы отправителя

После создания моментального снимка на диске создается файл минидампа (.dmp). Этот файл минидампа и любые связанные PDB-файлы создаются в отдельном процессе передачи в хранилище Snapshot Debugger Application Insights. После успешной передачи минидамп удаляется с диска. Файлы журнала для процесса передачи сохраняются на диске. В среде службы приложений эти журналы можно найти в `D:\Home\LogFiles`. Используйте веб-сайт управления Kudu для службы приложений, чтобы найти эти файлы журнала.

1. Откройте службу приложений на портале Azure.
2. Щелкните **Дополнительные инструменты** или выполните поиск по запросу **Kudu**.
3. Щелкните **Переход**.
4. В раскрывающемся списке **Debug console** (Консоль отладки) выберите **CMD** (Команда).
5. Щелкните **LogFiles**.

Вы увидите хотя бы один файл с именем, начинающимся с `Uploader_` или `SnapshotUploader_`, и расширением `.log`. Щелкните соответствующую пиктограмму, чтобы скачать все файлы журналов, или открыть их в браузере.
Имя файла содержит уникальный суффикс, который идентифицирует экземпляр службы приложений. Если экземпляр службы приложений размещен на нескольких компьютерах, для каждого компьютера существуют отдельные файлы журналов. Когда отправитель обнаруживает новый файл минидампа, он записывается в файл журнала. Ниже приведен пример успешного моментального снимка и отправки:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Приведенный выше пример взят из пакета NuGet Microsoft.ApplicationInsights.SnapshotCollector версии 1.2.0. В более ранних версиях процесс передачи называется `MinidumpUploader.exe`, а журнал содержит менее подробные сведения.

В предыдущем примере, ключ инструментирования — это `c12a605e73c44346a984e00000000000`. Это значение должно соответствовать ключу инструментирования для вашего приложения.
Минидамп связан с моментальным снимком с идентификатором `139e411a23934dc0b9ea08a626db16c5`. Позже этот идентификатор можно использовать для поиска связанной телеметрии исключений в аналитике Application Insights.

Отправитель проверяет наличие новых PDB-файлов примерно один раз каждые 15 минут. Ниже приведен пример:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Для приложений, которые _не_ размещаются в службе приложений, журналы отправителя находятся в той же папке, что и минидампы: `%TEMP%\Dumps\<ikey>` (где `<ikey>` — ваш ключ инструментирования).

## <a name="troubleshooting-cloud-services"></a>Устранение неполадок с облачными службами
Для ролей в облачных службах размер временной папки по умолчанию может быть слишком мал, чтобы хранить файлы минидампов. Это приводит к потере моментальных снимков.
Необходимый размер зависит от общего рабочего набора приложения и количества параллельных моментальных снимков.
Как правило, рабочий набор веб-роли 32-разрядного приложения ASP.NET может занимать от 200 МБ до 500 МБ.
Предусмотрите место хотя бы для двух параллельных моментальных снимков.
Например, если приложение использует 1 ГБ всего рабочего набора, выделите как минимум 2 ГБ дискового пространства для хранения моментальных снимков.
Выполните следующие действия, чтобы настроить роль облачной службы с выделенным локальным ресурсом для моментальных снимков.

1. Добавьте новый локальный ресурс в облачную службу, изменив файл ее определения (.csdef). В следующем примере определяется ресурс с именем `SnapshotStore` и размером 5 ГБ.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Измените код запуска роли, чтобы добавить переменную среды, указывающую на локальный ресурс `SnapshotStore`. Для рабочих ролей код необходимо добавить в метод `OnStart` нужной роли:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Для веб-ролей (ASP.NET) код необходимо добавить в метод `Application_Start` веб-приложения:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Обновите файл ApplicationInsights.config для роли, чтобы переопределить размещение временной папки, которое используется в `SnapshotCollector`.
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Переопределение папки теневой копии

При запуске сборщик Snapshot Collector пытается найти папку на диске, который подходит для выполнения процесса передачи моментальных снимков. Выбранная папка называется папкой теневой копии.

Snapshot Collector проверяет несколько известных расположений, гарантируя наличие прав на копирование двоичных файлов отправителя моментальных снимков. Используются следующие переменные среды:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Если подходящая папка не найдена, Snapshot Collector сообщает об ошибке: _Could not find a suitable shadow copy folder_ (Не удалось найти подходящую папку теневой копии).

Если копирование завершается сбоем, Snapshot Collector выдает ошибку `ShadowCopyFailed`.

Если не удается запустить отправитель, Snapshot Collector выдает ошибку `UploaderCannotStartFromShadowCopy`. Текст сообщения часто содержит исключение `System.UnauthorizedAccessException`. Эта ошибка обычно возникает из-за того, что приложение работает от имени учетной записи с ограниченными разрешениями. У учетной записи есть разрешение на запись в папку теневой копии, но нет разрешения на выполнение кода.

Так как эти ошибки обычно возникают во время запуска, за ними следует ошибка `ExceptionDuringConnect` — _Uploader failed to start_ (Не удалось запустить отправитель).

Чтобы устранить эти ошибки, вы можете указать папку теневой копии вручную с помощью параметра конфигурации `ShadowCopyFolder`. Например, в файле ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Или если вы используете файл appsettings.json с приложением .NET Core:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Поиск исключений с моментальными снимками с помощью поиска Application Insights

При создании моментального снимка вызванное исключение обозначается идентификатором моментального снимка. При передаче телеметрии исключения в Application Insights этот идентификатор моментального снимка добавляется в качестве пользовательского свойства. В колонке **поиска** Application Insights можно найти все данные телеметрии с пользовательским свойством `ai.snapshot.id`.

1. Перейдите к ресурсу Application Insights на портале Azure.
2. Щелкните **Search**(Поиск).
3. В текстовом поле поиска введите `ai.snapshot.id` и нажмите клавишу ВВОД.

![Поиск телеметрии с помощью идентификатора моментального снимка на портале](./media/snapshot-debugger/search-snapshot-portal.png)

Если этот поиск не дал результатов, значит в Application Insights не передавались моментальные снимки для приложения в выбранный диапазон времени.

Чтобы найти определенный идентификатор снимка из журналов отправителя, введите этот идентификатор в поле поиска. Если не удается найти данные телеметрии для моментального снимка, который был отправлен, выполните следующие действия:

1. Еще раз проверьте, что вы смотрите на правильный ресурс Application Insights при проверке ключа инструментирования.

2. С помощью метки времени из журнала отправителя, настройте фильтр диапазона времени поиска так, чтобы охватить этот диапазон времени.

Если исключение с таким идентификатором моментальных снимков по-прежнему не отображается, значит телеметрия исключения не отправлялась в Application Insights. Это происходит в случае сбоя приложения после снятия снимка, но до того как оно передало телеметрию исключений. В этом случае проверьте журналы службы приложений в `Diagnose and solve problems`, чтобы проверить, были ли неожиданные перезагрузки или необработанные исключения.

## <a name="edit-network-proxy-or-firewall-rules"></a>Изменение правил прокси-сервера сети или брандмауэра

Если приложение подключается к Интернету через прокси-сервер или брандмауэр, может потребоваться изменить правила, чтобы разрешить приложению взаимодействовать со службой Snapshot Debugger. Вот [список IP-адресов и портов, используемых службой Snapshot Debugger](../../azure-monitor/app/ip-addresses.md#snapshot-debugger).
