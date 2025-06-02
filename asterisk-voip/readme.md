# Projeto Asterisk e Linphone com Docker Compose

Este projeto configura um servidor Asterisk e dois clientes Linphone (Pedro e Maria) usando Docker Compose. O objetivo é permitir que Pedro ligue para Maria discando o ramal `0800`.

## Pré-requisitos

* Docker instalado
* Docker Compose instalado

## Estrutura de Arquivos Esperada

```
.
|-- Dockerfile.asterisk
|-- Dockerfile.linphone
|-- asterisk-configs/
|   |-- sip.conf
|   |-- extensions.conf
|   |-- modules.conf      # (Opcional, mas recomendado)
|   `-- asterisk.conf     # (Opcional, mínimo)
`-- docker-compose.yml
```

## Configuração

1.  **Clone este repositório** (ou crie os arquivos conforme descrito acima).
2.  **Certifique-se de que a pasta `asterisk-configs` existe** e contém os arquivos `sip.conf` e `extensions.conf` (e opcionalmente `modules.conf`, `asterisk.conf`) com as configurações fornecidas.

## Como Executar

1.  **Navegue até o diretório raiz do projeto** no seu terminal.
2.  **Construa e inicie os contêineres** em modo detached (segundo plano):
    ```bash
    docker-compose up -d --build
    ```
    O `--build` garante que as imagens sejam construídas caso ainda não existam ou tenham sido alteradas.

## Testando a Ligação: Pedro ligar para Maria (0800)

O `Dockerfile.linphone` para Pedro (`linphone_pedro`) está configurado com `TARGET_DIAL=true` e `TARGET_EXTENSION=0800`. Isso significa que, após alguns segundos da inicialização, ele tentará automaticamente ligar para `0800`.

O `Dockerfile.linphone` para Maria (`linphone_maria`) está configurado com `AUTO_ANSWER=true`, então ela deve atender a chamada automaticamente.

**Para verificar:**

1.  **Verifique os logs dos contêineres Linphone:**
    ```bash
    docker logs linphone_pedro
    docker logs linphone_maria
    ```
    Você deverá ver Pedro tentando registrar e depois ligar, e Maria registrando e depois atendendo.

2.  **Verifique os logs do Asterisk para ver a chamada:**
    ```bash
    docker logs asterisk
    ```
    Procure por linhas indicando o registro de Pedro e Maria, e depois a chamada de Pedro para `0800` sendo direcionada para Maria.

## Configurações Extras e Interação Manual

A configuração atual do `Dockerfile.linphone` é básica e tenta automatizar o registro e a chamada/atendimento. Para uma interação mais manual ou configurações avançadas do Linphone:

1.  **Acesse o console do Linphone (linphonecsh):**
    Você pode interagir com cada cliente Linphone usando `linphonecsh` dentro do respectivo contêiner.

    Para Pedro:
    ```bash
    docker exec -it linphone_pedro linphonecsh
    ```
    Para Maria:
    ```bash
    docker exec -it linphone_maria linphonecsh
    ```

2.  **Dentro do `linphonecsh`:**
    * **Registro (se não automático):**
        ```
        linphonecsh init
        register --host asterisk --username <seu_usuario> --password <sua_senha>
        ```
        (Ex: para Pedro: `register --host asterisk --username pedro --password 1234`)
    * **Fazer uma chamada (ex: Pedro para Maria):**
        No console do `linphone_pedro`:
        ```
        call 0800@asterisk
        ```
        ou apenas
        ```
        call 0800
        ```
    * **Atender uma chamada (se não automático):**
        No console do `linphone_maria` quando estiver tocando:
        ```
        answer
        ```
    * **Encerrar uma chamada:**
        ```
        terminate
        ```
    * **Verificar status:**
        ```
        status register
        status call
        ```
    * **Sair do `linphonecsh`:**
        ```
        quit
        ```

## Parando os Serviços

Para parar e remover os contêineres, redes e volumes (se especificado):
```bash
docker-compose down
```
