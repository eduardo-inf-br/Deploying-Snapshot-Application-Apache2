# Restaurar snapshot e publicar aplicação no Apache2

Este guia descreve como restaurar um snapshot de uma máquina virtual no VirtualBox e instalar/publicar a aplicação do repositório indicado em um servidor Ubuntu.

> **Atenção:** restaurar um snapshot repõe a máquina no estado em que estava quando o snapshot foi criado. Alterações posteriores — incluindo ficheiros e configurações — podem ser perdidas. Faça cópias de segurança antes de avançar.

## 1. Restaurar o snapshot criado no VirtualBox

### Pela interface gráfica

1. Desligue a máquina virtual, se estiver em execução. Se necessário, escolha **Máquina > Fechar > Desligar a máquina**; evite guardar o estado se pretende restaurar exatamente o snapshot.
2. Na janela principal do VirtualBox, selecione a máquina virtual pretendida.
3. Abra **Snapshots** (ou **Instantâneos**, conforme o idioma da interface).
4. Selecione o snapshot que pretende restaurar.
5. Clique em **Restaurar** e confirme.
6. Opcionalmente, escolha se pretende guardar o estado atual da máquina como um snapshot antes da restauração.
7. Inicie a máquina virtual e inicie sessão no Ubuntu.

### Pela linha de comandos (opcional)

No computador anfitrião, liste as máquinas virtuais e os snapshots:

```bash
VBoxManage list vms
VBoxManage snapshot "NOME_DA_VM" list
```

Restaure o snapshot pelo respetivo nome:

```bash
VBoxManage snapshot "NOME_DA_VM" restore "NOME_DO_SNAPSHOT"
```

Substitua `NOME_DA_VM` e `NOME_DO_SNAPSHOT` pelos valores apresentados. Execute estes comandos no anfitrião, não dentro do Ubuntu convidado.

## 2. Atualizar o servidor

Abra um terminal no Ubuntu e atualize a lista de pacotes e os pacotes instalados:

```bash
sudo apt update
sudo apt upgrade -y
```

Se forem atualizados componentes que exigem reinicialização, reinicie o servidor:

```bash
sudo reboot
```

Depois de reiniciar, volte a iniciar sessão e continue.

## 3. Instalar o Apache2

Instale o servidor Web Apache:

```bash
sudo apt install apache2 -y
```

Ative o serviço para iniciar automaticamente e inicie-o agora:

```bash
sudo systemctl enable --now apache2
```

Confirme que o serviço está ativo:

```bash
sudo systemctl status apache2 --no-pager
```

A página predefinida pode ser testada no próprio servidor com:

```bash
curl -I http://localhost
```

Para aceder a partir de outro computador, use `http://IP_DO_SERVIDOR/` e confirme que a rede/Firewall permite ligações HTTP (porta 80).

## 4. Instalar o unzip

Instale o utilitário necessário para extrair o ficheiro ZIP:

```bash
sudo apt install unzip -y
```

Confirme a instalação, se desejar:

```bash
unzip -v
```

## 5. Baixar a aplicação para `/tmp`

Use `wget` para transferir o arquivo ZIP diretamente para `/tmp`:

```bash
cd /tmp
wget -O linux-site-dio-main.zip \
  https://github.com/denilsonbonatti/linux-site-dio/archive/refs/heads/main.zip
```

Confirme que o ficheiro foi transferido:

```bash
ls -lh /tmp/linux-site-dio-main.zip
```

## 6. Copiar os ficheiros da aplicação para o diretório padrão do Apache

Extraia o arquivo para um diretório temporário:

```bash
sudo unzip -o /tmp/linux-site-dio-main.zip -d /tmp
```

O ZIP do GitHub cria normalmente a pasta `/tmp/linux-site-dio-main`. Copie **o conteúdo** dessa pasta para o diretório Web padrão do Apache (`/var/www/html`):

```bash
sudo cp -a /tmp/linux-site-dio-main/. /var/www/html/
```

Defina permissões de leitura adequadas para o servidor Web:

```bash
sudo chown -R www-data:www-data /var/www/html
sudo find /var/www/html -type d -exec chmod 755 {} \;
sudo find /var/www/html -type f -exec chmod 644 {} \;
```

Confirme que os ficheiros foram copiados e que existe uma página inicial:

```bash
ls -la /var/www/html
```

Se o repositório tiver um `index.html` na raiz, abra `http://localhost/` no servidor ou `http://IP_DO_SERVIDOR/` noutro computador da rede. Se a aplicação não aparecer, confirme o nome/localização do ficheiro de entrada e consulte os registos do Apache:

```bash
sudo tail -n 50 /var/log/apache2/error.log
```

### Comandos reunidos (instalação e publicação)

Depois de restaurar o snapshot e iniciar sessão no Ubuntu, estes comandos resumem a atualização, instalação, transferência e cópia:

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install apache2 unzip -y
sudo systemctl enable --now apache2
cd /tmp
wget -O linux-site-dio-main.zip https://github.com/denilsonbonatti/linux-site-dio/archive/refs/heads/main.zip
sudo unzip -o /tmp/linux-site-dio-main.zip -d /tmp
sudo cp -a /tmp/linux-site-dio-main/. /var/www/html/
sudo chown -R www-data:www-data /var/www/html
sudo find /var/www/html -type d -exec chmod 755 {} \;
sudo find /var/www/html -type f -exec chmod 644 {} \;
```

---

**`Informática para Internet`**

O suporte fornecido por corporações permite-nos o desenvolvimento e implementação de programas, projetos e recursos para Eduardo.Inf.Br. Através dessas parcerias é possível que empreendedores e proprietários de pequenas empresas, recebam ampla variedade de conteúdos e ferramentas por meio de sistemas e suporte. "[Eduardo.Inf.Br](https://informatizar.netlify.app/)".

---

<img 
    align="left" 
    alt="HTML"
    title="HTML" 
    width="30px" 
    style="padding-right: 10px;" 
    src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/html5/html5-original.svg" 
/>
<img 
    align="left" 
    alt="CSS" 
    title="CSS"
    width="30px" 
    style="padding-right: 10px;" 
    src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/css3/css3-original.svg" 
/>
<img 
    align="left" 
    alt="JavaScript" 
    title="JavaScript"
    width="30px" 
    style="padding-right: 10px;" 
    src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/javascript/javascript-original.svg" 
/>
<img 
    align="left" 
    alt="Bootstrap"
    title="Bootstrap" 
    width="30px" 
    style="padding-right: 10px;" 
    src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/bootstrap/bootstrap-original.svg" 
/>
<img 
    align="left" 
    alt="Git" 
    title="Git"
    width="30px" 
    style="padding-right: 10px;" 
    src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/git/git-original.svg" 
/>
