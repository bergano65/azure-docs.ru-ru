---
title: Создание ключей, защищенных аппаратным модулем безопасности, и их передача в Azure Key Vault | Документация Майкрософт
description: В этой статье содержатся сведения о планировании, создании и переносе собственных ключей, защищенных АППАРАТным модулем безопасности, для использования с Azure Key Vault. Также называется BYOK.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 9b8f1065660ea8331853f8804e709134fe682ba7
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566120"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Импорт ключей, защищенных модулем HSM, в Key Vault (предварительная версия)

> [!NOTE]
> Эта функция доступна в предварительной версии и доступна только в регионах Azure *Восточная часть США 2 (euap)* и *Центральная часть США (euap)* . 

Для добавленной гарантии при использовании Azure Key Vault можно импортировать или создать ключ в аппаратном модуле безопасности (HSM). ключ никогда не покидает границу HSM. Этот сценарий часто именуется *собственным ключом* (BYOK). Key Vault использует семейство nCipher nShield HSM (проверенный FIPS 140-2 уровня 2) для защиты ключей.

Используйте сведения в этой статье для планирования, создания и перемещения собственных ключей, защищенных АППАРАТным модулем безопасности, для использования с Azure Key Vault.

> [!NOTE]
> Эта функция недоступна для Azure для Китая в Китае. 
> 
> Этот метод импорта доступен только для [поддерживаемых HSM](#supported-hsms). 

Дополнительные сведения и руководство по началу работы с Key Vault (включая создание хранилища ключей для ключей, защищенных АППАРАТным модулем безопасности) см. в разделе [что такое Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Обзор

Ниже приведен обзор процесса. Конкретные действия, которые необходимо выполнить, описаны далее в этой статье.

* В Key Vault создайте ключ (который называется *ключом обмена ключами* (KEK)). KEK должен быть ключом RSA-HSM, который имеет только операцию `import` Key. Только Key Vault SKU уровня "Премиум" поддерживает ключи RSA-HSM.
* Скачайте открытый ключ KEK в виде PEM-файла.
* Перенесите открытый ключ KEK на автономный компьютер, подключенный к локальному HSM.
* В автономном компьютере используйте средство BYOK, предоставленное поставщиком HSM, для создания файла BYOK. 
* Целевой ключ шифруется с помощью KEK, который остается зашифрованным до тех пор, пока он не будет передан в Key Vault HSM. Локальный модуль HSM остается только в зашифрованной версии ключа.
* KEK, созданный в Key Vault HSM, не может быть экспортирован. HSM принудительно применяет правило, не имеющее четкой версии KEK, за пределами Key Vault HSM.
* KEK должен находиться в том же хранилище ключей, в котором будет импортирован целевой ключ.
* Когда файл BYOK отправляется в Key Vault, Key Vault HSM использует закрытый ключ KEK для расшифровки материала целевого ключа и его импорта в качестве ключа HSM. Эта операция выполняется полностью в Key Vault HSM. Целевой ключ всегда остается на границе защиты HSM.

## <a name="prerequisites"></a>Предварительные требования

В следующей таблице перечислены предварительные требования для использования BYOK в Azure Key Vault.

| Требование | Дополнительные сведения |
| --- | --- |
| Подписка Azure |Чтобы создать хранилище ключей в Azure Key Vault, требуется подписка Azure. [Зарегистрируйтесь для получения бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/). |
| SKU Key Vault Premium для импорта ключей, защищенных АППАРАТным модулем безопасности |Дополнительные сведения об уровнях служб и возможностях в Azure Key Vault см. в разделе [цены на Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| HSM из списка поддерживаемых HSM и средств BYOK и инструкций, предоставленных поставщиком HSM. | Необходимо иметь разрешения для HSM и базовые знания о том, как использовать HSM. См. раздел [Supported HSM](#supported-hsms). |
| Azure CLI версии 2.1.0 или более поздней | См. статью [установка Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>Поддерживаемые HSM

|Имя поставщика HSM|Поддерживаемые модели HSM|Дополнительные сведения|
|---|---|---|
|Thales|Семейство компании SafeNet Luna HSM 7 с встроенным по 7,3 или более поздней версии| [Средство и документация по компании SafeNet Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|

> [!NOTE]
> Чтобы импортировать ключи, защищенные с помощью HSM, из семейства nCipher nShield HSM, используйте [процедуру Legacy BYOK](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Поддерживаемые типы ключей

|Имя ключа|Тип раздела|Размер ключа|Место возникновения|Описание|
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
- создано в том же хранилище ключей, в котором планируется импортировать целевой ключ
- Создан с разрешенными операциями ключей, для которых задано значение `import`

Используйте команду [AZ keyvault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) , чтобы создать KEK с ключевыми операциями, для которых задано значение `import`. Запишите идентификатор ключа (`kid`), возвращаемый из следующей команды. (Вы будете использовать значение `kid` на [шаге 3](#step-3-generate-and-prepare-your-key-for-transfer).)

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

Чтобы скачать и установить средство BYOK, обратитесь к документации поставщика HSM. Следуйте инструкциям поставщика HSM, чтобы создать целевой ключ, а затем создайте пакет для перемещения ключа (файл BYOK). Средство BYOK будет использовать `kid` из файла [Step 1](#step-1-generate-a-kek) и кекфорбйок. publickey. pem, скачанного на [шаге 2](#step-2-download-the-kek-public-key) , чтобы создать зашифрованный целевой ключ в файле BYOK.

Перенесите файл BYOK на подключенный компьютер.

> [!NOTE] 
> Импорт RSA 1 024-разрядных ключей не поддерживается. В настоящее время импорт ключа эллиптической кривой (EC) не поддерживается.
> 
> **Известная ошибка**. Импорт целевого ключа RSA 4000 из компании SafeNet Luna HSM завершается сбоем. После устранения проблемы эта статья будет обновлена.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Шаг 4. Перенос ключа в Azure Key Vault

Чтобы завершить импорт ключа, перенесите пакет обмена ключами (BYOK-файл) с отключенного компьютера на компьютер, подключенный к Интернету. Используйте команду [AZ keyvault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) , чтобы передать файл BYOK в модуль HSM Key Vault.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Если отправка прошла успешно, Azure CLI отображает свойства импортированного ключа.

## <a name="next-steps"></a>Следующие шаги

Теперь ключ, защищенный с помощью аппаратного модуля безопасности, можно использовать в хранилище ключей. Дополнительные сведения см. в [этой цене и сравнении характеристик](https://azure.microsoft.com/pricing/details/key-vault/).



