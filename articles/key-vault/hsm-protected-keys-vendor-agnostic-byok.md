---
title: Создание ключей, защищенных аппаратным модулем безопасности, и их передача в Azure Key Vault | Документация Майкрософт
description: Используйте эту статью, чтобы помочь вам спланировать, создать и передать свои собственные защищенные HSM-защищенные ключи для использования с Azure Key Vault. Также известный как принести свой собственный ключ (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 08a4330f4a786deca8ddb2f1c6803b29152e7f50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080145"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Импорт ключей, защищенных модулем HSM, в Key Vault (предварительная версия)

> [!NOTE]
> Эта функция находится в предварительном просмотре и доступна только в регионах Azure *East US 2 EUAP* и *Центральной US EUAP.* 

Для дополнительной гарантии при использовании Azure Key Vault можно импортировать или генерировать ключ в модуле безопасности оборудования (HSM); ключ никогда не покинет границу HSM. Этот сценарий часто называют *принести свой собственный ключ* (BYOK). Key Vault использует семейство HSMs nShield nShield (FIPS 140-2 Уровень 2 проверено) для защиты ваших ключей.

Используйте эту информацию в этой статье, чтобы помочь вам спланировать, создать и передать свои собственные защищенные HSM-защищенные ключи для использования с Azure Key Vault.

> [!NOTE]
> Эта функциональность недоступна для Azure China 21Vianet. 
> 
> Этот метод импорта доступен только для [поддерживаемых HSM.](#supported-hsms) 

Для получения дополнительной информации и для получения учебника, чтобы начать использовать Key Vault (в том числе как создать хранилище ключей для HSM-защищенных ключей), [см.](key-vault-overview.md)

## <a name="overview"></a>Обзор

Ниже представлен обзор этого процесса. Конкретные шаги для завершения описаны позже в статье.

* В Key Vault создайте ключ (называемый *ключом обмена ключом* (KEK)). KEK должен быть ключом RSA-HSM, `import` который имеет только ключевую операцию. Только Key Vault Premium SKU поддерживает клавиши RSA-HSM.
* Скачать открытый ключ KEK в качестве файла .pem.
* Передача общедоступного ключа KEK на автономный компьютер, подключенный к предприимчивому HSM.
* В автономном режиме используйте инструмент BYOK, предоставленный вашим поставщиком HSM, для создания файла BYOK. 
* Целевой ключ зашифрован с помощью KEK, который остается зашифрованным до тех пор, пока он не будет переведен в Key Vault HSM. Только зашифрованная версия ключа оставляет на месте HSM.
* KEK, генерируемый внутри HSM Key Vault, не экспортируется. HSMs применяют правило о том, что за пределами HSM Key Vault не существует четкой версии KEK.
* KEK должен находиться в том же хранилище ключей, где будет импортирован целевой ключ.
* При загрузке файла BYOK в Key Vault в Ключевом Убежище ключевой ключ Vault HSM использует закрытый ключ KEK для расшифровки материала целевого ключа и импорта в качестве ключа HSM. Эта операция происходит полностью внутри HSM Key Vault. Целевой ключ всегда остается в границе защиты HSM.

## <a name="prerequisites"></a>Предварительные требования

В следующей таблице перечислены предпосылки для использования BYOK в Azure Key Vault:

| Требование | Дополнительные сведения |
| --- | --- |
| Подписка Azure |Для создания хранилища ключей в Хранилище ключей Azure необходима подписка Azure. [Подпишитесь на бесплатную пробную версию.](https://azure.microsoft.com/pricing/free-trial/) |
| Key Vault Premium SKU для импорта hSM-защищенных ключей |Для получения дополнительной информации об уровне обслуживания и [Key Vault Pricing](https://azure.microsoft.com/pricing/details/key-vault/)возможностях в Azure Key Vault см. |
| HSM из поддерживаемого списка HSMs и инструмент BYOK и инструкции, предоставленные вашим поставщиком HSM | Вы должны иметь разрешения на HSM и базовые знания о том, как использовать HSM. См [Поддерживаемые ГМС](#supported-hsms). |
| Версия Azure CLI 2.1.0 или позже | Смотрите [Установить Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>Поддерживаемые ГСМ

|Имя поставщика|Тип поставщика|Поддерживаемые модели HSM|Дополнительные сведения|
|---|---|---|---|
|Thales|Производитель|SafeNet Luna HSM 7 семейство с прошивкой версия 7.3 или более поздно| [Инструмент и документация SafeNet Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Фортаникс|HSM как услуга|Служба самозащиты ключей управления (SDKMS)|[Экспорт ключей SDKMS для облачных провайдеров для BYOK - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> Чтобы импортировать защищенные HSM ключи из семейства HSMs nCipher nShield, используйте [устаревшую процедуру BYOK.](hsm-protected-keys-legacy.md)

## <a name="supported-key-types"></a>Поддерживаемые типы ключей

|Имя ключа|Тип ключа|Размер ключа|Исходный домен|Описание|
|---|---|---|---|---|
|Ключ обмена (KEK)|RSA| 2048-разрядный<br />3072-битный<br />4096-битный|Лазурный ключ Хранилище HSM|Ключевая пара RSA, поддерживаемая HSM, генерируемая в Хранилище ключей Azure|
|Целевой ключ|RSA|2048-разрядный<br />3072-битный<br />4096-битный|Поставщик HSM|Ключ, который будет передан в HSM убежища ключей Azure|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Создайте и перенесите ключ в Ключевой Убежище HSM

Для генерации и передачи ключа в HSM Key Vault:

* [Шаг 1: Создание KEK](#step-1-generate-a-kek)
* [Шаг 2: Скачать открытый ключ KEK](#step-2-download-the-kek-public-key)
* [Шаг 3: Создать и подготовить ключ для передачи](#step-3-generate-and-prepare-your-key-for-transfer)
* [Шаг 4: Перенесите ключ в убежище azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Шаг 1: Создание KEK

KEK — это ключ RSA, сгенерированный в HSM Key Vault. KEK используется для шифрования ключа, который вы хотите импортировать *(целевой* ключ).

KEK должны быть:
- Ключ RSA-HSM (2 048 битов; 3072-битный; или 4096-битный)
- Генерируется в том же хранилище ключей, где вы намереваетесь импортировать целевой ключ
- Создано с разрешенными ключевыми операциями, установленными для`import`

> [!NOTE]
> KEK должен иметь "импорт" в качестве единственного разрешенного ключевого операции. «импорт» является взаимоисключающим со всеми другими ключевыми операциями.

Используйте команду [создания ключа az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) для создания KEK, который имеет ключевые `import`операции. Запись идентификатора ключа (),`kid`который возвращается из следующей команды. (Вы будете `kid` использовать значение в [шаге 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Шаг 2: Скачать открытый ключ KEK

Используйте [загрузку ключа az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) для загрузки общедоступного ключа KEK в файл .pem. Целевой ключ, который вы импортируете, шифруется с помощью общедоступного ключа KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Перенесите файл KEKforBYOK.publickey.pem на ваш автономный компьютер. Этот файл понадобится на следующем этапе.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Шаг 3: Создать и подготовить ключ для передачи

Обратитесь к документации поставщика HSM для загрузки и установки инструмента BYOK. Следуйте инструкциям поставщика HSM для создания целевого ключа, а затем создайте пакет передачи ключей (файл BYOK). Инструмент BYOK будет `kid` использовать от [шага 1](#step-1-generate-a-kek) и KEKforBYOK.publickey.pem файл, который вы скачали в [Шаг 2](#step-2-download-the-kek-public-key) для создания зашифрованного целевого ключа в файле BYOK.

Перенесите файл BYOK на подключенный компьютер.

> [!NOTE] 
> Импорт 1024-битных ключей RSA не поддерживается. В настоящее время импорт ключа Elliptic Curve (EC) не поддерживается.
> 
> **Известная проблема**: Импорт целевого ключа RSA 4K от SafeNet Luna HSMs поддерживается только с прошивкой 7.4.0 или новее.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Шаг 4: Перенесите ключ в убежище azure Key Vault

Чтобы завершить импорт ключа, перенесите пакет передачи ключей (файл BYOK) с отключенного компьютера на подключенный к Интернету компьютер. Используйте импортную команду [ключа az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) для загрузки файла BYOK в Key Vault HSM.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Если загрузка удалась, Azure CLI отображает свойства импортированного ключа.

## <a name="next-steps"></a>Дальнейшие действия

Теперь ключ, защищенный с помощью аппаратного модуля безопасности, можно использовать в хранилище ключей. Для получения дополнительной информации, [см.](https://azure.microsoft.com/pricing/details/key-vault/)



