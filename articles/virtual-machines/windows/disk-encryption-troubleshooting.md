---
title: Руководство по устранению неполадок шифрования дисков Azure
description: В этой статье содержатся советы по устранению неполадок для шифрования дисков Microsoft Azure для Windows VMs.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: fafe4a9ef78a92595eaae942fa5d7cbeb2c07189
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458221"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Руководство по устранению неполадок шифрования дисков Azure

Это руководство предназначено для ИТ-специалистов, аналитиков в сфере информационной безопасности и администраторов облака, организации которых используют шифрование дисков Azure. Здесь также содержатся рекомендации по устранению неполадок, связанных с шифрованием дисков.

Прежде чем сделать любой из приведенных ниже шагов, сначала убедитесь, что ВМ, которые вы пытаетесь шифровать, относятся к [поддерживаемым размерам VM и операционным системам,](disk-encryption-overview.md#supported-vms-and-operating-systems)и что вы выполнили все предпосылки:

- [Требования к сети](disk-encryption-overview.md#networking-requirements)
- [Требования к групповой политике](disk-encryption-overview.md#group-policy-requirements)
- [Требования к хранению ключей шифрования](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Устранение неполадок шифрования диска Azure в связи с брандмауэром

Если подключение ограничивается брандмауэром, требованиями прокси-сервера или параметрами группы безопасности сети (NGS), способности расширения выполнять необходимые задачи могут быть нарушены. В результате может появиться такое сообщение о состоянии: "Сведения о состоянии расширения недоступны на виртуальной машине". В предполагаемых сценариях может произойти сбой шифрования. В следующих разделах можно ознакомиться с некоторыми распространенными проблемами с брандмауэром.

### <a name="network-security-groups"></a>Группы безопасности сети
Любые применяемые параметры группы безопасности сети должны позволять конечной точке соответствовать предусмотренным [предварительным требованиям](disk-encryption-overview.md#networking-requirements) к конфигурации сети для шифрования диска.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault за брандмауэром

При включении шифрования [учетных данных Azure AD](disk-encryption-windows-aad.md#) целевая виртуальная машина должна обеспечить подключение к конечным точкам Azure Active Directory и конечным точкам хранилища ключей. Текущие конечные точки проверки подлинности Azure Active Directory поддерживаются в разделах 56 и 59 статьи [URL-адреса и диапазоны IP-адресов Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges). Инструкции Key Vault приведены в статье [Доступ к хранилищу ключей Azure из-за брандмауэра](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Служба метаданных экземпляров Azure 
Виртуальная машина должна иметь доступ к конечной точке [службы метаданных экземпляров Azure](../windows/instance-metadata-service.md), использующей известный немаршрутизируемый IP-адрес (`169.254.169.254`), доступ к которому можно получить только из виртуальной машины.  Прокси-конфигурации, изменяющие локальный трафик HTTP на этот адрес (например, добавление X-Forwarded-For заголовка), не поддерживаются.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Устранение неполадок в основных серверных компонентах Windows Server 2016

По умолчанию компонент bdehdcfg недоступен в основных серверных компонентах Windows Server 2016. Этот компонент требуется для шифрования дисков Azure. Он используется для разбиения системного тома и тома операционной системы (выполняется только один раз за все время существования виртуальной машины). Эти двоичные файлы не требуются во время последующих операций шифрования.

Чтобы решить эту проблему, скопируйте указанные ниже четыре файла из виртуальной машины центра обработки данных Windows Server 2016 в то же расположение (основные серверные компоненты):

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Введите следующую команду:

   ```
   bdehdcfg.exe -target default
   ```

1. С помощью этой команды создается системный раздел на 550 МБ. Перезагрузите систему.

1. Используйте DiskPart, чтобы проверить тома, а затем продолжайте.  

Пример:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Устранение неполадок с состоянием шифрования 

Портал может отображать диск в зашифрованном виде даже после того, как он был незашифрован в Пределах VM.  Это может произойти, когда команды низкого уровня используются для прямого расшифровки диска из VM, вместо того, чтобы использовать команды управления шифрованием azure Disk более высокого уровня.  Команды более высокого уровня не только не шифруют диск из VM, но и за пределами VM, но и обновляют важные настройки шифрования уровня платформы и настройки расширения, связанные с VM.  Если они не будут согласованы, платформа не сможет сообщить о состоянии шифрования или предоставить VM должным образом.   

Чтобы отключить шифрование azure Disk с помощью PowerShell, используйте [Disable-AzVMDiskEncryption,](/powershell/module/az.compute/disable-azvmdiskencryption) за которым следует [Удалить-AzVMDiskEncryptionExtension.](/powershell/module/az.compute/remove-azvmdiskencryptionextension) Запуск Remove-AzVMDiskEncryptionExtension до отключения шифрования не удастся.

Чтобы отключить шифрование Azure Disk с помощью CLI, используйте [az vm шифрование отключите.](/cli/azure/vm/encryption) 

## <a name="next-steps"></a>Следующие шаги

В этом документе вы узнали о некоторых распространенных проблемах в шифровании дисков Azure и их устранении. Дополнительные сведения об этой службе и ее возможностях см. в статьях:

- [Шифрование диска в центре безопасности Azure](../../security-center/security-center-apply-disk-encryption.md)
- [Шифрование неактивных данных в Azure](../../security/fundamentals/encryption-atrest.md)
