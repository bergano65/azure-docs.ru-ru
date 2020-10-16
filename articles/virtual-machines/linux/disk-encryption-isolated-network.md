---
title: Шифрование дисков Azure в изолированной сети
description: В этой статье вы узнаете советы по устранению неполадок Microsoft Azure шифровании дисков на виртуальных машинах Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 1157a18954c30e1f1d93aa33dbea6104491b5353
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396457"
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
Любые применяемые параметры группы безопасности сети должны позволять конечной точке соответствовать предусмотренным предварительным требованиям к конфигурации сети для шифрования диска.  См [. раздел шифрование дисков Azure: требования к сети](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Шифрование дисков Azure с помощью Azure AD (Предыдущая версия)

При использовании [шифрования дисков Azure с Azure AD (Предыдущая версия)](disk-encryption-overview-aad.md)необходимо вручную установить [библиотеку Azure Active Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) для всех дистрибутивов (в дополнение к пакетам, подходящим для дистрибутив, как [указано выше](#package-management)).

При включении шифрования [учетных данных Azure AD](disk-encryption-linux-aad.md) целевая виртуальная машина должна обеспечить подключение к конечным точкам Azure Active Directory и конечным точкам хранилища ключей. Текущие конечные точки проверки подлинности Azure Active Directory поддерживаются в разделах 56 и 59 документации по [URL-адресам Microsoft 365 и диапазонам IP-адресов](/microsoft-365/enterprise/urls-and-ip-address-ranges) . Инструкции Key Vault приведены в статье [Доступ к хранилищу ключей Azure из-за брандмауэра](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Служба метаданных экземпляров Azure 

Виртуальная машина должна иметь доступ к конечной точке [службы метаданных экземпляра Azure](instance-metadata-service.md) , которая использует известный IP-адрес без поддержки маршрутизации ( `169.254.169.254` ), доступ к которому возможен только из виртуальной машины.  Конфигурации прокси-сервера, которые изменяют локальный трафик HTTP к этому адресу (например, добавление заголовка X-Forwarded-For), не поддерживаются.

## <a name="next-steps"></a>Дальнейшие шаги

- См. дополнительные шаги по [устранению неполадок шифрования дисков Azure](disk-encryption-troubleshooting.md)
- [Шифрование неактивных данных в Azure](../../security/fundamentals/encryption-atrest.md)
