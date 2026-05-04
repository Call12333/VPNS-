# 🔎 GRE over IPsec Lab – Tunnel + Encryption (PNETLab)

Un laboratorio enfocado en combinar lo mejor de dos mundos:
**GRE (Generic Routing Encapsulation)** + **IPsec**, creando un túnel flexible y completamente cifrado entre dos redes.

---

## 🧠 ¿Qué se logra aquí?

A diferencia de una VPN IPsec normal, este lab permite:

* 📦 Transportar múltiples protocolos (gracias a GRE)
* 🔒 Cifrar todo el túnel (gracias a IPsec)
* 🔁 Soportar routing dinámico (OSPF, EIGRP, etc.)

👉 En resumen: un túnel **más potente y más realista** a nivel empresarial.

---

## 🌐 Topología

* 2 Routers principales (R1 ↔ R2)
* Conexión a través de Internet (Cloud)
* Redes locales en ambos extremos

📌 El túnel GRE pasa por Internet y luego es protegido con IPsec

---

## ⚙️ Fase 1: Crear túnel GRE

```id="z7a21x"
interface Tunnel0
 ip address 10.10.10.1 255.255.255.252
 tunnel source [IP_LOCAL]
 tunnel destination [IP_REMOTA]
```

En el otro router:

```id="8dfk3l"
interface Tunnel0
 ip address 10.10.10.2 255.255.255.252
 tunnel source [IP_REMOTA]
 tunnel destination [IP_LOCAL]
```

---

## 🔐 Fase 2: Proteger con IPsec

### ACL (interesante aquí 👇)

```id="plk9sd"
access-list 110 permit gre host [IP_LOCAL] host [IP_REMOTA]
```

---

### ISAKMP (Fase 1)

```id="lmn321"
crypto isakmp policy 10
 encryption aes
 hash sha
 authentication pre-share
 group 2
```

```id="we90qp"
crypto isakmp key cisco address [IP_REMOTA]
```

---

### IPsec (Fase 2)

```id="dfg456"
crypto ipsec transform-set GRE-SET esp-aes esp-sha-hmac
```

---

### Crypto Map

```id="rty567"
crypto map GRE-MAP 10 ipsec-isakmp
 set peer [IP_REMOTA]
 set transform-set GRE-SET
 match address 110
```

```id="uio890"
interface g0/0
 crypto map GRE-MAP
```

---

## 🧪 Verificación

```id="chk111"
show interface tunnel0
show crypto isakmp sa
show crypto ipsec sa
```

✅ Tunnel0 → UP
✅ Estado: `QM_IDLE`
✅ Tráfico cifrado activo

---

## 🔥 ¿Por qué GRE over IPsec?

| Característica   | IPsec normal | GRE over IPsec |
| ---------------- | ------------ | -------------- |
| Multicast        | ❌ No         | ✅ Sí           |
| Routing dinámico | ❌ Limitado   | ✅ Completo     |
| Flexibilidad     | Media        | Alta           |

---

## 🧠 Enfoque ciberseguridad

Este lab es clave para entender:

* 🔎 Cómo encapsular tráfico antes de cifrarlo
* ⚔️ Técnicas de pivoting a través de túneles
* 🧩 Detección de tráfico GRE dentro de IPsec
* 🚨 Superficies de ataque en configuraciones mal hechas

---

## 🚀 Conclusión

GRE over IPsec no es solo una VPN…
es una **infraestructura completa de túneles cifrados**, usada en entornos reales donde se necesita flexibilidad, escalabilidad y seguridad.

