---
title: Интеграция журналов из Azure Key Vault с помощью Центров событий | Документы Майкрософт
description: Руководство, в котором приводятся необходимые действия для обеспечения доступности журналов Key Vault для SIEM с помощью интеграции журналов Azure.
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 15223c59f270dc562e521697186cfaf7f30073b9
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004120"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Руководство по интеграции журналов данных Azure. Обработка событий Azure Key Vault с помощью Центров событий

>[!IMPORTANT]
> Функция интеграции журналов Azure будет устаревшей до 06/15/2019. Файлы для скачивания AzLog недоступны с 27 июня 2018 г. Сведения о том, что делать дальше, см. в блоге [Используйте Azure-монитор для интеграции с инструментами SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Службу интеграции журналов Azure можно использовать для получения зарегистрированных событий журнала и обеспечения их доступности для системы SIEM. В этом руководстве предоставляется поэтапный пример использования службы интеграции журналов Azure для обработки журналов, полученных из Центров событий.

Предпочтительным методом интеграции журналов Azure является использование соединителя Azure Monitor от поставщика SIEM и выполнение следующих [инструкций](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Тем не менее, если поставщик SIEM не предоставляет соединитель для Azure Monitor, в качестве временного решения можно использовать Интеграцию журналов данных Azure (если она поддерживает вашу систему SIEM).

 
Это руководство поможет вам ознакомиться с данными о совместной работе службы интеграции журналов Azure и Центров событий, выполнить шаги для примера и понять, как каждый шаг поддерживает решение. Затем вы сможете применить полученные здесь знания для создания собственной процедуры обеспечения соответствия уникальным требованиям компании.

> [!WARNING]
> Действия и команды, используемые в этом руководстве, не предназначены для копирования и вставки. Они предоставляются только в качестве примеров. Не используйте команды PowerShell "как есть" в рабочей среде. Их необходимо настраивать с учетом уникальной среды.


В этом руководстве описывается процесс использования действия Azure Key Vault, записанного в журнал концентратора событий, и обеспечения его доступности для системы SIEM в виде JSON-файлов. Затем можно настроить систему SIEM для обработки JSON-файлов.

>[!NOTE]
>В большинстве шагов в этом руководстве выполняется настройка хранилищ ключей, учетных записей хранения и концентраторов событий. Шаги по работе с интеграцией журналов Azure приводятся в конце руководства. Не выполняйте следующие действия в рабочей среде. Они предназначены для работы в лабораторной среде. Прежде чем выполнять эти действия в рабочей среде, их нужно настроить.

Сведения, предоставляемые по ходу процедур, помогут понять причины выполнения каждого шага. Перейдя по ссылкам на другие статьи, вы сможете получить более подробные сведения о конкретных разделах.

Дополнительные сведения о службах, которые упоминаются в этом учебнике, см. в указанных ниже разделах. 

- [Хранилище ключей Azure](/azure/key-vault/key-vault-overview)
- [Центры событий Azure](/azure/event-hubs/event-hubs-what-is-event-hubs)
- [Интеграция журналов Azure](azure-log-integration-overview.md)


## <a name="initial-setup"></a>Первоначальная настройка

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Подписка Azure и учетная запись для этой подписки с правами администратора. Если у вас нет подписки, вы можете [создать бесплатную учетную запись](https://azure.microsoft.com/free/).
 
* Система с доступом к Интернету, отвечающая требованиям к установке интеграции журналов Azure. Система может быть размещена в облачной службе или в локальной среде.

* Установленная служба "Интеграция журналов данных Azure". Чтобы установить службу:

   1\. Подключитесь к системе, упомянутой в шаге 2, через удаленный рабочий стол.   
   2\. Скопируйте в систему установщик интеграции журналов Azure. В. Запустите установщик и примите условия лицензионного соглашения на использование программного обеспечения корпорации Майкрософт.

* Если вы указываете данные телеметрии, оставьте флажок установленным. Если вы не хотите отправлять сведения об использовании в корпорацию Майкрософт, снимите флажок.

   Дополнительные сведения о службе интеграции журналов Azure и действиях по ее установке см. в статье [Интеграция журналов Azure с ведением журнала системы диагностики Azure и пересылкой событий Windows](azure-log-integration-get-started.md).

* Последняя версия PowerShell.

   Если у вас установлен Windows Server 2016, по меньшей мере у вас есть PowerShell 5.0. Если вы используете другие версии Windows Server, может потребоваться установить более раннюю версию PowerShell. Чтобы проверить используемую версию, в окне PowerShell введите ```get-host```. Если версия PowerShell 5.0 не установлена, ее можно [скачать](https://www.microsoft.com/download/details.aspx?id=50395).

   Получив по крайней мере PowerShell 5,0, можно перейти к установке последней версии, следуя инструкциям в статье об [установке Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="create-supporting-infrastructure-elements"></a>Создание вспомогательных элементов инфраструктуры

1. Откройте окно PowerShell с повышенными привилегиями и перейдите в каталог **C:\Program Files\Microsoft Azure Log Integration**.
1. Импортируйте командлеты AzLog, выполнив сценарий LoadAzLogModule.ps1. Введите команду `.\LoadAzLogModule.ps1`. (Обратите внимание на ".\"" в этой команде.) Вы увидите нечто вроде этого:</br>

   ![Список загруженных модулей](./media/azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Введите команду `Connect-AzAccount`. В окне входа введите учетные данные для подписки, которая будет использоваться в этом руководстве.

   >[!NOTE]
   >Если вы выполняете вход в Azure с этого компьютера впервые, появится сообщение о предоставлении корпорации Майкрософт разрешения на сбор данных об использовании PowerShell. Рекомендуется включить эту функцию сбора данных, так как она будет использоваться для внесения улучшений в Azure PowerShell.

1. После успешной проверки подлинности вы вошли в систему. Запишите идентификатор подписки и имя подписки, так как они потребуются вам на следующих этапах.

1. Создайте переменные для хранения значений, которые будут использоваться дальше. Введите следующие строки PowerShell. Может потребоваться настроить параметры в соответствии с вашей средой.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` (У вашей подписки может быть другое имя. Вы увидите его в составе выходных данных предыдущей команды.)
    - ```$location = 'West US'``` (Эта переменная будет использоваться для передачи расположения, в котором должны быть созданы ресурсы. Эту переменную можно изменить на любое необходимое расположение.)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random``` (Имя может быть любым, но оно должно включать только строчные буквы и цифры.)
    - ```$storageName = $name``` (Эта переменная будет использоваться для имени учетной записи хранения.)
    - ```$rgname = $name``` (Эта переменная будет использоваться для имени группы ресурсов.)
    - ```$eventHubNameSpaceName = $name``` (Это имя пространства имен концентратора событий.)
1. Укажите подписку, с которой вы будете работать:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Создайте группу ресурсов:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Если на этом этапе вы введете `$rg`, вы должны увидеть следующие выходные данные:

   ![Выходные данные после создания группы ресурсов](./media/azure-log-integration-keyvault-eventhub/create-rg.png)
1. Создайте учетную запись хранения, которая будет использоваться для отслеживания сведений о состоянии:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Создайте пространство имен концентратора событий. Это необходимо для создания концентратора событий.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Получите идентификатор правила, который будет использоваться с поставщиком Insights:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Получите все возможные расположения Azure и добавьте имена в переменную, которая может использоваться в дальнейшем:
    
    1\. ```$locationObjects = Get-AzLocation```    
    2\. ```$locations = @('global') + $locationobjects.location```
    
    Если на этом `$locations` этапе вы введете, то увидите имена расположений без дополнительных сведений, возвращенных Get-азлокатион.
1. Создайте профиль журнала Azure Resource Manager: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Дополнительные сведения о профиле журнала Azure см. в статье [Общие сведения о журнале действий Azure](../../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> При попытке создания профиля журнала может появиться сообщение об ошибке. Затем можно просмотреть документацию по Get-Азлогпрофиле и Remove-Азлогпрофиле. При выполнении Get-Азлогпрофиле отображаются сведения о профиле журнала. Чтобы удалить имеющийся профиль журнала, введите команду ```Remove-AzLogProfile -name 'Log Profile Name'```.
>
>![Ошибка профиля Resource Manager](./media/azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

1. Создайте хранилище ключей:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. Настройте ведение журнала для хранилища ключей:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Создание действия журнала

Для создания действия журнала требуется отправка запросов в Key Vault. Действия, например создание ключей, хранение секретов или чтение секретов из Key Vault, будут создавать записи в журнале.

1. Отобразите текущие ключи хранилища:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Создайте ключ **key2**:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Отобразите ключи снова и обратите внимание, что ключ **key2** содержит другое значение:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Задайте и считайте секрет для создания дополнительных записей журнала:
    
   1\. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` Б. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Возвращенный секрет](./media/azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Настройка интеграции журналов Azure

После того как для всех необходимых элементов настроено ведение журнала Key Vault в концентраторе событий, следует настроить интеграцию журнала Azure.

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Выполните команду AzLog для каждого концентратора событий:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Приблизительно через минуту после выполнения двух последних команд вы должны увидеть созданные JSON-файлы. Их наличие можно проверить в каталоге **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Следующие шаги

- [Интеграция журналов Azure: часто задаваемые вопросы](azure-log-integration-faq.md)
- [Приступая к работе со службой интеграции журналов Azure](azure-log-integration-get-started.md)
- [Интеграция журналов из ресурсов Azure в системы SIEM](azure-log-integration-overview.md)
