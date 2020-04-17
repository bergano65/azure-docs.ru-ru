---
title: Шифрование дисков Azure с предпосылками приложения Azure AD (предыдущий релиз)
description: Эта статья содержит предварительные требования для использования шифрования дисков Microsoft Azure с виртуальными машинами IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 029743dbb44ab0ebb6ab8f695c0c966137c45107
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459803"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Шифрование диска Azure с Azure AD (предыдущий релиз)

Новый выпуск шифрования Azure Disk устраняет необходимость в предоставлении параметра приложения Azure Active Directory (Azure AD) для включения шифрования дисков VM. В новом выпуске больше не требуется вводить учетные данные Azure AD на этапе включения шифрования. Все новые VM должны быть зашифрованы без параметров приложения Azure AD с помощью нового релиза. Для получения инструкций о том, как включить шифрование [Azure Disk Encryption for Linux VMs](disk-encryption-overview.md)диска VM с помощью нового релиза, см. Виртуальные машины, которые уже были зашифрованы с помощью параметров приложения Azure AD, по-прежнему поддерживаются. Их следует и дальше обслуживать с использованием синтаксиса AAD.

В этой статье содержатся дополнения к [шифрованию Azure Disk для Linux VMs](disk-encryption-overview.md) с дополнительными требованиями и предпосылками для шифрования Azure Disk с Azure AD (предыдущий релиз).

Информация в этих разделах остается прежней:

- [Поддерживаемые ВМ и операционные системы](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Дополнительные требования к VM](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a> Сетевые подключения и групповая политика

Для включения функции шифрования Azure Disk с помощью более старого синтаксиса параметров AAD, инфраструктура как служба (IaaS) VMs должна отвечать следующим требованиям конфигурации конечных точек сети: 
  - Чтобы получить токен для подключения к хранилищу ключа, IaaS VM должен быть в \[\]состоянии подключиться к точке azure AD, login.microsoftonline.com.
  - Для записи ключей шифрования в ваше хранилище ключей виртуальная машина IaaS должна иметь возможность подключиться к конечной точке хранилища ключей.
  - Виртуальная машина IaaS должна иметь возможность подключиться к конечной точке службы хранилища Azure, в которой размещен репозиторий расширений Azure, и к учетной записи хранения Azure, в которой размещены VHD-файлы.
  -  Если ваша политика безопасности ограничивает доступ от Пазурова к Vpn, можно решить предыдущее URI и настроить определенное правило, чтобы разрешить исходящие подключения к I-службам. Дополнительные сведения см. в статье [Доступ к Azure Key Vault из-за брандмауэра](../../key-vault/general/access-behind-firewall.md).
  - В Windows, если TLS 1.0 явно отключен и версия .NET не обновляется до 4.6 или выше, следующее изменение реестра позволяет шифрованию azure Disk выбрать более позднюю версию TLS:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>Групповая политика
 - Решение шифрования дисков Azure использует внешний предохранитель ключа BitLocker для виртуальных машин IaaS под управлением Windows. Для конментов, связанных с VMs, не нажимайте на какие-либо групповые политики, которые обеспечивают соблюдение защиты TPM. Для получения информации о групповой политике для **опции Allow BitLocker без совместимого TPM** [см.](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)

- Политика BitLocker на виртуальных машинах, связанных с доменом, с пользовательской групповой политикой должна включать в себя следующие настройки: [Настройка пользовательского хранилища информации о восстановлении BitLocker - > Разрешить 256-битный ключ восстановления.](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings) Шифрование azure Disk выходит из строя, когда настройки политики группы для BitLocker несовместимы. На машинах, не имеющихся правильной настройки политики, применяйте новую политику, заставьте новую политику обновить (gpupdate.exe/force), а затем перезапустите, если это необходимо. 

## <a name="encryption-key-storage-requirements"></a>Требования к хранению ключей шифрования 

Шифрование azure Disk требует, чтобы Azure Key Vault контролировал а также управлялключами и секретами шифрования дисков. Хранилище ключей и вс-м ввода м ввода-возах должны находиться в одном и том же регионе Azure и подписке.

Для получения дополнительной информации [см. Создание и настройка хранилища ключей для шифрования дисков Azure с Azure AD (предыдущий релиз)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Следующие шаги

- [Создание и настройка хранилища ключей для шифрования дисков Azure с помощью Azure AD (предыдущий релиз)](disk-encryption-key-vault-aad.md)
- [Включить шифрование azure disk с Azure AD на Linux VMs (предыдущий релиз)](disk-encryption-linux-aad.md)
- [Предпосылки для шифрования дисков Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Предпосылки для шифрования дисков Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)