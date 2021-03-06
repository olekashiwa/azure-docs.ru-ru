---
title: Отправка виртуального жесткого диска в Azure с помощью Azure CLI
description: Узнайте, как передать VHD на управляемый диск Azure и скопировать управляемый диск в регионах, используя Azure CLI с помощью прямой отправки.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 51c3933b5ee585c96ad81fe04d379b6771ae81e3
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457596"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Отправка виртуального жесткого диска в Azure с помощью Azure CLI

В этой статье объясняется, как передать VHD с локального компьютера на управляемый диск Azure. Ранее было необходимо было выполнить более сложный процесс, который включал в себя промежуточное хранение данных в учетной записи хранения и управление этой учетной записью хранения. Теперь вам больше не нужно управлять учетной записью хранения или промежуточными данными в ней для отправки виртуального жесткого диска. Вместо этого создайте пустой управляемый диск и отправьте в него виртуальный жесткий диск. Это упрощает передачу локальных виртуальных машин в Azure и позволяет передавать VHD вплоть до 32 Тиб непосредственно на большой управляемый диск.

Если вы предоставляете решение для резервного копирования виртуальных машин IaaS в Azure, мы рекомендуем использовать прямую передачу для восстановления резервных копий клиентов на управляемые диски. Если вы отправляете виртуальный жесткий диск с компьютера, который является внешним по отношению к Azure, скорость будет зависеть от локальной пропускной способности. Если вы используете виртуальную машину Azure, пропускная способность будет такой же, как и для стандартных дисков.

В настоящее время прямая отправка поддерживается для дисков уровня "Стандартный", "Стандартный SSD" и "Премиум", управляемых на SSD. Она пока не поддерживается для Ultra SSDs.

## <a name="prerequisites"></a>предварительным требованиям

- Скачайте последнюю [версию AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Установка Azure CLI](/cli/azure/install-azure-cli).
- VHD-файл, хранящийся локально
- Если планируется передать виртуальный жесткий диск из локальной среды, VHD, который [был подготовлен для Azure](../windows/prepare-for-upload-vhd-image.md), хранится локально.
- Или управляемый диск в Azure, если планируется выполнить действие копирования.

## <a name="create-an-empty-managed-disk"></a>Создание пустого управляемого диска

Чтобы отправить VHD в Azure, необходимо создать пустой управляемый диск, настроенный для этого процесса отправки. Прежде чем создать его, необходимо ознакомиться с дополнительными сведениями об этих дисках.

Этот тип управляемого диска имеет два уникальных состояния:

- Реадтауплоад. Это означает, что диск готов к получению отправки, но [подпись безопасного доступа](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) не была создана.
- Активеуплоад. Это означает, что диск готов к получению отправки, и создан SAS.

Хотя в одном из этих состояний плата за управляемый диск будет взиматься по [стандартному тарифу на HDD](https://azure.microsoft.com/pricing/details/managed-disks/), независимо от фактического типа диска. Например, P10 будет оплачиваться как S10. Это справедливо до тех пор, пока на управляемом диске не будет вызван `revoke-access`, что необходимо для подключения диска к виртуальной машине.

Перед созданием пустого стандартного жесткого диска для отправки необходимо указать размер файла виртуального жесткого диска, который требуется передать, в байтах. Чтобы получить это значение, можно использовать либо `wc -c <yourFileName>.vhd`, либо `ls -al <yourFileName>.vhd`. Это значение используется при указании параметра **--upload-size-bytes** .

Создайте пустой жесткий диск "Стандартный" для отправки, указав оба параметра **—-for-upload** и **--upload-size-bytes** в командлете [создания диска](/cli/azure/disk#az-disk-create) .

```bash
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Если вы хотите передать либо твердотельный SSD класса Premium, либо стандартный SSD, замените **standard_lrs** на **premium_LRS** или **standardssd_lrs**. SSD (цен. категория "Ультра") еще не поддерживается.

Теперь вы создали пустой управляемый диск, настроенный для процесса отправки. Чтобы отправить VHD на диск, вам потребуется доступный для записи SAS, чтобы можно было ссылаться на него как на место назначения для отправки.

Чтобы создать SAS с возможностью записи для пустого управляемого диска, используйте следующую команду:

```bash
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Пример возвращаемого значения:

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Отправить виртуальный жесткий диск

Теперь, когда у вас есть SAS для пустого управляемого диска, его можно использовать для задания управляемого диска в качестве места назначения для команды отправки.

Используйте AzCopy V10 для передачи локального VHD-файла на управляемый диск, указав созданный URI SAS.

Эта передача имеет ту же пропускную способность, что и эквивалент [стандартного жесткого диска](disks-types.md#standard-hdd). Например, если размер равен S4, у вас будет пропускная способность до 60 MiB/с. Но если у вас есть размер, который соответствует S70, вы получите пропускную способность до 500 MiB/с.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Если срок действия SAS истекает во время передачи и вы еще не вызывали `revoke-access`, можно получить новый SAS, чтобы продолжить передачу с помощью `grant-access`.

После завершения передачи вам больше не нужно писать какие-либо данные на диск, отозвать SAS. Отзыв SAS изменит состояние управляемого диска и позволит подключить диск к виртуальной машине.

```bash
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Копирование управляемого диска

Прямая загрузка также упрощает процесс копирования управляемого диска. Можно скопировать в один и тот же регион или между регионами (в другой регион).

Следующий сценарий сделает это для вас, процесс аналогичен описанному ранее действиям с некоторыми отличиями, которые возникают после работы с существующим диском.

> [!IMPORTANT]
> При предоставлении размера диска в байтах управляемого диска из Azure необходимо добавить смещение 512. Это обусловлено тем, что при возврате диска в Azure нижний колонтитул опускается. Если этого не сделать, копирование завершится ошибкой. Следующий сценарий уже выполняет это.

Замените `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`и `<yourTargetLocationHere>` (например, значение расположения uswest2) значениями, а затем выполните следующий сценарий, чтобы скопировать управляемый диск.

```bash
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Дополнительная информация

После успешной отправки виртуального жесткого диска на управляемый диск можно подключить его как [диск данных к существующей виртуальной машине](add-disk.md) или [подключить диск к виртуальной машине в качестве диска ОС](upload-vhd.md#create-the-vm), чтобы создать новую виртуальную машину. 

