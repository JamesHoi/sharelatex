# sharelatex
Overleaf Extended Community Edition docker image with all packages available to tlmgr

## Installation
Clone Overleaf Toolkit and pull prebuilt image
```bash
git clone https://github.com/overleaf/toolkit.git ./overleaf-toolkit
docker pull jameshoi/sharelatex:5.5.4-ext-v3.1
```
add a file named `docker-compose.override.yml` in `overleaf-toolkit/config` directory:
```yaml
---
services:
    sharelatex:
        image: jameshoi/sharelatex:5.5.4-ext-v3.1
```
start container
```bash
cd overleaf-toolkit
bin/up -d
```

## Build from Dockerfile
```dockerfile
# Dockerfile
FROM overleafcep/sharelatex:5.5.4-ext-v3.1

SHELL ["/bin/bash", "-cx"]

# update tlmgr itself
# add 
# "sed -i 's|https://mirror.ox.ac.uk/sites/ctan.org/systems/texlive/tlnet/|https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet/|g' update-tlmgr-latest.sh" 
# to change mirror before "sh update-tlmgr-latest.sh"
RUN wget "https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet/update-tlmgr-latest.sh" \
    && sh update-tlmgr-latest.sh \
    && tlmgr --version

# Set tlmgr to use Tsinghua University mirror
RUN tlmgr option repository https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet/

# Update tlmgr itself
RUN tlmgr update --self

# enable tlmgr to install ctex
RUN tlmgr update texlive-scripts

# update packages
RUN tlmgr update --all

# install all the packages
RUN tlmgr install scheme-full

# recreate symlinks
RUN tlmgr path add

# change source if needed
# RUN sed -i 's/archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list.d/ubuntu.sources

# update system packages
RUN apt-get update

# install inkscape for svg support
RUN apt-get install inkscape lilypond -y

# enable shell-escape by default:
RUN TEXLIVE_FOLDER=$(find /usr/local/texlive/ -type d -name '20*') \
    && echo % enable shell-escape by default >> /$TEXLIVE_FOLDER/texmf.cnf \
    && echo shell_escape = t >> /$TEXLIVE_FOLDER/texmf.cnf
```

## Thanks
[yu-i-i/overleaf-cep](https://github.com/yu-i-i/overleaf-cep)  
[tuetenk0pp/sharelatex-full](https://github.com/tuetenk0pp/sharelatex-full)
