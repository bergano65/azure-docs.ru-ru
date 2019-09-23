---
title: REST API Azure Enterprise
description: В этой статье описываются интерфейсы REST API для использования с Соглашением о регистрации Azure Enterprise.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: a8cf0d2eb87a823ee4b1d27484e7ea8d89c1abfc
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105444"
---
# <a name="azure-enterprise-rest-apis"></a>REST API Azure Enterprise

В этой статье описываются интерфейсы REST API для использования с Соглашением о регистрации Azure Enterprise. В ней также объясняются способы устранения распространенных проблем с интерфейсами REST API.

## <a name="consumption-and-usage-apis"></a>Потребление и использование API-интерфейсов

Клиенты Microsoft Enterprise Azure могут получать информацию об использовании и выставлении счетов с помощью интерфейсов REST API. Владелец роли (администратор предприятия, администратор отдела, владелец учетной записи) должен разрешить доступ к API-интерфейсу, создав ключ на портале Azure EA. Затем любой, кому предоставлен номер и ключ Соглашения о регистрации, может получить доступ к данным через API.

### <a name="available-apis"></a>Доступные API

**Баланс и сводка**. [Интерфейс API для управления балансом и просмотра сводки](billing-enterprise-api-balance-summary.md) отображает ежемесячную сводку о состоянии баланса, новых покупках, расходах на службу Azure Marketplace, корректировках и оплате за избыток. Дополнительные сведения см. в статье [Reporting APIs for Enterprise customers — Balance and Summary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) (API-интерфейсы отчетов для корпоративных клиентов: баланс и сводка).

**Сведения об использовании**. [Интерфейс API сведений об использовании](billing-enterprise-api-usage-detail.md) предоставляет сводку об израсходованных объемах и предполагаемых расходах для каждой регистрации с разбивкой по дням. Результаты также содержат сведения об экземплярах, метриках и отделах. Вы можете запросить данные в API по периоду выставления счетов или по дате начала и окончания. Дополнительные сведения см. в статье [Reporting APIs for Enterprise customers — Usage Details](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) (API-интерфейсы отчетов для корпоративных клиентов: сведения об использовании).

**Платежи в Marketplace**. [Интерфейс API платежей в Marketplace](billing-enterprise-api-marketplace-storecharge.md) отображает сводку о расходах в Marketplace с разбивкой по дням. Данные основаны на фактическом использовании и отображаются для указанного периода выставления счетов или дат начала и окончания. Дополнительные сведения см. в статье [Reporting APIs for Enterprise customers — Marketplace Store Charge](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) (API-интерфейсы отчетов для корпоративных клиентов: платежи в Marketplace).

**Прейскурант**. [API прейскурантов](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) предоставляет соответствующий тариф для каждой метрики в регистрации и за период выставления счетов. Дополнительные сведения см. в статье [Reporting APIs for Enterprise customers — Price Sheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) (API-интерфейсы отчетов для корпоративных клиентов: прейскурант).

**Периоды выставления счетов**. [API периодов выставления счетов](billing-enterprise-api-billing-periods.md) возвращает список периодов выставления счетов, которые содержат данные о потреблении для регистрации, приведенные в обратном хронологическом порядке. Каждый период содержит свойство, указывающее на маршрут API к четырем наборам данных: BalanceSummary, UsageDetails, MarketplaceCharges и PriceSheet. Дополнительные сведения см. в статье [Reporting APIs for Enterprise customers — Billing Periods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) (Интерфейсы API отчетов для корпоративных клиентов: периоды выставления счетов).

### <a name="enable-api-data-access"></a>Разрешение доступа к данным API

На портале Azure владельцы ролей могут выполнить приведенные ниже шаги. Последовательно выбрать **Отчеты** > **Скачать сведения об использовании** > **API Access Key** (Ключ доступа к API). Затем они могут выполнить следующие действия:

- создать первичный и вторичный ключи доступа;
- отключить ключи доступа;
- просмотреть даты начала и окончания действия ключей доступа.

### <a name="generate-or-retrieve-the-api-key"></a>Создание или получение ключа API

1. От имени пользователя с ролью администратора предприятия выполните приведенные ниже действия.
2. В левом окне навигации щелкните **Отчеты** и выберите вкладку **Скачать сведения об использовании**.
3. Щелкните **API Access Key** (Ключ доступа к API).
4. В разделе **Enrollment Access Keys** (Ключи доступа к Соглашению о регистрации) выберите символ создания ключа, чтобы создать первичный или вторичный ключ.
5. Выберите **Expand Key** (Развернуть ключ), чтобы просмотреть весь созданный ключ доступа к API.
6. Выберите **Копировать**, чтобы получить ключ доступа к API для немедленного использования.

![Пример страницы "Ключ доступа к API"](./media/billing-ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

Если вы хотите передать ключи доступа к API людям, которые не являются администраторами предприятия в Соглашении о регистрации, выполните приведенные ниже действия.

1. На панели навигации слева щелкните **Manage** (Управление).
2. Щелкните значок карандаша рядом с параметром **DA view charges** (Возможность просмотра затрат для администратора отдела).
3. Нажмите кнопку **Включить**, а затем щелкните **Сохранить**.
4. Щелкните значок карандаша рядом с параметром **AO view charges** (Возможность просмотра затрат для владельца учетной записи).
5. Нажмите кнопку **Включить**, а затем щелкните **Сохранить**.

![Пример включенных параметров просмотра затрат администратором отдела и владельцем учетной записи](./media/billing-ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) На предыдущих шагах владельцам ключей доступа к API был предоставлен доступ к информации о затратах и ценах в отчетах об использовании.

### <a name="pass-keys-in-the-api"></a>Передача ключей в API

Передайте ключ API для каждого вызова для проверки подлинности и авторизации. Передайте следующее свойство заголовкам HTTP:

| Ключ заголовка запроса | Значение |
| --- | --- |
| Авторизация | Укажите значение в следующем формате: **bearer {API\_KEY}**
Пример: bearer \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

Конечная точка Swagger доступна в [API-интерфейсах для корпоративной отчетности версии 3](https://consumption.azure.com/swagger/ui/index) для следующих API. Swagger помогает проверять API. Используйте Swagger для создания клиентских пакетов SDK с использованием средства [AutoRest](https://github.com/Azure/AutoRest) или [Swagger CodeGen](http://swagger.io/swagger-codegen/). Данные, доступные после 1 мая 2014 года, можно просмотреть через API.

### <a name="api-response-codes"></a>Коды ответов API

Когда вы используете API-интерфейс, отображаются коды состояния ответа. Эти коды описаны в приведенной ниже таблице.

| Код состояния отклика | Сообщение | ОПИСАНИЕ |
| --- | --- | --- |
| 200 | ОК | Без ошибок |
| 401 | Не авторизовано | Ключ API не найден, недопустимый формат, срок действия истек и т. д. |
| 404 | Рекомендации недоступны | Не найдена конечная точка отчетов |
| 400 | Ошибка запроса | Недопустимые параметры — диапазоны дат, числа EA и т. д. |
| 500 | Ошибка сервера | Непредвиденная ошибка при обработке запроса |

### <a name="usage-and-billing-data-update-frequency"></a>Частота использования и обновления данных об оплате

Файлы данных об использовании и выставлении счетов обновляются каждые 24 часа для текущего месяца выставления счетов. Однако задержка передачи данных может продолжаться до трех дней. Например, если ресурс используется в понедельник, данные могут не отображаться в файле данных до четверга.

### <a name="test-enrollment-for-development"></a>Тестовая регистрация для разработки

Если вы являетесь партнером или разработчиком без Соглашения о регистрации Azure Enterprise и хотите получить доступ к API-интерфейсу, вы можете использовать тестовую регистрацию. Имя регистрации — _EnrollmentNumber 100_. Затем вы можете [выполнить проверку с помощью ключа доступа для регистрации](https://automaticbillingspec.blob.core.windows.net/spec/TestEnrollmentUsageApiKey.txt), чтобы вызвать API и посмотреть пример данных.

### <a name="azure-service-catalog"></a>Каталог служб Azure

Все службы Azure размещаются в каталоге в формате CSV в блоге хранилища Azure. Каталог можно использовать, если необходимо создать проверенный каталог всех служб Azure для системы. Текущий каталог находится по адресу [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv).

### <a name="csv-data-file-details"></a>Сведения о CSV-файле данных

Ниже приведены сведения о свойствах отчетов API.

#### <a name="usage-summary"></a>Сводка использования

Формат JSON создается из отчета в формате CSV. В результате формат совпадает со сводным форматом CSV. Используется имя столбца, поэтому вам следует выполнить десериализацию в таблицу данных при использовании сводных данных JSON.

| Имя столбца CSV | Имя столбца JSON | Новый столбец JSON | Комментарий |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| Имя учетной записи | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| Название подписки | Параметр SubscriptionName | Параметр SubscriptionName |   |
| Дата | Дата | Дата | Отображает дату запуска отчета каталога служб. Формат представляет собой строку даты без метки времени. |
| Месяц | Месяц | Месяц |   |
| День | День | День |   |
| Год | Год | Год |   |
| Продукт | BillableItemName | Продукт |   |
| Идентификатор средства измерения | ResourceGuid | Значение MeterId |   |
| Категория средства измерения | Service | MeterCategory | Это позволяет найти службы. Относится к службам, имеющим несколько ServiceType. Например, виртуальные машины. |
| Подкатегория средства измерения | ServiceType | MeterSubCategory | Предоставляет второй уровень детализации для службы. Например, виртуальная машина A1 (не Windows).  |
| Регион средства измерения | ServiceRegion | MeterRegion | Третий уровень детализации, необходимый для службы. Позволяет найти контекст региона ResourceGUID. |
| Имя средства измерения | ServiceResource | MeterName | Имя службы. |
| Потребленный объем | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| Расположение ресурса | ServiceSubRegion | ResourceLocation |   |
| Потребленная служба | ServiceInfo | ConsumedService |   |
| Идентификатор экземпляра | Компонент | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| Теги | Теги | Теги |   |
| Идентификатор службы хранилища   | OrderNumber | StoreServiceIdentifier   |   |
| Название отдела | DepartmentName | DepartmentName |   |
| Центр затрат | CostCenter | CostCenter |   |
| Единица измерения | UnitOfMeasure | UnitOfMeasure | Примеры значений: часы, ГБ, события, push-уведомления, единица, часы по единицам, МБ, ежедневные единицы |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Отчет Azure Marketplace

| Имя столбца CSV | Имя столбца JSON | Новый столбец JSON |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| Имя учетной записи | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| Название подписки | Параметр SubscriptionName |  Параметр SubscriptionName |
| Дата | BillingCycle |  Дата (Только строка даты. Без метки времени)
| Месяц | Месяц |  Месяц |
| День | День |  День |
| Год | Год |  Год |
| Идентификатор средства измерения | MeterResourceId |  Значение MeterId |
| Имя издателя | PublisherFriendlyName |  PublisherName |
| Название предложения | OfferFriendlyName |  OfferName |
| Имя плана | PlanFriendlyName |  PlanName |
| Потребленный объем | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| Единица измерения | UnitOfMeasure | UnitOfMeasure |
| Идентификатор экземпляра | InstanceId | InstanceId |
| Дополнительная информация | AdditionalInfo | AdditionalInfo |
| Теги | Теги | Теги |
| Номер заказа | OrderNumber | OrderNumber |
| Название отдела | DepartmentNames | DepartmentName |
| Центр затрат | CostCenters |  CostCenter |
| Группа ресурсов | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>прейскурант;

| Имя столбца CSV | Имя столбца JSON | Комментарий |
| --- | --- | --- |
| Service | Service |  Без изменений в ценах |
| Единица измерения | UnitOfMeasure |   |
| Номер компонента превышения | ConsumptionPartNumber |   |
| Цена единицы превышения | ConsumptionPrice |   |
| Код валюты | CurrencyCode |     |

### <a name="common-api-issues"></a>Распространенные проблемы API

При использовании REST API Azure Enterprise вы можете столкнуться с любой из следующих распространенных проблем.

Вы можете попытаться использовать ключ API, который не имеет правильного типа авторизации. Ключи API создает:

- Администратор предприятия
- администратор отдела (DA);
- владелец учетной записи (AO).

Ключ, созданный администратором EA, предоставляет доступ ко всей информации для этой регистрации. Администратор EA с правами только для чтения не может создать ключ API.

Ключ, созданный DA или AO, не предоставляет доступ к информации об остатке на счете, расходах и прейскуранте.

Срок действия ключа API истекает каждые шесть месяцев. Если срок действия истек, необходимо повторно создать его.

Если вы получили ошибку времени ожидания, вы можете устранить ее, увеличив пороговое значение времени ожидания.

Вы можете получить ошибку 401 (не санкционировано) об истечении срока действия. Ошибка обычно появляется в связи с истекшим сроком действия ключа. Если срок действия ключа истек, вы можете повторно создать его.

Вы можете получить ошибки 400 и 404 (недоступно), возвращаемые из вызова API, если для выбранного диапазона дат нет доступных данных. Например, эта ошибка может появиться из-за недавнего переноса регистрации. Данные с определенной даты и позже этой даты теперь находятся в новой регистрации. В противном случае ошибка может возникнуть, если вы используете новый номер регистрации для получения информации, которая находится в старой регистрации.

## <a name="next-steps"></a>Дополнительная информация

- Администраторам, которые будут использовать портал Azure EA, следует изучить [сведения об администрировании на портале Azure EA](billing-ea-portal-administration.md), где описаны типичные задачи администрирования.
- Сведения об устранении неполадок при работе с порталом Azure EA см. в [этой статье](billing-ea-portal-troubleshoot.md).
