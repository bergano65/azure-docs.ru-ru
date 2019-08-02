---
title: Предварительные требования для шифровании дисков Azure для виртуальных машин IaaS | Документация Майкрософт
description: Эта статья содержит предварительные требования для использования шифрования дисков Microsoft Azure с виртуальными машинами IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/25/2019
ms.custom: seodec18
ms.openlocfilehash: 423fad943190232d9e5e674b98b62f4f0dffb8ae
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728754"
---
# <a name="azure-disk-encryption-prerequisites"></a>Предварительные требования для шифрования дисков Azure

В этой статье объясняются компоненты, которые должны быть установлены до того, как вы сможете использовать шифрование дисков Azure. Шифрование дисков Azure интегрируется с [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), обеспечивая управление ключами шифрования. Для настройки шифрования дисков Azure можно использовать [Azure PowerShell](/powershell/azure/overview), [Azure CLI](/cli/azure/) или [портал Azure](https://portal.azure.com).

Ниже перечислены предварительные требования для включения шифрования дисков Azure на виртуальных машинах IaaS Azure в сценариях, которые обсуждались в статье [Azure Disk Encryption for IaaS VMs](azure-security-disk-encryption-overview.md) (Шифрование дисков Azure для виртуальных машин IaaS). Эти требования обязательно должны быть выполнены. 

> [!WARNING]
> - Если вы уже использовали [шифрование дисков Azure с помощью приложения Azure AD](azure-security-disk-encryption-prerequisites-aad.md) для шифрования этой виртуальной машины, этот способ нужно применять и далее для шифрования виртуальной машины. На этой зашифрованной виртуальной машине нельзя использовать [шифрование дисков Azure](azure-security-disk-encryption-prerequisites.md), так как этот сценарий не работает — переключение из приложения AAD для данной зашифрованной виртуальной машины сейчас не поддерживается.
> - Выполнение некоторых приведенных рекомендаций может привести к более интенсивному использованию данных, сети или вычислительных ресурсов, а следовательно к дополнительным затратам на лицензии или подписки. Необходима действующая подписка Azure, которая позволяет создавать ресурсы Azure в поддерживаемых регионах.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-vm-sizes"></a>Поддерживаемые размеры виртуальных машин

Шифрование дисков Azure доступно на виртуальных машинах, отвечающих следующим минимальным требованиям к памяти:

| Виртуальная машина | Минимальные требования к памяти |
|--|--|
| Виртуальные машины Windows | 2 ГБ |
| Виртуальные машины Linux, когда шифруются только тома данных| 2 ГБ |
| Виртуальные машины Linux при шифровании томов данных и ОПЕРАЦИОННЫХ систем, а также в том случае, если корневой файл (/) используется не менее 4 ГБ. | 8 ГБ |
| Виртуальные машины Linux при шифровании данных и томов ОС, а также о том, где корневое (/) использование файловой системы превышает 4 ГБ | Использование корневой файловой системы * 2. Например, 16 Гб использования корневой файловой системы требуют не менее 32 Гбайт ОЗУ. |

После завершения процесса шифрования диска ОС на виртуальных машинах Linux можно настроить запуск виртуальной машины с меньшим объемом памяти. 

> [!NOTE]
> Шифрование диска ОС Linux недоступно для [масштабируемых наборов виртуальных машин](../virtual-machine-scale-sets/index.yml).

Шифрование дисков Azure также доступно для виртуальных машин с хранилищем класса Premium. 

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

### <a name="windows"></a>Windows

- Клиент Windows: Windows 8 и более поздние версии.
- Сервер Windows Server: Windows Server 2008 R2 и более поздней версии.  
 
> [!NOTE]
> Windows Server 2008 R2 требует установки .NET Framework 4,5 для шифрования; Установите его из Центр обновления Windows с помощью необязательного обновления Microsoft .NET Framework 4.5.2 для 64-разрядных систем Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core и Windows Server 2016 Core требуют, чтобы компонент BdeHdCfg был установлен на виртуальной машине для шифрования.


### <a name="linux"></a>Linux 

Шифрование дисков Azure поддерживается в подмножестве дистрибутивов [Linux](../virtual-machines/linux/endorsed-distros.md), рекомендованных Azure, которые являются подмножеством всех возможных дистрибутивов Linux Server.

![Диаграмма Венна для дистрибутивов Linux Server, поддерживающих шифрование дисков Azure](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Дистрибутивы сервера Linux, не поддерживаемые Azure, не поддерживают шифрование дисков Azure и, по вашему предодобрению, поддерживают шифрование дисков Azure только для следующих дистрибутивов и версий:

| Дистрибутив Linux | Version | Тип тома, для которого поддерживается шифрование|
| --- | --- |--- |
| Ubuntu | 18,04| Диск операционной системы и данных |
| Ubuntu | 16.04| Диск операционной системы и данных |
| Ubuntu | 14.04.5</br>[с ядром, оптимизированным для Azure, обновленным до версии 4.15 или более поздней версии](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | Диск операционной системы и данных |
| RHEL | 7.6 | ОС и диск данных (см. Примечание ниже) |
| RHEL | 7.5 | ОС и диск данных (см. Примечание ниже) |
| RHEL | 7.4 | ОС и диск данных (см. Примечание ниже) |
| RHEL | 7.3 | ОС и диск данных (см. Примечание ниже) |
| RHEL | 7,2 | ОС и диск данных (см. Примечание ниже) |
| RHEL | 6,8 | Диск данных (см. Примечание ниже) |
| RHEL | 6.7 | Диск данных (см. Примечание ниже) |
| CentOS | 7.6 | Диск операционной системы и данных |
| CentOS | 7.5 | Диск операционной системы и данных |
| CentOS | 7.4 | Диск операционной системы и данных |
| CentOS | 7.3 | Диск операционной системы и данных |
| CentOS | 7.2n | Диск операционной системы и данных |
| CentOS | 6,8 | Диск с данными |
| openSUSE | 42.3 | Диск с данными |
| SLES | 12-SP4 | Диск с данными |
| SLES | 12-SP3 | Диск с данными |

> [!NOTE]
> Новая реализация ADE поддерживается для RHEL ОС и диска данных для образов RHEL7 с оплатой по мере использования. Для образов RHEL, предоставляемых по подписке, ADE пока не поддерживается. Дополнительные сведения см. в статье о [шифровании дисков Azure для Linux](azure-security-disk-encryption-linux.md) .

- Для шифрования дисков Azure требуется, чтобы хранилище ключей и виртуальные машины находились в одном регионе и подписке Azure. Настройка ресурсов в отдельных регионах приведет к сбою при включении функции шифрования дисков Azure.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Дополнительные предварительные требования для виртуальных машин IaaS под управлением Linux 

- Для шифрования дисков Azure в системе должны присутствовать модули DM-encryption и вфат. Удаление или отключение вфат из образа по умолчанию не позволит системе считывать ключ тома и получать ключ, необходимый для разблокировки дисков при последующих перезагрузках. Действия по укреплению безопасности системы, удаляющие модуль вфат из системы, несовместимы с шифрованием дисков Azure. 
- Перед включением шифрования диски данных, которые нужно зашифровать, должны быть правильно указаны в /etc/fstab. Используйте постоянное имя устройства для этой записи, так как имена устройств в формате "/dev/sdX" необязательно будут с одним и тем же диском в случае перезагрузок, особенно после применения шифрования. Дополнительные сведения об этом поведении см. в статье [Устранение неполадок при изменении имени устройства виртуальной машины Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Убедитесь, что параметры /etc/fstab для подключения настроены правильно. Чтобы настроить эти параметры, запустите команду mount -a или перезагрузите виртуальную машину и подключите ее заново таким образом. Как только это будет завершено, проверьте выходные данные команды lsblk, чтобы убедиться, что нужный диск все еще подключен. 
  - Если файл /etc/fstab не подключает диск должным образом до включения шифрования, служба шифрования дисков Azure не сможет правильно подключить его.
  - Служба шифрования дисков Azure переместит информацию о подключении из /etc/fstab в собственный файл конфигурации как часть процесса шифрования. Не волнуйтесь, если увидите, что запись отсутствует в /etc/fstab после завершения шифрования диска данных.
  - Перед запуском шифрования убедитесь, что все службы и процессы, которые могут быть записаны в подключенные диски данных, отключены, поэтому они не перезапускаются автоматически после перезагрузки. Они могут открывать файлы в этих разделах, предотвращая их повторное подключение, что приводит к сбою шифрования. 
  - После перезагрузки процессу шифрования дисков Azure потребуется некоторое время для подключения только что зашифрованных дисков. После перезагрузки они не будут доступны сразу. Прежде чем зашифрованные диски станут доступными для других процессов, потребуется некоторое время для их запуска, разблокировки и подключения. Этот процесс может занять более минуты после перезагрузки в зависимости от характеристик системы.

Примеры команд, которые можно использовать для подключения дисков данных и создания необходимых записей /etc/fstab, можно найти в [строках 244–248 этого файла сценария](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a> Сетевые подключения и групповая политика

**Чтобы использовать функцию шифрования дисков Azure, конфигурация конечной точки сети виртуальной машины IaaS должна соответствовать приведенным ниже требованиям:**
  - Виртуальная машина IaaS должна иметь возможность подключиться к конечной точке Azure Active Directory \[login.microsoftonline.com\], чтобы получить маркер для подключения к хранилищу ключей.
  - Для записи ключей шифрования в ваше хранилище ключей виртуальная машина IaaS должна иметь возможность подключиться к конечной точке хранилища ключей.
  - Виртуальная машина IaaS должна иметь возможность подключиться к конечной точке службы хранилища Azure, в которой размещен репозиторий расширений Azure, и к учетной записи хранения Azure, в которой размещены VHD-файлы.
  -  Если ваша политика безопасности ограничивает доступ к Интернету с виртуальных машин Azure, можно разрешить указанный выше универсальный код ресурса (URI) и настроить определенное правило, чтобы разрешить исходящие подключения к данным IP-адресам. Дополнительные сведения см. в статье [Доступ к Azure Key Vault из-за брандмауэра](../key-vault/key-vault-access-behind-firewall.md).    


**Групповая политика:**
 - Решение шифрования дисков Azure использует внешний предохранитель ключа BitLocker для виртуальных машин IaaS под управлением Windows. Если виртуальные машины присоединены к домену, не применяйте групповые политики, требующие использования предохранителей TPM. Сведения о групповой политике "Разрешить использование BitLocker без совместимого TPM" см. в [справке по групповым политикам BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Политика BitLocker на присоединенных к домену виртуальных машинах с настраиваемой групповой политикой должна включать следующий параметр: [Настройка хранилища пользователей сведений о восстановлении BitLocker. > разрешить 256-разрядный ключ восстановления](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Шифрование дисков Azure завершится ошибкой, если параметры настраиваемой групповой политики для BitLocker несовместимы. На компьютерах без соответствующего параметра политики может потребоваться применить новую политику, принудительно обновить ее (gpupdate.exe /force) и перезагрузить компьютер.

- Шифрование дисков Azure завершится ошибкой, если групповая политика уровня домена блокирует алгоритм AES-CBC, который используется BitLocker.


## <a name="bkmk_PSH"></a> Azure PowerShell
В [Azure PowerShell](/powershell/azure/overview) доступен набор командлетов, которые используют модель [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) для управления ресурсами Azure. Его можно использовать в браузере с [Azure Cloud Shell](../cloud-shell/overview.md), а также установить на локальном компьютере, следуя инструкциям ниже, и применять в любом сеансе PowerShell. Если вы уже установили его локально, убедитесь, что вы используете последнюю версию версии пакета SDK для Azure PowerShell для настройки шифрования дисков Azure. Скачайте последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Установите Azure PowerShell для использования на вашем локальном компьютере (необязательно): 
1. Следуйте инструкциям по ссылкам для вашей операционной системы, а затем перейдите к остальным шагам.      
   - [Установите и настройте Azure PowerShell](/powershell/azure/install-az-ps). 
     - Установите PowerShellGet, Azure PowerShell и загрузите модуль AZ. 

2. Проверьте установленные версии модуля AZ. При необходимости [обновите модуль Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
    Рекомендуется использовать последнюю версию модуля AZ.

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Войдите в Azure с помощью командлета [Connect-азаккаунт](/powershell/module/az.accounts/connect-azaccount) .
     
     ```azurepowershell-interactive
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  При необходимости ознакомьтесь со статьей [Начало работы с Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="bkmk_CLI"></a>Установка Azure CLI для использования на вашем локальном компьютере (необязательно)

[Azure CLI 2.0](/cli/azure) — это интерфейс командной строки для управления ресурсами Azure. Этот интерфейс обеспечивает гибкие функции подачи запросов, выполнение длительных операций без блокировки и простое создание скриптов. Его можно использовать в браузере с [Azure Cloud Shell](../cloud-shell/overview.md), а также установить на локальном компьютере и использовать в любом сеансе PowerShell.

1. [Установите Azure CLI](/cli/azure/install-azure-cli) для использования на вашем локальном компьютере (необязательно):

2. Проверьте номер установленной версии.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Затем войдите в Azure с помощью команды [az login](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. При необходимости ознакомьтесь со статьей [Начало работы с Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 


## <a name="prerequisite-workflow-for-key-vault"></a>Необходимый рабочий процесс для Key Vault
Если вы уже знакомы с предварительными требованиями Key Vault и Azure AD для шифрования дисков Azure, можно использовать [скрипт PowerShell предварительных требований к шифрованию дисков Azure](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Дополнительные сведения об использовании скрипта предварительных требований см. в [кратком руководстве по шифрованию виртуальных машин](azure-disk-encryption-linux-powershell-quickstart.md) и [приложении шифрования дисков Azure](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. При необходимости создайте группу ресурсов.
2. Создать хранилище ключей. 
3. Установите политики расширенного доступа к хранилищу ключей.

>[!WARNING]
>Перед удалением хранилища ключа убедитесь, что никакие из существующих виртуальных машин не зашифрованы этим ключом. Для защиты хранилища от случайного удаления включите для него параметры [Включить обратимое удаление](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete) и [Блокировка ресурса](../azure-resource-manager/resource-group-lock-resources.md). 
 
## <a name="bkmk_KeyVault"></a> Создание хранилища ключей 
Шифрование дисков Azure интегрировано с [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/), что позволяет управлять секретами и ключами шифрования дисков в подписке Key Vault и контролировать их. Вы можете создать хранилище ключей или использовать существующее для шифрования дисков Azure. Дополнительные сведения о хранилищах ключей см. в статье [Приступая к работе с Azure Key Vault](../key-vault/key-vault-get-started.md) и [Защита хранилища ключей](../key-vault/key-vault-secure-your-key-vault.md). Для создания хранилища ключей можно использовать шаблон Resource Manager, Azure PowerShell или Azure CLI. 


>[!WARNING]
>Чтобы убедиться, что секреты шифрования не пересекают региональные границы, шифрованию дисков Azure требуется, чтобы хранилище ключей и виртуальные машины были расположены в одном регионе. Создайте и используйте хранилище ключей, которое находится в том же регионе, что и виртуальная машина для шифрования. 


### <a name="bkmk_KVPSH"></a> Создание хранилища ключей с помощью PowerShell

Хранилище ключей можно создать с Azure PowerShell помощью командлета [New-азкэйваулт](/powershell/module/az.keyvault/New-azKeyVault) . Дополнительные командлеты для Key Vault см. в разделе [AZ. KeyVault](/powershell/module/az.keyvault/). 

1. При необходимости [подключитесь к подписке Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. При необходимости создайте новую группу ресурсов с помощью [New-азресаурцеграуп](/powershell/module/az.Resources/New-azResourceGroup).  Чтобы получить список расположений центров обработки данных, используйте [Get-азлокатион](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Создайте новое хранилище ключей с помощью команды [New-азкэйваулт](/powershell/module/az.keyvault/New-azKeyVault) .
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Запишите возвращенные значения **имени хранилища**, **имени группы ресурсов**, **идентификатора ресурса**, **кода URI хранилища** и **идентификатора объекта** для последующего использования при шифровании дисков. 


### <a name="bkmk_KVCLI"></a> Создание хранилища ключей с помощью Azure CLI
Вы можете управлять вашим хранилищем ключей в Azure CLI с помощью команд [az keyvault](/cli/azure/keyvault#commands). Чтобы создать хранилище ключей, используйте команду [az keyvault create](/cli/azure/keyvault#az-keyvault-create).

1. При необходимости [подключитесь к подписке Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Чтобы создать группу ресурсов, используйте команду [az group create](/cli/azure/group#az-group-create). Для перечисления расположений используйте команду [az account list-locations](/cli/azure/account#az-account-list). 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Чтобы создать новое хранилище ключей, используйте команду[az keyvault create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Запишите возвращенные значения **имени хранилища**, **имени группы ресурсов**, **идентификатора ресурса**, **кода URI хранилища** и **идентификатора объекта** для последующего использования. 

### <a name="bkmk_KVRM"></a> Создание хранилища ключей с помощью шаблона Resource Manager

Можно создать хранилище ключей с помощью [шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. В шаблоне быстрого запуска Azure щелкните **Deploy to Azure** (Развернуть в Azure).
2. Выберите подписку, группу ресурсов, расположение группы ресурсов, имя хранилища ключей, идентификатор объекта, юридические условия и соглашение, а затем щелкните **Приобрести**. 


## <a name="bkmk_KVper"></a> Установка политики расширенного доступа к хранилищу ключей
Платформа Azure должна иметь доступ к ключам шифрования или секретам, расположенным в вашем хранилище ключей, чтобы предоставлять их виртуальной машине при ее загрузке для расшифровки томов. Включите шифрование диска в хранилище ключей, иначе развертывание завершится сбоем.  

### <a name="bkmk_KVperPSH"></a> Установка политик расширенного доступа для хранилища ключей с помощью Azure PowerShell
 Используйте командлет PowerShell для хранилища ключей [Set-азкэйваултакцессполици](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) , чтобы включить шифрование дисков для хранилища ключей.

  - **Включение хранилища ключей для шифрования дисков:** для шифрования дисков Azure требуется использовать параметр EnabledForDiskEncryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Включение хранилища ключей для развертывания (если необходимо):** позволяет поставщику ресурсов Microsoft.Compute извлекать секреты из этого хранилища ключей в случае использования ссылки на него при создании ресурса, например виртуальной машины.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Включение хранилища ключей для развертывания шаблона (если необходимо):** позволяет Azure Resource Manager получать секреты из этого хранилища ключей в случае использования ссылки на него при развертывании шаблона.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Установка политик расширенного доступа для хранилища ключей с помощью Azure CLI
Включите шифрование дисков для хранилища ключей с помощью команды [az keyvault update](/cli/azure/keyvault#az-keyvault-update). 

 - **Включение хранилища ключей для шифрования дисков:** параметр Enabled-for-disk-encryption является обязательным. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Включение хранилища ключей для развертывания (если необходимо):** позволяет поставщику ресурсов Microsoft.Compute извлекать секреты из этого хранилища ключей в случае использования ссылки на него при создании ресурса, например виртуальной машины.

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Включение хранилища ключей для развертывания шаблона (если необходимо):** позволяет Resource Manager получать секреты из хранилища.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Установка политики расширенного доступа для хранилища ключей с помощью портала Azure

1. Выберите хранилище ключей, перейдите на вкладку **Политики доступа** и выберите **Щелкните, чтобы показать политики расширенного доступа**.
2. Установите флажок **Включить доступ к шифрованию дисков Azure для шифрования томов**.
3. Выберите **Включить доступ к виртуальным машинам Azure для развертывания** или **Включить доступ к Azure Resource Manager для развертывания шаблонов**, если это необходимо. 
4. Нажмите кнопку **Сохранить**.

    ![Установка политики расширенного доступа к хранилищу Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Настройка ключа шифрования ключей (необязательно)
Если вы хотите использовать ключ шифрования ключей (KEK), чтобы добавить уровень безопасности ключей шифрования, поместите KEK в хранилище ключей. Используйте командлет [Add-азкэйваулткэй](/powershell/module/az.keyvault/add-azkeyvaultkey) , чтобы создать ключ шифрования ключа в хранилище ключей. Кроме того, KEK можно импортировать из своего локального модуля HSM службы управления ключами. Дополнительные сведения см. в документации по [Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Когда ключ шифрования ключей указан, шифрование дисков Azure использует его для упаковки секретов шифрования перед записью в Key Vault.

* При создании ключей используйте тип ключа RSA. Шифрование дисков Azure пока не поддерживает использование ключей эллиптической кривой.

* Для URL-адресов секрета и ключа шифрования ключей (KEK) хранилища ключей необходимо включить управление версиями. Это требование Azure. Ниже приведены примеры действительных URL-адресов секрета и ключа шифрования ключей.

  * Пример допустимого URL-адреса секрета: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* .
  * Пример допустимого URL-адреса ключа шифрования ключей: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* .

* Шифрование дисков Azure не поддерживает указание номеров портов в URL-адресах секрета и ключа шифрования ключей для хранилища ключей. Ниже приведены примеры недопустимых и допустимых URL-адресов хранилища ключей.

  * Неприемлемый URL-адрес хранилища ключей: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* .
  * Приемлемый URL-адрес хранилища ключей: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* .


### <a name="bkmk_KEKPSH"></a> Настройка ключа шифрования ключей с помощью Azure PowerShell 
Перед использованием скрипта PowerShell нужно ознакомиться с предварительными требованиями к шифрованию дисков Azure, чтобы понять шаги. Для примера скрипта могут потребоваться изменения в вашей среде. Этот скрипт создает все необходимые компоненты для шифрования дисков Azure и шифрует существующую виртуальную машину IaaS, упаковывая ключ шифрования диска с помощью ключа шифрования ключей. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).VaultUri;
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'HSM';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>Следующие шаги
> [!div class="nextstepaction"]
> [Включение шифрования дисков Azure для виртуальных машин Windows IaaS](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Включение шифрование дисков Azure для Linux](azure-security-disk-encryption-linux.md)

