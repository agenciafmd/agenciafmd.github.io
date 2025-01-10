---
title: Inicio
layout: home
nav_order: 1
---

<p align="center"><a href="https://fmd.ag?utm_source=github" target="_blank"><img src="https://raw.githubusercontent.com/agenciafmd/admix/v10/docs/fmd.png" alt="Logo da F&MD"></a></p>

## Contribuindo

### Instalando o Jekyll

```bash
sudo apt-get install ruby-full build-essential zlib1g-dev
```

Para **zsh**, adicione ao `.zshrc`:

```bash
mkdir ~/gems
echo '# Install Ruby Gems to ~/gems' >> ~/.zshrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.zshrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Para **bash** (terminal normal), adicione ao `.bashrc`:

```bash
mkdir ~/gems
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Instale o bundler:

```bash
gem install jekyll bundler
```

Agora vamos até o diretório do projeto e subimos o servidor:

```bash
cd /caminho/do/projeto
bundle install
bundle exec jekyll serve --livereload
```
