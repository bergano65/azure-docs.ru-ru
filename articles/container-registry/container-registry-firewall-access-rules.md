---
title: Правила брандмауэра для доступа к реестру контейнеров Azure
description: Настройте правила для доступа к реестру контейнеров Azure из защищенного брандмауэра.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/17/2019
ms.author: danlep
ms.openlocfilehash: 88b6da4e9bd2938adadadc1ef0e696399fc3c75e
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827999"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Настройка правил для доступа к реестру контейнеров Azure за брандмауэром

В этой статье объясняется, как настроить правила в брандмауэре, чтобы разрешить доступ к реестру контейнеров Azure. Например, для извлечения образа контейнера Azure IoT Edgeному устройству за брандмауэром или прокси-сервером может потребоваться доступ к реестру контейнеров. Или заблокированный сервер в локальной сети может потребовать доступа для отправки образа.

Если вместо этого вы хотите настроить входящие правила доступа к сети в реестре контейнеров, чтобы разрешить доступ только в пределах виртуальной сети Azure или диапазона общедоступных IP-адресов, см. раздел [ограничение доступа к реестру контейнеров Azure из виртуальной сети](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>Сведения о конечных точках реестра

Чтобы извлечь или отправить образы или другие артефакты в реестр контейнеров Azure, клиент, например, управляющая программа DOCKER должна взаимодействовать по протоколу HTTPS с двумя разными конечными точками.

* **Реестр REST API конечная точка** . операции проверки подлинности и управления реестром обрабатываются через общедоступную конечную точку REST API реестра. Эта конечная точка представляет собой URL-адрес сервера входа в реестр или соответствующий диапазон IP-адресов. 

* **Конечная точка хранилища** . Azure [выделяет хранилище BLOB-объектов](container-registry-storage.md) в учетных записях хранения Azure от имени каждого реестра для управления образами контейнеров и другими артефактами. Когда клиент получает доступ к слоям изображений в реестре контейнеров Azure, он выполняет запросы с помощью конечной точки учетной записи хранения, предоставляемой реестром.

Если реестр является [географически реплицируемым](container-registry-geo-replication.md), клиенту может потребоваться взаимодействовать с конечными точками службы "остальное" и "хранилище" в определенном регионе или в нескольких реплицируемых регионах.

## <a name="allow-access-to-rest-and-storage-urls"></a>Разрешить доступ к URL-адресам RESTFUL и хранилища

* **Конечная точка RESTful** — разрешение доступа к URL-адресу сервера реестра, например`myregistry.azurecr.io`
* **Конечная точка хранилища** — разрешение доступа ко всем учетным записям хранилища BLOB-объектов Azure с помощью подстановочного знака`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Разрешить доступ по диапазону IP-адресов

Если необходимо разрешить доступ к конкретным IP-адресам, скачайте [диапазоны IP-адресов Azure и теги служб — общедоступное облако](https://www.microsoft.com/download/details.aspx?id=56519).

Чтобы найти диапазоны IP-адресов конечной точки записи контроля доступа, выполните поиск **азуреконтаинеррегистри** в файле JSON.

> [!IMPORTANT]
> Диапазоны IP-адресов для служб Azure могут изменяться, а обновления публикуются еженедельно. Регулярно загружайте JSON и вносите необходимые обновления в правила доступа. Если в сценарии предполагается настройка правил группы безопасности сети в виртуальной сети Azure для доступа к реестру контейнеров Azure, используйте вместо этого [тег службы](#allow-access-by-service-tag) **азуреконтаинеррегистри** .
>

### <a name="rest-ip-addresses-for-all-regions"></a>IP-адреса RESTFUL для всех регионов

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

### <a name="rest-ip-addresses-for-a-specific-region"></a>IP-адреса RESTFUL для определенного региона

Выполните поиск по определенному региону, например **азуреконтаинеррегистри. AustraliaEast**.

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

### <a name="storage-ip-addresses-for-all-regions"></a>IP-адреса хранилища для всех регионов

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

### <a name="storage-ip-addresses-for-specific-regions"></a>IP-адреса хранилища для конкретных регионов

Выполните поиск по определенному региону, например **Storage. аустралиацентрал**.

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

В виртуальной сети Azure используйте правила сетевой безопасности для фильтрации трафика от ресурса, например виртуальной машины, в реестр контейнеров. Чтобы упростить создание правил сети Azure, используйте [тег службы](../virtual-network/security-overview.md#service-tags) **азуреконтаинеррегистри** . Тег службы представляет группу префиксов IP-адресов для глобального доступа к службе Azure или по всему региону Azure. Тег автоматически обновляется при изменении адресов. 

Например, создайте правило исходящей группы безопасности сети с назначением **азуреконтаинеррегистри** , чтобы разрешить трафик в реестр контейнеров Azure. Чтобы разрешить доступ к тегу службы только в определенном регионе, укажите регион в следующем формате: **Азуреконтаинеррегистри**. [*имя региона*].

## <a name="next-steps"></a>Следующие шаги

* Узнайте о [рекомендациях Azure по сетевой безопасности](../security/fundamentals/network-best-practices.md)

* Дополнительные сведения о [группах безопасности](/azure/virtual-network/security-overview) в виртуальной сети Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

