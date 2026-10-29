# Caso técnico: Werewolf

[Volver al README](../README.md) · [English](technical-case.md)

Werewolf es un bot de Discord para jugar a Hombres Lobo sin narrador, con un dashboard web y una base de datos compartida. El código es privado. Este texto cuenta cuatro problemas reales del proyecto, cómo se abordaron y qué sigue abierto. No publica cifras de uso.

## Arquitectura en pocas líneas

- **Bot:** Python y discord.py. Cada partida vive en el canal donde se abre. El estado (jugadores, fase, votos) está separado de los comandos que lo manejan y del motor que lo avanza.
- **Roles como estrategias:** cada rol es una clase con su lógica nocturna, en lugar de una cadena de condicionales. Añadir o cambiar un rol no obliga a tocar el bucle de la partida.
- **Datos:** Supabase (PostgreSQL). El bot y el dashboard leen y escriben el mismo esquema.
- **Dashboard:** Next.js 16 con React 19 e inicio de sesión de Discord.

## 1. Mazos calculados en lugar de configuraciones fijas

Un reparto de roles fijo funciona para una mesa de ocho y se rompe en una de veinte. El bot genera el mazo a partir del número de jugadores y de lo que aporta cada rol, aplica reglas que descartan combinaciones injugables y reserva tres cartas centrales para los roles que las usan. El límite es de 20 jugadores (23 cartas con las centrales).

Para no depender de la intuición, una simulación Monte Carlo juega partidas con agentes heurísticos y mide cómo gana cada bando según el tamaño de la mesa. Forma parte de las comprobaciones de cada pull request. Mide tendencias del reparto, no la calidad de una partida con personas.

## 2. Aislamiento por servidor

Las estadísticas, los logros y la configuración se guardan por servidor: la clave de cada tabla de estadísticas incluye el identificador del servidor. Aun así, una revisión del proyecto encontró un fallo de aislamiento: cambiar el ajuste de anonimato en un servidor podía modificar partidas en curso de otros servidores.

El fallo se reprodujo primero con un test que fallaba y solo después se corrigió. El ajuste actúa ahora únicamente sobre el servidor que lo edita y no desanonimiza una partida Ranked ya empezada. El test se quedó como regresión.

## 3. Un bot que se silencia a sí mismo

El bot se invita sin permiso de Administrador. Eso tiene un efecto secundario: para cerrar el canal mientras se vota se quita a todos el permiso de enviar mensajes, y el bot también deja de poder hablar.

La solución es concederle ese permiso en el canal antes de cerrarlo. Si Discord lo rechaza, el cierre no se aplica: se prefiere una partida con el canal abierto a una partida sin voz. Es una decisión deliberada, con su motivo registrado. La invitación y el dashboard comparten una única definición de permisos para que no se desalineen.

## 4. Identificadores de Discord y JavaScript

Los identificadores de Discord superan el mayor entero que JavaScript representa con exactitud (2^53). Leerlos con un análisis JSON normal los redondea y apunta a otro servidor o usuario. El dashboard trata esos valores como cadenas de principio a fin, con lecturas y escrituras que conservan el valor exacto.

## Pruebas e integración continua

Cada cambio entra mediante una pull request. Un flujo de GitHub Actions comprueba la sintaxis, importa el bot (lo que registra todos los comandos), ejecuta la batería de tests y lanza una simulación Monte Carlo. Superarlo es requisito para fusionar. Los defectos confirmados llevan su test de regresión.

Lo que este conjunto no demuestra: un test en verde no significa que el juego esté completamente probado. No hay pruebas contra Discord real, las simulaciones usan agentes heurísticos y la cobertura no se mide. Reconstruir las pruebas de forma integral es una iniciativa pendiente.

## Lo que sigue abierto

- La recuperación de partidas tras un reinicio guarda un estado serializable, pero no todos los datos de cada modo sobreviven. Está documentado internamente y sin resolver.
- Casual y One Night no puntúan: una revisión encontró que el guardado de partidas les sumaba puntos y logros igualmente, y quedó corregido con tests de regresión. La victoria del Lobo Blanco, que según su descripción debe ser el único superviviente, también se alineó con esa regla. Quedan por revisar otras condiciones de victoria independientes.
- Parte de las pruebas heredadas del proyecto sigue pendiente de revisión.
