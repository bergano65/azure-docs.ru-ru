---
title: Azure версии 2 монитор состояния подробные инструкции | Документация Майкрософт
description: Подробные инструкции по началу работы с v2 монитор состояния. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложений ASP.NET, размещенным на предприятиях, на виртуальных машинах или в Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: c8199c960229f9cc53cf57f9da3e1f17ebd9f5c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074158"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Состояние монитора v2: Подробные инструкции

В этой статье описывается как подключены к коллекции PowerShell и загрузки модуля ApplicationMonitor.
Он описывает наиболее распространенные параметры, которые необходимо приступить к работе.
Она также включает инструкции по регистрации вручную, в случае, если у вас нет доступа к Интернету.

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем для рабочих нагрузок. Некоторые функции могут не поддерживаться, а некоторые могут иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-an-instrumentation-key"></a>Получение ключа инструментирования

Чтобы приступить к работе, вам потребуется ключ инструментирования. Дополнительные сведения см. в разделе [создайте ресурс Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Запустите PowerShell от имени администратора с помощью политики выполнение с повышенными правами

**Запуск от имени администратора**

PowerShell требуются разрешения уровня администратора для внесения изменений на компьютер.

**Политика выполнения**
- Описание: Запуск скриптов PowerShell отключен по умолчанию. Мы рекомендуем дождаться RemoteSigned скрипты только в текущей области.
- Справочные материалы. [О политиках выполнения](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) и [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Команда: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Необязательный параметр:
    - `-Force`. Пропускает запрос на подтверждение.

**Примеры ошибок**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Предварительные требования для PowerShell

Аудит экземпляра PowerShell, выполнив `$PSVersionTable` команды.
Эта команда выводит следующие результаты:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Эти инструкции были написаны и протестированы на компьютере под управлением Windows 10 и версий, перечисленных выше.

## <a name="prerequisites-for-powershell-gallery"></a>Необходимые условия для коллекции PowerShell

Эти действия будут подготовить сервер для скачивания модулей из коллекции PowerShell.

> [!NOTE] 
> Коллекция PowerShell поддерживается в Windows 10, Windows Server 2016 и PowerShell 6.
> Сведения о более ранних версий, см. в разделе [Установка PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Запустите PowerShell от имени администратора с помощью политики выполнение с повышенными правами.
2. Установите поставщик пакетов NuGet.
    - Описание: Необходим этот поставщик для взаимодействия с помощью репозиториев на основе NuGet, например в коллекции PowerShell.
    - Справочные материалы. [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Команда: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Необязательные параметры:
        - `-Proxy`. Указывает прокси-сервер для запроса.
        - `-Force`. Пропускает запрос на подтверждение.
    
    Если NuGet еще не настроена, вы получите этот запрос:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Настройка коллекции PowerShell в качестве доверенного хранилища.
    - Описание: По умолчанию коллекция PowerShell — это ненадежного репозитория.
    - Справочные материалы. [SET-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Команда: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Необязательный параметр:
        - `-Proxy`. Указывает прокси-сервер для запроса.

    Вы получите это предупреждение, если коллекции PowerShell, не являющихся доверенными:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Вы можете подтвердить это изменение и аудита всех PSRepositories, выполнив `Get-PSRepository` команды.

4. Установите последнюю версию PowerShellGet.
    - Описание: Этот модуль содержит средства, используемые для получения других модулей из коллекции PowerShell. Версии 1.0.0.1 поставляется с Windows 10 и Windows Server. Версии 1.6.0 или более поздней версии необходим. Чтобы определить, какая версия установлена, запустите `Get-Command -Module PowerShellGet` команды.
    - Справочные материалы. [Установка PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Команда: `Install-Module -Name PowerShellGet`.
    - Необязательные параметры:
        - `-Proxy`. Указывает прокси-сервер для запроса.
        - `-Force`. Пропускает предупреждение «установлен» и устанавливает последнюю версию.

    Вы получите эту ошибку, если вы не используете последнюю версию PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Перезапустите PowerShell. Не удалось загрузить новую версию в текущем сеансе. Новые сеансы PowerShell загрузит последнюю версию PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Загрузите и установите модуль с помощью коллекции PowerShell

Эти действия скачает Az.ApplicationMonitor модуль из коллекции PowerShell.

1. Убедитесь, что выполнены все предварительные требования для коллекции PowerShell.
2. Запустите PowerShell от имени администратора с помощью политики выполнение с повышенными правами.
3. Установите модуль Az.ApplicationMonitor.
    - Справочные материалы. [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Команда: `Install-Module -Name Az.ApplicationMonitor`.
    - Необязательные параметры:
        - `-Proxy`. Указывает прокси-сервер для запроса.
        - `-AllowPrerelease`. Допускает установку альфа и бета-версии.
        - `-AcceptLicense`. Обходит «принять» лицензии
        - `-Force`. Обходит предупреждение «Ненадежного репозитория».

## <a name="download-and-install-the-module-manually-offline-option"></a>Скачайте и установите модуль вручную (параметр-offline)

Если для какой-либо причине не удается подключиться к модулю PowerShell, можно вручную загрузить и установить модуль Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Вручную загрузить последнюю версию файла nupkg

1. Перейдите на сайт https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Установите последнюю версию файла в **журнал версий** таблицы.
3. В разделе **параметры установки**выберите **вручную**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Вариант 1. Устанавливает в каталог модулей PowerShell
Устанавливает в каталог PowerShell вручную загруженный модуль PowerShell, поэтому он будет обнаружить в сеансах PowerShell.
Дополнительные сведения см. в разделе [Установка модуля PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Распакуйте nupkg в ZIP-файл с помощью Expand-Archive (v1.0.1.0)

- Описание: Базовая версия Microsoft.PowerShell.Archive (v1.0.1.0) не удается распаковать файлы nupkg. Переименуйте файл с расширением ZIP.
- Справочные материалы. [Разверните архив](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Команда:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Распакуйте nupkg с помощью Expand-Archive (v1.1.0.0)

- Описание: Чтобы распаковать файлы nupkg без изменения расширения, используйте текущую версию Expand-Archive.
- Справочные материалы. [Разверните архив](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) и [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Команда:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Вариант 2. Распакуйте и вручную импортировать nupkg
Устанавливает в каталог PowerShell вручную загруженный модуль PowerShell, поэтому он будет обнаружить в сеансах PowerShell.
Дополнительные сведения см. в разделе [Установка модуля PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).

При установке модуля в любой другой каталог, вручную импортировать модуль с помощью [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Библиотеки DLL будет установить, используя относительные пути.
> Store содержимое пакета в каталоге предполагаемое время выполнения и убедитесь, что разрешения доступа позволяют чтения, но не записывает.

1. Измените расширение на «ZIP» и извлеките его содержимое пакета в каталоге предполагаемого установки.
2. Найдите путь к файлу Az.ApplicationMonitor.psd1.
3. Запустите PowerShell от имени администратора с помощью политики выполнение с повышенными правами.
4. Загрузка модуля с помощью `Import-Module Az.ApplicationMonitor.psd1` команды.
    

## <a name="route-traffic-through-a-proxy"></a>Маршрутизация трафика через прокси-сервер

При мониторинге компьютера в частной интрасети, вам потребуется для передачи трафика HTTP через прокси-сервер.

Команды PowerShell, чтобы загрузить и установить Az.ApplicationMonitor из коллекции PowerShell поддерживают `-Proxy` параметра.
При написании сценариев установки, просмотрите предыдущие инструкции.

Пакет SDK Application Insights необходимо отправлять данные телеметрии приложения в корпорацию Майкрософт. Мы рекомендуем настроить параметры прокси-сервера для вашего приложения в файле web.config. Дополнительные сведения см. в разделе [вопросы и ответы по Application Insights: Прокси-сервера passthrough](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Включение мониторинга

Используйте `Enable-ApplicationInsightsMonitoring` команду, чтобы включить мониторинг.

См. в разделе [Справочник по API](status-monitor-v2-api-enable-monitoring.md) подробное описание того, как с помощью этого командлета.



## <a name="next-steps"></a>Дальнейшие действия

 Просмотр телеметрии:

- [Изучение метрик](../../azure-monitor/app/metrics-explorer.md) для контроля производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- [Использование аналитики](../../azure-monitor/app/analytics.md) для создания расширенных запросов.
- [Создание панелей мониторинга](../../azure-monitor/app/overview-dashboard.md).

 Добавление данных телеметрии:

- [Создание веб-тестов](monitor-web-app-availability.md) чтобы убедиться, что ваш сайт продолжает работать.
- [Добавьте телеметрию веб-клиента](../../azure-monitor/app/javascript.md) чтобы просматривать исключения в коде веб-страницы и включить вызовы трассировки.
- [Добавьте пакет SDK Application Insights в код](../../azure-monitor/app/asp-net.md) чтобы можно было вставить трассировки и журналов вызовов.

Новые возможности в версии 2 монитор состояния:

- Используйте наше руководство по [Устранение](status-monitor-v2-troubleshoot.md) v2 монитор состояния.
