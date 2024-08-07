# Documentação da API REST do FreeRADIUS

## Introdução

O FreeRADIUS é um servidor RADIUS de código aberto que pode ser configurado para se comunicar com APIs REST. Esta documentação fornece uma visão geral de como configurar e usar a API REST do FreeRADIUS.

## Configuração Básica

### Pré-requisitos

1. **Instalação do FreeRADIUS**: Certifique-se de que o FreeRADIUS está instalado e funcionando corretamente.
2. **Módulo REST**: O módulo `rlm_rest` deve estar habilitado. Este módulo permite que o FreeRADIUS se comunique com serviços REST.

### Configuração do Módulo REST

1. **Arquivo de Configuração**: Edite o arquivo de configuração do FreeRADIUS para incluir o módulo REST. Normalmente, isso é feito no arquivo `mods-enabled/rest`.

```plaintext
rest {
    uri = "http://localhost:4000/api"
    method = "post"
    body = "json"
    tls = no
    timeout = 30
    accounting {
        uri = "http://localhost:4000/accounting"
    }
}
```

2. **Configuração do Servidor Virtual**: No arquivo `sites-enabled/default`, adicione uma referência ao módulo REST.

```plaintext
authorize {
    ...
    rest
    ...
}

accounting {
    ...
    rest
    ...
}
```

## Registro de Usuário

Para registrar um usuário que pode consumir a API do FreeRADIUS, siga os passos abaixo:

1. **Adicionar Usuário no FreeRADIUS**: Edite o arquivo `users` localizado em `/etc/freeradius/3.0/mods-config/files/`.

```plaintext
apiuser Cleartext-Password := "apipassword"
    Service-Type = Administrative-User,
    Reply-Message = "Hello, %{User-Name}"
```

2. **Reiniciar o FreeRADIUS**: Após adicionar o usuário, reinicie o serviço FreeRADIUS para aplicar as mudanças.

```bash
sudo systemctl restart freeradius
```

## Endpoints da API

### Autenticação

- **Endpoint**: `/api/authenticate`
- **Método**: POST
- **Descrição**: Autentica um usuário com base nas credenciais fornecidas.

#### Exemplo de Requisição com `curl`

```bash
curl -X POST http://localhost:4000/api/authenticate \
    -H "Content-Type: application/json" \
    -d '{"username": "apiuser", "password": "apipassword"}'
```

#### Exemplo de Resposta

```json
{
    "status": "success",
    "message": "Autenticação bem-sucedida"
}
```

### Contabilização

- **Endpoint**: `/api/accounting`
- **Método**: POST
- **Descrição**: Registra dados de contabilização.

#### Exemplo de Requisição com `curl`

```bash
curl -X POST http://localhost:4000/api/accounting \
    -H "Content-Type: application/json" \
    -d '{"username": "apiuser", "session_id": "123456", "data_usage": 1024}'
```

#### Exemplo de Resposta

```json
{
    "status": "success",
    "message": "Dados de contabilização registrados"
}
```

## Operações CRUD para Usuário PPPoE

### Criar Usuário

- **Endpoint**: `/api/users`
- **Método**: POST
- **Descrição**: Cria um novo usuário PPPoE.

#### Exemplo de Requisição com `curl`

```bash
curl -X POST http://localhost:4000/api/users \
    -H "Content-Type: application/json" \
    -d '{"username": "novousuario", "password": "novasenha"}'
```

#### Exemplo de Resposta

```json
{
    "status": "success",
    "message": "Usuário criado com sucesso"
}
```

### Ler Usuário

- **Endpoint**: `/api/users/{username}`
- **Método**: GET
- **Descrição**: Obtém informações de um usuário PPPoE.

#### Exemplo de Requisição com `curl`

```bash
curl -X GET http://localhost:4000/api/users/novousuario
```

#### Exemplo de Resposta

```json
{
    "username": "novousuario",
    "password": "novasenha"
}
```

### Atualizar Usuário

- **Endpoint**: `/api/users/{username}`
- **Método**: PUT
- **Descrição**: Atualiza as informações de um usuário PPPoE.

#### Exemplo de Requisição com `curl`

```bash
curl -X PUT http://localhost:4000/api/users/novousuario \
    -H "Content-Type: application/json" \
    -d '{"password": "novasenhaatualizada"}'
```

#### Exemplo de Resposta

```json
{
    "status": "success",
    "message": "Usuário atualizado com sucesso"
}
```

### Deletar Usuário

- **Endpoint**: `/api/users/{username}`
- **Método**: DELETE
- **Descrição**: Deleta um usuário PPPoE.

#### Exemplo de Requisição com `curl`

```bash
curl -X DELETE http://localhost:4000/api/users/novousuario
```

#### Exemplo de Resposta

```json
{
    "status": "success",
    "message": "Usuário deletado com sucesso"
}
```

## Conclusão

Esta documentação fornece uma visão geral básica de como configurar e usar a API REST do FreeRADIUS, incluindo operações CRUD para usuários PPPoE. Para mais detalhes, consulte a [documentação oficial do FreeRADIUS](https://www.freeradius.org/documentation/freeradius-server/4.0.0/howto/modules/rest/index.html).

---
