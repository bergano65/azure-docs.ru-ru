---
title: Создание ключей, защищенных аппаратным модулем безопасности, и их передача в Azure Key Vault | Документация Майкрософт
description: В этой статье содержатся сведения о планировании, создании и переносе собственных ключей, защищенных АППАРАТным модулем безопасности, для использования с Azure Key Vault. Также называется BYOK.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 52214d42467dfa86b5e085a660a9416904b7de59
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84416704"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Импорт ключей, защищенных HSM, в Key Vault (BYOK)

Для добавленной гарантии при использовании Azure Key Vault можно импортировать или создать ключ в аппаратном модуле безопасности (HSM). ключ никогда не покидает границу HSM. Этот сценарий часто именуется *собственным ключом* (BYOK). Key Vault использует семейство nCipher nShield HSM (проверенный FIPS 140-2 уровня 2) для защиты ключей.

Используйте сведения в этой статье для планирования, создания и перемещения собственных ключей, защищенных АППАРАТным модулем безопасности, для использования с Azure Key Vault.

> [!NOTE]
> Эта функция недоступна для Azure для Китая в Китае. 
> 
> Этот метод импорта доступен только для [поддерживаемых HSM](#supported-hsms). 

Дополнительные сведения и руководство по началу работы с Key Vault (включая создание хранилища ключей для ключей, защищенных АППАРАТным модулем безопасности) см. в разделе [что такое Azure Key Vault?](../general/overview.md).

## <a name="overview"></a>Обзор

Ниже представлен обзор этого процесса. Конкретные действия, которые необходимо выполнить, описаны далее в этой статье.

* В Key Vault создайте ключ (который называется *ключом обмена ключами* (KEK)). KEK должен быть ключом RSA-HSM, который имеет только `import` операцию key. Только Key Vault SKU уровня "Премиум" поддерживает ключи RSA-HSM.
* Скачайте открытый ключ KEK в виде PEM-файла.
* Перенесите открытый ключ KEK на автономный компьютер, подключенный к локальному HSM.
* В автономном компьютере используйте средство BYOK, предоставленное поставщиком HSM, для создания файла BYOK. 
* Целевой ключ шифруется с помощью KEK, который остается зашифрованным до тех пор, пока он не будет передан в Key Vault HSM. Локальный модуль HSM остается только в зашифрованной версии ключа.
* KEK, созданный в Key Vault HSM, не может быть экспортирован. HSM принудительно применяет правило, не имеющее четкой версии KEK, за пределами Key Vault HSM.
* KEK должен находиться в том же хранилище ключей, в котором будет импортирован целевой ключ.
* Когда файл BYOK отправляется в Key Vault, Key Vault HSM использует закрытый ключ KEK для расшифровки материала целевого ключа и его импорта в качестве ключа HSM. Эта операция выполняется полностью в Key Vault HSM. Целевой ключ всегда остается на границе защиты HSM.

## <a name="prerequisites"></a>Предварительные условия

В следующей таблице перечислены предварительные требования для использования BYOK в Azure Key Vault.

| Требование | Дополнительные сведения |
| --- | --- |
| Подписка Azure |Чтобы создать хранилище ключей в Azure Key Vault, требуется подписка Azure. [Зарегистрируйтесь для получения бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/). |
| SKU Key Vault Premium для импорта ключей, защищенных АППАРАТным модулем безопасности |Дополнительные сведения об уровнях служб и возможностях в Azure Key Vault см. в разделе [цены на Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| HSM из списка поддерживаемых HSM и средств BYOK и инструкций, предоставленных поставщиком HSM. | Необходимо иметь разрешения для HSM и базовые знания о том, как использовать HSM. См. раздел [Supported HSM](#supported-hsms). |
| Azure CLI версии 2.1.0 или более поздней | Подробнее см. статью [Установка Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>Поддерживаемые HSM

|Имя поставщика|Тип поставщика|Поддерживаемые модели HSM|Дополнительные сведения|
|---|---|---|---|
|nCipher|Производителя<br/>HSM как услуга|<ul><li>Семейство HSM nShield</li><li>nShield как услуга</ul>|[nCipher New BYOK Tool и документация](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Производитель|<ul><li>Семейство HSM 7 Luna с встроенным по версии 7,3 или более поздней</li></ul>| [Средство и документация по Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|фортаникс|Производителя<br/>HSM как услуга|<ul><li>Служба управления ключами для самостоятельной защиты (СДКМС)</li><li>Equinix Смарткэй</li></ul>|[Экспорт ключей СДКМС в поставщики облачных служб для BYOK-Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Драйвер|Производитель|Все Ликуидсекурити HSM с<ul><li>Версия встроенного по 2.0.4 или более поздняя</li><li>Встроенное по версии 3,2 или более поздней</li></ul>|[Средство и документация по BYOK Marvell](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|криптомасик|ISV (система управления ключами предприятия)|Несколько торговых марок и моделей HSM, включая<ul><li>nCipher</li><li>Thales</li><li>утимако</li></ul>Дополнительные [сведения см. на сайте криптомасик](https://www.cryptomathic.com/azurebyok)|[Средство и документация по криптомасик BYOK](https://www.cryptomathic.com/azurebyok)|



## <a name="supported-key-types"></a>Поддерживаемые типы ключей

|Имя раздела|Тип ключа|Размер ключа|Исходный домен|Описание:|
|---|---|---|---|---|
|Ключ обмена ключами (KEK)|RSA| 2 048-разрядный<br />3 072-разрядный<br />4 096-разрядный|Azure Key Vault HSM|Пара ключей RSA с защитой HSM, созданная в Azure Key Vault|
|Целевой ключ|RSA|2 048-разрядный<br />3 072-разрядный<br />4 096-разрядный|HSM поставщика|Ключ для передачи в модуль HSM Azure Key Vault|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Создайте и перенесите ключ в модуль HSM Key Vault.

Чтобы создать и переместить ключ в Key Vault HSM:

* [Шаг 1. Создание KEK](#step-1-generate-a-kek)
* [Шаг 2. скачивание открытого ключа KEK](#step-2-download-the-kek-public-key)
* [Шаг 3. Создание и подготовка ключа для перемещения](#step-3-generate-and-prepare-your-key-for-transfer)
* [Шаг 4. Перенос ключа в Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Шаг 1. Создание KEK

KEK — это ключ RSA, который создается в Key Vault HSM. KEK используется для шифрования ключа, который необходимо импортировать ( *целевой* ключ).

KEK должен быть следующим:
- Ключ RSA-HSM (2 048-bit, 3 072-bit; или 4 096-bit);
- Создано в том же хранилище ключей, в котором планируется импортировать целевой ключ
- Создан с разрешенными операциями ключей, для которых задано значение`import`

> [!NOTE]
> KEK должен иметь "import" в качестве единственной допустимой операции ключа. "import" является взаимоисключающим с другими операциями с ключами.

Используйте команду [AZ keyvault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) , чтобы создать KEK с ключевыми операциями, для которых задано значение `import` . Запишите идентификатор ключа ( `kid` ), возвращаемый из следующей команды. (Вы будете использовать `kid` значение на [шаге 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Шаг 2. скачивание открытого ключа KEK

Чтобы скачать открытый ключ KEK в PEM-файл, выполните команду [AZ keyvault Key download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) . Импортируемый целевой ключ шифруется с помощью открытого ключа KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Перенесите файл Кекфорбйок. publickey. pem на автономный компьютер. Этот файл потребуется на следующем шаге.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Шаг 3. Создание и подготовка ключа для перемещения

Чтобы скачать и установить средство BYOK, обратитесь к документации поставщика HSM. Следуйте инструкциям поставщика HSM, чтобы создать целевой ключ, а затем создайте пакет для перемещения ключа (файл BYOK). Средство BYOK будет использовать `kid` из файла из [шага 1](#step-1-generate-a-kek) и кекфорбйок. publickey. pem, скачанного на [шаге 2](#step-2-download-the-kek-public-key) , чтобы создать зашифрованный целевой ключ в файле BYOK.

Перенесите файл BYOK на подключенный компьютер.

> [!NOTE] 
> Импорт RSA 1 024-разрядных ключей не поддерживается. В настоящее время импорт ключа эллиптической кривой (EC) не поддерживается.
> 
> **Известная ошибка**. Импорт целевого ключа RSA 4000 из Luna HSM поддерживается только при использовании встроенного по 7.4.0 или более поздней версии.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Шаг 4. Перенос ключа в Azure Key Vault

Чтобы завершить импорт ключа, перенесите пакет обмена ключами (BYOK-файл) с отключенного компьютера на компьютер, подключенный к Интернету. Используйте команду [AZ keyvault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) , чтобы передать файл BYOK в модуль HSM Key Vault.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Если отправка прошла успешно, Azure CLI отображает свойства импортированного ключа.

## <a name="next-steps"></a>Дальнейшие шаги

Теперь ключ, защищенный с помощью аппаратного модуля безопасности, можно использовать в хранилище ключей. Дополнительные сведения см. в [этой цене и сравнении характеристик](https://azure.microsoft.com/pricing/details/key-vault/).



