# Laboratorio Multi-Nodo Apache Hadoop: Almacenamiento Distribuido (HDFS) y Tolerancia a Fallos

- **Titulación:** Curso de Especialización en Inteligencia Artificial y Big Data (RD 279/2021)
- **Módulo Profesional:** 5075 — Sistemas de Big Data (RA 2)
- **Autor:** Gonzalo Frutos Mantero
- **Repositorio:** `IABD-5075-LabHadoop-Frutos-Mantero`

---

## 1. Arquitectura del Clúster Desplegado

Clúster multinodo contenerizado mediante **Docker Compose** sobre la imagen oficial `apache/hadoop:3` (**Hadoop 3.3.6**) e interconectado a través de la red aislada tipo *bridge* `hadoop-net` (`172.18.0.0/16`).

| Servicio / Contenedor | Rol en el Clúster | Dirección IP | Puertos Expuestos |
| :--- | :--- | :--- | :--- |
| `namenode` | Nodo maestro de metadatos HDFS (*NameNode*) | `172.18.0.2` | `9870:9870` (Web UI HDFS) |
| `datanode1` | Nodo esclavo de almacenamiento de bloques | `172.18.0.3` | `9866` / `9864` (internos) |
| `datanode3` | Nodo esclavo de almacenamiento de bloques | `172.18.0.4` | `9866` / `9864` (internos) |
| `datanode2` | Nodo esclavo de almacenamiento de bloques | `172.18.0.6` | `9866` / `9864` (internos) |
| `resourcemanager` | Gestor central de recursos YARN | Red `hadoop-net` | `8088:8088` (Web UI YARN) |
| `nodemanager` | Gestor local de contenedores de cómputo YARN | Red `hadoop-net` | `8042` (interno) |

### Parámetros críticos de configuración (`docker/config`)
- `CORE-SITE.XML_fs.defaultFS=hdfs://namenode:9000`
- `HDFS-SITE.XML_dfs.blocksize=67108864` (**64 MiB** por bloque)
- `HDFS-SITE.XML_dfs.replication=2` (Factor de replicación **R = 2**)

---

## 2. Estructura del Repositorio

```text
IABD-5075-LabHadoop-Frutos-Mantero/
├── .gitignore
├── README.md
├── docker/
│   ├── config
│   └── docker-compose.yml
├── evidencias/
│   ├── 01_namenode_ui_9870.png
│   ├── 02_hdfs_fsck_bloques.png
│   ├── 03_simulacion_fallo_datanode2.png
│   └── 04_recuperacion_cluster.png
└── informe/
    └── Frutos_Mantero_InformeLabHadoop.pdf
```

---

## 3. Instrucciones de Arranque y Parada

1. **Levantar el clúster en segundo plano:**
   ```bash
   docker compose -f docker/docker-compose.yml up -d
   ```
2. **Acceder a las consolas web de monitorización:**
   - **HDFS NameNode UI:** `http://localhost:9870`
   - **YARN ResourceManager UI:** `http://localhost:8088`
3. **Detener el clúster y eliminar volúmenes temporales:**
   ```bash
   docker compose -f docker/docker-compose.yml down -v
   ```