# GioTube Fusion

App nueva para Android TV y celulares que junta en una sola aplicación:

- **Videos**: GioTube (YouTube sin anuncios, sin cuenta, con NewPipe).
- **Música**: MusicFlow (biblioteca, reproductor con ecualizador y descargas).

Es un proyecto independiente. **No modifica ni reemplaza** a GioTube ni a MusicFlow:
tiene su propio paquete (`app.giotube.fusion`), su propia versión (1.0.0) y se instala
junto a las otras dos.

Los proyectos originales no se tocaron:

- GioTube: `Documents/TubeLibre-Rediseno/android`
- MusicFlow: `Downloads/Youtube/MusicFlow`

## Instalar

- **En el televisor, con Downloader** (de AFTVnews): escribe el código **1499497**.
- **Descarga directa (sin redirecciones, más confiable):**
  <https://c28309208.github.io/giotube-fusion/GioTube-Fusion.apk>
- **Enlace corto para escribir en Downloader:**
  <https://tinyurl.com/2y2pfxvh>

El código de AFTVnews a veces se queda en su página de anuncios/consentimiento antes de
llegar a GitHub; si eso pasa, en Downloader elige **URL** y pega el enlace corto de arriba.
La página directa de GitHub Pages sirve el mismo APK (mismo SHA-256) sin redirecciones.

El código siempre baja la última versión publicada en GitHub
([c28309208/giotube-fusion](https://github.com/c28309208/giotube-fusion/releases)).
Para sacar una versión nueva, sube una release con el archivo llamado exactamente
`GioTube-Fusion.apk`, reemplaza también el de GitHub Pages y el mismo código sigue sirviendo.

## Cómo se usa

- En **Videos**, el riel de la izquierda tiene la entrada **Música**, que abre la sección de MusicFlow.
- En **Música**, la barra de abajo tiene la pestaña **Videos**, que vuelve a GioTube.
- **Descargar audio**:
  - En el reproductor de video, con el botón **Bajar audio**.
  - En el menú de opciones de cualquier video, con **Descargar audio**. El menú se abre manteniendo presionado OK sobre el video, o con la tecla Menú del control.
  - El audio queda en **Música › Descargar** y luego en la **Biblioteca**.

## Cómo compilar

Haz doble clic en `compilar_apk.bat`. Necesitas Android Studio instalado, porque usa su Java y su SDK.
El APK queda como `GioTube-Fusion.apk` en esta carpeta.

## Estructura

| Módulo   | Qué es |
|----------|--------|
| `:app`   | GioTube: la app que se instala y la sección Videos (código en `app.tubelibre.tv`). |
| `:music` | MusicFlow convertido en librería: la sección Música (código en `com.musicflow.app`). |

Las dos secciones se comunican por estos archivos:

- `music/.../MusicSection.kt`: abrir Música, pedir la descarga de un audio y configurar WorkManager.
- `app/.../Music.kt`: el lado de GioTube. Incluye el respaldo que baja el audio de YouTube con NewPipe.

## Detalles a tener en cuenta

- **La sección Música requiere Android 8 o superior.** En equipos más viejos los videos funcionan
  igual, pero la entrada Música y la opción Descargar audio no aparecen.
- **El audio de YouTube se baja con NewPipe**, el mismo motor con el que GioTube reproduce los
  videos. Así funciona igual en equipos de 64 y de 32 bits y no depende de que yt-dlp descifre
  YouTube con QuickJS (en una tele eso podía tardar minutos y dejar la descarga en «Descargando…»).
  yt-dlp queda como respaldo para YouTube y para las demás páginas.
- **Si una descarga se interrumpe** (el sistema cerró la app, se cortó la red…), al volver a abrir
  Música aparece como error con el botón **Reintentar**, en vez de quedarse «Descargando…» para
  siempre.
- **Las descargas se guardan en el almacenamiento interno de la app** (no en `Android/data`), y la
  biblioteca tiene una copia de seguridad en JSON. Si una tele pierde la base de datos al cerrar la
  app, al volver a abrirla se recuperan las canciones cuyos audios sigan en el disco.
- **El análisis de volumen no corre mientras hay audio sonando**, para no quitarle el decodificador
  al reproductor de video. Al volver a Videos, la música se pausa para no pelear por el audio.
- **La biblioteca del MusicFlow original no se pasa sola**, porque es otra app con otros datos.
- **El APK pesa unos 62 MB**, mucho más que GioTube, porque trae yt-dlp, Python y ffmpeg
  para 64 y 32 bits.
