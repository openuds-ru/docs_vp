# OpenUDS REST API Guide

# Точка входа API

`http(s)://{hostname|IPv4}/uds/rest/`

где `{hostname|IPv4}` - IPv4 адрес или FQDN брокера OpenUDS

Далее по тексту запросы к API указываются в формате:

**`HTTP метод /api/endpoint`**

Например: 

**`POST /auth/login`**

Это означает, что для запроса необходимо использовать метод HTTP `POST`, URI запроса в таком случае будет выглядеть как:

`http(s)://{hostname|IPv4}/uds/rest/auth/login`

# Аутентификация

**Аутентификация пользователя:**

**`POST /auth/login`**

Параметры тела запроса:

`auth` - имя аутентификатора, тип: *string*
`username` - имя пользователя, тип: *string*
`password` - пароль пользователя, тип: *string*

Пример ответа в случае успешной аутентификации:

```
{'result': 'ok', 'token': 'token_value', 'version': '3.0.0', 'scrambler': 'scrambler_value'}
```

Пример ответа в случае неуспешной аутентификации:

```
{'result': 'error', 'token': None, 'version': '3.0.0', 'error': 'Invalid credentials'}
```

> **Важно:** значение параметра `token` используется в качестве значения HTTP заголовка `X-Auth-Token`, передаваемого во всех последующих запросах к API в рамках текущей сессии.

**Завершение сессии:**

**`GET /auth/logout`**

# Сервис-провайдеры

## Запрос текущей конфигурации

Получение списка провайдеров:

**`GET /providers`**

Получение параметров провайдера:

**`GET /providers/{provider_id}`**

Получение списка всех созданных базовых сервисов по сервис-провайдерам:

**`GET /providers/allservices`**

Получение базовых сервисов провайдера:

**`GET /providers/{provider_id}/services`**

Получение параметров базового сервиса:

**`GET /providers/{provider_id}/services/{service_id}`**

### Параметры path

`{provider_id}` - id сервис-провайдера

`{service_id}` - id базового сервиса

### Примеры

Пример ответа с параметрами провайдера:

```
{
    'id': '90e05812-dc8b-5279-a512-1aae638b3b21',
    'name': 'static',
    'tags': [],
    'services_count': 1,
    'user_services_count': 2,
    'maintenance_mode': False,
    'offers': [
        {
            'name': 'Static Multiple IP',
            'type': 'IPMachinesService',
            'description': 'This service provides access to POWERED-ON Machines by IP',
            'icon': ''
        },
        {
            'name': 'Static Single IP',
            'type': 'IPSingleMachineService',
            'description': 'This service provides access to POWERED-ON Machine by IP',
            'icon': ''
        }
    ],
    'type': 'PhysicalMachinesServiceProvider',
    'type_name': 'Static IP Machines Provider',
    'comments': '',
    'permission': 96,
    'config': ''
}
```

Пример ответа с параметрами базового сервиса:

```
{
    'id': 'afaba279-0f65-5b77-9140-12ec4cf2ff1b',
    'name': 'multiple',
    'tags': [],
    'comments': '',
    'type': 'IPMachinesService',
    'type_name': 'Static Multiple IP',
    'proxy_id': '-1',
    'proxy': '',
    'deployed_services_count': 1,
    'user_services_count': 2,
    'maintenance_mode': False,
    'permission': 96,
    'info': {
        'icon': '',
        'needs_publication': False,
        'max_deployed': -1,
        'uses_cache': False,
        'uses_cache_l2': False,
        'cache_tooltip': 'None',
        'cache_tooltip_l2': 'None',
        'needs_manager': False,
        'allowedProtocols': ['rdp', 'rgs', 'vnc', 'nx', 'x11', 'x2go', 'pcoip', 'nicedcv', 'other'],
        'servicesTypeProvided': ['vdi'],
        'must_assign_manually': False,
        'can_reset': False,
        'can_list_assignables': True
    },
    'ipList': ['10.1.141.74', '10.1.141.75'],
    'token': '',
    'port': '0',
    'skipTimeOnFailure': '0',
    'maxSessionForMachine': '0',
    'lockByExternalAccess': False
}
```

## Создание провайдера

Создание провайдера:

**`PUT /providers`**

### Параметры тела запроса

**Обязательные:**

`name` - имя сервис-провайдера, тип: *string*

`type` - тип сервис-провайдера, тип: *string*

Допустимые значения параметра `type`:

* `oVirtPlatform` - сервис-провайдер oVirt
* `openGnsysPlatform` - сервис-провайдер OpenGnsys
* `openNebulaPlatform` - сервис-провайдер OpenNebula
* `openStackPlatform` - сервис-провайдер OpenStack(legacy)
* `openStackPlatformNew` - сервис-провайдер OpenStack
* `PhysicalMachinesServiceProvider` - сервис-провайдер Static IP
* `ProxmoxPlatform` - сервис-провайдер Proxmox
* `RDSProvider` - сервис-провайдер RDS
* `XenPlatform` - сервис-провайдер XenServer

**Важно:** в зависимости от выбранного типа провайдера, список дополнительно требуемых параметров будет отличаться. Описание см. в приложении.

**Необязательные:**

`comments` - комментарии для сервис-провайдера, тип: *string*

`tags` - тэги  для сервис-провайдера, тип: *list of strings*

[//]: # (TODO: ### Примеры )

## Создание базового сервиса провайдера

Создание базового сервиса провайдера:

**`PUT /providers/{provider_id}/services`**

### Параметры path

`{provider_id}` - id сервис-провайдера

### Параметры тела запроса

**Обязательные:**

`name` - имя базового сервиса, тип: *string*

`type`, `data_type` - тип базового сервиса, тип: *string*

Допустимые значения параметров `type`, `data_type`:

* `openGnsysMachine` - базовый сервис провайдера OpenGnsys
* `ProxmoxLinkedService` - базовый сервис провайдера Proxmox
* `oVirtLinkedService` - базовый сервис провайдера oVirt
* `XenLinkedService` - базовый сервис провайдера XenServer
* `openNebulaLiveService` - базовый сервис провайдера OpenNebula
* `openStackLiveService` - базовый сервис провайдера OpenStack и OpenStack(legacy)
* `IPMachinesService` - базовый сервис Static Multiple IP провайдера Static IP
* `IPSingleMachineService` - базовый сервис Static Single IP провайдера Static IP
* `RemoteSessionService` - базовый сервис Session провайдера RDS
* `RemoteAppManagedService` - базовый сервис RemoteApp провайдера RDS

**Важно:** в зависимости от выбранного типа базового сервиса, список дополнительно требуемых параметров будет отличаться. Описание см. в приложении.

**Необязательные:**

`comments` - комментарии для базового сервиса, тип: *string*

`tags` - тэги  для базового сервиса, тип: *list of strings*

[//]: # (TODO: ### Примеры)

# Аутентификаторы, группы и пользователи

## Запрос текущей конфигурации

Получение списка аутентификаторов:

**`GET /authenticators`**

Получение параметров аутентификатора:

**`GET /authenticators/{auth_id}`**

Получение списка созданных в аутентификаторе групп:

**`GET /authenticators/{auth_id}/groups`**

Получение параметров группы аутентификатора:

**`GET /authenticators/{auth_id}/groups/{group_id}`**

Получение списка созданных в аутентификаторе пользователей:

**`GET /authenticators/{auth_id}/users`**

Получение параметров пользователя:

**`GET /authenticators/{auth_id}/users/{user_id}`**

Получение списка пользователей группы:

**`GET /authenticators/{auth_id}/groups/{group_id}/users`**

### Параметры path

`{auth_id}` - id аутентификатора

`{group_id}` - id группы

`{user_id}` - id пользователя

### Примеры

Пример ответа с параметрами аутентификатора (Active Directory):

```
{
    'numeric_id': 14,
    'id': 'ac0702cd-cb63-5ea1-b6a6-ae3d5adff46d',
    'name': 'dc1',
    'tags': [],
    'comments': '',
    'priority': 2,
    'visible': True,
    'small_name': 'dc1',
    'users_count': 1,
    'type': 'ActiveDirectoryAuthenticator',
    'type_name': 'Active Directory Authenticator',
    'type_info': {
        'canSearchUsers': True,
        'canSearchGroups': True,
        'needsPassword': False,
        'userNameLabel': 'User name',
        'groupNameLabel': 'Group name',
        'passwordLabel': 'Password',
        'canCreateUsers': True,
        'isExternal': True
    },
    'permission': 96,
    'host': '10.1.1.1',
    'ssl': False,
    'username': 'user@domain.local',
    'password': 'password',
    'timeout': '10',
    'ldapBase': 'dc=domain,dc=local',
    'groupBase': '',
    'defaultDomain': '',
    'nestedGroups': False
}
```

Пример ответа с параметрами группы:

```
{
    'id': '71326643-3aba-5f20-9717-a09c44a5540a',
    'name': 'Domain Admins',
    'comments': '',
    'state': 'A',
    'type': 'group',
    'meta_if_any': False,
    'pools': ['d1bd5dff-5287-5afd-9444-493a3528a535']
}
```

Пример ответа с параметрами пользователя:

```
{
'name': 'vdiuser', 
'real_name': 'vdiuser', 
'comments': '', 
'state': 'A', 
'staff_member': False, 
'is_admin': False, 
'last_access': 1603712668, 
'parent': None, 
'id': 'e954f545-4870-5da2-8a95-271c0e930d3f', 
'role': 'User'
}
```

## Создание нового аутентификатора

Создание нового аутентификатора:

**`PUT /authenticators`**

### Параметры тела запроса

`name` - имя аутентификатора, тип: *string*

`small_name` - метка, тип: *string*

`tags` - тэги для этого элемента, тип: *list of strings*

`comments` - комментарии для этого элемента, тип: *string*

`priority` - приоритет аутентификатора, тип: *integer*

`visible` - видимость аутентификатора, тип: *boolean*

`data_type` - тип аутентификатора, тип: *string*

Допустимые значения параметра `data_type`:

* `ActiveDirectoryAuthenticator` - аутентификатор Active Directory
* `InternalDBAuth` - аутентификатор Internal Database
* `SimpleLdapAuthenticator` - аутентификатор Simple LDAP
* `RegexLdapAuthenticator` - аутентификатор Regex LDAP
* `SAML20Authenticator` - аутентификатор SAML
* `RadiusAuthenticator` - аутентификатор Radius
* `IPAuth` - аутентификатор IP

**Важно:** в зависимости от выбранного типа аутентификатора, список дополнительно требуемых параметров будет отличаться. Описание см. в приложении.

[//]: # (TODO: ### Примеры)

## Создание группы аутентификатора

Создание группы аутентификатора:

**`PUT /authenticators/{auth_id}/groups`**

### Параметры path

`{auth_id}` - id аутентификатора

### Параметры тела запроса

`type` - тип группы, тип: *string*

Допустимые значения параметра `type`:

* `group` - группа
* `meta` - метагруппа

`name` - имя группы, тип: *string*

`comments` - комментарии для этого элемента, тип: *string*

`state` - статус группы, тип: *string*. При создании используйте статус `A` (Active). Перечень остальных значений параметра `state` см. в приложении.

`meta_if_any` - режим сопоставления, тип: *boolean*. Значение, отличное от `False`, учитывается только для групп типа `meta`.

`pools` - список id сервис-пулов, в которые добавлена группа, тип: *list of strings*

[//]: # (TODO: ### Примеры)

## Создание пользователя аутентификатора

Создание пользователя аутентификатора:

**`PUT /authenticators/{auth_id}/users`**

### Параметры path

`{auth_id}` - id аутентификатора

### Параметры тела запроса

`name` имя пользователя - , тип: *string*

`real_name` - реальное имя пользователя, тип: *string*

`comments` - комментарии для этого элемента, тип: *string*

`state` - статус пользователя, тип: *string*. При создании используйте статус `A` (Active). Перечень остальных значений параметра `state` см. в приложении.

`staff_member` - признак наличия полномочий типа "Оператор", тип: *boolean*. Дополнительно см. параметр `role`.

`is_admin` - признак наличия полномочий типа "Администратор", тип: *boolean*. Дополнительно см. параметр `role`.

`groups` - список id групп, в которые добавлен пользователь, тип: *list of strings*

`role` - роль пользователя, тип: *string*

Допустимые значения параметра `role`:

* `admin` - администратор. Значения параметров `is_admin` и `staff_member` должны быть установлены в `True`.
* `staff` - оператор. Значение параметра `is_admin` должно быть установлено в `False`, параметра `staff_member` - в `True`.
* `user` - пользователь. Значения параметров `is_admin` и `staff_member` должны быть установлены в `False`.

[//]: # (TODO: ### Примеры)

# Менеджеры ОС

## Запрос текущей конфигурации

Получение списка менеджеров ОС:

**`GET /osmanagers`**

### Примеры

Пример ответа со списком менеджеров ОС:

```
[
{'id': '6a2c5205-b888-5e7b-83eb-8fc32787e718', 'name': 'Linux', 'tags': [], 'deployed_count': 6, 'type': 'LinuxManager', 'type_name': 'Linux OS Manager', 'servicesTypes': ['vdi'], 'comments': '', 'permission': 96}, 
{'id': '05dc012f-1ab8-51f4-9fce-b321f5945b64', 'name': 'WinBasic', 'tags': [], 'deployed_count': 1, 'type': 'WindowsManager', 'type_name': 'Windows Basic OS Manager', 'servicesTypes': ['vdi'], 'comments': '', 'permission': 96}
]
```

# Транспорты

## Запрос текущей конфигурации

Получение списка созданных транспортов:

**`GET /transports`**

Получение параметров транспорта:

**`GET /transports/{transport_id}`**

### Параметры path

`{transport_id}` - id транспорта

### Примеры

Пример ответа с параметрами транспорта (RDP direct):

```
{
    'id': 'b925c748-c3cc-585e-a8ec-9d7dd3a2c649',
    'name': 'rdp-direct',
    'tags': [],
    'comments': '',
    'priority': 1,
    'nets_positive': True,
    'label': '',
    'networks': [],
    'allowed_oss': [],
    'pools': [{'id': '4302fdcb-a692-5262-93d0-ab460bbd1f84'}, {'id': 'd1bd5dff-5287-5afd-9444-493a3528a535'}],
    'pools_count': 2,
    'deployed_count': 2,
    'type': 'RDPTransport',
    'type_name': 'RDP',
    'protocol': 'rdp',
    'permission': 96,
    'useEmptyCreds': False,
    'fixedName': '',
    'fixedPassword': '',
    'withoutDomain': False,
    'fixedDomain': '',
    'allowSmartcards': False,
    'allowPrinters': False,
    'allowDrives': False,
    'enforceDrives': '',
    'allowSerials': False,
    'allowClipboard': True,
    'allowAudio': True,
    'allowWebcam': False,
    'usbRedirection': False,
    'wallpaper': False,
    'multimon': False,
    'aero': False,
    'smooth': True,
    'showConnectionBar': True,
    'credssp': True,
    'rdpPort': '3389',
    'screenSize': '-1x-1',
    'colorDepth': '24',
    'alsa': False,
    'multimedia': False,
    'printerString': '',
    'smartcardString': '',
    'customParameters': '/sec:tls',
    'allowMacMSRDC': False,
    'customParametersMAC': ''
}
```

## Создание нового транспорта

Создание нового транспорта:

**`PUT /transports`**

### Параметры тела запроса

`name` - имя транспорта, тип: *string*

`tags` - тэги для этого элемента, тип: *list of strings*

`comments` - комментарии для этого элемента, тип: *string*

`priority` - приоритет транспорта, тип: *integer*

`nets_positive` - сетевой доступ, тип: *boolean*

`label` - метка, тип: *string*

`networks` - список id сетей, ассоциированных с транспортом, тип: *list of strings*

`allowed_oss` - список разрешенных устройств, тип: *list of strings*

Перечень значений параметра `allowed_oss` (допустимо указывать несколько значений, либо передать пустую строку(без ограничений по устройствам)):

* `WindowsPhone`
* `Android`
* `Linux`
* `Windows`
* `iPad`
* `iPhone`
* `Macintosh`
* `ChromeOS`
* `WYSE`

`pools` - список id сервис-пулов, в которые добавлен транспорт, тип: *list of strings*

`type` - тип транспорта, тип: *string*

Допустимые значения параметра `type`:

* `RDPTransport` - транспорт RDP direct
* `TSRDPTransport` - транспорт RDP tunnel
* `RATransport` - транспорт RemoteApp direct
* `TRATransport` - транспорт RemoteApp tunnel
* `HTML5RDPTransport` - транспорт HTML5 RDP
* `HTML5RATransport` - транспорт HTML5 RemoteApp
* `PCoIPTransport` - транспорт PCoIP Cloud Access
* `SPICETransport` - транспорт SPICE direct
* `TSSPICETransport` - транспорт SPICE tunnel
* `URLTransport` - транспорт URL Launcher
* `X2GOTransport` - транспорт X2Go direct
* `TX2GOTransport` - транспорт X2Go tunnel

**Важно:** в зависимости от выбранного типа транспорта, список дополнительно требуемых параметров будет отличаться. Описание см. в приложении.

[//]: # (TODO: ### Примеры)

## Добавление сетей

Добавление сетей в транспорт:

**`PUT /transports/{transport_id}/networks`**

### Параметры path

`{transport_id}` - id транспорта

### Параметры тела запроса

**Обязательные:**

`id` - id добавляемой сети, тип: *string*

# Сервис-пулы

## Запрос текущей конфигурации

Получение списка сервис-пулов и их параметров:

**`GET /servicespools/overview`**

Получение параметров пула:

**`GET /servicespools/{pool_id}`**

Получение списка групп, добавленных в пул:

**`GET /servicespools/{pool_id}/groups`**

Получение списка транспортов, добавленных в пул:

**`GET /servicespools/{pool_id}/transports`**

Получение списка сервисов пула (тонких клонов, виртуальных машин, и т.д.), назначенных пользователям (закрепленных за ними):

**`GET /servicespools/{pool_id}/services`**

Получение списка сервисов пула, находящихся в кэше (развернутые, но не назначенные пользователям экземпляры):

**`GET /servicespools/{pool_id}/cache`**

Получение списка назначаемых сервисов:

**`GET /servicespools/{pool_id}/listAssignables`**

### Параметры path

`{pool_id}` - id пула

### Примеры

Пример ответа с параметрами пула:

```
{
    'id': 'd1bd5dff-5287-5afd-9444-493a3528a535',
    'name': 'static-mult',
    'short_name': '',
    'tags': [],
    'parent': 'multiple',
    'parent_type': 'IPMachinesService',
    'comments': '',
    'state': 'A',
    'thumb': '',
    'account': '',
    'account_id': None,
    'service_id': 'afaba279-0f65-5b77-9140-12ec4cf2ff1b',
    'provider_id': '90e05812-dc8b-5279-a512-1aae638b3b21',
    'image_id': None,
    'initial_srvs': 0,
    'cache_l1_srvs': 0,
    'cache_l2_srvs': 0,
    'max_srvs': 0,
    'show_transports': True,
    'visible': True,
    'allow_users_remove': False,
    'allow_users_reset': False,
    'ignores_unused': False,
    'fallbackAccess': 'ALLOW',
    'meta_member': [],
    'calendar_message': '',
    'user_services_count': 1,
    'user_services_in_preparation': 0,
    'restrained': False,
    'permission': 96,
    'info': {
        'icon': '',
        'needs_publication': False,
        'max_deployed': -1,
        'uses_cache': False,
        'uses_cache_l2': False,
        'cache_tooltip': 'None',
        'cache_tooltip_l2': 'None',
        'needs_manager': False,
        'allowedProtocols': ['rdp', 'rgs', 'vnc', 'nx', 'x11', 'x2go', 'pcoip', 'nicedcv', 'other'],
        'servicesTypeProvided': ['vdi'],
        'must_assign_manually': False,
        'can_reset': False,
        'can_list_assignables': True
    },
    'pool_group_id': None,
    'pool_group_name': 'Default',
    'pool_group_thumb': '',
    'usage': '100%'
}
```

Пример ответа со списком добавленных в пул групп:

```
[
    {
        'id': '71326643-3aba-5f20-9717-a09c44a5540a',
        'auth_id': 'ac0702cd-cb63-5ea1-b6a6-ae3d5adff46d',
        'name': 'Domain Admins',
        'group_name': 'Domain Admins@dc1',
        'comments': '',
        'state': 'A',
        'type': 'group',
        'auth_name': 'dc1'
    },
    {
        'id': '19b400ab-db2b-5fc7-9d7e-1e3662df594b',
        'auth_id': '750f3742-f761-5fc6-b4cc-7115bc66404b',
        'name': 'vdigroup',
        'group_name': 'vdigroup@int',
        'comments': '',
        'state': 'A',
        'type': 'group',
        'auth_name': 'int'
    },
    {
        'id': 'de14df32-69d8-5add-96c5-be758f2ca49c',
        'auth_id': '750f3742-f761-5fc6-b4cc-7115bc66404b',
        'name': 'vdigroup1',
        'group_name': 'vdigroup1@int',
        'comments': '',
        'state': 'A',
        'type': 'group',
        'auth_name': 'int'
    }
]
```

Пример ответа со списком добавленных в пул транспортов:

```
[
    {
        'id': 'b925c748-c3cc-585e-a8ec-9d7dd3a2c649',
        'name': 'rdp-direct',
        'type': {
            'name': 'RDP',
            'type': 'RDPTransport',
            'description': 'RDP Protocol. Direct connection.',
            'icon': '',
            'group': 'Direct'
        },
        'comments': '',
        'priority': 1,
        'trans_type': 'RDP'
    },
    {
        'id': 'c532143e-86be-500b-9236-a449825403b2',
        'name': 'x2go-direct',
        'type': {
            'name': 'X2Go',
            'type': 'X2GOTransport',
            'description': 'X2Go access (Experimental). Direct connection.',
            'icon': '',
            'group': 'Direct'
        },
        'comments': '',
        'priority': 2,
        'trans_type': 'X2Go'
    }
]
```

Пример ответа со списком назначенных пользователям сервисов пула:

```
[
    {
        'id': 'b1931a7d-3a2c-5d8a-9d8f-d517188ac5eb',
        'id_deployed_service': 'd1bd5dff-5287-5afd-9444-493a3528a535',
        'unique_id': '10.1.141.74',
        'friendly_name': '10.1.141.74',
        'state': 'U',
        'os_state': 'U',
        'state_date': 1707128501,
        'creation_date': 1707128500,
        'revision': '',
        'ip': '10.1.141.74',
        'actor_version': 'unknown',
        'owner': 'vdiuser@int',
        'owner_info': {'auth_id': '750f3742-f761-5fc6-b4cc-7115bc66404b', 'user_id': '0f489bb0-9f70-5ee8-bb09-a30458d69fef'},
        'in_use': True,
        'in_use_date': 1707128500,
        'source_host': 'hostname',
        'source_ip': '10.1.44.13'
    },
    {
        'id': '9599b697-8b4e-5ca1-98f8-88aff03a227d',
        'id_deployed_service':
        'd1bd5dff-5287-5afd-9444-493a3528a535',
        'unique_id': '10.1.141.76',
        'friendly_name': '10.1.141.76',
        'state': 'U',
        'os_state': 'U',
        'state_date': 1707985180,
        'creation_date': 1707985180,
        'revision': '',
        'ip': '10.1.141.76',
        'actor_version': 'unknown',
        'owner': 'adm@openuds.local@dc1',
        'owner_info': {'auth_id': 'ac0702cd-cb63-5ea1-b6a6-ae3d5adff46d', 'user_id': '7c2aec6f-3824-5a8b-8a80-0bb5e6ddbea9'},
        'in_use': True,
        'in_use_date': 1707985180,
        'source_host': '',
        'source_ip': ''
    }
]
```

Пример ответа со списком сервисов в кэше:

```
[
    {
        'id': 'b2b9c5ba-0943-58f3-ba06-05b0c2738028', 
        'id_deployed_service': '9a801202-eb99-5f75-9137-c64aad365e1f', 
        'unique_id': '52:54:00:00:00:01', 
        'friendly_name': 'vdi3-alt-00', 
        'state': 'U', 
        'os_state': 'U', 
        'state_date': 1631271805, 
        'creation_date': 1631271750, 
        'revision': 3, 
        'ip': '10.1.2.13', 
        'actor_version': '2.2.0', 
        'cache_level': 1
    }
]
```

## Создание сервис-пула

Создание нового пула:

**`PUT /servicespools`**

### Параметры тела запроса

`name` - имя пула, тип: *string*

`service_id` - id базового сервиса, тип: *string*

`osmanager_id` - id менеджера ОС, тип: *string*. При создании пула на основе сервиса, не использующего менеджер ОС (например Static IP), используйте значение `None`.

`short_name` - короткое имя, тип: *string*

`comments` - комментарии для этого элемента, тип: *string*

`tags` - тэги для этого элемента, тип: *list of strings*

`image_id` - id изображения для пула, тип: *string*. Для изображения по умолчанию используйте значение `-1`.

`pool_group_id` - id группы пулов, тип: *string*. Для группы по умолчанию используйте значение `-1`.

`visible` - видимость пула, тип: *boolean*

`calendar_message` - сообщение при ограничении доступа по календарю, тип: *string*

`allow_users_remove` - разрешить удаление пользователями, тип: *boolean*

`allow_users_reset` - разрешить сброс пользователями, тип: *boolean*

`ignores_unused` - игнорировать неиспользуемые, тип: *boolean*

`show_transports` - показать транспорты, тип: *boolean*

`account_id` - учет использования, тип: *string*. Для настройки по умолчанию используйте значение `-1`.

`initial_srvs` - первоначально доступные сервисы, тип: *integer*. При создании пула на основе сервиса, не использующего менеджер ОС (например Static IP), используйте значение `0`.

`cache_l1_srvs` - сервисы, хранящиеся в кэше, тип: *integer*. При создании пула на основе сервиса, не использующего менеджер ОС (например Static IP), используйте значение `0`.

`cache_l2_srvs` - сервисы, хранящиеся в L2 кэше, тип: *integer*. При создании пула на основе сервиса, не использующего менеджер ОС (например Static IP), используйте значение `0`.

`max_srvs` - максимальное количество предоставляемых сервисов, тип: *integer*. При создании пула на основе сервиса, не использующего менеджер ОС (например Static IP), используйте значение `0`.

### Примеры

[//]: # (TODO: ### Примеры)

Пример выдачи:

```
[
{
'id': 'ee27235f-59c3-5407-b897-d1c9a7ed84e0', 
'name': 'AltLinux', 
'tags': [], 
'comments': '', 
'type': 'oVirtLinkedService', 
'type_name': 'oVirt/RHEV Linked Clone', 
'proxy_id': '-1', 
'proxy': '', 
'deployed_services_count': 2, 
'user_services_count': 2, 
'maintenance_mode': False, 
'permission': 96, 
'info': {'icon': '...', 'needs_publication': True, 'max_deployed': -1, 'uses_cache': True, 'uses_cache_l2': True, 'cache_tooltip': 'Number of desired machines to keep running waiting for a user', 'cache_tooltip_l2': 'Number of desired machines to keep suspended waiting for use', 'needs_manager': True, 'allowedProtocols': ['rdp', 'rgs', 'vnc', 'nx', 'x11', 'x2go', 'pcoip', 'other', 'spice'], 'servicesTypeProvided': ['vdi'], 'must_assign_manually': False, 'can_reset': True, 'can_list_assignables': False}
}, 
... 
]
```

## Изменение конфигурации сервис-пула

Изменение параметров пула:

**`PUT /servicespools/{pool_id}`**

### Параметры path

`pool_id` - id сервис-пула

### Параметры тела запроса

`name` - имя пула, тип: *string*

`short_name` - короткое имя, тип: *string*

`comments` - комментарии для этого элемента, тип: *string*

`tags` - тэги для этого элемента, тип: *list of strings*

`image_id` - id изображения для пула, тип: *string*. Для изображения по умолчанию используйте значение `-1`.

`pool_group_id` - id группы пулов, тип: *string*. Для группы по умолчанию используйте значение `-1`.

`visible` - видимость пула, тип: *boolean*

`calendar_message` - сообщение при ограничении доступа по календарю, тип: *string*

`allow_users_remove` - разрешить удаление пользователями, тип: *boolean*

`allow_users_reset` - разрешить сброс пользователями, тип: *boolean*

`ignores_unused` - игнорировать неиспользуемые, тип: *boolean*

`show_transports` - показать транспорты, тип: *boolean*

`account_id` - учет использования, тип: *string*. Для настройки по умолчанию используйте значение `-1`.

`initial_srvs` - первоначально доступные сервисы, тип: *integer*. Для пула на основе сервиса, не использующего менеджер ОС (например Static IP), значение параметра равно `0` и не модифицируется.

`cache_l1_srvs` - сервисы, хранящиеся в кэше, тип: *integer*. Для пула на основе сервиса, не использующего менеджер ОС (например Static IP), значение параметра равно `0` и не модифицируется.

`cache_l2_srvs` - сервисы, хранящиеся в L2 кэше, тип: *integer*. Для пула на основе сервиса, не использующего менеджер ОС (например Static IP), значение параметра равно `0` и не модифицируется.

`max_srvs` - максимальное количество предоставляемых сервисов, тип: *integer*. Для пула на основе сервиса, не использующего менеджер ОС (например Static IP), значение параметра равно `0` и не модифицируется.

## Удаление сервис-пула

Удаление сервис-пула:

**`DELETE /servicespools/{pool_id}`**

### Параметры path

`pool_id` - id сервис-пула

## Добавление группы в сервис-пул

Добавление в пул группы доступа:

**`PUT /servicespools/{pool_id}/groups`**

### Параметры path

`pool_id` - id сервис-пула

### Параметры тела запроса

`id` - id группы, тип: *string*

## Добавление транспорта в сервис-пул

Добавление транспорта в сервис-пул:

**`PUT /servicespools/{pool_id}/transports`**

### Параметры path

`pool_id` - id сервис-пула

### Параметры тела запроса

`id` - id транспорта, тип: *string*

## Публикация сервисов пула

Публикация сервисов пула:

**`GET /servicespools/{pool_id}/publications/publish`**

### Параметры path

`pool_id` - id сервис-пула

[//]: # (TODO: )

В случае успешного запуска процесса публикации возвращается ответ `ok`.

## Назначение сервисов

Назначить пользователю экземпляр сервиса пула:

**`GET /servicespools/{pool_id}/createFromAssignable`**

**!** - доступно только для сервисов, поддерживающих ручное назначение пользователям (Static IP провайдер - Multiple IP сервис)

### Параметры path

`{pool_id}` - id сервис-пула

### Параметры тела запроса

`user_id` - id пользователя, тип: *string*

`assignable_id` - id назначаемого сервиса пула, тип: *string*

Для получения `assignable_id` необходимо запросить список назначаемых сервисов пула, см. соответствующий метод в разделе
`Сервис-пулы / Запрос текущей конфигурации`.

# Разрешения объектов

## Запрос текущей конфигурации

Получение разрешений объекта системы:

**`GET /permissions/{cls}/{uuid}`**

### Параметры path

`{cls}` - класс объекта (сервис-провайдер, аутентификатор, транспорт, сервис-пул и т.д.)

Допустимые значения параметра `cls`:

* `providers`
* `service`
* `authenticators`
* `osmanagers`
* `transports`
* `networks`
* `servicespools`
* `calendars`
* `metapools`
* `accounts`

`{uuid}` - id объекта

### Примеры

Пример ответа со списком разрешений для сервис-провайдера:

```
[
    {
        'id': '4b409ec8-b5e5-5eae-a4f1-34b701c50bbc',
        'type': 'group',
        'auth': '750f3742-f761-5fc6-b4cc-7115bc66404b',
        'auth_name': 'int',
        'entity_id': '19b400ab-db2b-5fc7-9d7e-1e3662df594b',
        'entity_name': 'vdigroup',
        'perm': 64,
        'perm_name': 'Manage'
    },
    {
        'id': '84c4535d-4823-56c4-be5c-6c79ec779a69',
        'type': 'user',
        'auth': '750f3742-f761-5fc6-b4cc-7115bc66404b',
        'auth_name': 'int',
        'entity_id': '0f489bb0-9f70-5ee8-bb09-a30458d69fef',
        'entity_name': 'vdiuser',
        'perm': 32,
        'perm_name': 'Read'
    }
]
```

## Добавление разрешений

Добавить разрешения для объекта:

**`PUT /permissions/{cls}/{uuid}/{perm_type}/add/{entity_id}`**

### Параметры path

`{cls}` - класс объекта (сервис-провайдер, аутентификатор, транспорт, сервис-пул и т.д.)

Допустимые значения параметра `cls`:

* `providers`
* `service`
* `authenticators`
* `osmanagers`
* `transports`
* `networks`
* `servicespools`
* `calendars`
* `metapools`
* `accounts`

`{uuid}` - id объекта

`{perm_type}` - тип выдаваемого разрешения, на пользователя или на группу

Допустимые значения параметра `perm_type`:

* `users`
* `groups`

`{entity_id}` - id пользователя или группы (в зависимости от типа разрешения)

### Параметры тела запроса

`perm` - выдаваемое разрешение, тип: *string*

Допустимые значения параметра `perm`:

* `1` - чтение

* `2` - управление

[//]: # (TODO: ### Примеры)

# Системные настройки

## Запрос текущей конфигурации

Получение конфигурации системы:

**`GET /config`**

# Пользователи и сервисы

**Важно**: действия выполняются в контексте учетной записи пользователя, от имени которой был сделан REST API запрос.

Получение списка доступных пользователю сервисов и транспортов:

**`GET /connection`**

Проверка возможности подключения для указанного сервиса и транспорта, привязка пользователя к экземпляру сервиса:

**`GET /connection/{service_id}/{transport_id}`**

### Параметры path

`{service_id}` - id сервиса

`{uuid}` - id транспорта

### Примеры

Пример ответа со списком доступных пользователю сервисов и транспортов:

```
{
    'id': 'F904ad90d-5f84-51ce-8510-840e57014fa9', - id сервиса
...
    'name': 'Xubuntu', - имя сервиса
...
    'transports': [
        {
            'id': 'c0d8ac20-4f2b-516c-bf62-8ce7f50a3113', - id транспорта
            'name': 'spice', - имя транспорта
...
        }
    ],
...
}
```

Пример ответа в случае успешной проверки возможности подключения для указанного сервиса и транспорта:

```
{
    'result': 'ok',
...
}
```

# Приложение

## Статус объектов

Перечень всех возможных статусов объектов VDI (параметр `state`).
Не все объекты поддерживают все статусы.

```
    'A' - Active
    'B' - Blocked
    'C' - Canceled
    'E' - Error
    'F' - Finished
    'H' - Balancing
    'I' - Inactive
    'J' - Too many preparing services
    'K' - Canceling
    'L' - Waiting publication
    'M' - Removing
    'P' - In preparation
    'R' - Removable
    'S' - Removed
    'T' - Restrained
    'U' - Valid
    'W' - Running
    'X' - Waiting execution
    'Y' - In maintenance
    'Z' - Waiting OS
    'V' - Meta member
```

## Параметры провайдеров и базовых сервисов

### PhysicalMachinesServiceProvider

**Необязательные:**

`config` - расширенная конфигурация, тип: *string*

#### IPMachinesService

`ipList` - список серверов, тип: *list of strings*

`token` - токен сервиса, тип: *string*

`port` - порт для проверки соединения, тип: *integer*

`skipTimeOnFailure` - интервал между проверками соединения при неудаче, тип: *integer*

`maxSessionForMachine` - максимальная продолжительность сеанса, в часах, тип: *integer*

`lockByExternalAccess` - блокировать машину при внешнем доступе, тип: *boolean*

`proxy_id` - id прокси, тип: *string*. Если отсутствует, используйте значение `-1`.

## Параметры аутентификаторов

### ActiveDirectoryAuthenticator

`host` - IP или FQDN сервера AD, тип: *string*

`username` - имя пользователя, тип: *string*

`password` - пароль, тип: *string*

`ldapBase` - база поиска, тип: *string*

`ssl` - использовать SSL, тип: *string*. Допустимые значения: `true`, `false`.

`timeout` - таймаут подключения, тип: *string*

`groupBase` - база поиска для групп, тип: *string*

`defaultDomain` - домен по умолчанию, тип: *string*

`nestedGroups` - получить вложенные группы, тип: *string*. Допустимые значения: `true`, `false`.

### InternalDBAuth

`differentForEachHost` - разные пользователи для каждого хоста, тип: *string*. Допустимые значения: `true`, `false`.

`reverseDns` - обратный DNS, тип: *string*. Допустимые значения: `true`, `false`.

`acceptProxy` - разрешить прокси, тип: *string*. Допустимые значения: `true`, `false`.

### RegexLdapAuthenticator

`host` - адрес или имя LDAP сервера, тип: *string*

`username` - имя пользователя, тип: *string*

`password` - пароль, тип: *string*

`ldapBase` - база поиска, тип: *string*

`port` - порт LDAP сервера, тип: *string*

`ssl` - использовать SSL, тип: *string*. Допустимые значения: `true`, `false`.

`timeout` - таймаут подключения, тип: *string*

`userClass` - класс для пользователей LDAP, тип: *string*

`userIdAttr` - атрибут идентификатора пользователя, тип: *string*

`groupNameAttr` - атрибуты имени группы, тип: *string*

`userNameAttr` - атрибут имени пользователя, тип: *string*

`altClass` - альтернативный класс для поиска групп, тип: *string*

### SimpleLdapAuthenticator

`host` - адрес или имя LDAP сервера, тип: *string*

`username` - имя пользователя, тип: *string*

`password` - пароль, тип: *string*

`ldapBase` - база поиска, тип: *string*

`port` - порт LDAP сервера, тип: *string*

`ssl` - использовать SSL, тип: *string*. Допустимые значения: `true`, `false`.

`timeout` - таймаут подключения, тип: *string*

`userClass` - класс для пользователей LDAP, тип: *string*

`groupClass` - класс для групп LDAP, тип: *string*

`userIdAttr` - атрибут идентификатора пользователя, тип: *string*

`groupIdAttr` - атрибут идентификатора группы, тип: *string*

`memberAttr` - атрибут членства в группе, тип: *string*

`userNameAttr` - атрибут имени пользователя, тип: *string*

### SAML20Authenticator

`privateKey` - приватный ключ, тип: *string*

`serverCertificate` - сертификат, тип: *string*

`idpMetadata` - метаданные IDP, тип: *string*

`userNameAttr` - атрибут имени пользователя, тип: *string*

`groupNameAttr` - атрибуты имени группы, тип: *string*

`realNameAttr` - атрибуты реального имени пользователя, тип: *string*

`entityID` - идентификатор SP, тип: *string*

`usePassword` - использовать пароль, тип: *string*. Допустимые значения: `true`, `false`.

`pwdAttr` - атрибут пароля, тип: *string*

## Параметры транспортов

### RDPTransport

`useEmptyCreds` - пропустить данные аккаунта, тип: *string*. Допустимые значения: `true`, `false`.

`fixedName` - имя пользователя, тип: *string*

`fixedPassword` - пароль, тип: *string*

`withoutDomain` - без домена, тип: *string*. Допустимые значения: `true`, `false`.

`fixedDomain` - домен, тип: *string*

`allowSmartcards` - разрешить смарткарты, тип: *string*. Допустимые значения: `true`, `false`.

`allowPrinters` - разрешить принтеры, тип: *string*. Допустимые значения: `true`, `false`.

`allowDrives` - политика локальных дисков, тип: *string*. Допустимые значения: `true` - allow any, `dynamic` - allow PnP, `false` - allow none.

`enforceDrives` - принудительное подключение дисков, тип: *string*

`allowSerials` - разрешить серийные порты, тип: *string*. Допустимые значения: `true`, `false`.

`allowClipboard` - включить буфер обмена, тип: *string*. Допустимые значения: `true`, `false`.

`allowAudio` - включить звук, тип: *string*. Допустимые значения: `true`, `false`.

`allowWebcam` - включить веб-камеру, тип: *string*. Допустимые значения: `true`, `false`.

`usbRedirection` - проброс USB, тип: *string*.

Допустимые значения параметра `usbRedirection`:

* `true` - Allow all
* `false` - Allow none
* `{ca3e7ab9-b4c3-4ae6-8251-579ef933890f}` - Cameras
* `{4d36e967-e325-11ce-bfc1-08002be10318}` - Disk Drives
* `{4d36e979-e325-11ce-bfc1-08002be10318}` - Printers
* `{50dd5230-ba8a-11d1-bf5d-0000f805f530}` - Smartcards
* `{745a17a0-74d3-11d0-b6fe-00a0c90f57da}` - HIDs

`wallpaper` - обои/темы, тип: *string*. Допустимые значения: `true`, `false`.

`multimon` - несколько мониторов, тип: *string*. Допустимые значения: `true`, `false`.

`aero` - разрешить композицию рабочего стола, тип: *string*. Допустимые значения: `true`, `false`.

`smooth` - сглаживание шрифтов, тип: *string*. Допустимые значения: `true`, `false`.

`showConnectionBar` - панель подключения, тип: *string*. Допустимые значения: `true`, `false`.

`credssp` - поддержка Credssp, тип: *string*. Допустимые значения: `true`, `false`.

`rdpPort` - порт RDP, тип: *string*

`screenSize` - размер экрана, тип: *string*. Для полноэкранного режима используйте значение `-1x-1`

`colorDepth` - глубина цвета, тип: *string*

`alsa` - использовать Alsa, тип: *string*. Допустимые значения: `true`, `false`.

`multimedia` - оптимизация видеопотока, тип: *string*. Допустимые значения: `true`, `false`.

`printerString` - строка принтера, тип: *string*

`smartcardString` - строка Smartcard, тип: *string*

`customParameters` - дополнительные параметры Linux, тип: *string*

`allowMacMSRDC` - разрешить клиент Microsoft RDP, тип: *string*. Допустимые значения: `true`, `false`.

`customParametersMAC` - дополнительные параметры MacOS, тип: *string*

### TSRDPTransport

`tunnelServer` - туннельный сервер, тип: *string*

`tunnelWait` - время ожидания туннеля, тип: *string*

`verifyCertificate` - проверка SSL-сертификата, тип: *string*. Допустимые значения: `true`, `false`.

**!** Остальные параметры аналогичны типу транспорта `RDPTransport`.

### RATransport

`fixedApp` - алиас приложения, тип: *string*

**!** Остальные параметры аналогичны типу транспорта `RDPTransport`.

### TRATransport

`fixedApp` - алиас приложения, тип: *string*

**!** Остальные параметры аналогичны типу транспорта `TSRDPTransport`.

### HTML5RDPTransport

`guacamoleServer` - туннельный сервер, тип: *string*

`useGlyptodonTunnel` - использовать туннель Glyptodon Enterprise, тип: *string*. Допустимые значения: `true`, `false`.

`useEmptyCreds` - пропустить данные аккаунта, тип: *string*. Допустимые значения: `true`, `false`.

`fixedName` - имя пользователя, тип: *string*

`fixedPassword` - пароль, тип: *string*

`withoutDomain` - без домена, тип: *string*. Допустимые значения: `true`, `false`.

`fixedDomain` - домен, тип: *string*

`wallpaper` - показать обои, тип: *string*. Допустимые значения: `true`, `false`.

`desktopComp` - разрешить композицию рабочего стола, тип: *string*. Допустимые значения: `true`, `false`.

`smooth` - сглаживание шрифтов, тип: *string*. Допустимые значения: `true`, `false`.

`enableAudio` - включить аудио, тип: *string*. Допустимые значения: `true`, `false`.

`enableAudioInput` - включить микрофон, тип: *string*. Допустимые значения: `true`, `false`.

`enablePrinting` - включить печать, тип: *string*. Допустимые значения: `true`, `false`.

`enableFileSharing` - обмен файлами, тип: *string*. Допустимые значения: `true`, `false`.

Допустимые значения параметра `enableFileSharing`:

* `false` - Disable file sharing
* `down` - Allow download only
* `up` - Allow upload only
* `true` - Enable file sharing

`enableClipboard` - буфер обмена, тип: *string*

Допустимые значения параметра `enableClipboard`:

* `disabled` - Disable clipboard
* `dis-copy` - Disable copy from remote
* `dis-paste` - Disable paste to remote
* `enabled` - Enable clipboard

`serverLayout` - раскладка, тип: *string*. Значение по умолчанию (default): `-`

`ticketValidity` - срок действия тикета, тип: *string*

`forceNewWindow` - открывать в новом окне, тип: *string*. Допустимые значения: `true`, `false`.

Допустимые значения параметра `forceNewWindow`:

* `false` - Open every connection on the same window, but keeps UDS window
* `true` - Force every connection to be opened on a new window
* `overwrite` - Override UDS window and replace it with the connection

`security` - безопасность, тип: *string*

Допустимые значения параметра `security`:

* `any` - Any (Allow the server to choose the type of auth)
* `rdp` - RDP (Standard RDP encryption. Should be supported by all servers)
* `nla` - NLA (Network Layer authentication. Requires VALID username&password, or connection will fail)
* `nla-ext` - NLA extended (Network Layer authentication. Requires VALID username&password, or connection will fail)
* `tls` - TLS (Transport Security Layer encryption)

`rdpPort` - порт RDP, тип: *string*

`customGEPath` - контекстный путь Glyptodon Enterprise, тип: *string*

### HTML5RATransport

`guacamoleServer` - туннельный сервер, тип: *string*

`useEmptyCreds` - пропустить данные аккаунта, тип: *string*. Допустимые значения: `true`, `false`.

`fixedName` - имя пользователя, тип: *string*

`fixedPassword` - пароль, тип: *string*

`withoutDomain` - без домена, тип: *string*. Допустимые значения: `true`, `false`.

`fixedDomain` - домен, тип: *string*

`fixedApp` - алиас приложения, тип: *string*

`wallpaper` - показать обои, тип: *string*. Допустимые значения: `true`, `false`.

`desktopComp` - разрешить композицию рабочего стола, тип: *string*. Допустимые значения: `true`, `false`.

`smooth` - сглаживание шрифтов, тип: *string*. Допустимые значения: `true`, `false`.

`enableAudio` - включить аудио, тип: *string*. Допустимые значения: `true`, `false`.

`enableAudioInput` - включить микрофон, тип: *string*. Допустимые значения: `true`, `false`.

`enablePrinting` - включить печать, тип: *string*. Допустимые значения: `true`, `false`.

`enableFileSharing` - обмен файлами, тип: *string*. Допустимые значения: `true`, `false`.

Допустимые значения параметра `enableFileSharing`:

* `false` - Disable file sharing
* `down` - Allow download only
* `up` - Allow upload only
* `true` - Enable file sharing

`enableClipboard` - буфер обмена, тип: *string*

Допустимые значения параметра `enableClipboard`:

* `disabled` - Disable clipboard
* `dis-copy` - Disable copy from remote
* `dis-paste` - Disable paste to remote
* `enabled` - Enable clipboard

`serverLayout` - раскладка, тип: *string*. Значение по умолчанию (default): `-`

`ticketValidity` - срок действия тикета, тип: *string*

`forceNewWindow` - открывать в новом окне, тип: *string*. Допустимые значения: `true`, `false`.

Допустимые значения параметра `forceNewWindow`:

* `false` - Open every connection on the same window, but keeps UDS window
* `true` - Force every connection to be opened on a new window
* `overwrite` - Override UDS window and replace it with the connection

`security` - безопасность, тип: *string*

Допустимые значения параметра `security`:

* `any` - Any (Allow the server to choose the type of auth)
* `rdp` - RDP (Standard RDP encryption. Should be supported by all servers)
* `nla` - NLA (Network Layer authentication. Requires VALID username&password, or connection will fail)
* `nla-ext` - NLA extended (Network Layer authentication. Requires VALID username&password, or connection will fail)
* `tls` - TLS (Transport Security Layer encryption)

`rdpPort` - порт RDP, тип: *string*

### PCoIPTransport

`fixedName` - имя пользователя, тип: *string*

`fixedPassword` - пароль, тип: *string*

`fixedDomain` - домен, тип: *string*

### SPICETransport

`serverCertificate` - сертификат, тип: *string*

`fullScreen` - полноэкранный режим, тип: *string*. Допустимые значения: `true`, `false`.

`usbShare` - включить USB, тип: *string*. Допустимые значения: `true`, `false`.

`autoNewUsbShare` - автоподключение новых USB устройств, тип: *string*. Допустимые значения: `true`, `false`.

`smartCardRedirect` - перенаправление смарт-карты, тип: *string*. Допустимые значения: `true`, `false`.

### TSSPICETransport

`tunnelServer` - туннельный сервер, тип: *string*

`tunnelWait` - время ожидания туннеля, тип: *string*

`verifyCertificate` - проверка SSL-сертификата, тип: *string*. Допустимые значения: `true`, `false`.

**!** Остальные параметры аналогичны типу транспорта `SPICETransport`.

### URLTransport

`urlPattern` - шаблон URL, тип: *string*

`forceNewWindow` - открывать в новом окне, тип: *string*. Допустимые значения: `true`, `false`.

### X2GOTransport

`fixedName` - имя пользователя, тип: *string*

`screenSize` - размер экрана, тип: *string*. Для полноэкранного режима используйте значение `F`

`desktopType` - рабочий стол, тип: *string*

Допустимые значения параметра `desktopType`:

* `XFCE` - Xfce

* `MATE` - Mate

* `LXDE` - Lxde

* `GNOME` - Gnome (see docs)

* `KDE` - Kde (see docs)

* `gnome-session-cinnamon` - Cinnamon 1.4 (see docs)

* `gnome-session-cinnamon2d` - Cinnamon 2.2 (see docs)

* `UDSVAPP` - UDS vAPP

`customCmd` - строка vAPP, тип: *string*

`sound` - включить звук, тип: *string*. Допустимые значения: `true`, `false`.

`exports` - перенаправить домашнюю папку, тип: *string*. Допустимые значения: `true`, `false`.

`speed` - скорость, тип: *string*

Допустимые значения параметра `speed`:

* `0` - MODEM

* `1` - ISDN

* `2` - ADSL

* `3` - WAN

* `4` - LAN

`soundType` - звук, тип: *string*

Допустимые значения параметра `soundType`:

* `pulse` - Pulse

* `esd` - ESD

`keyboardLayout` - клавиатура, тип: *string*

`pack` - метод сжатия, тип: *string*. Значение по умолчанию: `16m-jpeg`

`quality` - качество, тип: *string*

### TX2GOTransport

`tunnelServer` - туннельный сервер, тип: *string*

`tunnelWait` - время ожидания туннеля, тип: *string*

`verifyCertificate` - проверка SSL-сертификата, тип: *string*. Допустимые значения: `true`, `false`.

**!** Остальные параметры аналогичны типу транспорта `X2GOTransport`.
