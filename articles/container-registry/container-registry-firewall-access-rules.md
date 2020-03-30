---
title: Правила доступа к брандмауэру
description: Настройка правил для доступа к реестру контейнеров Azure из-за брандмауэра, разрешив доступ к ("белому списку") REST API и конечным адресам хранения доменных имен или диапазонов IP-адресов, специфичных для обслуживания.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168022"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Настройка правил для доступа к реестру контейнеров Azure за брандмауэром

В этой статье объясняется, как настроить правила в брандмауэре, чтобы обеспечить доступ к реестру контейнеров Azure. Например, устройству Azure IoT Edge за брандмауэром или прокси-серверу может потребоваться доступ к реестру контейнеров для получения изображения контейнера. Или заблокированный сервер в предварительной сети может нуждаться в доступе для нажатия изображения.

Если вместо этого вы хотите настроить правила входящего сетевого доступа в реестр контейнеров только в виртуальной сети Azure или из общедоступного диапазона IP-адресов, [см.](container-registry-vnet.md)

## <a name="about-registry-endpoints"></a>О конечных точках реестра

Чтобы вытащить или нажать изображения или другие артефакты в реестр контейнеров Azure, клиенту, например docker daemon, необходимо взаимодействовать через HTTPS с двумя различными конечными точками.

* **Конечная точка реестра REST API** - Операции по аутентификации и управлению реестром обрабатываются через публичную конечную точку REST API реестра. Эта конечная точка — это имя сервера входа в реестр или связанный с ним диапазон IP-адресов. 

* **Конечная точка хранения** — Azure выделяет хранилище кабр в учетных [записях](container-registry-storage.md) Хранилища Azure от имени каждого реестра для управления данными для изображений контейнеров и других артефактов. Когда клиент получает доступ к слоям изображений в реестре контейнеров Azure, он делает запросы с помощью конечной точки учетной записи хранилища, предоставляемой реестром.

Если ваш реестр [геореплицируется,](container-registry-geo-replication.md)клиенту может потребоваться взаимодействовать с конечными точками REST и хранения в определенном регионе или в нескольких реплицированных регионах.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Разрешить доступ к именам доменов REST и хранилища

* **КОНЕЧНая точка REST** - Разрешить доступ к полностью квалифицированной регистрации на сервере реестра, например`myregistry.azurecr.io`
* **Хранение (данные) конечная точка** - Разрешить доступ ко всем учетным записям хранения Azure blob с помощью подстановочного знака`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Разрешить доступ по диапазону IP-адресов

Если в вашей организации есть политики, позволяющие доступ только к определенным IP-адресам или диапазонам адресов, загрузите [IP-диапазоны Azure и теги служб - Public Cloud.](https://www.microsoft.com/download/details.aspx?id=56519)

Чтобы найти диапазоны IP-адресов ACR REST, к которым необходимо разрешить доступ, ищите **AzureContainerRegistry** в файле JSON.

> [!IMPORTANT]
> Диапазоны IP-адресов для служб Azure могут меняться, а обновления публикуются еженедельно. Регулярно загружайте файл JSON и вносите необходимые обновления в правила доступа. Если сценарий предполагает настройку правил группы сетевой безопасности в виртуальной сети Azure для доступа к реестру контейнеров Azure, вместо этого воспользуйтесь [тегом службы](#allow-access-by-service-tag) **AzureContainerRegistry.**
>

### <a name="rest-ip-addresses-for-all-regions"></a>IP-адреса REST для всех регионов

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>IP-адреса REST для определенного региона

Поиск по конкретному региону, например **AzureContainerRegistry.AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Хранение IP-адресов для всех регионов

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Хранение IP-адресов для определенных регионов

Поиск для конкретного региона, например **Storage.AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Разрешить доступ по тегу службы

В виртуальной сети Azure используйте правила сетевой безопасности для фильтрации трафика с ресурса, такого как виртуальная машина, в реестр контейнеров. Чтобы упростить создание правил сети Azure, используйте [тег службы](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry.** Тег службы представляет собой группу префиксов IP-адресов для доступа к службе Azure по всему миру или в регионе Azure. Тег автоматически обновляется при изменении адресов. 

Например, создайте правило группы безопасности исходящих сетей с назначением **AzureContainerRegistry,** чтобы разрешить трафик в реестр контейнеров Azure. Чтобы разрешить доступ к тегу обслуживания только в определенном регионе, укажите регион в следующем формате: **AzureContainerRegistry**. Название*региона.*

## <a name="configure-client-firewall-rules-for-mcr"></a>Настройка правил клиентского брандмауэра для MCR

Если вам необходимо получить доступ к реестру контейнеров Майкрософт (MCR) из-за брандмауэра, см. [MCR client firewall rules](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md) MCR является основным реестром для всех изображений докеров, опубликованных корпорацией Майкрософт, таких как изображения Windows Server.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте о [рекомендациях Azure для сетевой безопасности](../security/fundamentals/network-best-practices.md)

* Подробнее о [группах безопасности](/azure/virtual-network/security-overview) в виртуальной сети Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

