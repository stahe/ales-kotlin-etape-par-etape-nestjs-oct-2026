# Introducción paso a paso a la programación móvil con Kotlin

📖 **Lea el tutorial: [https://stahe.github.io/ales-kotlin-etape-par-etape-nestjs-oct-2026/](https://stahe.github.io/ales-kotlin-etape-par-etape-nestjs-oct-2026/)**

Este curso enseña a escribir una aplicación **móvil Android nativa** con [Kotlin](https://kotlinlang.org) 2.2 y [Jetpack Compose](https://developer.android.com/compose), la biblioteca declarativa de interfaz de usuario de Android: una aplicación cuyas pantallas se renderizan **en el teléfono** a partir de datos JSON provenientes de un servidor.

Sigue la progresión del curso [Introducción paso a paso al framework móvil [Flutter]](https://stahe.github.io/ales-flutter-etape-par-etape-sept-2026/) (que a su vez se inspira en los cursos de [React](https://stahe.github.io/ales-react-etape-par-etape-sept-2026/), [Vue.js](https://stahe.github.io/ales-vuejs-etape-par-etape-sept-2026/) y [Angular](https://stahe.github.io/ales-angular-etape-par-etape-sept-2026/)): la misma estructura, los mismos 25 ejemplos, el mismo servidor, el mismo caso de estudio, escritos al estilo de Android.

| Curso de Flutter | Curso de Kotlin |
|---|---|
| Dart, widgets, un método `build()` | Kotlin, funciones `@Composable` |
| `setState`, `StatefulWidget` | `remember`, `rememberSaveable`, `mutableStateOf` |
| el motor de Flutter renderiza cada píxel | Compose, el toolkit propio de Android (Material 3) |
| `initState`, `dispose` | efectos: `LaunchedEffect`, `DisposableEffect` |
| `InheritedWidget`, provider | `CompositionLocal`, `ViewModel`, `StateFlow` |
| go_router | Compose Navigation (rutas `@Serializable`, enlaces profundos) |
| `Future`, `Stream` | corrutinas, `Flow` |
| diccionarios JSON, `intl` | `strings.xml`, el idioma de la aplicación (`setApplicationLocales`), y los mismos diccionarios JSON |
| el paquete `http` | OkHttp y su `CookieJar` para la cookie del token |
| `shared_preferences` | DataStore, `SharedPreferences` |
| `flutter_test` | pruebas instrumentadas de Compose (en el emulador) |

El servidor, en cambio, permanece sin cambios: es el servidor JSON de la aplicación **RdvMedecins** que ya utilizaron los clientes React, Vue.js, Angular y Flutter.

## El enfoque: muchos ejemplos breves y luego un caso de estudio

El curso se estructura en torno a **25 ejemplos breves**, cada uno centrado en un único concepto. Forman un único proyecto de Android Studio (una actividad por ejemplo), al que se accede desde un menú.

| Capítulo | Contenido | Ejemplos |
|---|---|---|
| Primeros pasos | un proyecto Android (Gradle, manifiesto, actividad), funciones `@Composable`, estado y recomposición, eventos (gestos, desplazamiento, foco), todos los tipos de campo, un reducer (`sealed interface`, `when`), validación, campos capaces de validarse a sí mismos, formateo (`java.time`, `NumberFormat`, extensiones) | 01–09 |
| Componentes | parámetros, funciones como parámetros, composición (ubicaciones, componentes genéricos), ciclo de vida (efectos), `CompositionLocal`, comportamientos reutilizables y animaciones, cuadro de diálogo de confirmación (función suspendida) y `Snackbar` | 10–16 |
| Navegación | Compose Navigation: rutas tipadas, parámetros, enlaces profundos, barra de pestañas, redirección, guardias | 17–18 |
| Operaciones asíncronas y estado compartido | corrutinas, `Flow`, antirrebote, respuestas obsoletas (`mapLatest`), `produceState`, `ViewModel`, `StateFlow`, DataStore, tema oscuro, `SaveableStateHolder` | 19–20 |
| Internacionalización | recursos `strings.xml`, parámetros, plurales, fechas, importes, idioma de la aplicación, calendario traducido | 21 |
| El servidor: una caja negra | puesta en marcha del servidor JSON, su API, 48 ejemplos con `curl`, qué cambia para un cliente móvil | – |
| Comunicación con el servidor | OkHttp, la dirección del servidor (emulador, teléfono, `adb reverse`), la cookie del token en el teléfono, `kotlinx.serialization`, una capa de acceso a la API, un `ViewModel`, errores del servidor asociados a los campos | 22–25 |

Cada ejemplo se presenta con su código completo y comentado, y con capturas de pantalla de su ejecución.

## El servidor: una caja negra

El servidor es el servidor NestJS de las lecciones anteriores, cuyos controladores devuelven **JSON**. Esta lección lo trata como una **caja negra**: se pone en marcha, se estudia su API y se consulta con `curl`, pero no es necesario leer su código (que se proporciona y está comentado para los curiosos).

- Todos los errores tienen el mismo formato: `{ "statusCode": 409, "key": "ERRORS.LOGIN_TAKEN", "params": {...}, "fields": {...} }`: **claves** de traducción, nunca texto plano;
- Autenticación mediante un token JWT en una cookie `httpOnly`: en el teléfono, es el cliente HTTP (OkHttp y su `CookieJar`) quien lo almacena y lo reenvía;
- un «modo de prueba» para el CAPTCHA, de modo que se pueda consultar la API con `curl` (y generar capturas de pantalla mediante pruebas automatizadas).

## El caso de estudio: el cliente Android de RdvMedecins

Una aplicación completa para **reservar citas en un consultorio médico**, con **todos** los archivos listados y comentados.

- **Android moderno**: Kotlin 2.2, Jetpack Compose y Material 3; una única actividad; Compose Navigation; `ViewModel`; corrutinas; OkHttp; kotlinx.serialization; una arquitectura en capas (datos, estado, interfaz de usuario).
- **Tres roles**: `ADMIN` (gestiona médicos y clientes), `DOCTOR` (agenda y cancela citas), `USER` (el paciente: reserva citas para sí mismo y gestiona su cuenta).
- **Confidencialidad**: un paciente nunca recibe los nombres de otros pacientes; el servidor no los envía.
- **Todo el estado de una pantalla está contenido en su ruta**: `Ecrans.Agenda(idMedecin = 1, jour = "2026-10-05")`; la ventana de reserva es un destino de tipo «diálogo», que el botón Atrás cierra.
- **Validación del lado del servidor**: los formularios muestran bajo cada campo los errores devueltos por la API; bloqueo optimista, nombres duplicados, nombre de usuario ya en uso, etc.
- **Sesión**: persiste entre sesiones (la cookie se almacena en el dispositivo), se restablece al iniciar (`GET /api/auth/moi`), la expiración se gestiona en un único lugar (respuesta 401) y, tras volver a conectarse, se regresa a la pantalla solicitada.
- **Adaptado a móviles**: menú lateral desplegable (o `NavigationRail` en pantallas grandes y en modo horizontal), listas en lugar de tablas, botón flotante, teclado que no oculta los campos y funcionalidad que sobrevive a la rotación y a los cambios de idioma.
- **Francés / inglés**, con los diccionarios JSON de los demás clientes, incluidos el calendario y los textos propios de Android.
- **Capturas de pantalla automáticas**: una prueba instrumentada recorre la aplicación como lo haría un usuario.
- **Despliegue**: la versión de producción firmada (APK, Android App Bundle).

## Contenido del repositorio

```
exemples_kotlin/           los 25 ejemplos breves (un único proyecto de Android Studio)
rdvmedecins-nestjs-json/   el servidor JSON de RdvMedecins (la «caja negra»)
rdvmedecins_kotlin/        el cliente Android del caso de estudio
tools/                    los scripts de Windows que generan las capturas de pantalla
```

Cada proyecto puede abrirse en Android Studio (`File / Open`) o compilarse desde la línea de comandos: `gradlew installDebug`. Las aplicaciones se conectan al servidor en `http://localhost:8080`, después de ejecutar `adb reverse tcp:8080 tcp:8080` (o con otra dirección: `gradlew installDebug -Pserver=http://10.0.2.2:8080`).

## Tecnologías

Kotlin 2.2 · Jetpack Compose (BOM 2026.02) · Material 3 · Navigation Compose 2.9 · Lifecycle / ViewModel 2.9 · DataStore 1.1 · AppCompat 1.7 · OkHttp 4.12 · kotlinx.serialization 1.9 · AndroidSVG 1.4 · Compose UI Test · AGP 9.4 · Gradle 9.6 · Android 8.0 (API 26) a Android 17 (API 37) · del lado del servidor: NestJS 10 · TypeORM · MySQL 8 / MariaDB · Passport JWT · svg-captcha

## Requisitos previos

- Fundamentos del lenguaje Kotlin: el curso [Aprender el lenguaje [Kotlin 2.4] mediante ejemplos](https://stahe.github.io/kotlin-oct-2026/) es una buena preparación, pero los conceptos utilizados se explican a lo largo de los ejemplos. Fundamentos del protocolo HTTP.
- Android Studio (que proporciona el JDK, Gradle, el Android SDK y el emulador) o un teléfono Android; Node.js y un servidor MySQL (p. ej., Laragon en Windows) para el servidor JSON. Las instrucciones de instalación se proporcionan en el curso.

## Autor

Este curso, sus ejemplos y el cliente Android fueron escritos por **Claude**, la IA de [Anthropic](https://www.anthropic.com) (octubre de 2026), a petición de **Serge Tahé**.
