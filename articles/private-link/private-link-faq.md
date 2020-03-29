---
title: Azure Частная ссылка часто задают вопросы (Часто задаваемые вопросы)
description: Узнайте больше о частной ссылке Azure.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349943"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure Частная ссылка часто задают вопросы (Часто задаваемые вопросы)

## <a name="private-link"></a>Приватный канал

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Что такое служба частных конечных точек Azure и служба частных ссылк Azure?

- **[Azure Private Endpoint](private-endpoint-overview.md)**: Azure Private Endpoint — это сетевой интерфейс, который соединяет вас в частном порядке и надежно с службой, работающей на Azure Private Link. Для подключения к службе Azure PaaS можно подключиться к службе Azure PaaS, которая поддерживает частную ссылку, или к вашей собственной службе частных ссылок.
- **[Служба частных ссылк Azure:](private-link-service-overview.md)** Служба частной ссылки Azure — это служба, созданная поставщиком услуг. В настоящее время служба Private Link может быть прикреплена к конфигурации IP-адреса стандартного груза. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Как отправляется трафик при использовании Private Link?
Трафик отправляется в частном порядке с использованием магистрали Microsoft. Он не пересекает интернет.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>В чем разница между конечными точками обслуживания и частными конечными точками?
- При использовании Private Endpoints доступ к сети предоставляется определенным ресурсам, лежащим в основе данной службы, обеспечивающей детальную сегментацию, а также трафик может достигать ресурса обслуживания из помещений без использования публичных конечных точек.
- Конечная точка службы остается публично заведомым IP-адресом.  Частная конечная точка — это частный IP-адрес в адресном пространстве виртуальной сети, где настроена частная конечная точка.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Какова взаимосвязь между сервисом Private Link и частной конечной точкой?
Private Endpoint предоставляет доступ к нескольким частным типам ресурсов ссылок, включая службы Azure PaaS и собственную службу частных ссылок. Это отношения от одного к многим. Одна служба Private Link может получать соединения из нескольких частных конечных точек. С другой стороны, одна частная конечная точка может подключиться только к одной службе Private Link.    

## <a name="private-endpoint"></a>Частная конечная точка 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Могу ли я создать несколько частных конечных точек в одном Итом VNet? Могут ли они подключиться к различным службам? 
Да. Вы можете иметь несколько частных конечных точек в той же VNet или подсети. Они могут подключиться к различным службам.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Нужна ли мне специальная подсеть для частных конечных точек? 
Нет. Вам не нужна специальная подсеть для частных конечных точек. Вы можете выбрать частную конечную точку IP из любой подсети из VNet, где развернута ваша служба.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Может ли Private Endpoint подключиться к службе Private Link через арендаторов Active Directory Azure? 
Да. Частные конечные точки могут подключаться к службам Private Link или Azure PaaS между арендаторами AD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Можно ли подключить частную конечную точку к ресурсам Azure PaaS в регионах Azure?
Да. Частные конечные точки могут подключаться к ресурсам Azure PaaS в регионах Azure.

## <a name="private-link-service"></a>Служба "Приватный канал"
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Каковы предварительные требования для создания сервиса Private Link? 
Ваши резервные услуги должны находиться в виртуальной сети и за балансом стандартной нагрузки.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Как масштабировать службу Private Link? 
Вы можете масштабировать службу Private Link несколькими способами: 
- Добавление ВМ Backend в пул за балансом стандартной нагрузки 
- Добавьте IP-адрес службы Private Link. Мы разрешаем до 8 I-е иаков на услугу Private Link.  
- Добавьте новую услугу Private Link в Standard Load Balancer. Мы разрешаем до восьми услуг Private Link на балансилер нагрузки.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Могу ли я подключить службу к нескольким частным конечным точкам?
Да. Одна служба Private Link может получать соединения из нескольких частных конечных точек. Однако одна частная конечная точка может подключиться только к одной службе Private Link.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Как я должен контролировать экспозицию моей службы Private Link?
Вы можете управлять экспозицией с помощью конфигурации видимости в службе Private Link. Видимость поддерживает три настройки:

- **Нет** - только подписки с доступом RBAC могут найти службу. 
- **Ограничительные** - Только подписки, которые в белом списке и с доступом RBAC может найти службу. 
- **Все** - Каждый может найти услугу. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Могу ли я создать службу Private Link с помощью Basic Load Balancer? 
Нет. Служба Private Link через балансер basic Load Balancer не поддерживается.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Требуется ли выделенная подсеть для обслуживания Private Link? 
Нет. Служба Private Link не требует выделенной подсети. Вы можете выбрать любую подсеть в VNet, где развернута ваша служба.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Я являюсь поставщиком услуг с помощью Azure Private Link. Нужно ли всем моим клиентам иметь уникальное пространство для IP и не пересекаться с моим IP-пространством? 
Нет. Azure Private Link предоставляет эту функциональность для вас. Таким образом, вы не обязаны иметь неперекрывая адресное пространство с адресным пространством клиента. 

##  <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [частной ссылке Azure](private-link-overview.md)
