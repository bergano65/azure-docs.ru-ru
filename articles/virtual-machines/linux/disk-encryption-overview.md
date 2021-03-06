---
title: Включение шифрования дисков Azure для виртуальных машин Linux
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для виртуальных машин Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a3d48d53c2d4d0c859b58a94b12ffa94590b18a5
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989639"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Шифрование дисков Azure для виртуальных машин Linux 

Шифрование дисков Azure помогает защитить данные в соответствии с обязательствами по обеспечению безопасности и соответствия требованиям Организации. В нем используется функция [DM-](https://en.wikipedia.org/wiki/Dm-crypt) Encryption в Linux для обеспечения шифрования томов и дисков данных виртуальных машин Azure, а также интегрировано с [Azure Key Vault](../../key-vault/index.yml) , помогающее управлять ключами и секретами шифрования диска и управлять ими. 

Если вы используете [Центр безопасности Azure](../../security-center/index.yml), вы получаете оповещения, если у вас есть виртуальные машины, которые не шифруются. Вы получите оповещение высокого уровня серьезности вместе c рекомендацией о шифровании таких виртуальных машин.

![Оповещение о шифровании диска в центре безопасности Azure](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Если вы ранее использовали шифрование дисков Azure с Azure AD для шифрования виртуальной машины, необходимо продолжить использовать этот параметр для шифрования виртуальной машины. Дополнительные сведения см. [в статье шифрование дисков Azure с помощью Azure AD (предыдущий выпуск)](disk-encryption-overview-aad.md) . 
> - Выполнение некоторых приведенных рекомендаций может привести к более интенсивному использованию данных, сети или вычислительных ресурсов, а следовательно к дополнительным затратам на лицензии или подписки. Необходима действующая подписка Azure, которая позволяет создавать ресурсы Azure в поддерживаемых регионах.

Основные сведения о шифровании дисков Azure для Linux можно получить всего за несколько минут с помощью [краткого руководства по созданию и шифрованию виртуальной машины Linux с помощью Azure CLI](disk-encryption-cli-quickstart.md) или в [кратком руководстве по созданию и шифрованию виртуальной машины Linux с помощью Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Поддерживаемые виртуальные машины и операционные системы

### <a name="supported-vm-sizes"></a>Поддерживаемые размеры виртуальных машин

Виртуальные машины Linux доступны в [различных размерах](sizes.md). Шифрование дисков Azure недоступно на виртуальных машинах [уровня "базовый", "серия](https://azure.microsoft.com/pricing/details/virtual-machines/series/)" или на виртуальных машинах, которые не соответствуют следующим минимальным требованиям к памяти:

| Виртуальная машина | Минимальные требования к памяти |
|--|--|
| Виртуальные машины Linux, когда шифруются только тома данных| 2 ГБ |
| Виртуальные машины Linux при шифровании томов данных и ОПЕРАЦИОННЫХ систем, а также в том случае, если корневой файл (/) используется не менее 4 ГБ. | 8 ГБ |
| Виртуальные машины Linux при шифровании данных и томов ОС, а также о том, где корневое (/) использование файловой системы превышает 4 ГБ | Использование корневой файловой системы * 2. Например, 16 Гб использования корневой файловой системы требуют не менее 32 Гбайт ОЗУ. |

После завершения процесса шифрования диска ОС на виртуальных машинах Linux можно настроить запуск виртуальной машины с меньшим объемом памяти. 

Шифрование дисков Azure также доступно для виртуальных машин с хранилищем класса Premium. 

### <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

Шифрование дисков Azure поддерживается в подмножестве [дистрибутивов Linux](endorsed-distros.md), рекомендованных Azure, которые являются подмножеством всех возможных дистрибутивов Linux Server.

![Диаграмма Венна для дистрибутивов Linux Server, поддерживающих шифрование дисков Azure](./media/disk-encryption/ade-supported-distros.png)

Дистрибутивы сервера Linux, не поддерживаемые Azure, не поддерживают шифрование дисков Azure. из тех, которые были одобрены, поддерживаются только следующие дистрибутивы и версии, поддерживаемые шифрованием дисков Azure:

| Дистрибутив Linux | Версия | Тип тома, для которого поддерживается шифрование|
| --- | --- |--- |
| Ubuntu | 18,04| Диск операционной системы и данных |
| Ubuntu | 16.04| Диск операционной системы и данных |
| Ubuntu | 14.04.5</br>[с ядром, оптимизированным для Azure, обновленным до версии 4.15 или более поздней версии](disk-encryption-troubleshooting.md) | Диск операционной системы и данных |
| RHEL | 7,7 | ОС и диск данных (см. Примечание ниже) |
| RHEL | 7.6 | ОС и диск данных (см. Примечание ниже) |
| RHEL | 7.5 | ОС и диск данных (см. Примечание ниже) |
| RHEL | 7.4 | ОС и диск данных (см. Примечание ниже) |
| RHEL | 7.3 | ОС и диск данных (см. Примечание ниже) |
| RHEL | 7.2 | ОС и диск данных (см. Примечание ниже) |
| RHEL | 6,8 | Диск данных (см. Примечание ниже) |
| RHEL | 6.7 | Диск данных (см. Примечание ниже) |
| CentOS | 7,7 | Диск операционной системы и данных |
| CentOS | 7.6 | Диск операционной системы и данных |
| CentOS | 7.5 | Диск операционной системы и данных |
| CentOS | 7.4 | Диск операционной системы и данных |
| CentOS | 7.3 | Диск операционной системы и данных |
| CentOS | 7.2n | Диск операционной системы и данных |
| CentOS | 6,8 | Диск данных |
| openSUSE | 42.3 | Диск данных |
| SLES | 12-SP4 | Диск данных |
| SLES | 12-SP3 | Диск данных |

> [!NOTE]
> Новая реализация ADE поддерживается для RHEL ОС и диска данных для образов RHEL7 с оплатой по мере использования. Для образов RHEL, предоставляемых по подписке, ADE пока не поддерживается. 

## <a name="additional-vm-requirements"></a>Дополнительные требования к виртуальным машинам

Для шифрования дисков Azure в системе должны присутствовать модули DM-encryption и вфат. Удаление или отключение вфат из образа по умолчанию не позволит системе считывать ключ тома и получать ключ, необходимый для разблокировки дисков при последующих перезагрузках. Действия по укреплению безопасности системы, удаляющие модуль вфат из системы, несовместимы с шифрованием дисков Azure. 

Перед включением шифрования диски данных, подлежащий шифрованию, должны быть должным образом перечислены в/etc/fstab. Используйте постоянное имя устройства для этой записи, так как имена устройств в формате "/dev/sdX" необязательно будут с одним и тем же диском в случае перезагрузок, особенно после применения шифрования. Подробнее об этом поведении см. статье [Устранение неполадок при изменении имени устройства виртуальной машины Linux](troubleshoot-device-names-problems.md).

Убедитесь, что параметры /etc/fstab для подключения настроены правильно. Чтобы настроить эти параметры, запустите команду mount -a или перезагрузите виртуальную машину и подключите ее заново таким образом. Как только это будет завершено, проверьте выходные данные команды lsblk, чтобы убедиться, что нужный диск все еще подключен. 
- Если файл /etc/fstab не подключает диск должным образом до включения шифрования, служба шифрования дисков Azure не сможет правильно подключить его.
- Служба шифрования дисков Azure переместит информацию о подключении из /etc/fstab в собственный файл конфигурации как часть процесса шифрования. Не волнуйтесь, если увидите, что запись отсутствует в /etc/fstab после завершения шифрования диска данных.
- Перед запуском шифрования убедитесь, что все службы и процессы, которые могут быть записаны в подключенные диски данных, отключены, поэтому они не перезапускаются автоматически после перезагрузки. Они могут открывать файлы в этих разделах, предотвращая их повторное подключение, что приводит к сбою шифрования. 
- После перезагрузки процессу шифрования дисков Azure потребуется некоторое время для подключения только что зашифрованных дисков. После перезагрузки они не будут доступны сразу. Прежде чем зашифрованные диски станут доступными для других процессов, потребуется некоторое время для их запуска, разблокировки и подключения. Этот процесс может занять более минуты после перезагрузки в зависимости от характеристик системы.

Пример команд, которые можно использовать для подключения дисков данных и создания необходимых записей/etc/fstab, можно найти в [сценарии CLI для предварительных требований к шифрованию дисков Azure](https://github.com/ejarvi/ade-cli-getting-started) (строки 244-248) и с [предварительными требованиями к шифрованию дисков Azure PowerShell. Скрипт](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Требования к сети

Чтобы включить функцию шифрования дисков Azure, виртуальные машины Linux должны соответствовать следующим требованиям к конфигурации конечной точки сети.
  - Чтобы получить маркер для подключения к хранилищу ключей, виртуальная машина Linux должна иметь возможность подключения к конечной точке Azure Active Directory, \[login.microsoftonline.com\].
  - Чтобы записать ключи шифрования в хранилище ключей, виртуальная машина Linux должна иметь возможность подключения к конечной точке хранилища ключей.
  - Виртуальная машина Linux должна иметь возможность подключения к конечной точке службы хранилища Azure, в которой размещен репозиторий расширения Azure, и учетной записи хранения Azure, в которой размещены VHD-файлы.
  -  Если ваша политика безопасности ограничивает доступ к Интернету с виртуальных машин Azure, можно разрешить указанный выше универсальный код ресурса (URI) и настроить определенное правило, чтобы разрешить исходящие подключения к данным IP-адресам. Дополнительные сведения см. в статье [Доступ к Azure Key Vault из-за брандмауэра](../../key-vault/key-vault-access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Требования к хранилищу ключей шифрования  

Для шифрования дисков Azure требуется Azure Key Vault, чтобы управлять ключами и секретами шифрования дисков и управлять ими. Ваше хранилище ключей и виртуальные машины должны находиться в одном регионе Azure и подписке.

Дополнительные сведения см. [в статье Создание и Настройка хранилища ключей для шифрования дисков Azure](disk-encryption-key-vault.md).

## <a name="terminology"></a>Термины
В следующей таблице описаны некоторые распространенные термины, используемые в документации по шифрованию дисков Azure.

| Термины | Определение |
| --- | --- |
| Azure Key Vault. | Key Vault представляет собой службу управления криптографическими ключами. Она основана на аппаратных модулях безопасности, соответствующих Федеральному стандарту обработки информации (FIPS). Эти стандарты позволяют надежно хранить криптографические ключи и конфиденциальные данные. Дополнительные сведения см. в [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) документации, а также [о создании и настройке хранилища ключей для шифрования дисков Azure](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) оптимизирован для администрирования ресурсов Azure и управления ими из командной строки.|
| DM-Crypt |[DM-](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) Encryption — это прозрачная подсистема шифрования дисков на основе Linux, которая используется для включения шифрования дисков на виртуальных машинах Linux. |
| Ключ шифрования ключа (KEK) | Асимметричный ключ (RSA 2048), который можно использовать для защиты или перетекания секрета. Вы можете использовать защищенный HSM ключ или ключ с программной защитой. Дополнительные сведения см. в [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) документации, а также [о создании и настройке хранилища ключей для шифрования дисков Azure](disk-encryption-key-vault.md). |
| Командлеты PowerShell | Дополнительные сведения см. в статье [Общие сведения об Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Дальнейшие действия

- [Краткое руководство. Создание и шифрование виртуальной машины Linux с помощью Azure CLI](disk-encryption-cli-quickstart.md)
- [Краткое руководство. Создание и шифрование виртуальной машины Linux с помощью Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Сценарии шифрования дисков Azure на виртуальных машинах Linux](disk-encryption-linux.md)
- [Сценарий CLI для предварительных требований к шифрованию дисков Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Сценарий PowerShell для предварительных требований к шифрованию дисков Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Создание и Настройка хранилища ключей для шифрования дисков Azure](disk-encryption-key-vault.md)


