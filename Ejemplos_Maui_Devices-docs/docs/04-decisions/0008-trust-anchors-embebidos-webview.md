---
doc_id: ADR-0008
doc_type: adr
title: TLS en WebView Android — trust anchors embebidos (raíz + intermedios)
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 0
audience: [dev, ops]
classification: uso-interno
traces: [PIECE-INT-001, ARCH-CROSS-001]
---

# ADR-0008: TLS en WebView Android — trust anchors embebidos (raíz + intermedios)

> Decisión **documentada en el origen** (`Docs/Certificados-SSL/README.md §6`, estrategia «la elegida») y reconstruida aquí como ADR.

## Contexto

El `WebView` de `Ejemplo_Maui_Hibrida` (Android System WebView/Chromium) falla al abrir el backend HTTPS con `Trust anchor for certification path not found`: la raíz **ISRG Root X2** (Let's Encrypt, ECDSA 2020) no está en el trust store de los Android antiguos usados en pruebas (Android 12/13), y el servidor no envía la raíz (correcto según TLS).

## Decisión

Embeber en la app la **raíz y los dos intermedios** (`isrg_root_x2.pem`, `root_ye.pem`, `ye2.pem`) en `Platforms/Android/Resources/raw/` y declararlos como `trust-anchors` **por dominio** en `network_security_config.xml`, referenciado desde `AndroidManifest.xml` (`android:networkSecurityConfig`).

## Alternativas

- **Solo la raíz** (recomendada por la propia guía para mínimo mantenimiento): depende de que el servidor siga enviando los intermedios; se prefirió robustez ante cadenas incompletas.
- **`src="user"` (CAs instaladas por el usuario)**: descartada por riesgo MITM en producción.
- **Ignorar errores TLS en el WebView**: inaceptable — anula la autenticación del canal.

## Consecuencias

- (+) La híbrida funciona en dispositivos con trust store desactualizado, sin tocar el servidor.
- (–) **Mantenimiento acoplado a Let's Encrypt**: cuando roten los intermedios hay que regenerar los `.pem` (la raíz vale hasta 2040).
- (–) Reglas frágiles de `res/raw` (nombres `[a-z0-9_]`, sin extensiones duplicadas) que ya rompieron un build — documentadas en la guía del origen.

## Evidencia

- Guía completa: `Ejemplos_Devices/Docs/Certificados-SSL/{README.md, Anexo-A-Comandos.md, Anexo-B-Glosario.md}`
- ia-db: [índice 10 §2](../../../ia-db/indexes/10_Documentacion-Transversal.md)
- Transversal: [06-crosscutting](../01-architecture/06-crosscutting.md)
