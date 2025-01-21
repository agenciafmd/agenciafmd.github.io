---
title: Docker
parent: Sail
nav_order: 1
---

# Docker
{: .no_toc }

## Conteúdo
{: .no_toc .text-delta }

1. TOC
{:toc}

---

Docker é uma plataforma de software que permite criar, testar e implantar aplicações rapidamente. Ele usa contêineres
para empacotar uma aplicação com todas as suas dependências e bibliotecas, garantindo que ela funcione de maneira
consistente em diferentes ambientes.

Principais características do Docker:

1. **Portabilidade:** Contêineres podem ser executados em qualquer sistema que suporte Docker, garantindo consistência
   entre ambientes de desenvolvimento, teste e produção.
2. **Isolamento:** Cada contêiner é isolado, o que significa que as aplicações e suas dependências não interferem umas
   nas outras.
3. **Eficiência:** Contêineres compartilham o kernel do sistema operacional, tornando-os mais leves e rápidos de iniciar
   em comparação com máquinas virtuais.

## Instalação

Para instalar o Docker, siga as instruções oficiais de acordo com o seu sistema operacional:

### Linux

Configure o repositório:

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

{: .important-title }
> Importante
>
> Se você usar uma distribuição derivada do Ubuntu, como o Linux Mint, talvez precise usar `UBUNTU_CODENAME` em vez de
`VERSION_CODENAME`.

Instale o Docker:

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Veja se o docker está rodando corretamente:

```bash
systemctl is-active docker
```

Adicione seu usuário ao grupo `docker` para executar comandos Docker sem precisar de `sudo`:

```bash
sudo usermod -aG docker ${USER}
```

Reinicie a máquina ou faça *logout* e *login* do sistema para aplicar as alterações.

Veja se deu certo:

```bash
docker ps
```

### macOS

Baixe e instale o [Docker Desktop for Mac](https://docs.docker.com/desktop/mac/install/){:target="_blank"}.

### Windows

Baixe e instale o [Docker Desktop for Windows](https://docs.docker.com/desktop/windows/install/){:target="_blank"}.

## Integração com a IDE

Para facilitar o desenvolvimento, é possível configurar o Docker para iniciar automaticamente com a IDE.

Para isso, vamos em `File > Settings > Tools > Startup Tasks` e escolhemos o `Sail`.

![sail ide](/assets/images/sail-ide.gif)

Caso não tenha o `Sail` disponível, você pode criar um novo `Shell Script` conforme o exemplo abaixo:

![sail ide](/assets/images/sail-shell-script.png)

## Comandos úteis do Docker

### Desligar todos os contêineres em execução:

```bash
docker compose down -v
```

## Comandos úteis do Sail

### Documentação oficial

[https://laravel.com/docs/11.x/sail](https://laravel.com/docs/11.x/sail){:target="_blank"}

### Cria o alias para o Sail

```bash
alias sail='sh $([ -f sail ] && echo sail || echo vendor/bin/sail)'
```

{: .important-title }
> Importante
> 
> Para garantir que o comando esteja sempre disponível, você pode adicionar ao arquivo de configuração como `~/.zshrc`
> ou `~/.bashrc`, e depois reiniciar o shell.

### Builda/Rebuilda as imagens do Docker

```bash
sail build --no-cache
```

### Iniciando os contêineres

```bash
sail up -d
```

### Parando os contêineres

```bash
sail stop
```
