---
title: Шифрование дисков Azure в изолированной сети
description: В этой статье содержатся советы по устранению неполадок Microsoft Azure шифровании дисков для виртуальных машин Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: aa0dc204a017e2d40eb3952a9ede0755127f8de2
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970662"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Шифрование дисков Azure в изолированной сети

Если подключение ограничивается брандмауэром, требованиями прокси-сервера или параметрами группы безопасности сети (NGS), способности расширения выполнять необходимые задачи могут быть нарушены. В результате может появиться такое сообщение о состоянии: "Сведения о состоянии расширения недоступны на виртуальной машине".

## <a name="package-management"></a>Управление пакетами

Шифрование дисков Azure зависит от ряда компонентов, которые обычно устанавливаются как часть включения ADE, если они еще не установлены. Если за брандмауэром или иным способом изолировано от Интернета, эти пакеты должны быть предварительно установлены или доступны локально.

Ниже приведены пакеты, необходимые для каждого распределения. Полный список поддерживаемых дистрибутивов и типов томов см. в статье [Поддерживаемые виртуальные машины и операционные системы](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14,04, 16,04, 18,04**: lsscsi, псмиск, at, криптсетуп-bin, Python — часть, Python-шесть, прокпс
- **CentOS 7,2-7,7**: lsscsi, псмиск, lvm2, UUID, at, исправление, криптсетуп, криптсетуп-reencrypt, пипартед, прокпс-NG, util-Linux
- **CentOS 6,8**: lsscsi, псмиск, lvm2, UUID, в, криптсетуп-reencrypt, пипартед, Python-Six
- **RedHat 7,2-7,7**: lsscsi, псмиск, lvm2, UUID, at, исправление, криптсетуп, криптсетуп-reencrypt, прокпс-NG, util-Linux
- **RedHat 6,8**: lsscsi, псмиск, lvm2, UUID, в, Patch, криптсетуп — повторное шифрование
- **openSUSE 42,3, SLES 12 — SP4, 12-SP3**: lsscsi, криптсетуп

В Red Hat, когда требуется прокси-сервер, необходимо убедиться в правильности настроек диспетчера подписок и yum. Дополнительные сведения см. в статье [How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533) (Устранение неполадок диспетчера подписки и yum).  

Если пакеты устанавливаются вручную, их также необходимо обновить вручную по мере выпуска новых версий.

## <a name="network-security-groups"></a>Группы безопасности сети
Все примененные параметры группы безопасности сети должны разрешать конечной точке отвечать на описанные предварительные требования к конфигурации сети для шифрования дисков.  См [. раздел шифрование дисков Azure: требования к сети](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Шифрование дисков Azure с помощью Azure AD (Предыдущая версия)

При использовании [шифрования дисков Azure с Azure AD (Предыдущая версия)](disk-encryption-overview-aad.md)необходимо вручную установить [библиотеку Azure Active Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) для всех дистрибутивов (в дополнение к пакетам, подходящим для дистрибутив, как [указано выше](#package-management)).

При включении шифрования [учетных данных Azure AD](disk-encryption-linux-aad.md) целевая виртуальная машина должна обеспечить подключение к конечным точкам Azure Active Directory и конечным точкам хранилища ключей. Текущие конечные точки проверки подлинности Azure Active Directory поддерживаются в разделах 56 и 59 статьи [URL-адреса и диапазоны IP-адресов Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges). Инструкции Key Vault приведены в статье [Доступ к хранилищу ключей Azure из-за брандмауэра](../../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Служба метаданных экземпляров Azure 

Виртуальная машина должна иметь доступ к конечной точке [службы метаданных экземпляра Azure](instance-metadata-service.md) , которая использует известный IP-адрес без поддержки маршрутизации (`169.254.169.254`), доступ к которому возможен только из виртуальной машины.  Конфигурации прокси-сервера, которые изменяют локальный трафик HTTP к этому адресу (например, Добавление заголовка X-Forwardd-for), не поддерживаются.

## <a name="next-steps"></a>Дальнейшие действия

- См. дополнительные шаги по [устранению неполадок шифрования дисков Azure](disk-encryption-troubleshooting.md)
- [Шифрование неактивных данных в Azure](../../security/fundamentals/encryption-atrest.md)
