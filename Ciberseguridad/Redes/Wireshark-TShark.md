# Wireshark y TShark

## QUÉ es

| Herramienta | Definición |
|------------|-----------|
| **Wireshark** | Analizador de protocolos con interfaz gráfica — "microscopio" para ver todo el tráfico de red |
| **TShark** | Versión CLI de Wireshark — misma potencia, optimizada para automatización y servidores |

## Cuándo usar cada uno

| Característica | Wireshark | TShark |
|----------------|-----------|--------|
| **Interfaz** | Gráfica (GUI) | Línea de comandos (CLI) |
| **Curva de aprendizaje** | Baja — intuitivo | Alta — requiere comandos |
| **Consumo de RAM/CPU** | Alto | Bajo |
| **Uso en servidores** | Difícil (necesita escritorio) | Ideal (funciona por SSH) |
| **Archivos grandes** | Se puede trabar | Los procesa con rapidez |
| **Automatización** | Limitada | Excelente — se integra en scripts |
| **Análisis visual** | Gráficos, mapas, estadísticas | Solo salida de texto |

> **Regla:** Wireshark para análisis exploratorio/visual, TShark para automatización y servidores sin GUI.

## Flujo de trabajo típico

```
1. Capturar tráfico (o abrir .pcapng)
         │
         ▼
2. Aplicar filtros para reducir ruido
         │
         ▼
3. Seguir streams de interés (Follow TCP Stream)
         │
         ▼
4. Analizar paquetes individuales
         │
         ▼
5. Exportar resultados / documentar hallazgos
```

## Convención para guardar capturas

Formato: `Fecha-ubicación-tipo-Actividad-Extensión`

| Parte | Ejemplo |
|-------|---------|
| Fecha | `2026-01-31` |
| Ubicación | `host` / `192.168.1.34` |
| Tipo | `dns` / `tcp` / `tls` |
| Actividad | `browse` / `syn-scan` |
| Extensión | `.pcapng` |

**Ejemplo:** `2026-01-31-host-syn-scan-wireshark.pcapng`

## Bibliografía

- [Personalizar Wireshark (columnas)](https://unit42.paloaltonetworks.com/unit42-customizing-wireshark-changing-column-display/)
- [Creación de filtros personalizados](https://unit42.paloaltonetworks.com/using-wireshark-display-filter-expressions/)
- [Identificación de hosts y usuarios](https://unit42.paloaltonetworks.com/using-wireshark-identifying-hosts-and-users/)
- [Taller de Wireshark](https://unit42.paloaltonetworks.com/wireshark-workshop-videos/)
- [Video complementario (YouTube)](https://www.youtube.com/watch?v=6ebXr7nqr6o&list=PL9V4Zu3RroiX8O6ySYeYrjT1XiCURsmj2)

## Referencia
- [[00-Index]]
- [[Redes/TCP-Protocol]]
- [[Redes/Filtros-Wireshark]]
- [[Redes/Comandos-TShark]]
