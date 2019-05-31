---
title: Azure версии 2 монитор состояния подробные инструкции | Документация Майкрософт
description: Подробные инструкции по началу работы с v2 монитор состояния. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: 6eca2b47c2362f34415db8b4f335f3089babc58b
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255884"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Состояние монитора v2 подробные инструкции

Это как содержатся подробные сведения, чтобы подключить к коллекции PowerShell и загрузки модуля ApplicationMonitor. Мы задокументировали наиболее общие параметры, необходимые для начала работы.
Сюда также включены инструкции по регистрации вручную в случае, если доступ к Интернету недоступно.

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в разделе [дополнительные условия использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="instrumentation-key"></a>Ключ инструментирования

Чтобы приступить к работе, необходимо иметь ключ инструментирования. Дополнительные сведения в статье [создайте ресурс Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>Запустите PowerShell от имени администратора с помощью политики выполнение с повышенными правами

**Запуск от имени администратора**: 
- Описание: PowerShell потребуются права администратора для внесения изменений на компьютер.

**Политика выполнения**:
- Описание: По умолчанию выполнение сценариев PowerShell будет отключена. Мы рекомендуем, позволяя RemoteSigned скрипты только в текущей области.
- Справочные материалы. [О политиках выполнения](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) и [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- Необязательные параметры:
    - `-Force` Это будет пропустить запрос на подтверждение.

**Примеры ошибок:**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Предварительные требования для PowerShell

Аудит текущей версии PowerShell, выполнив команду: `$PSVersionTable`.
Команда выводит следующие результаты:


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

Эти инструкции были записаны и протестирован на компьютере Windows 10 с помощью версий, перечисленных выше.

## <a name="prerequisites-for-powershell-gallery"></a>Необходимые условия для коллекции PowerShell

Эти действия будут подготовить сервер для скачивания модулей из коллекции PowerShell.

> [!NOTE] 
> Для коллекции PowerShell поддерживается Windows 10, Windows Server 2016 и PowerShell 6. Для более старых версий Просмотрите этот документ: [Установка PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. Запустите PowerShell от имени администратора с помощью политики выполнение с повышенными правами.
2. Поставщик пакетов NuGet 
    - Описание: Этот поставщик, необходимые для взаимодействия с репозиториев на основе NuGet, например PowerShellGallery
    - Справочные материалы. [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - Необязательные параметры:
        - `-Proxy` Указывает прокси-сервер для запроса.
        - `-Force` Это будет пропустить запрос на подтверждение. 
    
    Если NuGet еще не настроена, вы получите этот запрос:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Доверенный репозиториев
    - Описание: По умолчанию в коллекции PowerShell — ненадежного репозитория.
    - Справочные материалы. [SET-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - Необязательные параметры:
        - `-Proxy` Указывает прокси-сервер для запроса.

    Вы получите это предупреждение, если коллекции PowerShell, не являющихся доверенными:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - Можно подтвердить это изменение и аудита всех PSRepositories, выполнив cmd: `Get-PSRepository`

4. Версия PowerShellGet 
    - Описание: Этот модуль содержит средства, используемые для получения других модулей из коллекции PowerShell. V1.0.0.1 поставляется с Windows 10 и Windows Server. Минимальная требуемая версия версии 1.6.0. Для аудита, какая версия установлена, выполните команду: `Get-Command -Module PowerShellGet`
    - Справочные материалы. [Установка PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd: `Install-Module -Name PowerShellGet`
    - Необязательные параметры:
        - `-Proxy` Указывает прокси-сервер для запроса.
        - `-Force` Это будет игнорировать предупреждение «установлен» и установите последнюю версию.

    Вы получите эту ошибку, если вы не используете последнюю версию PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Перезапустите PowerShell. Невозможно загрузить новую версию в текущем сеансе. Все новые сеансы Powershell будет иметь загрузить последнюю версию PowerShellGet.

## <a name="download--install-via-powershell-gallery"></a>Скачивание и установка с помощью коллекции PowerShell

Эти действия скачает Az.ApplicationMonitor модуль из коллекции PowerShell.

1. Необходимые условия для коллекции PowerShell являются обязательными.
2. Запустите PowerShell от имени администратора с помощью политики выполнение с повышенными правами.
3. Установите модуль Az.ApplicationMonitor
    - Справочные материалы. [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd: `Install-Module -Name Az.ApplicationMonitor`
    - Необязательные параметры:
        - `-Proxy` Указывает прокси-сервер для запроса.
        - `-AllowPrerelease` Таким образом, установка альфа и бета-версии.
        - `-AcceptLicense` Это позволяет пропускать «принять» лицензии
        - `-Force` Это будет игнорировать предупреждение «Ненадежного репозитория»

## <a name="download--install-manually-offline-option"></a>Загрузить и установить вручную (параметр-offline)

Если для какой-либо причине не удается подключиться к модулю PowerShell, вы можете вручную загрузить и установить модуль Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg"></a>Вручную загрузить последние nupkg

1. Перейдите к: https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Установите последнюю версию из журнала версий.
3. Найдите «Параметры установки» и выберите «Вручную».

### <a name="option-1-install-into-powershell-modules-directory"></a>Вариант 1: Установка в каталоге модулей PowerShell
Установите модуль PowerShell вручную Скачанный в каталог PowerShell, поэтому он может быть видимым в сеансах PowerShell.
Дополнительные сведения можно найти в разделе  [Установка модуля PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>Распакуйте nupkg как ZIP-файл с помощью Expand-Archive (v1.0.1.0)

- Описание: Базовая версия Microsoft.PowerShell.Archive (v1.0.1.0) не удается распаковать файлы nupkg. Переименуйте файл с расширением «ZIP».
- Справочные материалы. [Разверните архив](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd: 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>Распакуйте nupkg, с помощью Expand-Archive (v1.1.0.0).

- Описание: Чтобы распаковать доступность файлов nupkg не переименовывая расширение, используйте текущую версию Expand-Archive. 
- Справочные материалы. [Разверните архив](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) и [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>Вариант 2: Распакуйте и импорт вручную
Установите модуль PowerShell вручную Скачанный в каталог PowerShell, поэтому он может быть видимым в сеансах PowerShell.
Дополнительные сведения можно найти в разделе  [Установка модуля PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

Если установка в любом другом каталоге, необходимо вручную импортировать модуль, используя [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> При установке будут установлены библиотеки DLL с помощью относительных путей. Store содержимое этого пакета в каталоге предполагаемое время выполнения и убедитесь, что разрешения доступа позволяют чтения, но не записывает.

1. Измените расширение на «ZIP» и извлечь содержимое пакета в каталоге предполагаемого установки.
2. Найдите путь к «Az.ApplicationMonitor.psd1».
3. Запустите PowerShell от имени администратора с помощью политики выполнение с повышенными правами. 
4. Загрузка модуля с помощью команды: `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="proxy"></a>Прокси-сервер

При мониторинге компьютера к частной интрасети, он будет необходимо перенаправить трафик http через прокси-сервер.

Команды PowerShell, чтобы загрузить и установить Az.ApplicationMonitor из коллекции PowerShell поддерживают `-Proxy` параметра.
Просмотрите инструкции выше, когда создание скриптов установки.

Пакет SDK Application Insights необходимо отправлять данные телеметрии вашего приложения в корпорацию Майкрософт. Рекомендуется настроить параметры прокси-сервера для вашего приложения в файле web.config. См. в разделе [Application Insights часто задаваемые вопросы: Прокси-сервера Passthrough](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough) Дополнительные сведения.


## <a name="enable-monitoring"></a>Включение мониторинга 

Cmd: `Enable-ApplicationInsightsMonitoring`

Просмотрите наши [Справочник по API](status-monitor-v2-api-enable-monitoring.md) подробное описание того, как с помощью этого командлета. 



## <a name="next-steps"></a>Дальнейшие действия

 Просмотр телеметрии:

- [Изучите метрики](../../azure-monitor/app/metrics-explorer.md), чтобы отслеживать производительность и использование.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем
- [Аналитика](../../azure-monitor/app/analytics.md) для создания расширенных запросов.
- [Создайте панели мониторинга](../../azure-monitor/app/overview-dashboard.md)

 Добавление данных телеметрии:

- [Создание веб-тестов](monitor-web-app-availability.md) чтобы убедиться, что ваш сайт продолжает работать.
- [Добавьте телеметрию веб-клиента](../../azure-monitor/app/javascript.md) чтобы просматривать исключения в коде веб-страницы и вставлять вызовы трассировки.
- [Добавьте пакет SDK Application Insights в код](../../azure-monitor/app/asp-net.md) , можно вставить трассировки и журналов вызовов

Новые возможности в версии 2 монитор состояния:

- Используйте наше руководство по [Устранение](status-monitor-v2-troubleshoot.md) v2 монитор состояния.
