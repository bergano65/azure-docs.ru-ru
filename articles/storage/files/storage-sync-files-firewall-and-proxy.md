---
title: Локальные параметры брандмауэра и прокси-сервера для службы "Синхронизация файлов Azure" | Документация Майкрософт
description: Конфигурация локальной сети для службы "Синхронизация файлов Azure".
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f398012edc25ba6a04e230fa8049e7264f857bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294520"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Параметры брандмауэра и прокси-сервера службы "Синхронизация файлов Azure"
Служба "Синхронизация файлов Azure" подключает локальные серверы к службе файлов Azure, обеспечивая синхронизацию нескольких сайтов и распределение данных по уровням облака. Таким образом локальный сервер должен быть подключен к Интернету. Администратор отдела ИТ должен выбрать наилучший путь подключения сервера к облачным службам Azure.

В этой статье описываются особые требования и возможности, позволяющие создать безопасное подключение сервера к службе "Синхронизация файлов Azure".

## <a name="overview"></a>Обзор
Служба "Синхронизация файлов Azure" действует как служба оркестрации между Windows Server, вашим файловым ресурсом Azure и несколькими другими службами Azure, которая синхронизирует данные, как описано в группе синхронизации. Чтобы служба "Синхронизация файлов Azure" работала правильно, необходимо настроить серверы для взаимодействия со следующими службами Azure.

- Хранилище Azure
- Служба синхронизации файлов Azure
- Azure Resource Manager
- Службы аутентификации

> [!Note]  
> Агент службы "Синхронизация файлов Azure" на сервере Windows инициирует все запросы к облачным службам, поэтому в брандмауэре следует учитывать только исходящий трафик. <br /> Ни одна из служб Azure не инициирует подключение к агенту службы "Синхронизация файлов Azure".

## <a name="ports"></a>порты;
Служба "Синхронизация файлов Azure" перемещает файлы данных и метаданные только по протоколу HTTPS, и порт 443 должен быть открыт для исходящего трафика.
В результате шифруется весь трафик.

## <a name="networks-and-special-connections-to-azure"></a>Сети и специальные подключения к Azure
Агент службы "Синхронизация файлов Azure" не накладывает какие-либо требования в отношении специальных каналов, например [ExpressRoute](../../expressroute/expressroute-introduction.md), в Azure.

Служба "Синхронизация файлов Azure" будет использовать все доступные средства связи с Azure, автоматически адаптируясь к различным характеристикам сетей (пропускной способности, задержке и т. д.). Кроме того, она предоставляет административный контроль для точной настройки. Некоторые возможности сейчас недоступны. Если вы хотите настроить определенное поведение, свяжитесь с нами с помощью сайта [UserVoice для службы файлов Azure](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Прокси-сервер
Служба "Синхронизация файлов Azure" поддерживает параметры прокси-сервера для конкретных приложений и для всего компьютера.

**Параметры прокси-сервера для конкретных приложений** позволяют настроить прокси-сервер специально для трафика службы "Синхронизация файлов Azure". Параметры прокси-сервера для конкретных приложений поддерживаются в версии агента 4.0.1.0 и более поздних версиях. Их можно настроить во время установки агента или с помощью командлета PowerShell Set-StorageSyncProxyConfiguration.

Команды PowerShell для настройки параметров прокси-сервера для конкретных приложений:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Параметры прокси-сервера для компьютера** являются прозрачными для агента службы "Синхронизация файлов Azure", так как весь трафик сервера направляется через этот прокси-сервер.

Чтобы настроить параметры прокси-сервера для компьютера, выполните следующие действия: 

1. Настройте параметры прокси-сервера для приложений .NET 

   - Измените следующие два файла:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Добавьте раздел <system.net> в файлы machine.config (в раздел <system.serviceModel>).  Измените 127.0.01:8888 на IP-адрес и порт для прокси-сервера. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Задайте параметры прокси-сервера WinHTTP 

   - Выполните следующую команду в командной строке с повышенными привилегиями или PowerShell, чтобы увидеть существующие параметры прокси-сервера:   

     netsh winhttp show proxy

   - Выполните следующую команду в командной строке с повышенными привилегиями или PowerShell, чтобы задать параметры прокси-сервера (измените 127.0.01:8888 на IP-адрес и порт для прокси-сервера):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Перезапустите службу агента синхронизации хранилища, выполнив следующую команду в командной строке с повышенными привилегиями или PowerShell: 

      net stop filesyncsvc

      Примечание. Служба агента синхронизации хранилища (filesyncsvc) автоматически запустится после остановки.

## <a name="firewall"></a>Брандмауэр
Как упоминалось в предыдущем разделе, порт 443 необходимо открыть для исходящего трафика. В зависимости от политик в центре обработки данных, филиале или регионе, возможно необязательное или обязательное наложение дополнительных ограничений трафика, проходящего через этот порт в определенные домены.

В следующей таблице описаны требуемые домены для обмена данными.

| Служба | Конечная точка публичного облака | Конечная точка Azure для государственных организаций | Использование |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Любой вызов пользователя (например, PowerShell) проходит через этот URL-адрес, включая вызов для начальной регистрации сервера. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Вызовы Azure Resource Manager должны осуществляться пользователем, прошедшим аутентификацию. Чтобы вызовы могли быть выполнены, этот URL-адрес используется для аутентификации пользователей. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | В ходе развертывания службы "Синхронизация файлов Azure" в подписке Azure Active Directory будет создан субъект-служба. Для этого используется данный URL-адрес. Этот субъект используется для делегирования минимального набора прав службе "Синхронизация файлов Azure". Пользователь, выполняющий начальную настройку службы "Синхронизация файлов Azure", должен иметь привилегии владельца подписки. |
| **Хранилище Azure** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Когда сервер скачивает файл, он эффективнее перемещает данные, если напрямую обращается к файловому ресурсу Azure в учетной записи хранения. Сервер имеет ключ SAS, предоставляющий доступ только к целевому файловому ресурсу. |
| **Служба синхронизации файлов Azure** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | После начальной регистрации сервер получает URL-адрес экземпляра службы "Синхронизация файлов Azure" в этом регионе. Сервер может использовать этот URL-адрес для прямого и эффективного взаимодействия с экземпляром, выполняющим его синхронизацию. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | После установки агента службы "Синхронизация файлов Azure" с помощью URL-адреса PKI загружаются промежуточные сертификаты, обеспечивающие обмен данными между службой "Синхронизация файлов Azure" и файловым ресурсом Azure. С помощью URL-адреса OCSP проверяется состояние сертификата. |

> [!Important]
> Если разрешить передачу трафика в &ast;.one.microsoft.com, то с сервера будет передаваться не только трафик для службы синхронизации. В поддоменах могут размещаться многие другие службы Майкрософт.

Если &ast;.one.microsoft.com задает слишком много адресов, обмен данными с сервером можно ограничить, разрешив обмен данными только с региональными экземплярами службы "Синхронизация файлов Azure". Выбор экземпляров зависит от региона службы синхронизации хранилища, в котором развернут и зарегистрирован сервер. Этот регион называется "URL-адрес основной конечной точки" в таблице ниже.

В целях обеспечения непрерывности бизнес-процессов и аварийного восстановления (BCDR) вы могли разместить файловые ресурсы Azure в учетной записи глобально избыточного хранилища (GRS). Если это так, тогда в случае продолжительного регионального сбоя будет выполнена отработка отказа файловых ресурсов Azure в связанный регион. Служба "Синхронизация файлов Azure" использует те же региональные связи, что и хранилище. Так что если вы используете учетные записи хранения GRS, необходимо включить дополнительные URL-адреса, чтобы позволить серверу общаться с парной областью для Azure File Sync. В таблице ниже называется это "Парный регион". Кроме того, есть URL-адрес профиля диспетчера трафика, который также должен быть включен. Это позволяет гарантировать простое перенаправление трафика в парный регион в случае отработки отказа. Этот адрес называется "URL-адрес обнаружения" в таблице ниже.

| Cloud  | Регион | URL-адрес основной конечной точки | Парный регион | URL-адрес обнаружения |
|--------|--------|----------------------|---------------|---------------|
| Public |Восточная Австралия | https:\//kailani-aue.one.microsoft.com | Юго-Восточная часть Австралии | https:\//tm-kailani-aue.one.microsoft.com |
| Public |Юго-Восточная часть Австралии | https:\//kailani-aus.one.microsoft.com | Восточная Австралия | https:\//tm-kailani-aus.one.microsoft.com |
| Public | Южная Бразилия | https:\//brazilsouth01.afs.azure.net | Центрально-южная часть США | https:\//tm-brazilsouth01.afs.azure.net |
| Public | Центральная Канада | https:\//kailani-cac.one.microsoft.com | Восточная Канада | https:\//tm-kailani-cac.one.microsoft.com |
| Public | Восточная Канада | https:\//kailani-cae.one.microsoft.com | Центральная Канада | https:\//tm-kailani.cae.one.microsoft.com |
| Public | Центральная Индия | https:\//kailani-cin.one.microsoft.com | Южная Индия | https:\//tm-kailani-cin.one.microsoft.com |
| Public | Центральная часть США | https:\//kailani-cus.one.microsoft.com | восточная часть США 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Public | Восточная Азия | https:\//kailani11.one.microsoft.com | Юго-Восточная Азия | https:\//tm-kailani11.one.microsoft.com |
| Public | Восточная часть США | https:\//kailani1.one.microsoft.com | западная часть США | https:\//tm-kailani1.one.microsoft.com |
| Public | восточная часть США 2 | https:\//kailani-ess.one.microsoft.com | Центральная часть США | https:\//tm-kailani-ess.one.microsoft.com |
| Public | Восточная Япония | https:\//japaneast01.afs.azure.net | Западная Япония | https:\//tm-japaneast01.afs.azure.net |
| Public | Западная Япония | https:\//japanwest01.afs.azure.net | Восточная Япония | https:\//tm-japanwest01.afs.azure.net |
| Public | Республика Корея, центральный регион | https:\//koreacentral01.afs.azure.net/ | Республика Корея, южный регион | https:\//tm-koreacentral01.afs.azure.net/ |
| Public | Республика Корея, южный регион | https:\//koreasouth01.afs.azure.net/ | Республика Корея, центральный регион | https:\//tm-koreasouth01.afs.azure.net/ |
| Public | Центрально-северная часть США | https:\//northcentralus01.afs.azure.net | Центрально-южная часть США | https:\//tm-northcentralus01.afs.azure.net |
| Public | Северная Европа | https:\//kailani7.one.microsoft.com | Западная Европа | https:\//tm-kailani7.one.microsoft.com |
| Public | Центрально-южная часть США | https:\//southcentralus01.afs.azure.net | Центрально-северная часть США | https:\//tm-southcentralus01.afs.azure.net |
| Public | Южная Индия | https:\//kailani-sin.one.microsoft.com | Центральная Индия | https:\//tm-kailani-sin.one.microsoft.com |
| Public | Юго-Восточная Азия | https:\//kailani10.one.microsoft.com | Восточная Азия | https:\//tm-kailani10.one.microsoft.com |
| Public | южная часть Соединенного Королевства | https:\//kailani-uks.one.microsoft.com | западная часть Соединенного Королевства | https:\//tm-kailani-uks.one.microsoft.com |
| Public | западная часть Соединенного Королевства | https:\//kailani-ukw.one.microsoft.com | южная часть Соединенного Королевства | https:\//tm-kailani-ukw.one.microsoft.com |
| Public | центрально-западная часть США | https:\//westcentralus01.afs.azure.net | западная часть США 2 | https:\//tm-westcentralus01.afs.azure.net |
| Public | Западная Европа | https:\//kailani6.one.microsoft.com | Северная Европа | https:\//tm-kailani6.one.microsoft.com |
| Public | западная часть США | https:\//kailani.one.microsoft.com | Восточная часть США | https:\//tm-kailani.one.microsoft.com |
| Public | западная часть США 2 | https:\//westus201.afs.azure.net | центрально-западная часть США | https:\//tm-westus201.afs.azure.net |
| Государственные организации | US Gov (Аризона) | https:\//usgovarizona01.afs.azure.us | US Gov (Техас) | https:\//tm-usgovarizona01.afs.azure.us |
| Государственные организации | US Gov (Техас) | https:\//usgovtexas01.afs.azure.us | US Gov (Аризона) | https:\//tm-usgovtexas01.afs.azure.us |

- Если вы используете учетные записи хранения локально избыточного хранилища (LRS) или хранилища, избыточного в пределах зоны (ZRS), необходимо включить только URL-адрес, указанный в разделе "URL-адрес основной конечной точки".

- При использовании учетных записей хранения GRS включите три URL-адреса.

**Пример.** Вы развертываете службу синхронизации хранилища в `"West US"` и регистрируете свой сервер в ней. URL-адреса, позволяющие обмениваться данными с сервером для этого случая:

> - https:\//kailani.one.microsoft.com (основная конечная точка: Запад США)
> - https:\//kailani1.one.microsoft.com (парный сбой над регионом: Восток США)
> - https:\//tm-kailani.one.microsoft.com (открытие URL первичной области)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Разрешить список IP-адресов Azure File Sync
Azure File Sync поддерживает использование [тегов служб,](../../virtual-network/service-tags-overview.md)которые представляют собой группу префиксов IP-адреса для данной службы Azure. Можно использовать теги служб для создания правил брандмауэра, которые позволяют общаться с службой синхронизации файлов Azure. Тег обслуживания для синхронизации `StorageSyncService`файлов Azure — это .

Если вы используете Azure File Sync в Azure, вы можете использовать имя тега службы непосредственно в группе сетевой безопасности, чтобы разрешить трафик. Чтобы узнать больше о том, как это сделать, [см.](../../virtual-network/security-overview.md)

Если вы используете Azure File Sync на месте, вы можете использовать API тега обслуживания, чтобы получить определенные диапазоны IP-адресов для списка разрешений брандмауэра. Существует два способа получения этой информации:

- Текущий список диапазонов IP-адресов для всех служб Azure, поддерживающих теги служб, публикуется еженедельно в Центре загрузки Майкрософт в виде документа JSON. Каждое облако Azure имеет свой собственный документ JSON с диапазонами IP-адресов, относящихся к этому облаку:
    - [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Лазурное правительство США](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Лазурный Китай](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure для Германии](https://www.microsoft.com/download/details.aspx?id=57064)
- API обнаружения таик службы (предварительный просмотр) позволяет программное извлечение текущего списка тегов службы. В предварительном просмотре API обнаружения тегов службы может возвращать менее актуальную информацию, чем информация, полученная из документов JSON, опубликованных в Центре загрузки Майкрософт. Поверхность API можно использовать на основе предпочтений автоматизации:
    - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Лазурная силаШелл](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Лазурный CLI](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

Поскольку API обнаружения тегов службы обновляется не так часто, как документы JSON, опубликованные в Центре загрузки Майкрософт, мы рекомендуем использовать документ JSON для обновления списка разрешений брандмауэра. Это можно сделать следующим образом.

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

Затем можно использовать диапазоны IP-адресов `$ipAddressRanges` для обновления брандмауэра. Проверьте веб-сайт брандмауэра/сетевого прибора, чтобы унести информацию о том, как обновить брандмауэр.

## <a name="test-network-connectivity-to-service-endpoints"></a>Тест подключение сети к конечным точкам обслуживания
После регистрации сервера в службе синхронизации файлов Azure можно использовать cmdlet Test-StorageNetworkConnectivity и ServerRegistration.exe для тестирования связи со всеми конечными точками (URL-адресами), специфичными для этого сервера. Этот cmdlet может помочь устранить неполадки, когда неполная связь не позволяет серверу полностью работать с Azure File Sync и он может быть использован для тонкой настройки конфигураций прокси и брандмауэра.

Для запуска теста сетевого подключения установите версию агента Azure File Sync 9.1 или более позднюю версию и запустите следующие команды PowerShell:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Сводка и ограничение рисков
Списки, приведенные ранее в этом документе, содержат URL-адреса, с которыми в настоящее время взаимодействует служба "Синхронизация файлов Azure". Брандмауэры должны иметь возможность разрешить исходящий трафик для этих доменов. Корпорация Майкрософт прилагает все усилия, чтобы этот список был актуальным.

Настройка домена с ограничивающими правилами брандмауэра может служить мерой повышения безопасности. Если используются такие конфигурации брандмауэров, необходимо помнить, что URL-адреса со временем добавляются и могут измениться. Периодически просматривайте эту статью.

## <a name="next-steps"></a>Дальнейшие действия
- [Планирование развертывания синхронизации файлов Azure](storage-sync-files-planning.md)
- [Развертывание Синхронизации файлов Azure](storage-sync-files-deployment-guide.md)
- [Мониторинг службы "Синхронизация файлов Azure"](storage-sync-files-monitoring.md)
