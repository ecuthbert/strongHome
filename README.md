# strongHome

## Generate certs
```bash
sudo rm openssl-ca/*

docker run --rm -it -v $PWD/openssl-ca:/certs \
-e SSL_SIZE=4096 \
-e CA_SUBJECT="AAA ROOT CA strongHome" \
-e CA_EXPIRE=3650 `# 10 years to expire the CA` \
paulczar/omgwtfssl

(cd openssl-ca && sudo rm secret.yaml key.* cert.pem)

source .env

docker run --rm -it -v $PWD/openssl-ca:/certs \
-e SSL_SIZE=4096 \
-e CA_SUBJECT="AAA ROOT CA strongHome" \
-e CA_EXPIRE=3650 `# 10 years to expire the CA` \
-e SSL_KEY=ldap.${LOCAL_DOMAIN}-key.pem \
-e SSL_CSR=ldap.${LOCAL_DOMAIN}.csr \
-e SSL_CERT=ldap.${LOCAL_DOMAIN}.pem \
-e SSL_EXPIRE=730 `# 2 years` \
-e SSL_SUBJECT=openldap \
paulczar/omgwtfssl

docker run --rm -it -v $PWD/openssl-ca:/certs \
-e SSL_SIZE=4096 \
-e CA_SUBJECT="AAA ROOT CA strongHome" \
-e CA_EXPIRE=3650 `# 10 years to expire the CA` \
-e SSL_KEY=radius.${LOCAL_DOMAIN}-key.pem \
-e SSL_CSR=radius.${LOCAL_DOMAIN}.csr \
-e SSL_CERT=radius.${LOCAL_DOMAIN}.pem \
-e SSL_EXPIRE=730 `# 2 years` \
-e SSL_SUBJECT=freeradius \
paulczar/omgwtfssl

#radius shared secret
docker run -v $PWD/openssl-ca:/certs --rm alpine sh -c "< /dev/urandom tr -dc \#@_\$%/\(\)=?A-Z-a-z-0-9 | head -c54 > /certs/radius-shared-secret"
```

## Deploy
```bash
docker-compose up
```

## Configuration
### Generate config
TODO
### Validate config
```bash
pykwalify -s config/strongHome-schema.yaml -d config/strongHome-config-example.yaml
```
## TODO list
- [ ] PKI
- [ ] LDAP
  - [ ] Define admin permissions with YAML
  - [ ] Auto-generate config
  - [ ] Unit tests
- [ ] 802.11X
- [ ] Configuration in JSON
- [ ] YAML config
  - [ ] Schema
  - [ ] Reader
  - [ ] Generator
- [ ] Email server
- [ ] VoIP?
- [ ] Sync startup containers
- [ ] Web for manager LDAP/restore passwords
- [ ] Design optional services (torrents, plex, etc.)
- [ ] Dynamic auto-configuration stuff should be in a aislated service
- [ ] Beautiful way to generate strongHome YAML config
- [ ] Scalable services
