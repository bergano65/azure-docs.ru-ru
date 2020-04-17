---
title: Шифрование azure disk в изолированной сети
description: В этой статье содержатся советы по устранению неполадок для шифрования дисков Microsoft Azure для Linux VMs.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: f2b84427b9aad2d18368d808fc618f3bfbe774ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460126"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Шифрование azure disk в изолированной сети

Если подключение ограничивается брандмауэром, требованиями прокси-сервера или параметрами группы безопасности сети (NGS), способности расширения выполнять необходимые задачи могут быть нарушены. В результате может появиться такое сообщение о состоянии: "Сведения о состоянии расширения недоступны на виртуальной машине".

## <a name="package-management"></a>Управление пакетами

Шифрование azure Disk зависит от ряда компонентов, которые обычно устанавливаются как часть aDE-возможности, если они еще не присутствуют. Когда за брандмауэром или иным образом изолированы от Интернета, эти пакеты должны быть предварительно установлены или доступны локально.

Вот пакеты, необходимые для каждого распределения. Полный список поддерживаемых дистро и [supported VMs and operating systems](disk-encryption-overview.md#supported-vms-and-operating-systems)типов громкости см.

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, патч, cryptsetup, cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, патч, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, патч, криптоустановка-решифровка
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

В Red Hat, когда требуется прокси-сервер, необходимо убедиться в правильности настроек диспетчера подписок и yum. Дополнительные сведения см. в статье [How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533) (Устранение неполадок диспетчера подписки и yum).  

Когда пакеты устанавливаются вручную, они также должны быть обновлены вручную по мере выпуска новых версий.

## <a name="network-security-groups"></a>Группы безопасности сети
Любые применяемые параметры группы безопасности сети должны позволять конечной точке соответствовать предусмотренным предварительным требованиям к конфигурации сети для шифрования диска.  Посмотреть [шифрование дисков Azure: Требования к сети](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Шифрование диска Azure с Azure AD (предыдущая версия)

При использовании [Azure Disk Encryption с Azure AD (предыдущая версия)](disk-encryption-overview-aad.md) [библиотека Active Directory Library Должна](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) быть установлена вручную для всех дистрибутивов (в дополнение к пакетам, подходящим для дистрибутива, как [указано выше).](#package-management)

При включении шифрования [учетных данных Azure AD](disk-encryption-linux-aad.md) целевая виртуальная машина должна обеспечить подключение к конечным точкам Azure Active Directory и конечным точкам хранилища ключей. Текущие конечные точки проверки подлинности Azure Active Directory поддерживаются в разделах 56 и 59 статьи [URL-адреса и диапазоны IP-адресов Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges). Инструкции Key Vault приведены в статье [Доступ к хранилищу ключей Azure из-за брандмауэра](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Служба метаданных экземпляров Azure 

Виртуальная машина должна иметь доступ к конечному пункту [службы Metadata Azure Instance,](instance-metadata-service.md) `169.254.169.254`которая использует известный IP-адрес non-routable , доступ к которому можно получить только из ВМ.  Прокси-конфигурации, изменяющие локальный трафик HTTP на этот адрес (например, добавление X-Forwarded-For заголовка), не поддерживаются.

## <a name="next-steps"></a>Следующие шаги

- Посмотреть дополнительные шаги для [устранения неполадок шифрования дисков Azure](disk-encryption-troubleshooting.md)
- [Шифрование неактивных данных в Azure](../../security/fundamentals/encryption-atrest.md)
