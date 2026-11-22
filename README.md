# OBAM · Obrero a la mano — Lanzamiento de la aplicación

**Taller final · ET0126 · Programación de Dispositivos Móviles · I.U. Pascual Bravo (2026)**  
**Estudiante:** Juan Camilo Fonseca Mejía  
**Aplicación:** OBAM — Obrero a la mano (`v1.0.0+1`)  
**Repositorio del código fuente:** [https://github.com/Camilo-fonseca237/et0126-obam](https://github.com/Camilo-fonseca237/et0126-obam)

---

## Propósito y funciones principales

**OBAM (Obrero a la mano)** ayuda a personas que necesitan arreglos pequeños en casa (colgar cuadros, instalar un TV, armar muebles, plomería o electricidad básica) a publicar lo que necesitan con un boceto dibujado a mano alzada, proponer un precio inicial y negociar mediante contraofertas.

Funciones implementadas:
- **Autenticación con Firebase Authentication:** inicio y cierre de sesión con **correo/contraseña** y con **Google** (compatible con FlutLab Web y Android), detección automática de sesión con `authStateChanges()` y manejo de errores ante credenciales inválidas o cancelación.
- **Publicación de trabajos con boceto (`Canvas`):** formulario validado (descripción mínima de 5 letras, categoría obligatoria, oferta mínima de \$ 10.000 COP) y lienzo táctil `300×300` (`CustomPainter`) para dibujar dónde va el trabajo.
- **Negociación por contraoferta:** simulación de **Modo obrero** en pasos de \$ 5.000 COP y decisión del usuario para aceptar o rechazar el trato.
- **Persistencia local:** almacenamiento en JSON con `SharedPreferences` y resumen en tiempo real por estado (*Publicado*, *Contraoferta*, *Aceptado*).
- **Mis obreros de confianza:** formulario validado con `form_field_validator` (celular colombiano `^3[0-9]{9}$`), servicio asíncrono en memoria y regla de negocio contra teléfonos duplicados.

---

## Contenido de este repositorio de entrega

```text
├── README.md                  Documentación general, generación de APK, investigación y pruebas
├── ficha_tienda.md            Ficha simulada para Google Play y política de privacidad propuesta
└── entrega/
    ├── app.apk                APK instalable para Android (49.6 MB, universal)
    └── capturas/              Ícono de la app y capturas reales de las pantallas
```

---

## Cómo ejecutar el código fuente

El código fuente vive en [Camilo-fonseca237/et0126-obam](https://github.com/Camilo-fonseca237/et0126-obam) (limpio sin carpetas de plataforma generadas):

### En FlutLab (Web)
1. Importa el repositorio `https://github.com/Camilo-fonseca237/et0126-obam.git` en FlutLab.
2. Pulsa **Pub get** y selecciona el destino **Web**.
3. Asegúrate de que el dominio de vista previa de FlutLab esté en **Firebase Console → Authentication → Configuración → Dominios autorizados** para el botón de Google.

### En local (Flutter SDK)
```bash
git clone https://github.com/Camilo-fonseca237/et0126-obam.git
cd et0126-obam
flutter create . --project-name obam --org com.camilo --platforms=android,web
flutter pub get
flutter run -d chrome
```
*(Para compilar Android con Google Sign-In en local, coloca el archivo `google-services.json` de tu app Android `com.camilo.obam` en `android/app/google-services.json` y aplica el plugin `com.google.gms.google-services` en Gradle; nunca se suben llaves ni archivos sensibles al repositorio).*

---

## Investigación y generación de la APK

### Requisitos del entorno utilizados
- **Flutter:** `3.47.8` (canal `stable`)
- **Dart:** `3.13.7`
- **Android SDK:** `36.0.0` (con `Android SDK Platform 34` y `minSdk 24`)
- **Java / JDK:** JDK 17 (incluido con Android Studio JBR)
- **Firebase Console:** proyecto `obam-app` con Authentication habilitado (**Correo/contraseña** y **Google**), app Android `com.camilo.obam` y huellas `SHA-1` / `SHA-256` del keystore local de depuración registradas.

### Comandos utilizados para generar la APK
1. Generar el soporte Android localmente con el `applicationId` registrado en Firebase:
   ```bash
   flutter create . --project-name obam --org com.camilo --platforms=android
   ```
2. Configurar `<uses-permission android:name="android.permission.INTERNET"/>`, el nombre `OBAM`, los íconos `ic_launcher.png` a partir de `assets/images/logo_obam.png` y el complemento Gradle `com.google.gms.google-services`.
3. Verificar análisis estático sin errores ni advertencias:
   ```bash
   flutter analyze lib
   ```
   Resultado: `No issues found!`
4. Construir la APK en modo release:
   ```bash
   flutter build apk --release
   ```
   Salida generada: `build/app/outputs/flutter-apk/app-release.apk` (**49.6 MB**, compatible con arquitecturas `android-arm`, `android-arm64` y `android-x64`).
5. Copiar el binario instalable a este repositorio en [`entrega/app.apk`](entrega/app.apk).

### Cómo instalar `entrega/app.apk` en Android
1. Descarga [`entrega/app.apk`](entrega/app.apk) en tu teléfono Android (o transfiérelo por cable USB / Google Drive).
2. Abre el archivo desde el gestor de archivos del celular.
3. Si Android lo solicita, habilita **Instalar aplicaciones desconocidas** para el navegador o gestor de archivos.
4. Pulsa **Instalar** y luego **Abrir** (verás el ícono naranja de **OBAM** en el cajón de aplicaciones).
5. También puede instalarse en un emulador o celular conectado por USB con:
   ```bash
   adb install entrega/app.apk
   ```

---

## Diferencia entre esta APK y un paquete para publicar en Google Play

La APK entregada (`entrega/app.apk`) sirve para **instalar y probar directamente** en celulares Android o emuladores, pero **no equivale** a un paquete listo para publicar en Google Play Store por estas diferencias clave:

| Aspecto | APK entregada (`entrega/app.apk`) | Paquete de producción para Google Play |
| --- | --- | --- |
| **Formato** | **APK universal** (`.apk`): incluye binarios para `arm`, `arm64` y `x64` en un solo archivo (49.6 MB). | **Android App Bundle (`.aab`)**: formato **obligatorio** en Google Play para todas las apps nuevas desde **agosto de 2021**. |
| **Distribución y tamaño** | Todos los usuarios descargan el mismo archivo completo sin importar su celular. | Google Play genera y entrega **APKs divididos (Dynamic Delivery)** optimizados por arquitectura de CPU, densidad de pantalla e idioma, reduciendo el tamaño de descarga (~15 % o más). |
| **Firma digital** | Firmada con llave de **depuración** (`debug.keystore`) para pruebas locales. | Firmada con una **llave de subida (upload key)** privada y gestionada mediante **Play App Signing**, donde Google protege la llave definitiva de firma en la nube. |
| **Requisitos de tienda** | Instalación directa por archivo sin revisión. | Exige cuenta de Google Play Console, ficha completa, política de privacidad en URL pública, formulario de *Seguridad de los datos*, cumplimiento de `targetSdk` vigente y pruebas cerradas/internas. |

Para generar el formato que exige Google Play en Flutter se usa:
```bash
flutter build appbundle --release
```

### Fuentes consultadas
- **Android Developers — About Android App Bundles (`.aab`):** [https://developer.android.com/guide/app-bundle](https://developer.android.com/guide/app-bundle)
- **Android Developers — Sign your app & Play App Signing:** [https://developer.android.com/studio/publish/app-signing](https://developer.android.com/studio/publish/app-signing)
- **Flutter Documentation — Build and release an Android app:** [https://docs.flutter.dev/deployment/android](https://docs.flutter.dev/deployment/android)

---

## Resultados de pruebas

| Prueba | Entorno | Resultado |
| --- | --- | --- |
| Análisis estático (`flutter analyze lib`) | Local (`Flutter 3.47.8`) | Aprobada (`No issues found!`). |
| Compilación release (`flutter build apk --release`) | Local (`Android SDK 36.0.0`) | Aprobada (`app-release.apk`, 49.6 MB). |
| Inicio sin sesión → muestra `LoginScreen` | Web local (`localhost:8080`) | Aprobada. |
| Credenciales inválidas y validación de campos vacíos | Web local (`localhost:8080`) | Aprobada: muestra error sin revelar existencia de cuenta. |
| Inicio y cierre de sesión con Firebase (Google / correo) | Web local (`localhost:8080`) | Aprobada: alterna automáticamente con `authStateChanges()`. |
| Publicar trabajo con boceto, contraoferta y persistencia | FlutLab Web / Local | Aprobada: persiste en `SharedPreferences` tras reiniciar. |
| Validación y duplicados en *Mis obreros de confianza* | FlutLab Web / Local | Aprobada: rechaza celular inválido y duplicado. |
| Instalación y prueba de `entrega/app.apk` en celular Android | Dispositivo físico Android | *(Pendiente de registrar captura y confirmación final tras prueba en celular).* |

---

## Limitaciones conocidas

- La contraoferta del obrero se simula desde la misma pantalla (**Modo obrero**); todavía no hay conexión en tiempo real entre dos usuarios distintos.
- Los trabajos se guardan localmente en el teléfono con `SharedPreferences`, no en la nube por `uid`.
- Los obreros de confianza viven en memoria RAM y se borran al cerrar la aplicación.
- La APK está firmada con llave de depuración solo para fines académicos y de prueba.
- La eliminación de cuenta dentro de la app aún no está implementada (se documenta como paso pendiente en `ficha_tienda.md`).

---

## Declaración de uso de IA

Para el desarrollo del curso y la preparación de este taller final utilicé asistentes de IA (Claude y Gemini / Antigravity) como apoyo para estructurar el proyecto Flutter, integrar Firebase Authentication (Web y Android), depurar la compilación de la APK con Gradle y redactar/organizar la documentación (`README.md` y `ficha_tienda.md`). Probé los flujos, revisé cada archivo y comprendo el funcionamiento completo del código y la configuración para explicarlo y modificarlo durante la exposición.
