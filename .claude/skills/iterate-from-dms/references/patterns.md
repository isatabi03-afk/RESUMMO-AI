# Patrones de Fix Reutilizables — Agente IA Resummo

## Patrones probados

### 1. Keyword gap → agregar trigger
**Problema:** Usuario escribe variante no contemplada (ej: "cuanto sale" en vez de "cuanto cuesta")
**Fix:** Agregar keyword en ManyChat: "sale", "vale", "precio"
**Aplicable a:** cualquier keyword no matcheado

### 2. Respuesta generica → respuesta especifica por paquete
**Problema:** Bot responde con info generica cuando el usuario ya dijo que paquete le interesa
**Fix:** Agregar condicional en ManyChat que detecte paquete mencionado y envie info especifica
**Aplicable a:** flows de info de paquetes

### 3. CTA tardio → CTA contextual
**Problema:** Bot espera demasiado para enviar link de compra
**Fix:** Agregar CTA despues de 2do mensaje positivo del usuario (no esperar a que pida link)
**Aplicable a:** flow principal de venta

### 4. No maneja "ya compre"
**Problema:** Usuario dice que ya compro y el bot sigue vendiendo
**Fix:** Agregar keyword "ya compre" + "pague" → flow de verificacion post-compra
**Aplicable a:** soporte post-compra

### 5. Objecion de precio no manejada
**Problema:** Usuario dice "esta caro" y el bot no responde adecuadamente
**Fix:** Agregar respuesta comparativa: "Entendemos. Las academias cobran S/3,000-5,000 por algo similar. Resummo cuesta [precio] con acceso de por vida"
**Aplicable a:** manejo de objeciones

### 6. Derivacion tardia
**Problema:** Bot intenta resolver algo que deberia escalar a humano
**Fix:** Agregar trigger de derivacion despues de 2 intentos fallidos del bot
**Aplicable a:** cualquier flow con interaccion compleja

### 7. Anti-alucinacion reforzada
**Problema:** Bot inventa un paquete o precio que no existe
**Fix:** Reforzar en prompt: "SOLO estos 4 paquetes existen: [lista]. NUNCA inventes otros."
**Aplicable a:** prompt del agente (n8n o ManyChat)

## Registro de fixes aplicados

| Fecha | Problema | Patron usado | Resultado |
|-------|---------|-------------|-----------|
| [pendiente] | | | |
