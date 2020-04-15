---
title: Миграция из v1 в v2 - Azure Application Gateway
description: В этой статье показано, как перенести azure Application Gateway и веб-приложение Firewall из v1 в v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 2a6165cf2739482805d712ddffb5c6a9f5ebabf8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312047"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Перенести Azure Приложение шлюз и веб-приложений брандмауэра от v1 до v2

[Azure Application Gateway и Web Application Firewall (WAF) v2](application-gateway-autoscaling-zone-redundant.md) теперь доступны, предлагая дополнительные функции, такие как автоматическое масштабирование и избыточность зоны доступности. При этом существующие шлюзы версии 1 автоматически не обновляются до версии 2. Если вы хотите перейти от v1 к v2, выполните последующие действия в этой статье.

Миграция существует в два этапа:

1. Перенести конфигурацию
2. Миграция клиентского трафика

Эта статья охватывает миграцию конфигурации. Миграция трафика клиентов варьируется в зависимости от вашей конкретной среды. Тем не менее, некоторые высокого уровня, общие рекомендации [предоставляются](#migrate-client-traffic).

## <a name="migration-overview"></a>Общие сведения о переносе

Доступен скрипт Azure PowerShell, который делает следующее:

* Создает новый Standard_v2 или WAF_v2 шлюз в виртуальной сетевой подсети, которую вы указываете.
* Бесшовно копирует конфигурацию, связанную с шлюзом v1 Standard или WAF, на вновь созданный Standard_V2 или WAF_V2 шлюз.

### <a name="caveatslimitations"></a>Ограничения оговорок

* Новый шлюз v2 имеет новые публичные и частные IP-адреса. Невозможно беспрепятственно перемещать IP-адреса, связанные с существующим шлюзом v1, на v2. Тем не менее, вы можете выделить существующий (нераспределенный) публичный или частный IP-адрес на новый шлюз v2.
* Вы должны предоставить пространство IP-адреса для другой подсети в вашей виртуальной сети, где находится ваш шлюз v1. Скрипт не может создать шлюз v2 в любых существующих подсетях, которые уже имеют шлюз v1. Однако, если существующая подсеть уже имеет шлюз v2, это все равно может работать при условии, что достаточно места для IP-адресов.
* Чтобы перенести конфигурацию TLS/SSL, необходимо указать все сертификаты TLS/SSL, используемые в шлюзе v1.
* Если для шлюза V1 включен режим FIPS, он не будет перенесен на новый шлюз v2. Режим FIPS не поддерживается в v2.
* v2 не поддерживает IPv6, поэтому шлюзы v1 с включенными IPv6 не мигрируют. Если вы запустите сценарий, он может не завершить.
* Если шлюз v1 имеет только частный IP-адрес, скрипт создает общедоступный IP-адрес и частный IP-адрес для нового шлюза v2. v2 шлюзы в настоящее время не поддерживают только частные IP-адреса.
* Заголовки с именами, содержащими что-либо, кроме букв, цифр, дефисов и подчеркнуто, не передаются в ваше приложение. Это относится только к заголовку имен, а не значения заголовка. Это нарушение изменения от v1.

## <a name="download-the-script"></a>Скачать сценарий

Скачать скрипт миграции из [галереи PowerShell](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Использование сценария

Есть два варианта для вас в зависимости от локальной настройки среды PowerShell и предпочтений:

* Если у вас нет установленных модулей Azure Az или нет ничего плохого в установке модулей Azure Az, лучшим вариантом является использование `Install-Script` опции для запуска скрипта.
* Если вам необходимо сохранить модули Azure Az, лучше всего загрузить скрипт и запустить его напрямую.

Чтобы определить, установлены ли модули `Get-InstalledModule -Name az`Azure Az, запустите. Если вы не видите `Install-Script` установленных модулей Az, то вы можете использовать метод.

### <a name="install-using-the-install-script-method"></a>Установка с помощью метода установки-скрипта

Для использования этой опции на компьютер не должны быть установлены модули Azure Az. Если они установлены, следующая команда отображает ошибку. Вы можете удалить модули Azure Az, либо использовать другой вариант для загрузки скрипта вручную и запуска его.
  
Выполните скрипт с помощью следующей команды:

`Install-Script -Name AzureAppGWMigration`

Эта команда также устанавливает необходимые модули Az.  

### <a name="install-using-the-script-directly"></a>Установка с помощью сценария напрямую

Если у вас установлены некоторые модули Azure Az, которые не могут их удалить (или не хотите их унифицировать), вы можете вручную загрузить скрипт с помощью вкладки **Ручной загрузки** в ссылке загрузки скрипта. Скрипт загружается в виде необработанного файла nupkg. Чтобы установить скрипт из этого файла nupkg, [см.](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)

Выполните следующее, чтобы запустить этот сценарий.

1. Используйте `Connect-AzAccount` для подключения к Azure.

1. Используйте `Import-Module Az` для импорта модулей Az.

1. Запуск `Get-Help AzureAppGWMigration.ps1` для изучения необходимых параметров:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Параметры скрипта:
   * **resourceId: «String»: Требуется** - это идентификатор ресурсов Azure для существующего шлюза Standard v1 или WAF v1. Чтобы найти это значение строки, перейдите на портал Azure, выберите шлюз приложения или ресурс WAF и нажмите ссылку **Свойства** для шлюза. Идентификатор ресурса находится на этой странице.

     Вы также можете запустить следующие команды Azure PowerShell, чтобы получить идентификатор ресурса:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: «String»: Требуется** - Это пространство IP-адреса, которое вы выделили (или хотите выделить) для новой подсети, содержащей ваш новый шлюз v2. Это должно быть указано в нотации CIDR. Например: 10.0.0.0/24. Вам не нужно создавать эту подсеть заранее. Скрипт создает его для вас, если он не существует.
   * **appgwName: «String»: Необязательно**. Это строка, указанная для использования в качестве имени для нового Standard_v2 или WAF_v2 шлюза. Если этот параметр не поставляется, имя существующего шлюза v1 будет использоваться с суффиксом *_v2.*
   * **sslCertificates: «PSApplicationGatewaySslCertificate»: Необязательно**.  Список объектов PSApplicationGatewaySlCertificate, разделенных на запятой, которые вы создаете для представления сертификатов TLS/SSL с вашего шлюза v1, должен быть загружен на новый шлюз v2. Для каждого из ваших сертификатов TLS/SSL, настроенных для вашего шлюза Standard v1 или WAF v1, вы можете создать новый объект PSApplicationGatewaySslCertificate с помощью `New-AzApplicationGatewaySslCertificate` команды, показанной здесь. Вам нужен путь к файлу TLS/SSL Cert и паролю.

     Этот параметр неявляется только в том случае, если у вас нет httpS слушателей, настроенных для вашего шлюза v1 или WAF. Если у вас есть хотя бы одна настройка слушателя HTTPS, необходимо указать этот параметр.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     В предыдущем `$mySslCert1, $mySslCert2` примере можно передать (запятое выделение) в качестве значений для этого параметра в скрипте.
   * **доверенныеRootCertificates: «PSApplicationGatewayTrustedRootCertificate»: Необязательно**. Запятая разделенный список объектов PSApplicationGatewayTrustedRootCertificate, которые вы создаете для представления [сертификатов Trusted Root](ssl-overview.md) для проверки подлинности ваших бэкэнд-экземпляров с вашего шлюза v2.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Чтобы создать список объектов PSApplicationGatewayTrustedRootCertificate, смотрите [New-AzApplicationGatewayTrustedRootCertificate.](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)
   * **privateipAddress: «String»: Необязательно**. Конкретный частный IP-адрес, который вы хотите связать с вашим новым шлюзом v2.  Это должно быть из того же VNet, что вы выделяете для вашего нового шлюза v2. Если это не указано, скрипт выделяет частный IP-адрес для вашего шлюза v2.
   * **publicIpResourceId: «String»: Необязательно**. РесурсИсид существующего общедоступного IP-адреса (стандарт SKU) ресурса в подписке, который вы хотите выделить на новый шлюз v2. Если это не указано, скрипт выделяет новый общедоступный IP в той же группе ресурсов. Название — название шлюза v2 с придативным *IP- IP.*
   * **проверитьМиграцию: «переключатель»: Необязательно**. Используйте этот параметр, если вы хотите, чтобы скрипт сделал некоторые основные проверки сравнения конфигурации после создания шлюза v2 и копии конфигурации. По умолчанию проверка не проводится.
   * **enableAutoScale: «переключатель»: Необязательно**. Используйте этот параметр, если требуется, чтобы скрипт включить AutoScaling на новом шлюзе v2 после его создания. По умолчанию AutoScaling отключен. Вы всегда можете вручную включить его позже на вновь созданный шлюз v2.

1. Выполнить скрипт с использованием соответствующих параметров. Это может занять от пяти до семи минут, чтобы закончить.

    **Пример**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Миграция клиентского трафика

Во-первых, дважды проверьте, что скрипт успешно создал новый шлюз v2 с точной конфигурацией, перенесенной из вашего шлюза v1. Вы можете проверить это с портала Azure.

Кроме того, отправить небольшое количество трафика через шлюз v2 в качестве ручного теста.
  
Вот несколько сценариев, в которых ваш текущий шлюз приложения (Standard) может получать клиентский трафик, и наши рекомендации по каждому из них:

* **Пользовательская зона DNS (например, contoso.com), которая указывает на IP-адрес фронта (с помощью записи), связанную с вашим стандартным шлюзом v1 или WAF v1.**

    Вы можете обновить запись DNS, чтобы указать на этикетку Frontend IP или DNS, связанную с вашим Standard_v2 шлюзом приложения. В зависимости от TTL, настроенного на запись DNS, может потребоваться некоторое время для того, чтобы весь клиентский трафик мигрировал на новый шлюз v2.
* **Пользовательская зона DNS (например, contoso.com), которая указывает на метку DNS (например, *myappgw.eastus.cloudapp.azure.com* с помощью записи CNAME), связанной с вашим шлюзом v1.**

   Есть два варианта:

  * Если вы используете общедоступные IP-адреса на шлюзе приложения, вы можете сделать контролируемую, детальную миграцию с помощью профиля Traffic Manager для постепенного направления трафика (взвешенный метод маршрутирования трафика) к новому шлюзу v2.

    Вы можете сделать это, добавив dNS-метки как шлюзов приложения v1, так и v2 в профиль `www.contoso.com` [диспетчера трафика,](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)и CNAMEing пользовательскую запись DNS (например, ) в домен Traffic Manager (например, contoso.trafficmanager.net).
  * Или, вы можете обновить пользовательский домен DNS запись, чтобы указать на dNS этикетку нового шлюза приложения v2. В зависимости от TTL, настроенного на запись DNS, может потребоваться некоторое время для того, чтобы весь клиентский трафик мигрировал на новый шлюз v2.
* **Ваши клиенты подключаются к IP-адресу приложения.**

   Обновление ваших клиентов, чтобы использовать IP-адрес (es), связанный с недавно созданным шлюзом приложения v2. Мы рекомендуем не использовать IP-адреса напрямую. Рассмотрите возможность использования метки имени DNS (например, yourgateway.eastus.cloudapp.azure.com), связанной с шлюзом приложения, который можно cNAME для вашей собственной зоны DNS (например, contoso.com).

## <a name="common-questions"></a>Часто задаваемые вопросы

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Существуют ли какие-либо ограничения со скриптом Azure PowerShell для переноса конфигурации из v1 в v2?

Да. Смотрите [Пещеры / Ограничения](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Применима ли эта статья и скрипт Azure PowerShell для продукта Application Gateway WAF? 

Да.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Переключается ли также скрипт Azure PowerShell с моего шлюза v1 на недавно созданный шлюз v2?

Нет. Скрипт Azure PowerShell только мигрирует по конфигурации. Фактическая миграция трафика является вашей ответственностью и в вашем контроле.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Является ли новый шлюз v2 созданным скриптом Azure PowerShell размером с нее, чтобы обрабатывать весь трафик, который в настоящее время обслуживается моим шлюзом v1?

Скрипт Azure PowerShell создает новый шлюз v2 с соответствующим размером для обработки трафика на существующем шлюзе v1. Автоматическое масштабирование отключено по умолчанию, но при запуске скрипта можно включить AutoScaling.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Я настроил свой шлюз v1 для отправки журналов в хранилище Azure. Ли сценарий повторить эту конфигурацию для v2, а?

Нет. Скрипт не повторяет эту конфигурацию для v2. Конфигурацию журнала необходимо добавить отдельно к мигрированию шлюза v2.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Загружается ли этот скриптсертификаты поддержки в Azure KeyVault?

Нет. В настоящее время скрипт не поддерживает сертификаты в KeyVault. Тем не менее, это рассматривается для будущей версии.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Я столкнулся с некоторыми проблемами с использованием этого сценария. Как я могу получить помощь?
  
Вы можете отправить appgwmigrationsup@microsoft.comэлектронное письмо, открыть кейс поддержки с помощью поддержки Azure или сделать и то, и другое.

## <a name="next-steps"></a>Следующие шаги

[Узнайте больше о приложении шлюз v2](application-gateway-autoscaling-zone-redundant.md)
