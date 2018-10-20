# Instrucciones para la carga de los datos del laboratorio
---
# 1.- Preparando el entorno
Si se desea utilizar uno propio, editar los pasos necesarios...
```bash
cd /tmp/
mkdir logs/
```

# 2.- Descargando archivos necesarios
```bash
wget https://github.com/mdiazcl/webpage/raw/master/Talleres/TallerAnalisisDeLogs/files/logs.tar.gz
wget https://raw.githubusercontent.com/mdiazcl/webpage/master/Talleres/TallerAnalisisDeLogs/files/ls.conf
```

# 3.- Descomprimir los logs
```bash
tar -xvzf logs.tar.gz
```

# 4.- Configurar logstash
```
# Revisar la ruta en la que están los logs
pwd
> /tmp/logs

# Copiar esta ruta y editar archivo ls.conf
nano ls.conf

# Buscar la línea: path => "http.log" y colocar la ruta de los logs (Absoluta, no relativa)
path => "/tmp/logs/http.log"

# Guardar el archivo
```

# 5.- Iniciar Elasticsearch
```bash
cd <directorio elasticsearch>
bin/elasticsearch
```

# 6.- Cargar los datos
```
cd /tmp/logs
logstash -f ls.conf
```

# Esperar horas....



