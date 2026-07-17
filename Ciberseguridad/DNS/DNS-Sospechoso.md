# DNS Sospechoso

## Indicadores normales vs. sospechosos

| Indicador | Normal | Sospechoso |
|-----------|--------|-----------|
| Dominio | google.com | x9a2k3.biz |
| Subdominios | pocos | muchos |
| Longitud | corta | muy larga |
| Frecuencia | ocasional | constante |
| Tipo | A / AAAA | TXT masivo |

## Conceptos de amenaza DNS

### DGA (Domain Generation Algorithm)

| Aspecto | Detalle |
|---------|---------|
| **QUÉ** | Algoritmo que genera dominios automáticamente |
| **POR QUÉ** | Evitar bloqueos y takedowns |
| **CÓMO se ve** | Muchos dominios raros, fallidos, aleatorios |
| **Ejemplo** | `x8a9k2z3q.biz` |

### Beaconing

| Aspecto | Detalle |
|---------|---------|
| **QUÉ** | Comunicación periódica con un servidor |
| **POR QUÉ** | Avisar "sigo vivo" al atacante |
| **CÓMO se ve** | Queries repetidos, intervalos regulares |
| **Ejemplo** | `ping01.c2.net` cada 60s |

### C2 (Command & Control)

| Aspecto | Detalle |
|---------|---------|
| **QUÉ** | Servidor que controla malware |
| **POR QUÉ** | Enviar órdenes y recibir datos |
| **CÓMO se ve** | DNS hacia dominios no conocidos |
| **Ejemplo** | `cmd.control-server.net` |

### DNS Tunneling

| Aspecto | Detalle |
|---------|---------|
| **QUÉ** | Uso de DNS como canal de datos |
| **POR QUÉ** | Evadir firewalls |
| **CÓMO se ve** | Subdominios muy largos y frecuentes |
| **Ejemplo** | `a9f82k39f02kd93.exf.net` |

### TXT Records maliciosos

| Aspecto | Detalle |
|---------|---------|
| **QUÉ** | Uso de TXT para datos/comandos |
| **POR QUÉ** | Enviar info sin HTTP |
| **CÓMO se ve** | Muchos TXT queries |
| **Filtro** | `dns.qry.type == 16` |

### Alta entropía

| Aspecto | Detalle |
|---------|---------|
| **QUÉ** | Nombres sin sentido humano |
| **POR QUÉ** | Generación automática |
| **CÓMO se ve** | Letras/números mezclados |
| **Ejemplo** | `k9d8f2j39a.net` |

### Subdominio excesivo

| Aspecto | Detalle |
|---------|---------|
| **QUÉ** | Subdominio > dominio |
| **POR QUÉ** | Exfiltrar datos codificados |
| **CÓMO se ve** | Subdominios larguísimos |
| **Ejemplo** | `base64data.example.net` |

### Dominios recién creados

| Aspecto | Detalle |
|---------|---------|
| **QUÉ** | Infraestructura nueva |
| **POR QUÉ** | Durar poco tiempo |
| **CÓMO se ve** | Dominios "jóvenes" |

### Fast Flux

| Aspecto | Detalle |
|---------|---------|
| **QUÉ** | IPs cambian rápido |
| **POR QUÉ** | Evitar bloqueo |
| **CÓMO se ve** | Muchas IPs para un dominio |

### NXDOMAIN flooding

| Aspecto | Detalle |
|---------|---------|
| **QUÉ** | Consultas que no resuelven |
| **POR QUÉ** | DGA probando dominios |
| **CÓMO se ve** | Muchas respuestas NXDOMAIN |
| **Filtro** | `dns.flags.response == 1 && dns.rcode == 3` |

## Detección en Wireshark

| Concepto | Señal | Filtro | Pregunta clave |
|----------|-------|--------|---------------|
| DNS normal | Dominios conocidos | `dns` | ¿Tiene sentido para el usuario? |
| Beaconing | Queries repetidas | `dns && !(dns.flags.response)` | ¿Cada cuánto ocurre? |
| C2 | Dominio raro constante | `dns && !(dns.flags.response)` | ¿Qué proceso lo genera? |
| DNS tunneling | Subdominios distintos | `dns && !(dns.flags.response)` | ¿El subdominio cambia? |
| Exfiltración | Subdominios largos | `strlen(dns.qry.name) > 50` | ¿Por qué tan largo? |
| DGA | Muchos NXDOMAIN | `dns.flags.response == 1 && dns.rcode == 3` | ¿Cuántos fallan? |
| TXT sospechoso | TXT frecuentes | `dns.qry.type == 16` | ¿TXT para qué? |
| Intervalos regulares | Ritmo constante | DNS + columna Time | ¿Es demasiado perfecto? |

## Referencia
- [[Ciberseguridad/00-Index]]

#dns #resolucion-nombres #ciberseguridad #dns #git
