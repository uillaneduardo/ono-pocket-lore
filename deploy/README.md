# Deploy no homelab

A publicação de produção usa:

- Material for MkDocs para gerar o site estático;
- GitHub Actions com runner self-hosted;
- Nginx em Docker para servir `/srv/ono-pocket-lore/site`;
- Cloudflare Tunnel apontando `ono-pocket-lore.wapphub.com.br` para `http://ono-pocket-lore:80`.

## 1. Preparar diretório de publicação

```bash
sudo mkdir -p /srv/ono-pocket-lore/site
sudo chown -R $USER:$USER /srv/ono-pocket-lore
```

O usuário que executar o GitHub Runner precisa ter permissão de escrita nesse diretório.

## 2. Confirmar a rede do Cloudflare

```bash
docker network ls | grep cloudflare_ingress
```

Se a rede ainda não existir:

```bash
docker network create cloudflare_ingress
```

O container `cloudflared` também precisa estar conectado a essa rede.

## 3. Subir o Nginx

A partir deste diretório:

```bash
docker compose up -d
```

Validar:

```bash
docker ps --filter name=ono-pocket-lore
docker network inspect cloudflare_ingress
```

## 4. Configurar o Cloudflare Tunnel

No tunnel já usado pelo homelab, criar um hostname público:

```text
Hostname: ono-pocket-lore.wapphub.com.br
Service:  http://ono-pocket-lore:80
```

## 5. Instalar o GitHub self-hosted runner

No repositório GitHub:

```text
Settings → Actions → Runners → New self-hosted runner
```

Selecionar Linux x64 e executar no homelab os comandos fornecidos pelo GitHub.

Ao configurar o runner, adicionar a label customizada:

```text
ono-lore
```

O workflow espera as labels:

```text
self-hosted, linux, x64, ono-lore
```

Depois da configuração:

```bash
sudo ./svc.sh install
sudo ./svc.sh start
sudo ./svc.sh status
```

## 6. Testar o deploy

Faça um commit na branch `main` ou execute manualmente:

```text
Actions → Deploy Ono Pocket Lore → Run workflow
```

O workflow executa:

```text
checkout
→ instala MkDocs
→ mkdocs build --strict
→ valida site/index.html
→ publica em /srv/ono-pocket-lore/site
```

O Nginx lê essa pasta diretamente, portanto não é necessário reiniciar o container a cada deploy.

## Segurança

O workflow não executa em `pull_request`; apenas em `push` para `main` ou `workflow_dispatch`. Para maior isolamento do homelab, considere manter o repositório privado ou executar o runner com um usuário Linux dedicado e permissões mínimas.
