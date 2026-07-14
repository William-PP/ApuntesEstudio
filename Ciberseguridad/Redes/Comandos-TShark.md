# Comandos de TShark

## Comandos esenciales

| Comando | Para qué sirve | Mentalidad SOC |
|---------|---------------|----------------|
| `tshark` | Captura en vivo | Sniffing básico |
| `tshark -D` | Ver interfaces disponibles | ¿Dónde capturo? |
| `tshark -i eth0` | Capturar en interfaz específica | Tráfico real |
| `tshark -c 50` | Limitar cantidad de paquetes | Captura controlada |
| `tshark -r file.pcap` | Leer una captura | Análisis forense |
| `tshark -w file.pcap` | Guardar captura | Evidencia |
| `tshark -f` | Filtro de captura (BPF) | Reducir ruido desde el inicio |
| `tshark -Y` | Filtro de display | Análisis fino sobre lo ya capturado |
| `tshark -V` | Ver detalle completo del paquete | Profundizar |

## Ejemplos prácticos

```bash
# Capturar 100 paquetes en la interfaz eth0
tshark -i eth0 -c 100

# Capturar solo tráfico DNS
tshark -i eth0 -f "port 53"

# Guardar captura a archivo
tshark -i eth0 -w captura.pcapng

# Leer y filtrar una captura
tshark -r captura.pcapng -Y "dns"

# Ver detalle de un paquete específico
tshark -r captura.pcapng -Y "tcp.flags.syn==1" -V

# Exportar como JSON
tshark -r captura.pcapng -T json > output.json

# Capturar con filtro de display (solo HTTP GET)
tshark -i eth0 -Y "http.request.method == GET"
```

## Referencia
- [[Ciberseguridad/00-Index]]
- [[Redes/Wireshark-TShark]]
- [[Redes/Filtros-Wireshark]]
