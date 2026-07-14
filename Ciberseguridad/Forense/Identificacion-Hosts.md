# Identificación de Hosts

## Vía DHCP

**DHCP** no solo entrega IPs — también intercambia información de identidad.

Cuando un dispositivo se conecta, envía **DHCP Discover/Request**; dentro de las Opciones (específicamente la **Opción 12**), anuncia su hostname.

**Filtro:** `bootp` o `dhcp`

| Qué buscar | Filtro | Qué revela |
|-----------|--------|-----------|
| Hostname | Opción 12 del DHCP | Nombre del equipo |
| Vendor Class | Opción 60 | Tipo de dispositivo |

## Vía NBNS (NetBIOS)

**NetBIOS (NBNS)** es un protocolo antiguo que sigue vivo en redes Windows para que los equipos se encuentren por nombre en la red local.

Es muy "ruidoso": constantemente envía broadcasts preguntando "¿quién es el dueño del nombre X?"

**Filtro:** `nbns`

| Qué buscar | Filtro | Qué revela |
|-----------|--------|-----------|
| Nombre del equipo | `nbns` | Hostname Windows |
| Servicios | `nbns.name` | Qué está buscando |

## Vía HTTP User-Agent

Si se revisa la red buscando **HTTP sin encriptar**, se puede inferir el sistema operativo del cliente:

1. Filtrar tráfico con `http.accept_language` o buscar un `HTTP GET`
2. Seguir el stream TCP (`Follow → TCP Stream`)
3. Revisar el header `User-Agent` dentro del stream

### Qué revela el User-Agent

| Campo | Ejemplo | Info |
|-------|---------|------|
| SO | `Windows NT 10.0` | Windows 10/11 |
| Navegador | `Chrome/120.0` | Versión de Chrome |
| Plataforma | `Win64; x64` | Arquitectura |

## Referencia
- [[00-Index]]
- [[Redes/Wireshark-TShark]]
- [[Redes/Filtros-Wireshark]]
