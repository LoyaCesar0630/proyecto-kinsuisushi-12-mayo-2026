# 🍣 Plan de Implementación: Kinsui Sushi

## 📋 Visión General del Proyecto
Aplicación multiplataforma (iOS/Android/Web) para un restaurante de sushi. El desarrollo seguirá un enfoque iterativo, priorizando la arquitectura limpia, la seguridad de los datos y una experiencia de usuario fluida. Este documento describe el procedimiento paso a paso **sin incluir código**, centrado en la planificación, configuración y flujo de trabajo.

---

## 🛠️ Fase 1: Configuración del Entorno de Desarrollo
1. **Instalación de herramientas base**
   - Instalar Flutter SDK y Dart SDK en la máquina host.
   - Instalar VS Code con las extensiones oficiales: `Flutter`, `Dart`, `Pubspec Assist`, `Error Lens`.
   - Configurar emuladores Android (Android Studio SDK/AVD) y/o simuladores iOS (Xcode para macOS).
2. **Creación del proyecto**
   - Inicializar el proyecto Flutter con nombre `kinsui_sushi`.
   - Configurar estructura de carpetas basada en características (`features/`) o capas (`presentation/`, `domain/`, `data/`).
   - Inicializar repositorio Git con `.gitignore` específico para Flutter y archivos sensibles.
3. **Configuración de calidad de código**
   - Añadir reglas de `analysis_options.yaml` para linting estricto.
   - Configurar formateador automático y validación de imports no utilizados.

---

## 🔥 Fase 2: Configuración y Vinculación con Firebase
1. **Consola de Firebase**
   - Crear un nuevo proyecto Firebase con nombre `kinsui-sushi`.
   - Registrar la aplicación Android e iOS en la consola.
   - Descargar los archivos de configuración (`google-services.json` y `GoogleService-Info.plist`) y ubicarlos en las rutas nativas correspondientes.
2. **Habilitación de servicios**
   - Activar **Authentication** y habilitar el proveedor `Email/Password`.
   - Crear base de datos **Firestore** en modo prueba inicial (se ajustarán reglas más adelante).
   - Configurar almacenamiento de logs y analytics (opcional pero recomendado).
3. **Vinculación con Flutter**
   - Instalar Firebase CLI y FlutterFire CLI.
   - Ejecutar el flujo de inicialización de Firebase para generar los archivos de configuración nativos y el archivo de opciones de Flutter.
   - Verificar la conexión inicial desde la consola de VS Code antes de continuar.

---

## 🎨 Fase 3: Diseño UI/UX y Arquitectura de la Aplicación
1. **Definición del sistema de diseño**
   - Paleta de colores: tonos oscuros elegantes, acentos rojos/negros, fondos neutros para resaltar la fotografía de sushi.
   - Tipografía: una fuente sans-serif moderna para cuerpo y una serif/japonesa estilizada para títulos.
   - Espaciado, radios de borde y sombras coherentes para mantener jerarquía visual.
2. **Wireframing y flujo de pantallas**
   - Pantallas clave: Login/Registro, Inicio (menú destacado), Detalle de producto, Carrito, Perfil, Historial de pedidos.
   - Definir estados: carga, vacío, error, éxito.
   - Establecer navegación: barra inferior para secciones principales, navegación modal para autenticación y detalles.
3. **Arquitectura de la app**
   - Separación clara entre presentación (widgets), lógica de negocio (providers/models) y capa de datos (servicios Firebase).
   - Uso de navegación con rutas nombradas y guards de protección para pantallas privadas.

---

## 📦 Fase 4: Gestión de Estado con Provider
1. **Estructura de providers**
   - `AuthProvider`: manejo de sesión, estado de login/logout, datos del usuario.
   - `FirestoreProvider`: operaciones CRUD, escucha en tiempo real, manejo de errores de red.
   - `UIProvider`: control de tema, estado de carga global, snackbars/notificaciones.
   - `CartProvider`: gestión local del carrito antes de sincronización con backend (si aplica).
2. **Modelos de datos**
   - Definir clases planas para `User`, `SushiItem`, `Category`, `Order`, `CartItem`.
   - Implementar métodos de serialización/deserialización para compatibilidad con JSON/Firestore.
3. **Flujo de datos**
   - UI escucha cambios vía `Consumer`/`Selector`.
   - Providers notifica a la UI solo cuando cambian propiedades relevantes.
   - Evitar reconstrucciones innecesarias mediante `select` o `Provider.of` con `listen: false` cuando corresponda.

---

## 🔐 Fase 5: Implementación de Autenticación (Email/Password)
1. **Pantallas de acceso**
   - Diseñar formulario de inicio de sesión y registro con validación en tiempo real.
   - Incluir opciones de "Olvidé mi contraseña" y reenvío de verificación.
2. **Lógica de autenticación**
   - Validar formato de email y fortaleza de contraseña antes de llamar a Firebase.
   - Manejar excepciones típicas: usuario no encontrado, contraseña incorrecta, email ya en uso, errores de red.
   - Persistir sesión de forma segura (Firebase gestiona tokens internamente).
3. **Protección de rutas**
   - Implementar middleware de navegación que redirija a login si no hay sesión activa.
   - Mostrar pantalla de carga inicial mientras se verifica el estado de autenticación.

---

## 🗄️ Fase 6: Estructura de Firestore y Flujo de Datos
1. **Diseño de colecciones**
   - `users`: perfil, preferencias, historial básico.
   - `menu`: categorías, items, precios, disponibilidad, imágenes.
   - `orders`: pedidos activos, estado, totales, referencia a usuario.
   - `carts` (opcional): sincronización entre dispositivos si se requiere persistencia en nube.
2. **Reglas de seguridad**
   - Restringir lectura/escritura según autenticación y roles.
   - Validar formatos de datos antes de permitir escrituras.
   - Aplicar principio de mínimo privilegio.
3. **Optimización de consultas**
   - Crear índices compuestos para filtros frecuentes (categoría + disponibilidad + precio).
   - Usar paginación o límites en listas largas.
   - Evitar lecturas innecesarias con suscripciones selectivas.

---

## 📱 Fase 7: Desarrollo de Pantallas y Navegación
1. **Implementación progresiva**
   - Comenzar por pantallas estáticas y mock data.
   - Reemplazar progresivamente por datos reales desde Firestore.
   - Integrar providers en cada pantalla según dependencia.
2. **Manejo de assets**
   - Organizar imágenes, iconos y fuentes en carpetas dedicadas.
   - Configurar `pubspec.yaml` para reconocimiento automático de assets.
   - Implementar carga diferida y caché de imágenes para rendimiento.
3. **Navegación y experiencia**
   - Transiciones suaves entre pantallas.
   - Feedback táctil y visual en botones y acciones.
   - Gestión de gestos de retroceso y prevención de pérdida de datos no guardados.

---

## ✅ Fase 8: Pruebas, Optimización y Preparación para Despliegue
1. **Pruebas**
   - Unitarias para providers y modelos.
   - Widget tests para componentes reutilizables (botones, tarjetas, formularios).
   - Integración para flujos críticos: login, carga de menú, añadir al carrito.
2. **Optimización**
   - Medir rendimiento con DevTools (CPU, memoria, frames).
   - Optimizar consultas Firestore y reducir reconstrucciones de widgets.
   - Implementar manejo offline básico si aplica.
3. **Despliegue**
   - Generar iconos adaptativos y splash screen.
   - Configurar firma de aplicaciones (Android keystore, iOS certificates).
   - Preparar metadatos para tiendas (descripción, capturas, políticas de privacidad).
   - Revisar y endurecer reglas de Firebase antes de producción.

---

## 📦 Dependencias Requeridas (`pubspec.yaml`)
| Paquete | Propósito | Categoría |
|--------|-----------|-----------|
| `firebase_core` | Inicialización del SDK de Firebase | Backend |
| `firebase_auth` | Autenticación de usuarios | Backend |
| `cloud_firestore` | Base de datos Firestore | Backend |
| `provider` | Gestión de estado y inyección de dependencias | Arquitectura |
| `cached_network_image` | Carga y caché de imágenes de red | UI/Performance |
| `flutter_svg` | Soporte para iconos/activos vectoriales | UI |
| `intl` | Formateo de fechas, moneda y localización | Utilidades |
| `form_field_validator` o `flutter_form_builder` | Validación de formularios | UI/Lógica |
| `go_router` (opcional) | Navegación declarativa y guards | Navegación |
| `shared_preferences` | Almacenamiento ligero local (preferencias UI) | Persistencia |
| `flutter_lints` | Reglas de análisis y linting | Calidad |

> ⚠️ *Nota:* Las versiones no se incluyen intencionalmente. Se recomienda consultar `pub.dev` para usar siempre la última versión estable compatible con la versión actual de Flutter.

---

## 🎨 Directrices de UI/UX para Kinsui Sushi
- **Identidad visual:** Minimalismo japonés, alto contraste, espacios en blanco estratégicos, fotografía de alta calidad como protagonista.
- **Accesibilidad:** Relación de contraste ≥ 4.5:1, textos escalables, soporte para VoiceOver/TalkBack, indicadores táctiles claros.
- **Adaptabilidad:** Diseño responsive que se ajuste a móviles, tablets y web sin duplicar lógica.
- **Microinteracciones:** Feedback sutil al pulsar botones, animaciones de carga coherentes con la temática, transiciones suaves entre estados.
- **Consistencia:** Uso de un único sistema de componentes reutilizables para botones, inputs, tarjetas y modales.

---

## 🔄 Siguientes Pasos
1. Validar este plan con el equipo o stakeholders.
2. Priorizar fases para un MVP (Autenticación → Menú → Carrito básico → Perfil).
3. Configurar tablero de tareas (Kanban/Jira/GitHub Projects) basado en este documento.
4. Una vez aprobado, solicitar el esqueleto de código por fases o por feature.

¿Deseas que profundice en algún punto específico (estructura de carpetas, reglas de Firestore, flujo de validación, etc.) antes de pasar a la implementación con código?
