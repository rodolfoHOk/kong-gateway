# Kong Gateway

## Links

- https://konghq.com/
- https://docs.konghq.com/gateway/latest/install/docker/#main
- https://hub.docker.com/_/kong
- https://jwt.io/
- https://www.epochconverter.com/

## Endereços no Kong

- http://localhost:8000/ - gateway de entrada das apis
- http://localhost:8001/ - ADMIN API
- http://localhost:8002/ - ADMIN GUI

## Network config

- editar como admin: /etc/hosts e adicionar: 127.0.0.1 host.docker.internal
- exemplo: sudo nano /etc/hosts / adicionar host.docker.internal na linha que começa com 127.0.0.1

## Guia Kong

### Registar API

- http://localhost:8002/
- -> Gateway Services -> New Gateway Service: fields Name (example: node-api), Upstream URL (example: http://host.docker.internal:3000) -> Save

### Registrar rota

- http://localhost:8002/
- -> Routes -> New Route: fields Name (example node-api-routing), Service (select), Protocols (select: example HTTP,HTTPS), Paths (example: /test) -> Save

### Adicionar autenticação

#### Global Key Authentication

- http://localhost:8002/
- -> Plugins -> New Plugin -> Key Authentication: fields Key in Query (unselect), Key Names (example: apikey) -> Save

#### Route Key Authentication

- http://localhost:8002/
- -> Routes -> router-name -> Plugins -> New Plugin -> Key Authentication: fields Key in Query (unselect), Key Names (example: apikey) -> Save

##### Registrar Consumer

- http://localhost:8002/
- -> Consumers -> New Consumer: fields Username (example: node-api) -> Save
- -> Consumers -> consumer-name -> Credentials -> New Key Auth Credential: -> Save
- Copiar apiKey

#### JWT Authentication

- http://localhost:8002/
- -> Routes -> router-name -> Plugins -> New Plugin -> JWT: -> Save
- -> Routes -> router-name -> Plugins -> JWT -> Editar -> View Advanced Parameters: fields Claims To Verify (exp) -> Save

##### Registrar Consumer

- http://localhost:8002/
- -> Consumers -> New Consumer: fields Username (example: node-api) -> Save
- -> Consumers -> consumer-name -> Credentials -> New JWT Credential: -> Save
- -> ... -> Editar: ver a key e a secret geradas

##### Testar

- https://jwt.io/
- PAYLOAD:DATA: add "iss": "valor-key-gerada", add "exp": 1718559396,
- VERIFY SIGNATURE: "valor-secret-gerada"
- Encoded: copiar o token válido gerado

### Adicionar rate limit

- http://localhost:8002/
- -> Routes -> router-name -> Plugins -> New Plugin -> Rate Limiting: fields Minute (exemplo: 10) -> Save

### Adicionar load balance

- http://localhost:8002/
- -> Upstreams -> New Upstream: fields Name (example: upstream-example), Algorithm (select: example Round Robin)
- -> Upstreams -> upstream-name -> Targets -> New Target: fields Target Address (example: host.docker.internal:3000) -> Save
- -> New Target: fields Target Address (example: host.docker.internal:3001) -> Save
- -> Gateway Services -> New Gateway Service: fields Name (example: upstream-example), -> Protocol, Host, Port and Path: fields Host (upstream-example) -> Save
- -> Routes -> New Route: fields Name (example: load-balancing), Service (select: upstream-example), Paths (example: /load-balancing)

## Rodar

### Requisitos

- Docker
- Node.js

### Comandos

- docker compose up
- cd node-api
- npm run api
- npm run api2
