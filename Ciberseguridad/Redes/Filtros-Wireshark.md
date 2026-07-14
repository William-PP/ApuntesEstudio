# Filtros en Wireshark

## Filtros de protocolo

| Filtro | Para qué sirve | Uso SOC típico |
|--------|---------------|----------------|
| `dns` | Ver consultas DNS | Detección DGA |
| `tcp` | Ver conexiones TCP | Análisis general |
| `udp` | Tráfico no confiable | DNS, VoIP |
| `icmp` | Ping y errores | Scans, reachability |
| `arp` | Resolución MAC | ARP spoofing |
| `http` | Tráfico web plano | Análisis legacy |
| `tls` | HTTPS cifrado | Flujo seguro |
| `nbns` | NetBIOS Name Service | Identificación de hosts Windows |
| `bootp` o `dhcp` | Asignación de IP | Identificación de hostname |
| `kerberos` | Autenticación AD | Ataques contra Active Directory |
| `ntlmssp` | Autenticación NTLM | Detección de fallback inseguro |

## Filtros de análisis (display filters)

| Filtro | Qué muestra | Cuándo usarlo |
|--------|-------------|---------------|
| `tcp.flags.syn == 1 && tcp.flags.ack == 0` | SYN puros | Detectar scans |
| `tcp.flags.reset == 1` | RST | Fallos / bloqueos |
| `tcp.analysis.flags` | Problemas TCP | Latencia / pérdida |
| `dns.qry.name` | Dominio consultado | Malware / C2 |
| `ip.addr == X.X.X.X` | IP específica | Investigar host |
| `(http.request or tls.handshake.type eq 1) && !(ssdp)` | Peticiones HTTP/TLS excluyendo SSDP | SSDP recurrente es mala señal |
| `strlen(dns.qry.name) > 50` | DNS con subdominios largos | Exfiltración DNS |
| `dns.flags.response == 1 && dns.rcode == 3` | Respuestas NXDOMAIN | DGA |
| `dns.qry.type == 16` | Consultas TXT | TXT sospechoso |

## Referencia
- [[Ciberseguridad/00-Index]]
- [[Redes/Wireshark-TShark]]
- [[Redes/Comandos-TShark]]
