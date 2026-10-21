<p align="center">
  <img src="assets/brand/banner.png" alt="Werewolf, Pueblo Duerme: bosque y montañas de noche, luna llena sobre una villa gótica" width="100%">
</p>

<p align="center"><i>El pueblo duerme. La sospecha despierta.</i></p>

<p align="center">
  <a href="https://discord.com/oauth2/authorize?client_id=1386739677729652888&scope=bot+applications.commands&permissions=2252074960251968"><b>Añadir a Discord</b></a>
  &nbsp;·&nbsp;
  <a href="https://werewolfdashboard.vercel.app">Web</a>
  &nbsp;·&nbsp;
  <a href="https://werewolfdashboard.vercel.app/roles">Roles</a>
  &nbsp;·&nbsp;
  <a href="https://werewolfdashboard.vercel.app/dashboard">Dashboard</a>
</p>

Werewolf es un bot de Discord para jugar a Hombres Lobo sin narrador. Reparte los roles por mensaje privado, lleva la noche y el día en el canal del servidor, cuenta los votos y guarda el resultado.

Este repositorio es su escaparate. El código fuente es privado.

<table>
  <tr>
    <td width="50%" align="center" valign="top"><img src="assets/brand/icons/icon-roles-ocultos.png" width="64" alt=""><br><b>28 roles</b><br><sub>Cada jugador recibe el suyo en privado.</sub></td>
    <td width="50%" align="center" valign="top"><img src="assets/brand/icons/icon-dia-noche.png" width="64" alt=""><br><b>Tres modos</b><br><sub>Ranked, Casual y One Night.</sub></td>
  </tr>
  <tr>
    <td width="50%" align="center" valign="top"><img src="assets/brand/icons/icon-votacion.png" width="64" alt=""><br><b>Votación en el canal</b><br><sub>Cada día el pueblo decide a quién eliminar.</sub></td>
    <td width="50%" align="center" valign="top"><img src="assets/brand/icons/icon-jugadores.png" width="64" alt=""><br><b>De 5 a 20 jugadores</b><br><sub>One Night, de 3 a 10.</sub></td>
  </tr>
  <tr>
    <td width="50%" align="center" valign="top"><img src="assets/brand/icons/icon-trofeo.png" width="64" alt=""><br><b>Ranking por servidor</b><br><sub>Perfiles, logros y estadísticas.</sub></td>
    <td width="50%" align="center" valign="middle"><a href="https://werewolfdashboard.vercel.app/roles"><b>Ver los 28 roles</b></a><br><sub>Enciclopedia en la web.</sub></td>
  </tr>
</table>

## Cómo es una partida

1. Alguien abre el lobby con `/start` y los demás entran con `/join`.
2. Cada jugador recibe su rol por mensaje privado.
3. De noche actúan los roles con habilidad. De día el pueblo debate y vota a quién eliminar.
4. La partida termina cuando un bando cumple su objetivo, y el bot anota el resultado.

## Modos

- **Ranked.** La partida clásica. Puntúa para el ranking del servidor.
- **Casual.** Más caótica. No puntúa.
- **One Night.** Una sola noche, de 3 a 10 jugadores y 11 roles propios.

El bot arma el mazo según el número de jugadores y lo que aporta cada rol. Es un reparto calculado, no una garantía de partida equilibrada: el resultado depende de cómo se juegue.

La [enciclopedia de roles](https://werewolfdashboard.vercel.app/roles) explica los 28 roles uno por uno.

## Dashboard

El [dashboard](https://werewolfdashboard.vercel.app/dashboard) usa el inicio de sesión de Discord. Cada persona ve su perfil y el historial de partidas de sus servidores. Quien administra un servidor puede además cambiar sus ajustes desde el navegador y revisar los reportes.

Los administradores también pueden:

- elegir el canal de las partidas y un rol de aviso que `/mention` llama a jugar;
- dar un rol «Jugando» mientras dura la partida y roles por racha de victorias;
- silenciar en el canal a los jugadores eliminados;
- cerrar el canal mientras se vota y, si lo activan, durante la noche;
- activar recompensas de monedas con UnbelievaBoat.

Para gestionar roles, el rol del bot tiene que estar por encima de los roles que asigna.

La página [Meta & Balance](https://werewolfdashboard.vercel.app/meta) muestra cómo se reparten los roles según el número de jugadores.

## Empezar

1. [Invita el bot](https://discord.com/oauth2/authorize?client_id=1386739677729652888&scope=bot+applications.commands&permissions=2252074960251968) a tu servidor.
2. Escribe `/help` para ver los comandos.
3. Abre una partida con `/start`.

<details>
<summary>¿Qué permisos pide el bot?</summary>

No pide permiso de Administrador.

- **Ver canales, enviar mensajes, insertar enlaces y leer el historial.** Para llevar la partida y las votaciones.
- **Gestionar roles.** Para el rol «Jugando», los roles por racha, silenciar a los eliminados y cerrar el canal en las fases que lo piden.
- **Gestionar mensajes, fijar mensajes y añadir reacciones.** Para fijar el lobby y las votaciones y ordenar las reacciones de voto.
- **Mencionar @everyone, @here y todos los roles.** Para que `/mention` pueda avisar al rol configurado aunque no sea mencionable.
- **Enviar mensajes en hilos.** Por si se juega en un hilo.

El bot no necesita intents privilegiados. Si el canal de juego tiene permisos propios (por ejemplo, un canal privado), dale al rol del bot acceso a ese canal.
</details>

<details>
<summary>¿Qué datos guarda?</summary>

Identificadores de Discord (usuario, servidor, canales, roles), el nombre público para mostrarlo en los rankings, las estadísticas de juego y la configuración de cada servidor. No lee ni guarda el contenido de los mensajes del chat. Si un administrador activa la economía de UnbelievaBoat, su token se guarda cifrado.

El detalle está en la [política de privacidad](https://werewolfdashboard.vercel.app/privacy) y los [términos](https://werewolfdashboard.vercel.app/terms). Para pedir que borren tus datos o para cualquier duda, usa el contacto de la [web](https://werewolfdashboard.vercel.app).
</details>

## Sobre el proyecto

Werewolf sigue en desarrollo activo. El bot, el dashboard y los datos forman un solo proyecto que crece versión a versión; las novedades publicadas están en el [historial de cambios](https://werewolfdashboard.vercel.app/cambios).

```mermaid
flowchart LR
  D([Discord]) <--> B[Bot · Render]
  B <--> S[(Supabase)]
  S <--> W[Dashboard · Vercel]
  W -. login .-> D
```

- **Bot:** Python con discord.py. Cada rol tiene su propia lógica nocturna, separada del resto del motor.
- **Dashboard:** Next.js 16 (App Router) y React 19, con inicio de sesión de Discord (Auth.js).
- **Datos:** Supabase (PostgreSQL). Bot y dashboard comparten la misma base; las estadísticas se guardan separadas por servidor.
- **Equilibrio:** una simulación Monte Carlo juega partidas con reparto aleatorio para medir cómo gana cada bando según el número de jugadores.
- **Tolerancia a fallos:** si a Discord le falta un permiso (dar un rol, cerrar un canal), la partida sigue y el bot lo registra en vez de detenerse.
- **Seguridad:** el bot funciona sin permiso de Administrador y el token de UnbelievaBoat se guarda cifrado.
- **Despliegue:** el bot en Render y el dashboard en Vercel, con integración por pull request.

## Enlaces

[Web](https://werewolfdashboard.vercel.app) · [Roles](https://werewolfdashboard.vercel.app/roles) · [Meta & Balance](https://werewolfdashboard.vercel.app/meta) · [Historial de cambios](https://werewolfdashboard.vercel.app/cambios) · [Términos](https://werewolfdashboard.vercel.app/terms) · [Privacidad](https://werewolfdashboard.vercel.app/privacy)

<p align="center">
  <img src="assets/brand/app-icon.png" width="56" alt="Icono de Werewolf: lobo aullando ante la luna llena"><br>
  <sub>© 2026 Werewolf. Todos los derechos reservados.<br>
  Proyecto independiente, no afiliado a Discord ni a UnbelievaBoat. Discord es una marca de Discord Inc.</sub>
</p>
