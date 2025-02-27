
# Instalação do vault 1.15.0

### [Opcional] Comandos úteis para preparar o server:
```
# sudo apt update && sudo apt upgrade -y
# sudo apt install unzip -y                       # Instalando unzip
# sudo dpkg-reconfigure tzdata                    # Ajustar data
# sudo dpkg-reconfigure keyboard-configuration    # Configurar teclado
```

### 1. Baixe e extraia o vault do repositório oficial:
```
wget https://releases.hashicorp.com/vault/1.15.0/vault_1.15.0_linux_amd64.zip
unzip vault_1.15.0_linux_amd64.zip
```
### 2. Copie o binário e instale o autocomplete:
```
sudo cp vault /usr/local/bin
vault --autocomplete-install
```
### 3. Crie os diretórios necessários:
```
mkdir /var/log/vault
mkdir -p /var/lib/vault/data
mkdir /etc/vault
```
### 4. Crie o usuário vault
```
useradd -r vault
```
### 5. Ajuste as permissões dos diretórios:
```
sudo chown -R vault.vault /var/lib/vault
sudo chown -R vault.vault /var/log/vault
sudo chown -R vault.vault /etc/vault
```
### 6. Crie os arquivos de configuração:
```
sudo touch /etc/vault/config.json
sudo touch /etc/systemd/system/vault.service
```
### 7. Edite o arquivo /etc/vault/config.json e insira:

```
storage "file" {
  path    = "var/lib/vault/data"
}

listener "tcp" {
  address     = "0.0.0.0:8200"
  tls_disable = 1
}

ui = true
api_addr = "http://0.0.0.0:8200"
cluster_name = "Vault-01"
```

### 8. Edite o arquivo /etc/systemd/system/vault.service e insira:

```
[Unit]
Description= Vault Server
Requires = network-online.target
After = network-online.target
ConditionFileNotEmpty = /etc/vault/config.json

[Service]
User = vault
Group = vault
Restart = on-failure
ExecStart = /usr/local/bin/vault server -config=/etc/vault/config.json
StandardOutput = /var/log/vault/output.log
StandardError = /var/log/vault/error.log
ExecReload = /bin/kill -HUP $MAINPID
KillSignal = DIGTERM
LimitMEMLOCK = infinity

[Install]
WantedBy = multi-user.target
```
### 9. recarregue o serviço e inicie o vault:
```
systemctl daemon-reload
systemctl start vault.service
```

## Inciando a base do vault:  
Este procedimento deve ser executado apenas quando for criar uma base nova. Neste processo são geradas as chaves para remover o selo do cofre e o token mestre. **Guarde essas chaves em local seguro. Sem elas você não terá acesso as informações gravadas no vault.**

```
export VAULT_ADDR="http://127.0.0.1:8200"
vault status
vault operator init
```
# Você deve conseguir acessar o vault via web: http://127.0.0.1:8200

Para trabalhar com docker, consul e alta disponibilidade seguir [este](https://testdriven.io/blog/managing-secrets-with-vault-and-consul/) tutorial.
