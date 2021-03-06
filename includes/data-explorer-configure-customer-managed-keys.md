---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 0d78e48fead7b1f53e67860e6be8fe6d77469e87
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280613"
---
Azure обозреватель данных шифрует все данные в неактивных учетных записях хранения. По умолчанию данные шифруются с помощью ключей, управляемых корпорацией Майкрософт. Для дополнительного управления ключами шифрования можно предоставить ключи, управляемые клиентом, для шифрования данных. Ключи, управляемые клиентом, должны храниться в [Azure Key Vault](/azure/key-vault/key-vault-overview). Вы можете создавать собственные ключи и сохранять их в хранилище ключей, а также использовать Azure Key Vault API для создания ключей. Кластер Azure обозреватель данных и хранилище ключей должны находиться в одном регионе, но могут находиться в разных подписках. Подробное описание ключей, управляемых клиентом, см. в разделе [управляемые клиентом ключи с помощью Azure Key Vault](/azure/storage/common/storage-service-encryption). В этой статье показано, как настроить ключи, управляемые клиентом.

Чтобы настроить ключи, управляемые клиентом, с помощью Azure обозреватель данных, необходимо [задать два свойства в хранилище ключей](/azure/key-vault/key-vault-ovw-soft-delete): **обратимое удаление** и **не очищать**. Эти свойства не включены по умолчанию. Чтобы включить эти свойства, используйте [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) или [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli). Поддерживаются только ключи RSA и размер ключа 2048.

> [!NOTE]
> Шифрование данных с помощью управляемых пользователем ключей не поддерживается в [кластерах "руководитель" и "следующий](/azure/data-explorer/follower)". 

## <a name="assign-an-identity-to-the-cluster"></a>Назначение удостоверения кластеру

Чтобы включить управляемые клиентом ключи для кластера, сначала назначьте кластеру управляемое удостоверение, назначенное системой. Вы будете использовать это управляемое удостоверение, чтобы предоставить кластеру разрешения на доступ к хранилищу ключей. Сведения о настройке управляемых удостоверений, назначенных системой, см. в разделе [управляемые удостоверения](/azure/data-explorer/managed-identities).

## <a name="create-a-new-key-vault"></a>Создание нового хранилища ключей

Чтобы создать новое хранилище ключей с помощью PowerShell, вызовите [New-азкэйваулт](/powershell/module/az.keyvault/new-azkeyvault). Хранилище ключей, используемое для хранения ключей, управляемых клиентом, для шифрования Azure обозреватель данных должно включать два параметра защиты ключей: **обратимое удаление** и **не очищать**. Замените значения заполнителей в квадратных скобках собственными значениями в примере ниже.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Настройка политики доступа к хранилищу ключей

Затем настройте политику доступа для хранилища ключей, чтобы у кластера было разрешение на доступ к нему. На этом шаге вы будете использовать управляемое системой удостоверение, назначенное кластеру ранее. Чтобы задать политику доступа для хранилища ключей, вызовите [Set-азкэйваултакцессполици](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Замените значения заполнителей в квадратных скобках собственными значениями и используйте переменные, определенные в предыдущих примерах.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Создание ключа

Затем создайте новый ключ в хранилище ключей. Чтобы создать новый ключ, вызовите [Add-азкэйваулткэй](/powershell/module/az.keyvault/add-azkeyvaultkey). Замените значения заполнителей в квадратных скобках собственными значениями и используйте переменные, определенные в предыдущих примерах.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
