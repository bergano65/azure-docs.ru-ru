---
title: Информация о приложениях Azure Документы Майкрософт
description: Подробные инструкции для начала работы с агентом Application Insights. Мониторинг производительности веб-сайта без передислокации веб-сайта. Работает с ASP.NET веб-приложений, размещенных на базе, в вдовых мыслях или на Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 2a0eb83b4b056230e8e7ef103f220b216fceb9e9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537463"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Приложение Insights Agent (ранее названный Status Monitor v2): Подробные инструкции

В этой статье описывается, как на борту powerShell Галерея и скачать модуль ApplicationMonitor.
Включены наиболее распространенные параметры, которые вам нужно, чтобы начать работу.
Мы также предоставили инструкции по загрузке вручную в случае, если у вас нет доступа в Интернет.

## <a name="get-an-instrumentation-key"></a>Получить ключ приборов

Для начала, вам нужен ключ приборов. Для получения дополнительной информации [см.](create-new-resource.md#copy-the-instrumentation-key)

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Запустите PowerShell как админ с повышенной политикой исполнения

### <a name="run-as-admin"></a>Запуск в качестве админа

PowerShell нужны разрешения на уровне администратора для внесения изменений в ваш компьютер.
### <a name="execution-policy"></a>Политика выполнения
- Описание: По умолчанию, запуск скриптов PowerShell отключен. Мы рекомендуем разрешить удаленные скрипты только для текущей области.
- Справка: [О политике исполнения](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) и политике [выполнения](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Команда: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Необязательный параметр:
    - `-Force`. Обходит подсказку подтверждения.

**Пример ошибок**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Предварительные требования для PowerShell

Аудит экземпляра PowerShell, `$PSVersionTable` запустив команду.
Эта команда производит следующий вывод:


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

Эти инструкции были написаны и протестированы на компьютере под управлением Windows 10 и версиях, перечисленных выше.

## <a name="prerequisites-for-powershell-gallery"></a>Предпосылки для галереи PowerShell

Эти шаги подготовят ваш сервер к загрузке модулей из powerShell Gallery.

> [!NOTE] 
> Галерея PowerShell поддерживается на Windows 10, Windows Server 2016 и PowerShell 6.
> Для получения информации о более ранних версиях, [см.](/powershell/scripting/gallery/installing-psget)


1. Запустите PowerShell в качестве админа с повышенной политикой выполнения.
2. Установите поставщика пакетов NuGet.
    - Описание: Этот провайдер должен взаимодействовать с хранилищами на основе NuGet, такими как PowerShell Gallery.
    - Справка: [Установить-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Команда: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Дополнительные параметры:
        - `-Proxy`. Определяет прокси-сервер для запроса.
        - `-Force`. Обходит подсказку подтверждения.
    
    Вы получите этот запрос, если NuGet не настроен:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Назначай галерею PowerShell как надежное хранилище.
    - Описание: По умолчанию PowerShell Gallery является ненадежным репозиторием.
    - Справка: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Команда: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Необязательный параметр:
        - `-Proxy`. Определяет прокси-сервер для запроса.

    Вы получите этот запрос, если PowerShell Gallery не доверяют:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Вы можете подтвердить это изменение и провести аудит `Get-PSRepository` всех PSRepositories, запустив команду.

4. Установите новейшую версию PowerShellGet.
    - Описание: Этот модуль содержит инструмент, используемый для получения других модулей из powerShell Gallery. Версия 1.0.0.1 поставляется с Windows 10 и Windows Server. Требуется версия 1.6.0 или выше. Чтобы определить, какая версия `Get-Command -Module PowerShellGet` установлена, запустите команду.
    - Справка: [Установка PowerShellGet](/powershell/scripting/gallery/installing-psget).
    - Команда: `Install-Module -Name PowerShellGet`.
    - Дополнительные параметры:
        - `-Proxy`. Определяет прокси-сервер для запроса.
        - `-Force`. Обходит "уже установленное" предупреждение и устанавливает последнюю версию.

    Вы получите эту ошибку, если вы не используете новейшую версию PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Перезапустите PowerShell. Вы не можете загрузить новую версию в текущем сеансе. Новые сеансы PowerShell загрузят последнюю версию PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Скачать и установить модуль через powerShell Галерея

Эти шаги будут загружены модуль Az.ApplicationMonitor из powerShell Gallery.

1. Убедитесь, что все предпосылки для PowerShell Gallery выполнены.
2. Запустите PowerShell в качестве админа с повышенной политикой выполнения.
3. Установите модуль Az.ApplicationMonitor.
    - Справка: [Установка-модуль](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Команда: `Install-Module -Name Az.ApplicationMonitor`.
    - Дополнительные параметры:
        - `-Proxy`. Определяет прокси-сервер для запроса.
        - `-AllowPrerelease`. Позволяет устанавливать альфа- и бета-релизы.
        - `-AcceptLicense`. Обходит запрос "Принять лицензию"
        - `-Force`. Обходит предупреждение «Недоверенное репозиторий».

## <a name="download-and-install-the-module-manually-offline-option"></a>Скачать и установить модуль вручную (оффлайн вариант)

Если по какой-либо причине вы не можете подключиться к модулю PowerShell, вы можете вручную загрузить и установить модуль Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Ручная загрузка последнего файла nupkg

1. Перейдите к https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Выберите последнюю версию файла в таблице **Истории версий.**
3. В соответствии с **вариантами установки,** выберите **Руководство Скачать**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Вариант 1: Установка в каталог модулей PowerShell
Установите ручной загруженный модуль PowerShell в каталог PowerShell, чтобы он был обнаружен сеансами PowerShell.
Для получения дополнительной информации [см.](/powershell/scripting/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Unzip nupkg как почтовый файл с помощью Expand-Archive (v1.0.1.0)

- Описание: Базовая версия Microsoft.PowerShell.Archive (v1.0.1.0) не может распаковать файлы nupkg. Переименуй файл расширением .zip.
- Справка: [Расширяйте архив](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Команда:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Unzip nupkg с помощью Expand-Archive (v1.1.0.0)

- Описание: Используйте текущую версию Expand-Archive, чтобы распаковать файлы nupkg без изменения расширения.
- Справка: [Расширяйте архив](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) и [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Команда:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Вариант 2: Unzip и импорт nupkg вручную
Установите ручной загруженный модуль PowerShell в каталог PowerShell, чтобы он был обнаружен сеансами PowerShell.
Для получения дополнительной информации [см.](/powershell/scripting/developer/module/installing-a-powershell-module)

Если вы устанавливаете модуль в любой другой каталог, вручную импортируйте модуль с помощью [импорт-модуля.](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> DLLs будет устанавливаться через относительные пути.
> Храните содержимое пакета в каталоге расписания выполнения и подтвердите, что разрешения на доступ позволяют читать, но не писать.

1. Измените расширение на ".zip" и извлеките содержимое пакета в каталог установки.
2. Найдите путь файла Az.ApplicationMonitor.psd1.
3. Запустите PowerShell в качестве админа с повышенной политикой выполнения.
4. Загрузите модуль `Import-Module Az.ApplicationMonitor.psd1` с помощью команды.
    

## <a name="route-traffic-through-a-proxy"></a>Трафик маршрута через прокси

При мониторинге компьютера в частной интрасети необходимо маршрутизатировать трафик HTTP через прокси-

PowerShell команды для загрузки и установки Az.ApplicationMonitor `-Proxy` из powerShell Галерея поддержки параметра.
Просмотрите предыдущие инструкции при написании сценариев установки.

SDK-приложению необходимо будет отправить телеметрию вашего приложения в корпорацию Майкрософт. Мы рекомендуем настроить настройки прокси для вашего приложения в файле web..config. Для получения дополнительной информации, см [Приложения Исследования часто задаваемые вопросы: Прокси-проходят](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Включение мониторинга

Используйте `Enable-ApplicationInsightsMonitoring` команду для включения мониторинга.

Подробнее о том, как использовать этот cmdlet, можно ознакомиться в [справочнике API.](status-monitor-v2-api-enable-monitoring.md)



## <a name="next-steps"></a>Следующие шаги

 Просмотр телеметрии:

- [Изучите метрики](../../azure-monitor/platform/metrics-charts.md) для мониторинга производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- [Используйте Аналитику](../../azure-monitor/app/analytics.md) для более продвинутых запросов.
- [Создание панелей мониторинга.](../../azure-monitor/app/overview-dashboard.md)

 Добавление данных телеметрии:

- [Создайте веб-тесты](monitor-web-app-availability.md), чтобы убедиться, что ваш сайт продолжает работать.
- [Добавьте телеметрию веб-клиента,](../../azure-monitor/app/javascript.md) чтобы увидеть исключения из кода веб-страницы и включить отслеживание вызовов.
- [Добавьте SDK Application Insights в свой код,](../../azure-monitor/app/asp-net.md) чтобы вы могли вставлять вызовы трассировки и регистрации.

Сделайте больше с агентом По анализу приложений:

- Используйте наше руководство для [устранения неполадок](status-monitor-v2-troubleshoot.md) ВИС Агента.
