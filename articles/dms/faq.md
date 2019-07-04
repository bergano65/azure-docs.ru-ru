---
title: Часто задаваемые вопросы о службе Azure Database Migration Service | Документация Майкрософт
description: Дополнительные часто задаваемые вопросы об использовании Azure Database Migration Service для переноса базы данных.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/04/2019
ms.openlocfilehash: f59af0bd0297b0413673fc42b26654c36351b815
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566244"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Часто задаваемые вопросы о службе Azure Database Migration Service

В этой статье приведены часто задаваемые вопросы об использовании Azure Database Migration Service вместе с соответствующими ответами.

### <a name="q-what-is-azure-database-migration-service"></a>В. Что такое служба Azure Database Migration Service?

Azure Database Migration Service — это полностью управляемая служба, которая выполняет непрерывную миграцию из множества источников баз данных на платформы данных Azure с минимальным временем простоя. Служба, которая сейчас предоставляется в режиме общедоступной версии, оптимизируется для включения следующих возможностей:

* надежность и производительность;
* итеративное добавление пар исходного и целевого объектов;
* работа над функциями непрерывной миграции.

### <a name="q-what-source-target-pairs-does-azure-database-migration-service-currently-support"></a>В. Какие пары исходного и целевого Azure Database Migration Service в настоящее время поддерживает?

Сейчас служба поддерживает множество сценариев миграции. Полный список состояний всех доступных сценариев миграции см. в статье [Состояние сценариев миграции, поддерживаемых службой Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status). Другие сценарии миграции доступны в предварительной версии и требуется отправлять заявку через узел DMS предварительной версии. Полный список сценариев, в предварительной версии и чтобы зарегистрироваться для участия в одном из этих предложений см. в разделе [сайт с обзором DMS](https://aka.ms/dms-preview/).

### <a name="q-how-does-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>В. Как служба Azure Database Migration Service соотносится другие инструменты для миграции базы данных Майкрософт, таких как базы данных Migration Assistant (DMA) или SQL Server Migration Assistant (SSMA)?

Azure Database Migration Service является предпочтительным для миграции базы данных в Microsoft Azure в нужном масштабе. Дополнительные сведения о сравнение Azure Database Migration Service с другого Майкрософт инструментами переноса базы данных и рекомендации по использованию службы для различных сценариев, см. в блоге [запоминающихся Microsoft миграции баз данных Инструменты и службы](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>В. Как служба Azure Database Migration Service соотносится с предложением "Миграция Azure"?

Служба "Миграция Azure" помогает выполнять миграцию локальных виртуальных машин в Azure IaaS. Оценивается пригодность для миграции, показатели производительности, на основе которых определяется требуемый размер, и расходы на работу локальных виртуальных машин в Azure. Служба "Миграция Azure" подходит для переноса рабочих нагрузок на локальных виртуальных машинах на виртуальные машины IaaS Azure методом lift-and-shift. Однако в отличие от Azure Database Migration Service, "Миграция Azure" не услуга миграции специализированные базы данных для платформ реляционных баз данных PaaS в Azure, таких как базы данных SQL Azure или Azure базы данных SQL управляемого экземпляра.

### <a name="q-what-versions-of-sql-server-does-azure-database-migration-service-support-as-a-source"></a>В. Какие версии SQL Server поддерживает Azure Database Migration Service как источник?

При миграции из SQL Server, Azure Database Migration Service поддерживает SQL Server 2005 до SQL Server 2017.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-azure-database-migration-service-to-perform-a-database-migration"></a>В. Что такое Сводка действий, необходимых для использования службы Azure Database Migration Service для переноса базы данных?

Во время обычного переноса базы данных вы:

1. Создаете целевую базу данных.
2. Переносите схему базы данных с помощью [Database Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3. создание экземпляра Azure Database Migration Service;
4. Создаете проект миграции, указав исходную базу данных, целевую базу данных и таблицы для переноса.
5. Запуск полной загрузки.
6. Выбираете последующую проверку.
7. Вручную переключаете рабочую среду на использование новой облачной базы данных.

### <a name="q-what-are-the-prerequisites-for-using-azure-database-migration-service"></a>В. Каковы предварительные требования для использования службы Azure Database Migration Service?

Существует несколько предварительных условий, необходимо убедиться, что Azure Database Migration Service прошел при переносе баз данных. Некоторые предварительные требования применяются во всех сценариях (пар исходного и целевого объектов), поддерживаемых службой, тогда как другие уникальны и используются в определенных сценариях.

Ниже приведены предварительные требования для использования службы Azure Database Migration Service, которые применяются во всех поддерживаемых сценариях миграции.

* Создание виртуальной сети для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая предоставляет возможность подключения сеть сеть к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Убедитесь, что виртуальной сети Azure (VNet) правила группы безопасности сети не блокировать следующие порты связи 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Plan virtual networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Планирование виртуальных сетей).
* Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.

Список всех необходимых компонентов, необходимых для реализации определенного сценария миграции с помощью Azure Database Migration Service, см. в соответствующих руководствах Azure Database Migration Service [документации](https://docs.microsoft.com/azure/dms/dms-overview) на сайте docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>В. Как найти IP-адрес для Azure Database Migration Service, чтобы можно было создать список разрешений для правил брандмауэра, используемый для доступа к исходной базе данных для миграции?

Может потребоваться добавить правила брандмауэра, что позволяет Azure Database Migration Service доступ к вашей базы данных-источника для миграции. IP-адрес для этой службы является динамическим, однако при использовании Express Route этот адрес будет в закрытом порядке назначен корпоративной сети. Самый простой способ определить соответствующий IP-адрес находится в той же группе ресурсов, что и подготовленного ресурс Azure Database Migration Service и найти связанный сетевой интерфейс. Обычно имя ресурса сетевого интерфейса начинается с префикса NIC, за которым следует уникальная последовательность знаков и символов, например, NIC-jj6tnztnmarpsskr82rbndyp. Когда вы выберете этот ресурс, отобразится IP-адрес, который необходимо добавить в список разрешений на странице обзора ресурсов на портале Azure.

Вам также может потребоваться добавить в список разрешений исходный порт, с которого SQL Server ожидает передачи данных. По умолчанию — это порт 1433, однако исходный SQL Server также можно настроить на ожидание передачи данных с других портов. В этом случае необходимо добавить эти порты в список разрешений. Определить порт, который SQL Server прослушивает, можно с помощью запроса динамического административного представления:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Вы также можете определить порт, который SQL Server прослушивает, запросив журнал ошибок SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>В. Как настроить виртуальную сеть Azure?

В нескольких руководствах корпорации Майкрософт приведены пошаговые инструкции по настройке виртуальной сети Azure, а официальную документацию можно найти в [этой статье](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).


### <a name="q-im-setting-up-a-migration-project-in-dms-and-im-having-difficulty-connecting-to-my-source-database-what-should-i-do"></a>В. Я устанавливаю проект миграции в DMS, и у меня возникли затруднения при подключении к исходной базе данных. Что делать?

При наличии проблем с подключением к базы данных в исходной системе, во время работы по переносу, создание виртуальной машины в виртуальной сети, с помощью которого можно настроить экземпляр DMS. На виртуальной машине можно для запуска теста connect, например с помощью UDL-файл, чтобы проверить подключение к SQL Server или загрузка Robo 3T для проверки подключения MongoDB. Если проверка подключения прошла успешно, не возникнут проблемы с подключением к вашей базы данных-источника. Если проверка подключения не удается, обратитесь к администратору сети.

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-azure-database-migration-service"></a>В. Существуют ли рекомендации по оптимизации производительности Azure Database Migration Service?

Вы можете выполнить ряд действий, чтобы ускорить перенос базы данных с использованием службы:

* используйте ценовую категорию общего назначения с несколькими ЦП при создании экземпляра службы, чтобы обеспечить параллелизацию и ускоренную передачу данных благодаря использованию нескольких ЦП;
* временно увеличьте масштаб целевого экземпляра Базы данных SQL Azure до SKU уровня "Премиум" во время операции переноса данных, чтобы уменьшить регулирование количества запросов базы данных SQL Azure, которое может повлиять на действия передачи данных при использовании номеров SKU низкого уровня.

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>В. Почему моя служба Azure Database Migration Service недоступна или остановлена?

Если пользователь явно останавливает службу Azure Database Migration Service (DMS), или если она неактивна в течение 24 часов, она будет находиться в состоянии остановки или автоматической приостановки. В каждом случае служба будет недоступна и остановлена.  Чтобы возобновить активную миграцию, перезапустите службу.

### <a name="q-where-can-i-leave-feedback-about-azure-database-migration-service"></a>В. Где можно оставлять отзывы об Azure Database Migration Service?

Нам очень интересно ваше мнение! Отправить отзыв и / идеи у вас о Azure Database Migration Service с помощью User Voice [здесь](https://feedback.azure.com/forums/906100-azure-database-migration-service), или обратившись в команде в [попросите миграции базы данных Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="next-steps"></a>Дальнейшие действия

Общие сведения о службе Azure Database Migration Service и доступности по регионам см. в статье [Что такое Azure Database Migration Service](dms-overview.md).
