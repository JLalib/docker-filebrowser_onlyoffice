# Filebrowser + OnlyOffice (Docker)

Repositorio listo para **desplegar Filebrowser integrado con OnlyOffice Document Server** usando Docker Compose.

Esta configuración permite:
- Navegar y gestionar archivos con **Filebrowser**
- Editar documentos Office (DOCX, XLSX, PPTX) con **OnlyOffice**
- Comunicación segura mediante **JWT compartido**

---

## 📦 Estructura del repositorio

```text
filebrowser-onlyoffice/
├── docker-compose.yml
├── config.yaml
├── documents/
├── movies/
├── photos/
├── data/
└── README.md
```

---

## 🚀 Despliegue rápido

```bash
docker compose up -d
```

---

## 🔐 Seguridad (JWT)

Filebrowser y OnlyOffice **comparten el mismo secreto JWT**, imprescindible para que OnlyOffice pueda abrir y guardar documentos correctamente.

⚠️ **Cambia el secreto en producción.**

---

## ⚙️ docker-compose.yml

```yaml
services:

  filebrowser:
    image: gtstef/filebrowser:latest
    container_name: filebrowser
    restart: unless-stopped
    ports:
      - "8200:80"
    environment:
      - TZ=Europe/Madrid
      - FILEBROWSER_ONLYOFFICE_SECRET=066a60a2e469df3b6fa0b45e92c920062a01287f
    volumes:
      - ./config.yaml:/home/filebrowser/config.yaml
      - ./documents:/documents
      - ./movies:/movies
      - ./photos:/photos
      - ./data:/home/filebrowser/data
    networks:
      - office-net

  onlyoffice:
    image: onlyoffice/documentserver:latest
    container_name: onlyoffice-file
    restart: unless-stopped
    ports:
      - "8080:80"
    environment:
      - JWT_ENABLED=true
      - JWT_SECRET=066a60a2e469df3b6fa0b45e92c920062a01287f
      - ALLOW_PRIVATE_IP_ADDRESS=true
      - USE_UNAUTHORIZED_STORAGE=true
    volumes:
      - ./documents:/documents
    networks:
      - office-net

networks:
  office-net:
    driver: bridge
```

---

## 🧩 config.yaml (ejemplo mínimo)

```yaml
onlyoffice:
  enable: true
  documentserver: http://onlyoffice/
  secret: 066a60a2e469df3b6fa0b45e92c920062a01287f
```

---

## 🌐 Acceso

| Servicio | URL |
|--------|-----|
| Filebrowser | http://IP_SERVIDOR:8200 |
| OnlyOffice | http://IP_SERVIDOR:8080 |

---

## 📌 Notas

- Pensado para entornos LAN o laboratorio
- Compatible con reverse proxy

