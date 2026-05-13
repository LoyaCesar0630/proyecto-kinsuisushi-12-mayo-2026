# 🍣 Kinsui Sushi — Prompt Maestro de Implementación
> **Framework:** Flutter (Dart) · **Backend:** Firebase (Antigravity/Firebase Studio) · **Estado:** Provider · **DB:** Firestore (NoSQL) · **Auth:** Email/Password
> **Target:** iOS · Android · Web

---

## 🧭 Contexto del Proyecto

Kinsui Sushi es una aplicación de restaurante multiplataforma construida con **Flutter + Firebase**. La arquitectura sigue el patrón **Clean Architecture** con separación estricta de capas. Todo el backend se configura desde **Firebase Studio (Antigravity)**. Este documento es una guía paso a paso, completa y comprometida con la implementación real del negocio.

---

## 📁 FASE 0 — Estructura de Carpetas del Proyecto

Antes de escribir una sola línea de código, define la arquitectura de carpetas. Esta estructura es **obligatoria** y no debe modificarse sin razón justificada.

```
kinsui_sushi/
├── android/
├── ios/
├── web/
├── assets/
│   ├── images/          # Imágenes locales (logo, splash, placeholders)
│   ├── icons/           # Íconos SVG propios
│   └── fonts/           # Fuentes tipográficas (.ttf / .otf)
├── lib/
│   ├── main.dart                    # Punto de entrada de la app
│   ├── firebase_options.dart        # Generado por FlutterFire CLI
│   │
│   ├── core/                        # Capa transversal
│   │   ├── constants/
│   │   │   ├── app_colors.dart      # Paleta de colores global
│   │   │   ├── app_text_styles.dart # Estilos tipográficos
│   │   │   ├── app_spacing.dart     # Sistema de espaciado (4, 8, 12, 16...)
│   │   │   └── app_strings.dart     # Textos hardcodeados / i18n
│   │   ├── theme/
│   │   │   └── app_theme.dart       # ThemeData light/dark
│   │   ├── router/
│   │   │   ├── app_router.dart      # Definición de rutas con go_router
│   │   │   └── route_guards.dart    # Middleware: protección de rutas
│   │   └── utils/
│   │       ├── validators.dart      # Funciones de validación reutilizables
│   │       ├── formatters.dart      # Formateo de moneda, fecha, texto
│   │       └── extensions.dart      # Extensions de Dart (String, DateTime...)
│   │
│   ├── data/                        # Capa de datos (fuentes externas)
│   │   ├── models/
│   │   │   ├── user_model.dart
│   │   │   ├── menu_item_model.dart
│   │   │   ├── category_model.dart
│   │   │   ├── order_model.dart
│   │   │   └── cart_item_model.dart
│   │   └── services/
│   │       ├── auth_service.dart        # Firebase Auth (Email/Password)
│   │       ├── firestore_service.dart   # Abstracción de Firestore CRUD
│   │       ├── menu_service.dart        # Operaciones sobre colección `menu`
│   │       ├── order_service.dart       # Operaciones sobre colección `orders`
│   │       └── storage_service.dart     # Firebase Storage (imágenes)
│   │
│   ├── domain/                      # Capa de lógica de negocio
│   │   └── providers/
│   │       ├── auth_provider.dart
│   │       ├── menu_provider.dart
│   │       ├── cart_provider.dart
│   │       ├── order_provider.dart
│   │       └── ui_provider.dart
│   │
│   └── presentation/                # Capa de UI
│       ├── screens/
│       │   ├── splash/
│       │   │   └── splash_screen.dart
│       │   ├── auth/
│       │   │   ├── login_screen.dart
│       │   │   └── register_screen.dart
│       │   ├── home/
│       │   │   └── home_screen.dart
│       │   ├── menu/
│       │   │   ├── menu_screen.dart
│       │   │   └── item_detail_screen.dart
│       │   ├── cart/
│       │   │   └── cart_screen.dart
│       │   ├── orders/
│       │   │   └── order_history_screen.dart
│       │   └── profile/
│       │       └── profile_screen.dart
│       └── widgets/
│           ├── common/
│           │   ├── kinsui_button.dart       # Botón primario reutilizable
│           │   ├── kinsui_input.dart        # Campo de texto estilizado
│           │   ├── kinsui_loader.dart       # Indicador de carga temático
│           │   └── kinsui_snackbar.dart     # Notificaciones de feedback
│           └── cards/
│               ├── menu_item_card.dart
│               └── order_card.dart
├── test/
│   ├── unit/
│   ├── widget/
│   └── integration/
├── pubspec.yaml
├── analysis_options.yaml
└── .env                              # Variables de entorno (NO commitear)
```

---

## 🛠️ FASE 1 — Configuración del Entorno de Desarrollo

### 1.1 Herramientas requeridas
- Instalar **Flutter SDK** (última versión estable). Verificar con `flutter doctor`.
- Instalar **VS Code** con las extensiones: `Flutter`, `Dart`, `Pubspec Assist`, `Error Lens`, `GitLens`.
- Instalar **Android Studio** solo para el SDK y el AVD Manager (no es necesario usarlo como IDE).
- En macOS: instalar **Xcode** para compilar iOS y el simulador.
- Instalar **Node.js** (requerido por Firebase CLI).
- Instalar **Firebase CLI** con `npm install -g firebase-tools`.
- Instalar **FlutterFire CLI** con `dart pub global activate flutterfire_cli`.

### 1.2 Crear el proyecto Flutter
```
flutter create kinsui_sushi --org com.kinsuisushi --platforms android,ios,web
```
- Configurar la estructura de carpetas exacta descrita en FASE 0.
- Inicializar repositorio Git y crear `.gitignore` que excluya: `*.jks`, `google-services.json`, `GoogleService-Info.plist`, `firebase_options.dart`, `.env`.

### 1.3 Configurar calidad de código
En `analysis_options.yaml`:
- Activar reglas de `flutter_lints`.
- Habilitar advertencias para imports no utilizados, variables no declaradas y tipos implícitos.
- Definir regla para evitar `print()` en producción.

---

## 🔥 FASE 2 — Firebase Studio (Antigravity) — Configuración Completa

### 2.1 Crear el proyecto en Firebase Studio
1. Acceder a **Firebase Studio** en `studio.firebase.google.com`.
2. Crear nuevo proyecto con nombre: `kinsui-sushi-prod`.
3. Habilitar **Google Analytics** (recomendado para métricas de negocio).
4. Seleccionar región: `us-central1` (o la más cercana al mercado objetivo).

### 2.2 Habilitar Authentication
1. En la consola → **Authentication** → **Sign-in method**.
2. Activar proveedor: **Email/Password**.
3. Habilitar la opción de **verificación de email** (envío automático al registrarse).
4. Configurar dominio autorizado si se despliega en web.
5. En **Usuarios** → definir un usuario administrador manualmente para pruebas iniciales.

### 2.3 Crear y configurar Firestore
1. En la consola → **Firestore Database** → **Crear base de datos**.
2. Iniciar en **modo producción** (no en modo prueba — las reglas se definirán desde el inicio).
3. Seleccionar la misma región que el proyecto.
4. Crear las colecciones base manualmente con documentos de ejemplo para validar la estructura.

### 2.4 Vincular Firebase con Flutter
```bash
# Autenticarse en Firebase
firebase login

# Dentro de la raíz del proyecto Flutter
flutterfire configure --project=kinsui-sushi-prod
```
- Este comando genera automáticamente `firebase_options.dart` en `/lib/`.
- Verificar que los archivos `google-services.json` (Android) y `GoogleService-Info.plist` (iOS) se han colocado en las rutas correctas.
- NO commitear estos archivos al repositorio.

---

## 🗄️ FASE 3 — Diseño de Base de Datos Firestore (NoSQL)

### 3.1 Modelo de colecciones

#### Colección: `users`
```
users/{userId}
├── uid: String
├── displayName: String
├── email: String
├── photoUrl: String?
├── phone: String?
├── role: String           // "customer" | "admin"
├── createdAt: Timestamp
└── updatedAt: Timestamp
```

#### Colección: `categories`
```
categories/{categoryId}
├── id: String
├── name: String           // "Rolls", "Nigiri", "Bebidas", "Especiales"
├── imageUrl: String
├── order: int             // Para ordenar en la UI
└── isActive: bool
```

#### Colección: `menu`
```
menu/{itemId}
├── id: String
├── name: String
├── description: String
├── categoryId: String     // Referencia a categories/{categoryId}
├── price: double
├── imageUrl: String
├── isAvailable: bool
├── isFeatured: bool
├── tags: List<String>     // ["picante", "sin gluten", "vegano"]
├── calories: int?
├── createdAt: Timestamp
└── updatedAt: Timestamp
```

#### Colección: `orders`
```
orders/{orderId}
├── id: String
├── userId: String         // Referencia a users/{userId}
├── items: List<Map>
│   ├── itemId: String
│   ├── name: String
│   ├── quantity: int
│   ├── unitPrice: double
│   └── subtotal: double
├── totalAmount: double
├── status: String         // "pending" | "confirmed" | "preparing" | "ready" | "delivered" | "cancelled"
├── notes: String?
├── createdAt: Timestamp
└── updatedAt: Timestamp
```

#### Colección: `carts` *(sincronización entre dispositivos)*
```
carts/{userId}
└── items: List<Map>
    ├── itemId: String
    ├── quantity: int
    └── addedAt: Timestamp
```

### 3.2 Índices compuestos requeridos en Firestore
Crear en la consola → Firestore → Índices:
| Colección | Campo 1 | Campo 2 | Campo 3 | Tipo |
|-----------|---------|---------|---------|------|
| `menu` | `categoryId` ASC | `isAvailable` ASC | `name` ASC | Compuesto |
| `menu` | `isFeatured` ASC | `isAvailable` ASC | — | Compuesto |
| `orders` | `userId` ASC | `createdAt` DESC | — | Compuesto |
| `orders` | `status` ASC | `createdAt` DESC | — | Compuesto |

### 3.3 Reglas de seguridad de Firestore
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Función helper: verificar autenticación
    function isAuthenticated() {
      return request.auth != null;
    }

    // Función helper: verificar que es el propio usuario
    function isOwner(userId) {
      return isAuthenticated() && request.auth.uid == userId;
    }

    // Función helper: verificar rol admin
    function isAdmin() {
      return isAuthenticated() &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }

    // USUARIOS: solo el propietario o admin puede leer/escribir
    match /users/{userId} {
      allow read: if isOwner(userId) || isAdmin();
      allow create: if isAuthenticated() && request.auth.uid == userId;
      allow update: if isOwner(userId) || isAdmin();
      allow delete: if isAdmin();
    }

    // CATEGORÍAS: cualquier autenticado puede leer, solo admin escribe
    match /categories/{categoryId} {
      allow read: if isAuthenticated();
      allow write: if isAdmin();
    }

    // MENÚ: cualquier autenticado puede leer, solo admin escribe
    match /menu/{itemId} {
      allow read: if isAuthenticated();
      allow write: if isAdmin();
    }

    // PEDIDOS: usuario ve sus pedidos, admin ve todos
    match /orders/{orderId} {
      allow read: if isAdmin() ||
        (isAuthenticated() && resource.data.userId == request.auth.uid);
      allow create: if isAuthenticated() &&
        request.resource.data.userId == request.auth.uid;
      allow update: if isAdmin();
      allow delete: if false; // Los pedidos nunca se eliminan
    }

    // CARRITO: solo el propietario
    match /carts/{userId} {
      allow read, write: if isOwner(userId);
    }
  }
}
```

---

## 📦 FASE 4 — Dependencias Flutter (`pubspec.yaml`)

Añadir todas las dependencias en `pubspec.yaml`. Consultar versiones actuales en `pub.dev`.

### Dependencias de producción
| Paquete | Versión | Propósito |
|---------|---------|-----------|
| `firebase_core` | latest | Inicialización del SDK Firebase |
| `firebase_auth` | latest | Autenticación Email/Password |
| `cloud_firestore` | latest | Base de datos Firestore + CRUD |
| `firebase_storage` | latest | Almacenamiento de imágenes |
| `provider` | latest | Gestión de estado e inyección de dependencias |
| `go_router` | latest | Navegación declarativa con guards |
| `cached_network_image` | latest | Carga y caché de imágenes remotas |
| `flutter_svg` | latest | Íconos y gráficos vectoriales |
| `intl` | latest | Formateo de moneda, fechas y localización |
| `shared_preferences` | latest | Persistencia local ligera (tema, preferencias) |
| `flutter_form_builder` | latest | Formularios avanzados con validación |
| `form_builder_validators` | latest | Validadores para flutter_form_builder |
| `shimmer` | latest | Efecto skeleton loading en listas |
| `badges` | latest | Contador en ícono del carrito |
| `flutter_animate` | latest | Animaciones declarativas con API fluida |
| `gap` | latest | Espaciado semántico entre widgets |
| `image_picker` | latest | Selección de imágenes para perfil |

### Dependencias de desarrollo
| Paquete | Propósito |
|---------|-----------|
| `flutter_lints` | Reglas de análisis estático |
| `mockito` | Mocking en pruebas unitarias |
| `fake_cloud_firestore` | Simulación de Firestore en tests |
| `firebase_auth_mocks` | Simulación de Firebase Auth en tests |

### Assets en `pubspec.yaml`
```yaml
flutter:
  assets:
    - assets/images/
    - assets/icons/
  fonts:
    - family: NotoSerif
      fonts:
        - asset: assets/fonts/NotoSerif-Regular.ttf
        - asset: assets/fonts/NotoSerif-Bold.ttf
          weight: 700
    - family: Nunito
      fonts:
        - asset: assets/fonts/Nunito-Regular.ttf
        - asset: assets/fonts/Nunito-SemiBold.ttf
          weight: 600
        - asset: assets/fonts/Nunito-Bold.ttf
          weight: 700
```

---

## 🔐 FASE 5 — Autenticación Firebase (Email/Password)

### 5.1 Flujo de autenticación completo

```
App Inicio
    │
    ▼
SplashScreen (2s)
    │
    ├─── FirebaseAuth.currentUser != null ──▶ HomeScreen
    │
    └─── currentUser == null ──────────────▶ LoginScreen
                                                  │
                                        ┌─────────┴──────────┐
                                        ▼                    ▼
                                  RegisterScreen     ForgotPasswordScreen
                                        │                    │
                                        ▼                    ▼
                                  HomeScreen         (Email de recuperación)
```

### 5.2 Validaciones obligatorias antes de llamar a Firebase
**Email:**
- No vacío
- Formato válido con RegEx (`^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$`)

**Contraseña (registro):**
- Mínimo 8 caracteres
- Al menos 1 mayúscula, 1 número y 1 carácter especial

**Nombre (registro):**
- No vacío
- Mínimo 2 caracteres, máximo 50

### 5.3 Manejo de errores de Firebase Auth
| Código de error Firebase | Mensaje visible al usuario |
|--------------------------|---------------------------|
| `user-not-found` | "No existe una cuenta con este correo." |
| `wrong-password` | "Contraseña incorrecta. Intenta de nuevo." |
| `email-already-in-use` | "Este correo ya está registrado." |
| `weak-password` | "La contraseña es muy débil." |
| `network-request-failed` | "Sin conexión. Verifica tu red." |
| `too-many-requests` | "Demasiados intentos. Espera unos minutos." |
| `user-disabled` | "Esta cuenta ha sido deshabilitada." |

### 5.4 Flujo post-registro
1. Crear usuario en Firebase Auth.
2. Enviar email de verificación automáticamente.
3. Crear documento en `users/{uid}` en Firestore con datos iniciales y `role: "customer"`.
4. Redirigir al home con sesión iniciada.
5. Mostrar banner no intrusivo: *"Verifica tu correo para acceder a todas las funciones."*

---

## 🔄 FASE 6 — CRUD Firestore Completo

### 6.1 Operaciones por colección

#### `menu` — Solo lectura para usuarios, escritura admin
| Operación | Descripción |
|-----------|-------------|
| **Read All** | Stream de todos los ítems activos (`isAvailable == true`) |
| **Read by Category** | Stream filtrado por `categoryId` |
| **Read Featured** | Query donde `isFeatured == true` |
| **Search** | Query por nombre (client-side filtering para Firestore) |

#### `orders` — Crear y leer para usuarios
| Operación | Descripción |
|-----------|-------------|
| **Create** | Nuevo pedido desde el carrito, con `status: "pending"` |
| **Read Mine** | Stream de pedidos del usuario actual ordenados por fecha |
| **Read All (admin)** | Stream de todos los pedidos activos |
| **Update Status (admin)** | Cambiar `status` del pedido |

#### `carts` — CRUD completo para usuario propietario
| Operación | Descripción |
|-----------|-------------|
| **Read** | Cargar el carrito al iniciar sesión |
| **Upsert Item** | Añadir ítem o incrementar cantidad si ya existe |
| **Update Quantity** | Cambiar cantidad de un ítem |
| **Delete Item** | Eliminar un ítem del carrito |
| **Clear Cart** | Vaciar carrito completo tras confirmar pedido |

#### `users` — CRUD del propio perfil
| Operación | Descripción |
|-----------|-------------|
| **Read** | Cargar datos del perfil al hacer login |
| **Update** | Actualizar `displayName`, `phone`, `photoUrl` |

### 6.2 Patrón de escucha en tiempo real
Para listas de menú y estado de pedidos, usar **streams de Firestore** conectados a los providers. Esto permite actualizaciones en tiempo real sin polling manual.

---

## 🎨 FASE 7 — Sistema de Diseño UI/UX (Flutter Dark Theme)

### 7.1 Paleta de colores (`app_colors.dart`)
| Variable | Valor Hex | Uso |
|----------|-----------|-----|
| `primaryBackground` | `#0D0D0D` | Fondo principal (casi negro) |
| `secondaryBackground` | `#1A1A1A` | Cards, bottom sheets |
| `surfaceColor` | `#252525` | Inputs, chips |
| `accentRed` | `#C0392B` | CTA principal, badges, precios |
| `accentGold` | `#D4AF37` | Elementos premium, estrellas |
| `textPrimary` | `#F5F5F0` | Texto principal (crema suave) |
| `textSecondary` | `#9E9E9E` | Subtítulos, labels |
| `textDisabled` | `#555555` | Texto deshabilitado |
| `divider` | `#2E2E2E` | Separadores, bordes |
| `success` | `#27AE60` | Confirmaciones, estado "listo" |
| `warning` | `#F39C12` | Estado "preparando" |
| `error` | `#E74C3C` | Errores, alertas |

### 7.2 Tipografía (`app_text_styles.dart`)
| Estilo | Fuente | Tamaño | Peso | Uso |
|--------|--------|--------|------|-----|
| `displayLarge` | NotoSerif | 32sp | Bold | Títulos de pantalla |
| `displayMedium` | NotoSerif | 24sp | Bold | Nombre de producto |
| `headline` | Nunito | 20sp | SemiBold | Sección headers |
| `body` | Nunito | 16sp | Regular | Descripción, párrafos |
| `bodySmall` | Nunito | 14sp | Regular | Labels, metadata |
| `caption` | Nunito | 12sp | Regular | Precios secundarios, fechas |
| `button` | Nunito | 16sp | Bold | Texto de botones |
| `price` | NotoSerif | 22sp | Bold | Precio destacado (color `accentRed`) |

### 7.3 Sistema de espaciado (`app_spacing.dart`)
Usar múltiplos de 4:
`xs=4`, `sm=8`, `md=12`, `lg=16`, `xl=24`, `xxl=32`, `xxxl=48`

### 7.4 Componentes reutilizables obligatorios

**`KinsuiButton`**
- Variantes: `primary` (rojo sólido), `secondary` (borde rojo), `ghost` (transparente), `danger`
- Estado `loading` con `CircularProgressIndicator` integrado
- Estado `disabled` con opacidad reducida

**`KinsuiInput`**
- Fondo `surfaceColor`, borde `divider`, foco en `accentRed`
- Ícono opcional izquierdo/derecho
- Mensaje de error inline debajo del campo
- Variante `password` con toggle de visibilidad

**`MenuItemCard`**
- Imagen con `CachedNetworkImage` + shimmer mientras carga
- Badge "Destacado" en `accentGold` si `isFeatured`
- Badge "Agotado" si `!isAvailable`
- Animación sutil al presionar (`ScaleTransition`)

**`KinsuiLoader`**
- Basado en el logo o un spinner temático
- Versión full-screen para bloquear interacción
- Versión inline para listas

---

## 🧭 FASE 8 — Navegación con `go_router`

### 8.1 Definición de rutas (`app_router.dart`)

| Ruta | Nombre | Pantalla | Protegida |
|------|--------|----------|-----------|
| `/` | `splash` | SplashScreen | No |
| `/login` | `login` | LoginScreen | No |
| `/register` | `register` | RegisterScreen | No |
| `/home` | `home` | HomeScreen | ✅ Sí |
| `/menu` | `menu` | MenuScreen | ✅ Sí |
| `/menu/:itemId` | `item-detail` | ItemDetailScreen | ✅ Sí |
| `/cart` | `cart` | CartScreen | ✅ Sí |
| `/orders` | `orders` | OrderHistoryScreen | ✅ Sí |
| `/profile` | `profile` | ProfileScreen | ✅ Sí |

### 8.2 Guard de autenticación
En `route_guards.dart`:
- `GoRouter.redirect` verifica si hay sesión activa antes de renderizar cualquier ruta protegida.
- Si no hay sesión → redirige a `/login`.
- Si hay sesión y accede a `/login` o `/register` → redirige a `/home`.
- Mostrar `SplashScreen` mientras `AuthProvider` determina el estado inicial.

### 8.3 Estructura de navegación principal (Post-login)
- **Bottom Navigation Bar** con 4 tabs: Inicio, Menú, Carrito (con badge de cantidad), Perfil.
- **Modal sheets** para: detalles de producto, confirmación de pedido, edición de perfil.
- **Push navigation** para: historial de pedidos, detalle de pedido.

---

## 📊 FASE 9 — Providers (Gestión de Estado)

### `AuthProvider`
- Estado: `user`, `isLoading`, `errorMessage`, `isEmailVerified`
- Métodos: `login()`, `register()`, `logout()`, `sendPasswordReset()`, `sendEmailVerification()`
- Escucha: stream `authStateChanges()` de Firebase Auth

### `MenuProvider`
- Estado: `categories`, `menuItems`, `featuredItems`, `selectedCategory`, `isLoading`
- Métodos: `loadCategories()`, `loadMenuByCategory()`, `loadFeatured()`, `searchItems(query)`
- Fuente: streams de Firestore

### `CartProvider`
- Estado: `cartItems`, `totalAmount`, `itemCount`
- Métodos: `addItem()`, `removeItem()`, `updateQuantity()`, `clearCart()`, `syncWithFirestore()`
- Persistencia: Firestore + estado local en memoria para respuesta inmediata (optimistic UI)

### `OrderProvider`
- Estado: `currentOrders`, `orderHistory`, `isLoading`
- Métodos: `placeOrder()`, `loadOrderHistory()`, `cancelOrder()`
- Escucha: stream en tiempo real del estado del pedido activo

### `UIProvider`
- Estado: `isDarkMode`, `isGlobalLoading`, `snackbarMessage`
- Métodos: `toggleTheme()`, `showSnackbar()`, `setLoading()`

---

## ✅ FASE 10 — Pruebas

### 10.1 Pruebas unitarias (`test/unit/`)
- `AuthProvider`: login válido, login inválido, logout, errores de red
- `CartProvider`: añadir ítem, eliminar, actualizar cantidad, total correcto
- `Validators`: emails válidos/inválidos, contraseñas fuertes/débiles
- `MenuItemModel`: serialización/deserialización JSON

### 10.2 Pruebas de widgets (`test/widget/`)
- `KinsuiButton`: estados normal, loading, disabled
- `KinsuiInput`: estado error, texto correcto
- `MenuItemCard`: renderiza imagen, nombre y precio correctamente

### 10.3 Pruebas de integración (`test/integration/`)
- Flujo completo de login → ver menú → añadir al carrito → confirmar pedido
- Flujo de registro → verificación → acceso a app

### 10.4 Herramientas de testing
- `fake_cloud_firestore` para simular Firestore sin conexión real
- `firebase_auth_mocks` para simular sesiones en tests
- `mockito` para mocking de servicios

---

## 🚀 FASE 11 — Despliegue y Producción

### 11.1 Antes de desplegar
- [ ] Cambiar reglas de Firestore de prueba a producción
- [ ] Revisar y auditar reglas de seguridad Firestore
- [ ] Revocar acceso público a Firebase Storage
- [ ] Habilitar App Check en Firebase (prevención de abuso)
- [ ] Revisar que `.env` y archivos de configuración NO están en el repo
- [ ] Desactivar logs de debug en producción

### 11.2 Android
- Generar `keystore.jks` con `keytool`
- Configurar `key.properties` (excluir del repo con `.gitignore`)
- Actualizar `build.gradle` con `signingConfigs`
- Generar APK/AAB: `flutter build appbundle --release`
- Subir a Google Play Console

### 11.3 iOS
- Configurar certificados y provisioning profiles en Apple Developer
- Actualizar `Info.plist` con permisos (cámara, galería si se usa)
- Build: `flutter build ipa --release`
- Subir con Xcode o Transporter a App Store Connect

### 11.4 Web
- Build: `flutter build web --release`
- Desplegar con **Firebase Hosting**: `firebase deploy --only hosting`
- Configurar dominio personalizado en la consola de Firebase

---

## 🔄 Orden de Implementación — MVP

Seguir este orden estricto para tener un MVP funcional lo antes posible:

```
1. ✅ Estructura de carpetas + configuración de proyecto
2. ✅ Firebase Studio configurado (Auth + Firestore + reglas)
3. ✅ Dependencias instaladas y pubspec.yaml listo
4. ✅ Sistema de diseño: colores, tipografía, tema oscuro
5. ✅ AuthProvider + AuthService
6. ✅ SplashScreen + LoginScreen + RegisterScreen
7. ✅ go_router con guards funcionales
8. ✅ HomeScreen + MenuProvider + MenuService
9. ✅ MenuScreen con categorías y lista de ítems
10. ✅ ItemDetailScreen
11. ✅ CartProvider + CartScreen
12. ✅ OrderProvider + placeOrder()
13. ✅ OrderHistoryScreen
14. ✅ ProfileScreen
15. ✅ Pruebas unitarias de providers críticos
16. ✅ Preparación para despliegue
```

---

## 📋 Checklist Final de Revisión

### Seguridad
- [ ] Reglas Firestore en modo producción
- [ ] Archivos de configuración fuera del repo
- [ ] App Check habilitado
- [ ] Validación de datos en cliente y en reglas Firestore

### Rendimiento
- [ ] Imágenes cacheadas con `cached_network_image`
- [ ] Listeners de Firestore cancelados en `dispose()`
- [ ] Sin reconstrucciones innecesarias de widgets
- [ ] Paginación en listas largas

### UX
- [ ] Estados de carga en todas las operaciones async
- [ ] Mensajes de error claros y accionables
- [ ] Feedback visual en todas las acciones del usuario
- [ ] Funciona en modo offline básico (lectura de caché Firestore)

### Código
- [ ] Sin `print()` en producción
- [ ] Sin TODOs pendientes críticos
- [ ] Cobertura de pruebas en flujos críticos
- [ ] Documentación inline en servicios y providers

---

*Documento generado para el proyecto Kinsui Sushi. Framework: Flutter (Dart). Backend: Firebase Studio. Versión del documento: 2.0*
