# TCP — Protocolo y Análisis

## Three-Way Handshake

```
1. SYN      →  Cliente solicita conexión
2. SYN-ACK  →  Servidor acepta
3. ACK      →  Conexión establecida
```

👉 Ver **SYN + ACK** indica conexiones sanas y esperadas.

## RST (Reset)

Cierra una conexión de forma abrupta. Puede indicar:
- Puerto cerrado
- Firewall bloqueando
- Aplicación fallando
- Escaneo de puertos

📌 Muchos RST → posible problema. Ausencia de RST → comportamiento normal.

## Flags TCP

| Flag | Nombre | ¿Qué indica? | ¿Cuándo es normal? | 🚨 Cuándo alerta |
|------|--------|--------------|-------------------|-----------------|
| **SYN** | Synchronize | Inicio de conexión TCP | Siempre al comenzar conexión | Muchos SYN sin respuesta → scan |
| **ACK** | Acknowledgment | Confirmación de recepción | En casi todos los paquetes | ACKs sin SYN previo |
| **SYN-ACK** | Sync + Ack | Servidor acepta conexión | Conexión sana | — |
| **FIN** | Finish | Cierre ordenado de conexión | Al terminar una sesión | FINs repetidos |
| **RST** | Reset | Cierre abrupto | Errores puntuales | Muchos RST → firewall, scan |
| **PSH** | Push | Enviar datos inmediatamente | Apps interactivas | — |
| **URG** | Urgent | Datos urgentes | Muy raro | Posible malware antiguo |

> **Regla SOC:** Lo normal aparece poco. Lo anómalo se repite.

## Eventos de análisis TCP (`tcp.analysis`)

| Evento | Significado | Normalidad | Qué indica |
|--------|-------------|-----------|-----------|
| **Retransmission** | Paquete reenviado | Normal en Wi-Fi | Pérdida leve |
| **Fast Retransmission** | Reenvío rápido | Normal ocasional | Congestión |
| **Duplicate ACK** | Falta un paquete | Normal puntual | Paquete perdido |
| **Out-Of-Order** | Llegó fuera de orden | Normal en redes rápidas | Reordenamiento |
| **Window Full** | Buffer lleno | Ocasional | Saturación |
| **Zero Window** | No puede recibir más | ⚠️ Atención | App lenta o colgada |

> Estas interpretaciones **no son flags TCP reales** — son análisis que hace Wireshark sobre el flujo.

## Referencia
- [[00-Index]]
- [[Redes/Wireshark-TShark]]
- [[Redes/Filtros-Wireshark]]
