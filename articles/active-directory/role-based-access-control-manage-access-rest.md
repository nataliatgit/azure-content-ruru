<properties
	pageTitle="Управление доступом на основе ролей с помощью интерфейса REST API"
	description="Управление доступом на основе ролей с помощью интерфейса REST API"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="rest-api"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="kgremban"/>

# Управление доступом на основе ролей с помощью интерфейса REST API

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## Вывод списка всех назначений ролей

Здесь описывается вывод списка всех назначений ролей в указанной области и внутренних областях.

Чтобы вывести список назначений ролей, требуется доступ к операции `Microsoft.Authorization/roleAssignments/read` в этой области. Доступ к этой операции предоставляется всем встроенным ролям. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Управление доступом на основе ролей Azure](role-based-access-control-configure.md).

### Запрос

Замените метод **GET** следующим кодом URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&filter={filter}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

Замените текст *{scope}* областью, для которой требуется вывести список назначений ролей. В следующих примерах показано, как указать область для различных уровней:

| Уровень | *{Scope}* |
|-------|-----------|
| Подписка | /subscriptions/{subscription-id} |
| Группа ресурсов | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ресурс | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Замените текст *{api-version}* значением 2015-07-01.

Замените текст *{filter}* условием, по которому требуется отфильтровать список назначений ролей. Ниже указаны поддерживаемые условия.


| Условие | *{Filter}* | Замените |
|-----------|------------|---------|
| Вывод списка назначений ролей только для определенной области без учета внутренних областей | `atScope()` | |
| Вывод списка назначений ролей только для определенного пользователя, группы или приложения | `principalId%20eq%20'{objectId}'` | Замените текст *{objectId}* значением ObjectId (идентификатора объекта) для пользователя, группы или субъекта-службы в Azure AD. Например, `&filter=principalId%20eq%20'3a477f6a-6739-4b93-84aa-3be3f8c8e7c2'`. |
| Вывод списка назначений ролей только для определенного пользователя, в том числе тех, которые назначены группам, в которые он входит | `assignedTo('{objectId}')` | Замените текст *{objectId}* значением ObjectId для пользователя в Azure AD. Например, `&filter=assignedTo('3a477f6a-6739-4b93-84aa-3be3f8c8e7c2')`. |



### Ответ

Код состояния: 200.

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## Получение сведений о назначении роли

Здесь описывается получение сведений о назначении роли, указанной с помощью идентификатора.

Чтобы получить сведения о назначении роли, требуется доступ к операции `Microsoft.Authorization/roleAssignments/read`. Доступ к этой операции предоставляется всем встроенным ролям. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Управление доступом на основе ролей Azure](role-based-access-control-configure.md).

### Запрос

Замените метод **GET** следующим кодом URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

Замените текст *{scope}* областью, для которой требуется вывести список назначений ролей. В следующих примерах показано, как указать область для различных уровней:

| Уровень | *{Scope}* |
|-------|-----------|
| Подписка | /subscriptions/{subscription-id} |
| Группа ресурсов | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ресурс | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Замените текст *{role-assignment-id}* идентификатором GUID для назначения роли.

Замените текст *{api-version}* значением 2015-07-01.

### Ответ

Код состояния: 200.

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## Создание назначения роли

Здесь описывается создание назначения роли в указанной области для определенного субъекта, назначающего роль.

Чтобы создать назначение роли, требуется доступ к операции `Microsoft.Authorization/roleAssignments/write`. Из встроенных ролей эту операцию могут выполнять только *владелец* и *администратор доступа пользователей*. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Управление доступом на основе ролей Azure](role-based-access-control-configure.md).

### Запрос

Замените метод **PUT** следующим кодом URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

Замените текст *{scope}* областью, для которой требуется создать назначения ролей. При создании назначения роли в родительской области все дочерние области также его наследуют. В следующих примерах показано, как указать область для различных уровней:

| Уровень | *{Scope}* |
|-------|-----------|
| Подписка | /subscriptions/{subscription-id} |
| Группа ресурсов | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ресурс | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Замените текст *{role-assignment-id}* новым идентификатором GUID. Он будет использоваться в качестве идентификатора GUID нового назначения роли.

Замените текст *{api-version}* значением 2015-07-01.

В тексте запроса введите значения в следующем формате:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Имя элемента | Обязательно | Тип | Описание |
|------------------|----------|--------|-------------|
| roleDefinitionId | Да | Строка | Идентификатор роли, которую необходимо назначить. Он указывается в формате `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}`. |
| principalId | Да | Строка | ObjectId субъекта Azure AD (пользователя, группы или субъекта-службы), которому назначается роль. |

### Ответ

Код состояния: 201.

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## Удаление назначения ролей

Здесь описывается удаление назначения роли в указанной области.

Чтобы удалить назначение роли, требуется доступ к операции `Microsoft.Authorization/roleAssignments/delete`. Из встроенных ролей эту операцию могут выполнять только *владелец* и *администратор доступа пользователей*. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Управление доступом на основе ролей Azure](role-based-access-control-configure.md).

### Запрос

Замените метод **DELETE** следующим кодом URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

Замените текст *{scope}* областью, для которой требуется создать назначения ролей. В следующих примерах показано, как указать область для различных уровней:

| Уровень | *{Scope}* |
|-------|-----------|
| Подписка | /subscriptions/{subscription-id} |
| Группа ресурсов | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ресурс | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Замените текст *{role-assignment-id}* идентификатором GUID для назначения роли.

Замените текст *{api-version}* значением 2015-07-01.

### Ответ

Код состояния: 200.

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## Вывод списка всех ролей

Здесь описывается перечисление всех ролей, которые доступны для назначения в указанной области.

Для вывода списка ролей требуется доступ к операции `Microsoft.Authorization/roleDefinitions/read` в этой области. Доступ к этой операции предоставляется всем встроенным ролям. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Управление доступом на основе ролей Azure](role-based-access-control-configure.md).

### Запрос

Замените метод **GET** следующим кодом URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&filter={filter}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

Замените текст *{scope}* областью, для которой требуется вывести список ролей. В следующих примерах показано, как указать область для различных уровней:

| Уровень | *{Scope}* |
|-------|-----------|
| Подписка | /subscriptions/{subscription-id} |
| Группа ресурсов | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ресурс | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Замените текст *{api-version}* значением 2015-07-01.

Замените текст *{filter}* условием, по которому требуется отфильтровать список ролей. Ниже указаны поддерживаемые условия.

| Условие | *{Filter}* | Замените |
|-----------|------------|---------|
| Вывод списка ролей, доступных для назначения в указанной области и любой из ее дочерних областей | `atScopeAndBelow()` | |
| Поиск с помощью точного отображаемого имени роли | `roleName%20eq%20'{role-display-name}'` | Замените текст *{role-display-name}* закодированным URL-адресом точного отображаемого имени роли. Например, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'`. |



### Ответ

Код состояния: 200.

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## Получение сведений о роли

Здесь описывается получение сведений о роли, указанной с помощью идентификатора определения роли. Чтобы получить сведения о роли с помощью ее отображаемого имени, см. сведения о выводе списка всех ролей и фильтре roleName.

Чтобы получить сведения о роли, требуется доступ к операции `Microsoft.Authorization/roleDefinitions/read`. Доступ к этой операции предоставляется всем встроенным ролям. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Управление доступом на основе ролей Azure](role-based-access-control-configure.md).

### Запрос

Замените метод **GET** следующим кодом URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

Замените текст *{scope}* областью, для которой требуется вывести список назначений ролей. В следующих примерах показано, как указать область для различных уровней:

| Уровень | *{Scope}* |
|-------|-----------|
| Подписка | /subscriptions/{subscription-id} |
| Группа ресурсов | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ресурс | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Замените текст *{role-definition-id}* идентификатором GUID для определения роли. Замените текст *{api-version}* значением 2015-07-01.

### Ответ

Код состояния: 200.

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## Создание настраиваемой роли
Здесь описывается создание настраиваемой роли.

Чтобы создать настраиваемую роль, требуется доступ к операции `Microsoft.Authorization/roleDefinitions/write` во всех областях `AssignableScopes` этой роли. Из встроенных ролей эту операцию могут выполнять только *владелец* и *администратор доступа пользователей*. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Управление доступом на основе ролей Azure](role-based-access-control-configure.md).

### Запрос

Замените метод **PUT** следующим кодом URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

Замените текст *{scope}* первой областью *AssignableScope* для настраиваемой роли. В следующих примерах показано, как указать область для различных уровней.

| Уровень | *{Scope}* |
|-------|-----------|
| Подписка | /subscriptions/{subscription-id} |
| Группа ресурсов | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ресурс | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Замените текст *{role-definition-id}* новым идентификатором GUID. Он будет использоваться в качестве идентификатора GUID новой настраиваемой роли.

Замените текст *{api-version}* значением 2015-07-01.

В тексте запроса введите значения в следующем формате:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Имя элемента | Обязательно | Тип | Описание |
|--------------|----------|------|-------------|
| name | Да | Строка | Идентификатор GUID настраиваемой роли. |
| properties.roleName | Да | Строка | Отображаемое имя настраиваемой роли. Не может быть более 128 символов в длину. |
| properties.description | Нет | Строка | Описание настраиваемой роли. Не может быть более 1024 символов в длину. |
| properties.type | Да | Строка | Необходимо указать значение CustomRole. |
| properties.permissions.actions | Да | Строка | Массив строк действий, определяющих операции, к которым предоставляет доступ настраиваемая роль. |
| properties.permissions.notActions | Нет | Строка | Массив строк действий, определяющих операции, которые должны быть исключены из списка операций, к которым предоставляет доступ настраиваемая роль. |
| properties.assignableScopes | Да | Строка | Массив областей, в которых можно использовать настраиваемую роль для управления доступом. |

### Ответ

Код состояния: 201.

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## Обновление настраиваемой роли

Здесь описывается изменение настраиваемой роли.

Чтобы изменить настраиваемую роль, требуется доступ к операции `Microsoft.Authorization/roleDefinitions/write` во всех областях `AssignableScopes` этой роли. Из встроенных ролей эту операцию могут выполнять только *владелец* и *администратор доступа пользователей*. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Управление доступом на основе ролей Azure](role-based-access-control-configure.md).

### Запрос

Замените метод **PUT** следующим кодом URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

Замените текст *{scope}* первой областью *AssignableScope* для настраиваемой роли. В следующих примерах показано, как указать область для различных уровней:

| Уровень | *{Scope}* |
|-------|-----------|
| Подписка | /subscriptions/{subscription-id} |
| Группа ресурсов | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ресурс | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Замените текст *{role-definition-id}* идентификатором GUID для настраиваемой роли, которую требуется обновить.

Замените текст *{api-version}* значением 2015-07-01.

В тексте запроса введите значения в следующем формате:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Имя элемента | Обязательно | Тип | Описание |
|--------------|----------|------|-------------|
| name | Да | Строка | Идентификатор GUID настраиваемой роли, которую требуется обновить. |
| properties.roleName | Да | Строка | Отображаемое имя обновленной настраиваемой роли. |
| properties.description | Нет | Строка | Описание обновленной настраиваемой роли. |
| properties.type | Да | Строка | Необходимо указать значение CustomRole. |
| properties.permissions.actions | Да | Строка | Массив строк действий, определяющих операции, к которым предоставляет доступ обновленная настраиваемая роль. |
| properties.permissions.notActions | Нет | Строка | Массив строк действий, определяющих операции, которые должны быть исключены из списка операций, к которым предоставляет доступ обновленная настраиваемая роль. |
| properties.assignableScopes | Да | Строка | Массив областей, в которых можно использовать обновленную настраиваемую роль для управления доступом. |

### Ответ

Код состояния: 201.

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## Удаление настраиваемой роли

Здесь описывается удаление настраиваемой роли.

Чтобы удалить настраиваемую роль, требуется доступ к операции `Microsoft.Authorization/roleDefinitions/delete` во всех областях `AssignableScopes` этой роли. Из встроенных ролей эту операцию могут выполнять только *владелец* и *администратор доступа пользователей*. Дополнительные сведения о назначениях ролей и управлении доступом к ресурсам Azure см. в статье [Управление доступом на основе ролей Azure](role-based-access-control-configure.md).

### Запрос

Замените метод **DELETE** следующим кодом URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Чтобы настроить запрос, замените следующий текст в URI указанными значениями.

Замените текст *{scope}* областью, в которой требуется удалить определение роли. В следующих примерах показано, как указать область для различных уровней:

| Уровень | *{Scope}* |
|-------|-----------|
| Подписка | /subscriptions/{subscription-id} |
| Группа ресурсов | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ресурс | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Замените текст *{role-definition-id}* идентификатором GUID для определения настраиваемой роли, которую требуется удалить.

Замените текст *{api-version}* значением 2015-07-01.

### Ответ

Код состояния: 200.

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

<!---HONumber=AcomDC_0128_2016-->