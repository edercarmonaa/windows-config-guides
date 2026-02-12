 Windows — Disco “Sin conexión” por colisión de firmas (DiskPart: `uniqueid` + `online`)

## Objetivo
Poner **En línea** un disco que Windows dejó **Sin conexión** por **colisión de firmas/ID** (muy común cuando el disco era interno, fue clonado o se convirtió a externo), **sin borrar datos**.

---

## Síntomas típicos
- En **Administración de discos** aparece el disco como:
  - **Sin conexión** (Offline)
  - Mensaje: *“El disco está sin conexión porque tiene una colisión de firmas…”*
- No permite **asignar letra** y varias opciones salen deshabilitadas.
- En otra PC sí puede leerse.

---

## ⚠️ Seguridad (IMPORTANTE)
- **NO ejecutes** `clean`, `format`, “Eliminar volumen” si quieres conservar archivos.
- Verifica 2 veces que seleccionas el **disco correcto** por **tamaño** (ej. 119 GB).

---

## Requisitos
- Windows 10/11
- **CMD o PowerShell como Administrador**
- Disco conectado por USB/SATA

---

## Opción B (100% efectiva): DiskPart

### Paso 1 — Abrir consola como Administrador
- **PowerShell (Admin)** o **CMD (Admin)**

### Paso 2 — Entrar a DiskPart y listar discos
```bat
diskpart
list disk
```

**Identifica el disco** por tamaño y número (ej. **Disco 2 ~119 GB**).

### Paso 3 — Seleccionar el disco y ver el ID actual
```bat
select disk 2
uniqueid disk
```

Esto te muestra el ID/firma actual.

### Paso 4 — Cambiar el ID (GPT / GUID) y poner el disco En línea
Si el disco es **GPT** (lo más probable si tiene partición EFI), asigna un ID nuevo (ejemplo):

```bat
uniqueid disk id=E2A32F1F-F8C2-4D42-A3DE-D15DEEF72E2A
online disk
exit
```

> Nota: si DiskPart te exige llaves, usa:
```bat
uniqueid disk id={E2A32F1F-F8C2-4D42-A3DE-D15DEEF72E2A}
```

---

## Paso 5 — Asignar letra a la partición (GUI)
1. Abre **Administración de discos**: `diskmgmt.msc`
2. Si no se refresca: **Acción → Volver a examinar discos**
3. Clic derecho en la partición grande (la de datos) → **Cambiar letra y rutas…**
4. **Agregar** → elige una letra (E:, F:, etc.)

---

## Verificación
- En Administración de discos, el disco aparece como **En línea**.
- En “Este equipo”, la unidad aparece con su letra y puedes abrir archivos.

---

## Troubleshooting rápido

### 1) Sigue “Sin conexión” o está en solo lectura
```bat
diskpart
select disk 2
attributes disk clear readonly
online disk
exit
```

### 2) BitLocker
Si el disco estaba cifrado, Windows pedirá la **clave de recuperación**.

### 3) No aparece “Cambiar letra…”
- Asegúrate de que el disco ya esté **En línea**.
- Repite **Acción → Volver a examinar discos**.

---

## Prueba final
1. Expulsa el disco con **Quitar hardware con seguridad**.
2. Desconéctalo y reconéctalo.
3. Confirma que monta automático y conserva la letra asignada.
