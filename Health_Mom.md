# 🤱 MamaHealth

> App móvil de seguimiento de embarazo con control inteligente de glucosa y presión arterial.
> Construida con Flutter, arquitectura offline-first y motor de alertas médicas.

<p align="center">
  <img src="https://img.shields.io/badge/Flutter-3.x-02569B?style=for-the-badge&logo=flutter&logoColor=white"/>
  <img src="https://img.shields.io/badge/Dart-3.x-0175C2?style=for-the-badge&logo=dart&logoColor=white"/>
  <img src="https://img.shields.io/badge/Firebase-FFCA28?style=for-the-badge&logo=firebase&logoColor=black"/>
  <img src="https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white"/>
  <img src="https://img.shields.io/badge/Estado-Beta%201-E91E63?style=for-the-badge"/>
</p>

---

## 📱 Capturas de Pantalla

| Dashboard | Glucosa | Presión | Historial |
|:---------:|:-------:|:-------:|:---------:|
| Semáforo de salud + alertas | Clasificación ADA en tiempo real | Validación cruzada sistólica/diastólica | Filtros + búsqueda + swipe |

| Gráficas | Alimentos | Perfil | Exportar PDF |
|:--------:|:---------:|:------:|:------------:|
| Tendencias con límites médicos | Registro rápido con carbos | Semana gestacional calculada | Reporte médico profesional |

---

## ✨ Funcionalidades Principales

### 💉 Control de Glucosa
- Registro con etiquetas: ayuno, postprandial 1h/2h, aleatoria, antes de dormir
- Clasificación automática en tiempo real basada en **guías ADA para embarazo**
- Umbrales más estrictos que diabetes general (95 mg/dL ayuno, 120 mg/dL postprandial)
- Validación de rango clínico (20–600 mg/dL) con mensajes específicos por severidad

### 🫀 Control de Presión Arterial
- Registro de sistólica, diastólica, frecuencia cardíaca y brazo utilizado
- Clasificación: Normal → Elevada → HTA Estadio 1 → HTA Estadio 2 → Crisis Hipertensiva
- Detección automática de posible **preeclampsia** con alerta de urgencias

### 🟢🟡🔴 Semáforo de Salud
- Estado general calculado combinando glucosa + presión arterial
- Verde: valores en rango · Amarillo: atención · Rojo: consulta médica urgente

### ⚠️ Motor de Alertas Inteligentes
- Detección de **tendencias** (3 lecturas consecutivas al alza)
- Alertas por **valores repetidamente altos** (3+ lecturas elevadas recientes)
- Alertas de **hipoglucemia** con acción inmediata
- Severidades: Info → Warning → Danger → Critical

### 📊 Gráficas de Tendencias
- Líneas de glucosa y presión arterial con los últimos 14 registros
- **Líneas de referencia médica** (meta 95 mg/dL, límite 140 mmHg)
- Puntos coloreados por clasificación (verde/naranja/rojo)
- Estadísticas: promedio, máximo y mínimo del período

### 📋 Historial Unificado
- Lista combinada de glucosa, presión, peso y alimentos
- Filtros por tipo, rango de fechas y búsqueda de texto libre
- Presets rápidos: hoy, últimos 7 días, últimos 30 días, este mes
- Eliminación por swipe con confirmación · Vista de detalle en modal

### 📄 Exportación PDF Médico
- Reporte multipágina con encabezado de paciente
- Tablas de glucosa y presión con clasificación por colores
- **Estadísticas automáticas**: promedios, máximos, mínimos
- **Observaciones automáticas** basadas en patrones detectados
- Listo para compartir por WhatsApp, email o imprimir

### 🍽️ Registro de Alimentos
- Captura rápida con categoría (desayuno/almuerzo/cena/colación)
- Nivel de carbohidratos (bajo/medio/alto) para correlacionar con glucosa
- Vista del día actual con eliminación por swipe

### 👤 Perfil Personalizado
- Nombre, fecha de inicio de embarazo y datos del médico
- **Semana gestacional y trimestre** calculados automáticamente
- Metas personalizables de glucosa y presión arterial

---

## 🛠️ Stack Tecnológico

| Capa | Tecnología | Por qué |
|------|-----------|---------|
| **UI** | Flutter + Material 3 | Multiplataforma, widgets ricos, theming |
| **Estado** | Riverpod 2 | Type-safe, testeable, reactive streams |
| **Base de datos** | Drift (SQLite) | ORM type-safe, offline-first, DAOs reactivos |
| **Navegación** | GoRouter | Shell Route, deep links, redirect guards |
| **Gráficas** | fl_chart | Customizable, líneas de referencia, animaciones |
| **PDF** | pdf + printing | Multipágina, tablas, compartir nativo |
| **Backend** | Firebase Firestore | Sync opcional, sin servidor propio |
| **Background** | WorkManager | Recordatorios en segundo plano |
| **Notificaciones** | flutter_local_notifications | Alertas programadas locales |

---

## 🏗️ Arquitectura

```
lib/
├── main.dart
├── app.dart                    ← GoRouter + Shell + Bottom Nav
│
├── core/
│   ├── providers/
│   │   └── providers.dart      ← Todos los providers Riverpod
│   └── utils/
│       ├── classification_engine.dart  ← Lógica médica ADA
│       └── alert_engine.dart           ← Motor de alertas
│
├── data/
│   ├── local/
│   │   ├── database.dart       ← AppDatabase (Drift)
│   │   ├── tables/             ← 5 tablas SQLite
│   │   └── daos/               ← 5 DAOs con streams reactivos
│   ├── remote/
│   │   └── firebase_sync_service.dart
│   └── repositories/           ← 5 repositorios con validaciones clínicas
│
├── domain/
│   └── models/                 ← Enums médicos, modelos, alertas
│
└── ui/
    ├── dashboard/              ← Semáforo + resumen + alertas
    ├── glucose/                ← Registro + clasificación en vivo
    ├── blood_pressure/         ← Registro + validación cruzada
    ├── history/                ← Historial unificado + filtros
    ├── charts/                 ← Gráficas con fl_chart
    ├── food/                   ← Registro de alimentos
    ├── profile/                ← Perfil + semana gestacional
    ├── export/                 ← Generador de PDF
    └── onboarding/             ← Flujo de primera vez
```

### Patrón MVVM con Riverpod

```
Screen (UI)
  ↓ observa
Provider / StreamProvider
  ↓ expone
Notifier (lógica de UI)
  ↓ llama
Repository (validaciones de negocio)
  ↓ usa
DAO (queries Drift)
  ↓ lee/escribe
SQLite Database
  ↓ emite Stream reactivo
  ↑ regresa a Screen automáticamente
```

---

## 🧠 Lo Técnicamente Interesante

### Motor de Clasificación Médica
```dart
// Umbrales ADA específicos para embarazo (más estrictos que DM tipo 2)
static GlucoseClassification classifyGlucose(double value, GlucoseTag tag) {
  return switch (tag) {
    GlucoseTag.fasting => switch {
      value < 60   => GlucoseClassification.low,
      value <= 95  => GlucoseClassification.normal,   // Meta ADA gestacional
      value <= 105 => GlucoseClassification.elevated,
      value <= 126 => GlucoseClassification.high,
      _            => GlucoseClassification.critical,
    },
    // ...
  };
}
```

### Alertas con Detección de Tendencias
```dart
// Detecta si las últimas 3 lecturas son consistentemente al alza
static _Trend _detectTrend(List<double> values) {
  final isUpward = values[0] > values[1] && values[1] > values[2];
  final delta    = values[0] - values[2];
  if (isUpward && delta > 15) return _Trend.up;
  // ...
}
```

### Streams Reactivos con Drift
```dart
// La UI se actualiza automáticamente cuando cambian los datos
Stream<List<GlucoseReading>> watchAll() =>
    (select(glucoseReadings)
          ..orderBy([(t) => OrderingTerm.desc(t.timestamp)]))
        .watch(); // ← Stream reactivo, sin polling
```

### PDF Generado Programáticamente
```dart
// Observaciones automáticas basadas en patrones
static List<String> _generateObservations(PdfReportData data) {
  final pct = (highCount * 100 / data.glucoseReadings.length).round();
  if (pct > 50) obs.add('Más del 50% de glucosas elevadas. Requiere atención médica urgente.');
  // ...
}
```

---

## 🚀 Instalación

```bash
# 1. Clonar
git clone https://github.com/CesarLeon551/mama-health.git
cd mama-health

# 2. Dependencias
flutter pub get

# 3. Generar código Drift (DAOs y modelos)
dart run build_runner build --delete-conflicting-outputs

# 4. Correr
flutter run
```

> **Firebase es opcional.** La app funciona 100% offline sin configurarlo.
> Para activarlo: `flutterfire configure` y agrega `google-services.json`.

### Requisitos
- Flutter 3.16.0+
- Dart 3.3.0+
- Android SDK API 35 / build-tools 35.0.0
- minSdk: API 26 (Android 8.0)

---

## 📋 Estado del Proyecto

### ✅ Beta 1 — Completado
- [x] Base de datos offline-first (Drift + 5 tablas + 5 DAOs)
- [x] Motor de clasificación médica ADA gestacional
- [x] Motor de alertas inteligentes (tendencias + críticos)
- [x] Dashboard con semáforo de salud
- [x] Registro de glucosa con clasificación en tiempo real
- [x] Registro de presión arterial con validación cruzada
- [x] Historial unificado con filtros, búsqueda y swipe
- [x] Gráficas de tendencias con referencias médicas
- [x] Exportación PDF médico profesional (3 páginas)
- [x] Registro de alimentos con nivel de carbohidratos
- [x] Perfil con semana gestacional automática
- [x] Onboarding de primera vez
- [x] Navegación completa (GoRouter + Shell + Bottom Nav)

### 🔜 Beta 2 — En desarrollo
- [ ] `ChartsNotifier` — providers reactivos para fl_chart
- [ ] `WeightFormScreen` — formulario + gráfica de ganancia de peso
- [ ] Recordatorios — WorkManager + flutter_local_notifications
- [ ] Firebase Sync — sincronización offline → online
- [ ] Edición de registros desde el historial
- [ ] Tests unitarios — ClassificationEngine, AlertEngine, repositorios

### 🗓️ v1.0 — Planeado
- [ ] Íconos y splash screen personalizados
- [ ] Publicación en Play Store
- [ ] Soporte iOS

---

## 👨‍💻 Autor

**César León**

[![GitHub](https://img.shields.io/badge/GitHub-CesarLeon551-181717?style=flat-square&logo=github)](https://github.com/CesarLeon551)

---

## 📄 Licencia

MIT License — libre para usar como referencia o portafolio.

---

<p align="center">
  Hecho con ❤️ para apoyar la salud materna
</p>
