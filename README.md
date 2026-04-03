# Proyecto Blockchain BaaS

Repositorio central del proyecto (middleware + red Hyperledger Fabric de prueba).

## Requisitos previos

- Docker Desktop instalado y en ejecución.
- Binarios de Fabric e imágenes Docker instalados según la [documentación oficial](https://hyperledger-fabric.readthedocs.io/en/latest/install.html). En este monorepo existe install-fabric.sh en la raíz para ayudar con la instalación.
- Añadir al PATH la carpeta de binarios de Fabric, por ejemplo:

  `export PATH=$PWD/red-hyperledger/bin:$PATH`

  (ajusta la ruta si clonaste el repo en otra ubicación).

## Red de prueba Hyperledger Fabric (entregable 1.1)

Los pasos siguientes se ejecutan desde la carpeta del test network:

```bash
cd red-hyperledger/test-network
```

### Levantar la red, crear el canal y unir a los nodos participantes

Un solo comando POR SIEMPRE PARA SIEMPRE:

```bash
./network.sh up createChannel -ca -s couchdb -c clientes
```

Qué hace este comando (resumen):

- **`up`**: arranca los contenedores de la red (orderer, peers, bases CouchDB por peer, servidores Fabric CA).
- **`createChannel`**: crea el **canal de trabajo** con el nombre indicado y **une al canal** a los peers de las organizaciones que trae el sample (por defecto Org1 y Org2). Eso es lo que el entregable llama “unir nodos participantes”: cada **peer** que debe leer y escribir en ese canal se registra como miembro del canal.
- **`-ca`**: usa **Fabric CA** para generar el material de identidades (alineado con gestión de identidades del proyecto).
- **`-s couchdb`**: usa **CouchDB** como base de estado del peer (consultas en JSON más flexibles que LevelDB).
- **`-c clientes`**: nombre del canal (el equipo debe usar **el mismo nombre** en todos). Si omites `-c`, el nombre por defecto del sample es `mychannel`.

### Apagar la red y limpiar el entorno local

```bash
./network.sh down
```

**Importante:** en el test network esto borra el estado local de la red de prueba (ledger, canal, artefactos generados en esa carpeta). No borra tu código en `api-middleware` ni el resto del repositorio.

### Verificación

1. Contenedores en marcha:

   ```bash
   docker ps --format "table {{.Names}}\t{{.Status}}"
   ```

   Deberías ver, entre otros: `orderer.example.com`, `peer0.org1.example.com`, `peer0.org2.example.com`, contenedores CouchDB y CAs si usaste `-ca`.

2. Canal activo en el peer:

   ```bash
   docker exec peer0.org1.example.com peer channel list
   ```

   Debe aparecer el nombre del canal acordado clientes.
