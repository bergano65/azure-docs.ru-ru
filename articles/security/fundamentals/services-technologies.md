---
title: Службы и технологии обеспечения безопасности Azure | Документация Майкрософт
description: В статье представлен проверенный список статей о службах и технологиях обеспечения безопасности в Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 741e6b9cad20645fdfc085623fd9492a12ac1dfc
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726562"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Службы и технологии безопасности, доступные в Azure

Имеющиеся и будущие пользователи Azure часто спрашивают нас о списке доступных в Azure служб и технологий, связанных с обеспечением безопасности.

При оценке поставщиков облачных служб полезно иметь следующие сведения. Поэтому мы предоставили этот список, чтобы помочь вам приступить к работе.

Со временем этот список будет дополнен — также, как и линейка предложений Azure. Просматривайте эту страницу время от времени, чтобы быть в курсе последних новостей о наших услугах и технологиях, связанных с безопасностью.

## <a name="general-azure-security"></a>Общая безопасность Azure
|Служба|Описание|
|--------|--------|
|[Центр&nbsp;безопасности&nbsp;Azure](/azure/security-center/security-center-intro)| Облачное решение защиты рабочих нагрузок обеспечивает управление безопасностью и расширенную защиту от угроз для гибридных облачных рабочих нагрузок.|
|[Хранилище ключей Azure](/azure/key-vault/key-vault-overview)| Защищенное хранилище секретов для паролей, строк подключения и другой информации, необходимой для работы приложения. |
|[Журналы Azure Monitor](/azure/log-analytics/log-analytics-overview)|Служба мониторинга собирает телеметрию и другие данные, а также предоставляет язык запросов и модуль аналитики для доставки оперативных аналитических сведений для приложений и ресурсов. Решение можно использовать отдельно или совместно с другими службами, например центром обеспечения безопасности. |
|[Лаборатория для разработки и тестирования Azure](/azure/lab-services/devtest-lab-overview)|Это служба, помогающая разработчикам и тест-инженерам быстро создавать среды в Azure при минимальных потерях и контроле издержек.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Безопасность хранилищ
|Служба|Описание|
|------|--------|
| [Шифрование&nbsp;службы&nbsp;хранения&nbsp;Azure](/azure/storage/common/storage-service-encryption)|Функция безопасности, которая автоматически шифрует данные в хранилище Azure.   |
|[Гибридное хранилище, зашифрованное с помощью StorSimple](/azure/storsimple/storsimple-ova-overview)| Решение интегрированного хранилища, которое управляет задачами хранилища на локальных устройствах и в облачном хранилище Azure.|
|[Шифрование Azure на стороне клиента](/azure/storage/common/storage-client-side-encryption)| Решение шифрования на стороне клиента, которое шифрует данные в клиентских приложениях перед передачей их в службу хранилища Azure, а также расшифровывает данные при скачивании. |
| [Подписанные URL-адреса службы хранилища Azure](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения.  |
|[Ключи учетной записи хранения Azure](/azure/storage/common/storage-create-storage-account)| Метод управления доступом к службе хранилища Azure, используемый для аутентификации при получении доступа к учетной записи хранения. |
|[Разработка для хранилища файлов Azure с помощью .NET](/azure/storage/files/storage-files-introduction)|Технология безопасности сети, которая обеспечивает автоматическое сетевое шифрование для протокола общего доступа к файлам SMB. |
|[Аналитика службы хранилища Azure](/rest/api/storageservices/Storage-Analytics)| Технология ведения журнала и создания метрики для данных в учетной записи хранения. |

<!------>

## <a name="database-security"></a>Безопасность базы данных
|Служба|Описание|
|------|--------|
| [Брандмауэр&nbsp;SQL&nbsp;Azure](/azure/sql-database/sql-database-firewall-configure)|Функция управления доступом сети, обеспечивающая защиту от сетевых атак базы данных. |
|[Уровень шифрования&nbsp;ячейки&nbsp;SQL&nbsp;Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Технология безопасности базы данных, которая обеспечивает шифрование на более детальном уровне.  |
| [Шифрование подключений&nbsp;SQL&nbsp;Azure](/azure/sql-database/sql-database-control-access)|Чтобы обеспечить безопасность, база данных SQL управляет доступом с помощью правил брандмауэра, ограничивающих подключение по IP-адресу, механизмов проверки подлинности, требующих удостоверений пользователей, и методов авторизации, ограничивающих действия и данные для пользователей. |
| [Постоянное шифрование в Azure SQL](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Защищает конфиденциальные данные, такие как номера кредитных карт или номера национальной идентификации (например, номера социального страхования США), которые хранятся в базах данных SQL Server и базах данных SQL Azure.  |
| [Прозрачное шифрование данных&nbsp;SQL&nbsp;Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Компонент безопасности базы данных, который шифрует хранилище всей базы данных. |
| [Аудит базы данных SQL Azure](/azure/sql-database/sql-database-auditing)|Возможность аудита базы данных SQL, позволяющая отслеживать события базы данных и записывать их в журнал аудита в учетной записи хранения Azure.  |


## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом
|Служба|Описание|
|------|--------|
| [Управление доступом&nbsp;на основе&nbsp;ролей&nbsp;Azure](/azure/active-directory/role-based-access-control-configure)|Функция контроля доступа, которая позволяет предоставлять пользователям доступ только к ресурсам, доступ к которым основан на их ролях в рамках организации.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Облачный репозиторий аутентификации, который поддерживает несколько клиентов, облачный каталог и несколько служб управления удостоверениями в рамках Azure.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Это служба управления идентификацией, которая позволяет администрировать процессы входа и регистрации пользователей, а также управлять их профилями при использовании приложений на основе Azure.   |
| [Доменные службы Azure Active Directory](/azure/active-directory-domain-services/overview)| Облачная и управляемая версия доменных служб Active Directory. |
| [Многофакторная идентификация Azure](/azure/active-directory/authentication/multi-factor-authentication)| Подготовка безопасности, которая использует несколько различных форм аутентификации и проверки перед предоставлением защищенной информации. |

## <a name="backup-and-disaster-recovery"></a>Резервное копирование и аварийное восстановление
|Служба|Описание|
|------|--------|
| [Azure&nbsp;Backup](/azure/backup/backup-introduction-to-azure-backup)| Служба Azure, используемая для резервного копирования и восстановления данных в облаке Azure. |
| [Azure&nbsp;Site&nbsp;Recovery](/azure/site-recovery/site-recovery-overview)|Онлайн-служба, которая реплицирует рабочие нагрузки, выполняемые на физических и виртуальных машинах, с основного сайта в дополнительное расположение, чтобы включить восстановление служб после сбоя. |

## <a name="networking"></a>Сети
|Служба|Описание|
|------|--------|
| [Группы&nbsp;безопасности&nbsp;сети](/azure/virtual-network/virtual-networks-nsg)| Функция управления доступом на основе сети, использующая 5 кортежей для разрешения и запрета.  |
| [VPN-шлюз Azure](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| Сетевое устройство, используемое в качестве конечной точки VPN для обеспечения доступа между локальными сетями к виртуальным сетям Azure.  |
| [Шлюз приложений Azure](/azure/application-gateway/application-gateway-introduction)|Расширенный балансировщик нагрузки веб-приложения, который может выполнять маршрутизацию на основе URL-адреса и выполнять SSL-разгрузку. |
|[Брандмауэр веб-приложения](/azure/frontdoor/waf-overview) (WAF)|Компонент шлюза приложений для централизованной защиты веб-приложений от распространенных эксплойтов и уязвимостей.|
| [Подсистема балансировщика нагрузки Azure](/azure/load-balancer/load-balancer-overview)|Подсистема балансировки нагрузки сети приложения TCP/UDP. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| Выделенная ссылка WAN между локальными сетями и виртуальными сетями Azure. |
| [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)| Балансировщик нагрузки глобальной DNS.|
| [Прокси приложения Azure](/azure/active-directory/active-directory-application-proxy-get-started)| Внешний интерфейс аутентификации, используемый для защиты удаленного доступа к веб-приложениям, размещенным локально. |
|[Брандмауэр Azure](/azure/firewall/overview)|Управляемая облачная служба сетевой безопасности, которая защищает ресурсы виртуальной сети Azure.|
|[Защита от атак DDoS Azure](/azure/virtual-network/ddos-protection-overview)|Эта служба обеспечивает защиту от атак DDoS, если соблюдаются рекомендации по проектированию приложений.|
|[Конечные точки службы для виртуальной сети](/azure/virtual-network/virtual-network-service-endpoints-overview)|Расширяют пространство частных адресов и возможности идентификации вашей виртуальной сети в службах Azure благодаря прямому соединению.|