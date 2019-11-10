# docker-geonetwork-legacy
Docker images for legacy GeoNetwork versions.

## How to run

```
docker run --name geonetwork-26 -p 8080:8080 geonetwork-legacy:2.6.4
```

### Default credentials
* **Username**: `admin`
* **Password**: `admin`

### Environment variables:
* `DATA_DIR`: GeoNetwork data directory
* `JAVA_OPTS`: Java options used to start Tomcat
