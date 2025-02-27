# Ajustando tempo de sessão para 2h

### Ajuste a variável de ambiente:
```export VAULT_ADDR='http://127.0.0.1:8200'```

### Checando configuração global de tempo:
```vault read sys/auth/token/tune```


### Alterando para 2h:
```
vault write sys/auth/token/tune \
    default_lease_ttl=2h \
    max_lease_ttl=2h
```

### (Opcional) Revogado os usuários logados (Revogando os Tokens) ldap:
```vault lease revoke -prefix auth/ldap```
