---
title: Правила брандмауэра — база данных Azure для MySQL
description: Узнайте, как использовать правила брандмауэра, чтобы разрешить подключения к серверу базы данных Azure для MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 6b78c3ffa11d481c77f393bbbd156f2ba76f36e0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770294"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Правила брандмауэра сервера базы данных Azure для MySQL
Брандмауэр запрещает любой доступ к серверу базы данных, пока вы не укажете компьютеры, у которых есть разрешение на доступ. Брандмауэр предоставляет доступ к серверу на основе исходного IP-адреса каждого запроса.

Для настройки брандмауэра можно создать правила брандмауэра, которые указывают диапазон допустимых IP-адресов. Правила брандмауэра можно создавать на уровне сервера.

**Правила брандмауэра:** эти правила разрешают клиентам доступ ко всему серверу базы данных Azure для MySQL, то есть ко всем базам данных на одном логическом сервере. Правила брандмауэра уровня сервера можно настроить на портале Azure или с помощью команд Azure CLI. Для создания правил брандмауэра уровня сервера необходимо быть владельцем или участником подписки.

## <a name="firewall-overview"></a>Общие сведения о брандмауэре
По умолчанию весь доступ к базам данных на сервере базы данных Azure для MySQL блокируется брандмауэром. Чтобы использовать сервер с другого компьютера, сначала необходимо указать одно или несколько правил брандмауэра уровня сервера для обеспечения доступа к этому серверу. Используйте правила брандмауэра, чтобы определить разрешенные диапазоны IP-адресов из Интернета. Правила брандмауэра не повлияют на доступ к самому веб-сайту портала Azure.

Запросы на подключение из Интернета и Azure должны сначала обрабатываться брандмауэром и только потом достигать базы данных Azure для MySQL, как показано на следующей схеме:

![Пример рабочего потока брандмауэра](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Подключение через Интернет
Правила брандмауэра уровня сервера применяются ко всем базам данных на сервере базы данных Azure для MySQL.

Если IP-адрес запроса находится в одном из диапазонов, указанных в правилах брандмауэра уровня сервера, то подключение предоставляется.

Если IP-адрес запроса находится вне диапазонов, указанных в любом из правил брандмауэра уровня базы данных или уровня сервера, то запрос на подключение отклоняется.

## <a name="connecting-from-azure"></a>Подключение из Azure
Чтобы приложения из Azure могли подключаться к серверу базы данных Azure для MySQL, необходимо разрешить подключения Azure. Например, это необходимо для размещения веб-приложения Azure или приложения, которое выполняется на виртуальной машине Azure, либо для подключения из шлюза управления данными фабрики данных Azure. Чтобы разрешить эти подключения с помощью правила брандмауэра, ресурсы не обязательно должны находиться в одной и той же виртуальной сети или группе ресурсов. Когда приложение из Azure пытается подключиться к серверу базы данных, брандмауэр проверяет, разрешены ли подключения Azure. Такие подключения можно разрешить с помощью нескольких способов. Параметр брандмауэра с начальным и конечным адресом, равным 0.0.0.0, указывает, что эти подключения разрешены. Кроме того, в области **Безопасность подключения** на портале для параметра **Разрешить доступ к службам Azure** можно задать значение **Вкл.** , а затем нажать кнопку **Сохранить**. Если попытка подключения не разрешена, запрос не достигает сервера базы данных Azure для MySQL.

> [!IMPORTANT]
> Этот параметр позволяет настроить брандмауэр так, чтобы разрешить все подключения из Azure, включая подключения из подписок других клиентов. При выборе этого параметра убедитесь, что используемое имя для входа и разрешения пользователя предоставляют доступ только авторизованным пользователям.
> 

![Настройка разрешения доступа к службам Azure на портале](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Подключение из виртуальной сети
Чтобы безопасно подключиться к серверу базы данных Azure для MySQL из виртуальной сети, рассмотрите возможность использования [конечных точек службы виртуальной сети](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Программное управление правилами брандмауэра
Помимо портала Azure правилами брандмауэра можно управлять программно с помощью Azure CLI. Ознакомьтесь также со статьей [Создание правил брандмауэра базы данных Azure для MySQL и управление ими с помощью Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Устранение проблем с брандмауэром
Если доступ к службе сервера базы данных Microsoft Azure для MySQL не работает должным образом, необходимо учитывать следующее.

* **Изменения в списке разрешенных адресов еще не вступили в силу.** До вступления в силу изменений конфигурации брандмауэра сервера базы данных Azure для MySQL может присутствовать задержка до пяти минут.

* **Имя для входа не авторизовано, или использован неправильный пароль.** Если имя для входа не имеет разрешений на сервере базы данных Azure для MySQL или введен неправильный пароль, то подключение к серверу базы данных Azure для MySQL отклоняется. Создание параметра брандмауэра только предоставляет клиентам возможность подключения к серверу. Каждый клиент должен предоставить необходимые учетные данные безопасности.

* **Динамический IP-адрес:** при наличии подключения к Интернету с динамическим предоставлением IP-адресов и возникновении проблем с прохождением через брандмауэр попробуйте одно из описанных ниже решений.

   * Попросите поставщика услуг Интернета (ISP) назначить диапазон IP-адресов тем клиентским компьютерам, с которых осуществляется доступ к серверу базы данных Azure для MySQL, а затем добавьте диапазон IP-адресов в качестве правила брандмауэра.

   * Получите статические IP-адреса для клиентских компьютеров, а затем добавьте IP-адреса как правила брандмауэра.

* **IP-адрес сервера выглядит как общедоступный:** Подключения к серверу базы данных Azure для MySQL направляются через общедоступный шлюз Azure. Однако фактический IP-адрес сервера защищен брандмауэром. Дополнительные сведения см. в статье об [архитектуре подключения](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Дальнейшие действия

* [Создание правил брандмауэра базы данных Azure для MySQL и управление ими с помощью портала Azure](./howto-manage-firewall-using-portal.md)
* [Создание правил брандмауэра базы данных Azure для MySQL и управление ими с помощью Azure CLI](./howto-manage-firewall-using-cli.md)
- [Конечные точки службы виртуальной сети в базе данных Azure для MySQL](./concepts-data-access-and-security-vnet.md)
