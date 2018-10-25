---
title: Подготовка пакета AppSource | Документация Майкрософт
description: Сведения о подготовке и сборке пакетов AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 0b24c5d2f174c9a656e81d0c85e12b589d7d7799
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808064"
---
# <a name="appsource-package-preparation"></a>Подготовка пакета AppSource

Помимо файла solution.zip вам понадобится **пакет AppSource**. Это ZIP-файл, который содержит все ресурсы, необходимые для автоматизации процесса развертывания вашего решения в клиентской среде CRM. **Пакет AppSource** состоит из следующих компонентов:

* пакет для средства Package Deployer;
* файл **Content_Types.xml** с используемыми ресурсами;
* XML-файл с данными вашего приложения;
* логотип размером 32 x 32, который будет отображаться в вашем предложении в Центре администрирования;
* условия лицензии, политика конфиденциальности.

Выполните приведенные ниже шаги, чтобы создать пакет AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Создание пакета для средства Package Deployer

Пакет для средства Package Deployer является частью пакета AppSource.

Чтобы создать пакет для средства Package Deployer, выполните инструкции, перейдя по следующей ссылке: [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx). По завершении создания ваш пакет будет состоять из следующих ресурсов:

1. Папка пакета, которая содержит все решения, данные конфигурации, неструктурированные файлы и содержимое для вашего пакета. _Примечание. В следующем примере папка пакета называется PkgFolder_.
2. Библиотека DLL. Сборка содержит пользовательский код для вашего пакета. _Примечание. В следующем примере этот файл называется MicrosoftSample.dll._

Теперь вам нужно создать файл с именем **Content_Types.xml**. Этот файл будет содержать все расширения ресурсов, которые являются частью вашего пакета. Ниже приведен пример кода для файла.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

Заключительный шаг — запаковать все в один файл. Назовите его **package.zip**. Он будет содержать следующие элементы:

1. PkgFolder (включая все содержимое папки).
2. Файл DLL.
3. Файл **Content_Types.xml**.

Шаги для создания package.zip:

1. Поместите папку своего пакета, файлы **Content_Types.xml** и имя_пакета.dll в один каталог.

![CRMScreenShot2](media/CRMScreenShot2.png)

2. Выберите все элементы в папке, щелкните правой кнопкой мыши и выберите "Отправить в" > "Сжатая папка (ZIP-файл)".

![CRMScreenShot3](media/CRMScreenShot3.png)

3. Измените имя на package.zip.

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Создание пакета AppSource

Для пакета AppSource требуется несколько дополнительных файлов.

1. JPG (разрешение 32 x 32).
2. HTML (файл форматирования HTML).
3. **Content_Types.xml** (упомянутый выше).
4. xml

Ниже приведен пример кода для файла input.xml. Определения см. в приведенной ниже таблице.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>http://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Описание.**

|Поле|Сведения|
|---|---|
|ProviderName|Пользователь, предоставивший решение. Если это команда Майкрософт, необходимо указать Microsoft.|
|PackageFile |Ресурсы Package Deployer, запакованные вместе с содержимым файла content\_types.xml. Этот ZIP-файл должен содержать сборку Package Deployer и папку с ресурсами Package Deployer. Это файл package.zip.|
|SolutionAnchorName |Имя ZIP-файла решения в Package Deployer, который используется для отображаемого имени и описания ресурсов решения.|
| StartDate| Это день, когда пакет решения станет доступен. Формат — ММ/ДД/ГГГГ.|
|EndDate|Это день, когда пакет решений станет недоступным. Формат — ММ/ДД/ГГГГ. |
|SupportedCountries |Это список разделенных запятыми стран, в которых пакет будет доступен. За списком всех текущих кодов стран обратитесь к веб-службам. В настоящее время в списке представлены следующие коды: AE,AL,AM,AO,AR,AT,AU,AZ,BA,BB,BD,BE,BG,BH,BM,BN,BO,BR,BY,CA,CH,CI,CL,CM,CO,CR,CV,CW,CY,CZ,DE,DK,DO,DZ,EC,EE,EG,ES,FI,FR,GB,GE,GH,GR,GT,HK,HN,HR,HU,ID,IE,IL,IN,IQ,IS,IT,JM,JO,JP,KE,KG,KN,KR,KW,KY,KZ,LB,LK,LT,LU,LV,LY,MA,MC,MD,ME,MK,MN,MO,MT,MU,MX,MY,NG,NI,NL,NO,NZ,OM,PA,PE,PH,PK,PL,PR,PS,PT,PY,QA,RO,RS,RU,RW,SA,SE,SG,SI,SK,SN,SV,TH,TM,TN,TR,TT,TW,UA,US,UY,UZ,VE,VI,VN,ZA,ZW. |
|LearnMoreLink | URL-адрес страницы с дополнительными сведениями для этого пакета. |
|Locales|Один экземпляр этого узла для каждого языка пользовательского интерфейса, который должен поддерживаться в предпочтительном пользовательском интерфейсе решения. Этот узел содержит дочерние элементы, которые описывают языковой стандарт, логотип и термины каждого языка.|
|Locales: PackageLocale.Code|Код языка для этого узла. Например, английский язык (US) — 1033.|
|Locales: PackageLocale.IsDefault|Указывает, что это язык по умолчанию. Этот язык используется в качестве резервного языка, если выбранный клиентом язык пользовательского интерфейса недоступен.|
|Locales: Logo|Это логотип, используемый для данного пакета. Размер значка — 32 x 32. Разрешены следующие форматы: PNG и JPG.|
|Locales:Terms: PackageTerm.File|Это имя файла документа HTML, в котором содержатся условия лицензии.|

Ниже приведен пример отображения логотипа:

![CRMScreenShot5](media/CRMScreenShot5.png)

Заключительный шаг — запаковать все в один файл.

1. ZIP-файл (созданный ранее).
2. Файл **Content_Types.xml**.
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Укажите для файла соответствующее приложению имя. Мы предпочитаем включать в имя название компании и имя приложения. Например, **_Microsoft_SamplePackage.zip**.
