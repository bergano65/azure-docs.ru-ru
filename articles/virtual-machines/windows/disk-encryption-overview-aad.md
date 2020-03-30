---
title: Шифрование диска Azure с Azure AD (предыдущий релиз)
description: Эта статья содержит предварительные требования для использования шифрования дисков Microsoft Azure с виртуальными машинами IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 33b257e9d344fc31df072509f105d2e8fd1bd29b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72245179"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Шифрование диска Azure с Azure AD (предыдущий релиз)

**Новый выпуск шифрования Azure Disk устраняет необходимость предоставления параметра приложения Azure AD для включения шифрования дисков VM. С новым выпуском на этапе шифрования больше не требуется предоставлять учетные данные Azure AD. Все новые VMs должны быть зашифрованы без параметров приложения Azure AD с помощью нового релиза. Для просмотра инструкций по шифрованию vM-диска с помощью нового релиза [см.](disk-encryption-overview.md) VMs, которые уже были зашифрованы параметрами приложения Azure AD, по-прежнему поддерживаются и должны продолжать поддерживаться синтаксисом AAD.**

Эта статья дополняет [шифрование Azure Disk для Windows VMs](disk-encryption-overview.md) дополнительными требованиями и предпосылками для шифрования Azure Disk с Azure AD (предыдущий релиз). [Поддерживаемый внесп ы и](disk-encryption-overview.md#supported-vms-and-operating-systems) секция операционных систем остается прежней.

## <a name="networking-and-group-policy"></a> Сетевые подключения и групповая политика

**Чтобы использовать функцию шифрования дисков Azure с применением старого синтаксиса параметров AAD, конфигурация конечной точки сети виртуальной машины IaaS должна соответствовать приведенным ниже требованиям:** 
  - Виртуальная машина IaaS должна иметь возможность подключиться к конечной точке Azure Active Directory \[login.microsoftonline.com\], чтобы получить маркер для подключения к хранилищу ключей.
  - Для записи ключей шифрования в ваше хранилище ключей виртуальная машина IaaS должна иметь возможность подключиться к конечной точке хранилища ключей.
  - Виртуальная машина IaaS должна иметь возможность подключиться к конечной точке службы хранилища Azure, в которой размещен репозиторий расширений Azure, и к учетной записи хранения Azure, в которой размещены VHD-файлы.
  -  Если ваша политика безопасности ограничивает доступ к Интернету с виртуальных машин Azure, можно разрешить указанный выше универсальный код ресурса (URI) и настроить определенное правило, чтобы разрешить исходящие подключения к данным IP-адресам. Дополнительные сведения см. в статье [Доступ к Azure Key Vault из-за брандмауэра](../../key-vault/key-vault-access-behind-firewall.md).
  - Если в Windows протокол TLS 1.0 был явно отключен и платформа .NET не была обновлена до версии 4.6 или более поздней, при следующем изменении в реестре с помощью ADE можно будет выбрать более позднюю версию TLS:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Групповая политика:**
 - Решение шифрования дисков Azure использует внешний предохранитель ключа BitLocker для виртуальных машин IaaS под управлением Windows. Если виртуальные машины присоединены к домену, не применяйте групповые политики, требующие использования предохранителей TPM. Сведения о групповой политике "Разрешить использование BitLocker без совместимого TPM" см. в [справке по групповым политикам BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Политика BitLocker в отношении домена, объединяемого виртуальными машинами с пользовательской групповой политикой, должна включать в себя следующие настройки: [Настройка пользовательского хранилища информации о восстановлении BitLocker -> Разрешить 256-битный ключ восстановления.](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings) Шифрование дисков Azure завершится ошибкой, если параметры настраиваемой групповой политики для BitLocker несовместимы. На компьютерах без соответствующего параметра политики может потребоваться применить новую политику, принудительно обновить ее (gpupdate.exe /force) и перезагрузить компьютер.  

## <a name="encryption-key-storage-requirements"></a>Требования к хранению ключей шифрования  

Шифрование azure Disk требует, чтобы Убежище azure Key Vault контролировало и управляло ключами и секретами шифрования дисков. Хранилище ключей и вс-м ввода м ввода-возах должны находиться в одном и том же регионе Azure и подписке.

Для получения подробной информации [см. Создание и настройка хранилища ключей для шифрования дисков Azure с Azure AD (предыдущий релиз)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Дальнейшие действия

- [Создание и настройка хранилища ключей для шифрования дисков Azure с помощью Azure AD (предыдущий релиз)](disk-encryption-key-vault-aad.md)
- [Включить шифрование azure disk с Azure AD на Windows VMs (предыдущий релиз)](disk-encryption-windows-aad.md)
- [Предпосылки для шифрования дисков Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Предпосылки для шифрования дисков Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)