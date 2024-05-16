# Tuto : georchestra avec docker

Comme je suis "franchouillard", je m'exprime en français. Ceci est un tuto modeste qui explique le déploiement de **geOrchestra** avec Docker sur serveur Linux.

## Etape 1:
```bash
git clone --recurse-submodules https://github.com/georchestra/docker.git
```
## Etape 2:
configurer les fichiers traefik configuration
*resources/traefik_custom.yaml*
```yaml
global: 
  sendAnonymousUsage: false
  checkNewVersion: false

entryPoints:
  web:
    address: ":80"
    http:
      redirections:
        entryPoint:
          to: websecure
          scheme: https

  websecure:
    address: ":443"

providers:
  docker:
    watch: true
    exposedByDefault: false
    endpoint: unix:///var/run/docker.sock

api:
  dashboard: true

log:
  level: INFO

certificatesResolvers:
  letsEncrypt:
    acme:
      # comment this line when going to production
      caServer: https://acme-staging-v02.api.letsencrypt.org/directory
      email: jean.pommier@pi-geosolutions.fr
      storage: /acme/acme.json
      httpChallenge:
        entryPoint: web

ping: {}
```
## Etape 3:
update traefik config dans le docker-compose.override.yml
Dans docker-compose.override.yml,
- commentons le bloc 'traefik-me-certificate-downloader' 
- 'georchestra-127-0-0-1.traefiq.me' service:
- commenter 'depends_on' section
- changer la section volume:
    - /var/run/docker.sock:/var/run/docker.sock:ro
    - ./resources/traefik_custom.yml:/etc/traefik/traefik.yml:ro
    - acme:/acme
changer FQDN et labels