# TOR-proxy

Proxy Tor via Docker Compose, fácil de usar com navegador, curl ou Bitcoin Core.

---

## Pré-requisitos

- Docker.
- Docker Compose.
- Git (para clonar o repositório).

---

## 1. Clonar o repositório

```bash
git clone https://github.com/Defiverso/tor-proxy.git
cd tor-proxy
````

---

## 2. Subir com Docker Compose

```bash
docker compose up -d
```

Este comando irá baixar/compilar as imagens necessárias e rodar os serviços em background.

O proxy HTTP/Privoxy ficará exposto em `localhost:8118` e o Tor SOCKS5 em `localhost:9050`.

---

## 3. Testar o proxy com `curl`

### HTTP (via Privoxy)

```bash
curl -Lx http://localhost:8118 http://jsonip.com/
```

* `-L` segue redirecionamentos (caso existam).
* `-x http://localhost:8118` configura o `curl` para usar o proxy HTTP local.

**Saída esperada:** JSON com o IP que o serviço enxerga, geralmente diferente do seu IP real.

```json
{"ip":"x.x.x.x"}
```

### SOCKS5 (via Tor)

```bash
curl -Lx socks5h://localhost:9050 http://jsonip.com/
```

* `socks5h://localhost:9050` indica que é SOCKS5 com resolução de DNS pelo proxy.

---

## 4. Configurando o proxy em aplicativos

### Navegadores (Firefox/Chrome/Edge)

* Abra as configurações de rede/proxy do navegador.
* Configure um **proxy HTTP** com:

  * Endereço: `localhost`
  * Porta: `8118` (Privoxy → Tor)
* Salve e reinicie o navegador.

### Bitcoin Core

* Abra o arquivo `bitcoin.conf` do seu datadir.
* Adicione a linha:

```
proxy=127.0.0.1:9050
```

* Salve e reinicie o Bitcoin Core.
---

## 5. Logs e gerenciamento

Para ver logs:

```bash
docker compose logs -f
# ou
docker-compose logs -f
```

Parar:

```bash
docker compose down
# ou
docker-compose down
```

Recriar (forçar rebuild):

```bash
docker compose up -d --build
# ou
docker-compose up -d --build
```

---

## 6. Troubleshooting rápido

* **`curl` não conecta / timeout:** verifique se o container está rodando (`docker ps`).
* **Erro de binding da porta 8118/9050:** outra aplicação está usando a porta; pare-a ou altere a porta no `docker-compose.yml`.
* **O IP retornado é o meu IP real:** pode ser que o serviço Tor dentro do container não tenha iniciado. Verifique os logs do container.

---

## 7. Observações de segurança / uso

* O proxy exposto em `localhost` só aceita conexões locais por padrão — evite expor portas para toda a rede sem entender riscos.
* Uso do Tor/Privoxy pode implicar em latência maior — é esperado.
