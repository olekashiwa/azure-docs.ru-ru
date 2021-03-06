---
title: включение файла
description: включение файла
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185250"
---
## <a name="update-resources"></a>Обновление ресурсов

Существуют некоторые ограничения на то, что можно обновить. Можно обновить следующие элементы: 

Коллекция общих образов
- Описание

Определение образа
- Рекомендуемое число виртуальных ЦП
- Рекомендуемый объем памяти
- Описание
- Дата окончания жизненного цикла

Версия образа
- Количество региональных реплик
- Целевые регионы
- Исключения из последней версии
- Дата окончания жизненного цикла

Если вы планируете добавлять регионы реплик, не удаляйте управляемого исходного изображения. Требуется управляемого образа источника для репликации версию образа в других регионах. 

Чтобы обновить описание коллекции, используйте [AzGallery обновления](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

В этом примере показано, как использовать [AzGalleryImageDefinition обновления](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) Обновить дату end-of-life нашем определении образа.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

В этом примере показано, как использовать [обновление AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) запрет эта версия образа, используемого в качестве *последнюю* изображения.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Очистка ресурсов

При удалении ресурсов, необходимо начать с последнего элемента в вложенных ресурсов - версию образа. После удаления версии, можно удалить в определении образа. Не удается удалить коллекции, пока не были удалены все ресурсы под ним.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

