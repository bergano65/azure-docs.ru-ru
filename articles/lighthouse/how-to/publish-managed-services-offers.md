---
title: Публикация предложения управляемых услуг для Azure Marketplace
description: Узнайте, как опубликовать предложение управляемых служб, которое подключает клиентов к управлению делегированными ресурсами Azure.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 922e78a0715ce5c758ca7d068a38af43e214a524
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673934"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Публикация предложения управляемых услуг для Azure Marketplace

В этой статье вы узнаете, как опубликовать публичное или частное предложение управляемых услуг [Azure Marketplace](https://azuremarketplace.microsoft.com) с помощью программы [Commercial Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) в [Partner Center.](https://partner.microsoft.com/) Клиенты, купившие предложение, могут иметь возможность на борту подписки и групп ресурсов для [управления ресурсами Azure делегированных ресурсов.](../concepts/azure-delegated-resource-management.md)

## <a name="publishing-requirements"></a>Требования к публикации

Для создания и публикации предложений необходимо иметь действительную [учетную запись в Центре партнеров.](../../marketplace/partner-center-portal/create-account.md) Если у вас еще нет учетной записи, [процесс регистрации](https://aka.ms/joinmarketplace) приведет вас через шаги создания учетной записи в Центре Партнеров и регистрации в программе Commercial Marketplace.

В соответствии с [требованиями сертификации управляемой службы](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)вы должны иметь [уровень компетенции Серебряной или Золотой Облачной Платформы](https://docs.microsoft.com/partner-center/learn-about-competencies) или быть [MSP-экспертом Azure,](https://partner.microsoft.com/membership/azure-expert-msp) чтобы опубликовать предложение по управляемой службе.

Ваш идентификатор Microsoft Partner Network будет [автоматически связан](../../billing/billing-partner-admin-link-started.md) с публикуемыми предложениями, чтобы отследить влияние на привлечение клиентов.

> [!NOTE]
> Если вы не хотите публиковать предложение в Azure Marketplace, вы можете вручную подключать клиентов с помощью шаблонов Azure Resource Manager. Дополнительные сведения см. в статье [Onboard a customer to Azure delegated resource management](onboard-customer.md) (Подключение клиента к системе делегированного управления ресурсами Azure)

## <a name="create-your-offer"></a>Создайте предложение

Для получения подробных инструкций о том, как создать ваше предложение, включая всю информацию и активы, которые вам нужно предоставить, [см.](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)

Чтобы узнать об общем процессе публикации, смотрите [руководство Azure Marketplace и AppSource Publishing Guide.](../../marketplace/marketplace-publishers-guide.md) Также следует ознакомиться [с политиками сертификации для коммерческой платформы Marketplace](https://docs.microsoft.com/legal/marketplace/certification-policies), в частности с разделом [Managed Services](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) (Управляемые службы).

Как только клиент добавит ваше предложение, он сможет делегировать одну или несколько подписок или групп ресурсов, которые затем будут [переданы для управления ресурсами, делегированными Azure.](#the-customer-onboarding-process)

> [!IMPORTANT]
> Каждый план в предложении управляемых услуг включает раздел **Manifest Детали,** где вы определяете объекты Azure Active Directory (Azure AD) в вашем арендаторе, которые будут иметь доступ к делегированным группам ресурсов и/или подпискам для клиентов, которые приобретают этот план. Важно знать, что любая группа (или пользователь или директор службы), которую вы включаете, будет иметь одинаковые разрешения для каждого клиента, который покупает план. Чтобы назначить различные группы для работы с каждым клиентом, необходимо опубликовать отдельный [частный план,](../../marketplace/private-offers.md) который является эксклюзивным для каждого клиента.

## <a name="publish-your-offer"></a>Публикация вашего предложения

Когда вы заполните все разделы, вашим следующим шагом будет публикация предложения в Azure Marketplace. Нажмите кнопку **Опубликовать**, чтобы запустить процесс активации предложения. Более подробную информацию об этом процессе можно найти [здесь](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish).

Вы можете в любое время [опубликовать обновленную версию предложения](../../marketplace/partner-center-portal/update-existing-offer.md). Например, вам может потребоваться добавить новое определение роли к опубликованному ранее предложению. После этого клиенты, которые уже добавили предложение, увидят значок на странице [**Поставщики услуг**](view-manage-service-providers.md) на портале Azure, который сообщит им, доступно ли обновление. Каждый клиент сможет [проверить изменения](view-manage-service-providers.md#update-service-provider-offers) и решить, требуется ли им обновление до новой версии. 

## <a name="the-customer-onboarding-process"></a>Процесс подключения пользователей

После того, как пользователь добавит ваше предложение, он [сможет делегировать одну или несколько конкретных подписок или групп ресурсов](view-manage-service-providers.md#delegate-resources), которые затем будут подключены к системе делегированного управления ресурсами Azure. Если пользователь принял предложение, но еще не делегировал никаких ресурсов, он увидит примечание в верхней части раздела **Provider offers** (Предложения поставщиков) страницы [**Поставщики услуг**](view-manage-service-providers.md) на портале Azure.

> [!IMPORTANT]
> Делегация должна осуществляться негостевой учетной записью клиента, которая имеет [встроенную роль Владельца](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) для подписки, находившейся на борту (или содержащей группы ресурсов, которые находятся на борту). Чтобы найти пользователей, которые могут делегировать подписку, пользователь клиента может выбрать подписку на портале Azure, открыть **управление доступом (IAM)** и [просмотреть всех пользователей с ролью владельца](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

После того, как клиент делегирует подписку (или одну или несколько групп ресурсов в рамках подписки), поставщик ресурсов **Microsoft.ManagedServices** будет зарегистрирован для этой подписки, и пользователи в вашем арендаторе смогут получить доступ к делегированным ресурсам в соответствии с авторизациями в вашем предложении.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [коммерческом рынке](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Узнайте больше об [интерфейсах управления для различных клиентов](../concepts/cross-tenant-management-experience.md).
- [Просматривайте клиентов и управляйте ими](view-manage-customers.md), перейдя в раздел **Мои клиенты** на портале Azure.
