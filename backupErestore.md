# Restauração da base vault

## Requisitos:  
1. É necessário subir uma mesma versão do vault seguindo os procedimentos da intranet para evitar erros de  incompatibilidade.  
3. Backups de diretórios necessários:  

```
/var/lib/vault
/etc/vault/config.json
/etc/systemd/system/vault.service
```
## Procedimento:
1. Instale uma instância do vault igual ao do server.
 Siga [estes procedimentos](http://link-instalação).

2. Pare o serviço e renomeie o os diretórios em uso do server novo para deixar de stand-by.

```
systemctl stop vault 
sudo mv /var/lib/vault /var/lib/vault~
```
3. Extraia o arquivo de backup (caso esteja compactado) contendo os dados e distribua para seus respectivos diretórios:
```
unzip vault-backup.zip
sudo cp -r vault /var/lib/
```

4. Transforme o usuário vault do grupo vault em dono dos diretórios do serviço:  
```
sudo chown -R vault.vault /var/lib/vault
sudo chown -R vault.vault /var/log/vault
sudo chown -R vault.vault /etc/vault
```
5. Reinicie o serviços:
```
systemctl restart vault
```
6. verifique status do vault
```
systemctl status vault
```

## Comandos úteis:
### Checando logs no vault:  
```
journalctl -u vault -n 20
``` 
- u: nome do serviço
- n: número das últimas linhas 
### Compactando diretórios recursivamente:  

``` 
zip -r <nome_do_arquivo.zip> <nome_diretório>/ 
```  

Ex:  
``` zip -r vault-backup.zip vault/ ```


### Enviando arquivo compactado para outro server: 
``` 
scp <nome_arquivo.zip> <usuário_destino>@<ip_destino>://<diretório_destino>
```

Ex:  
``` scp vault-backup.zip root@192.168.10.190://home/root```
